缓存使用思路
我们使用缓存，一般的思路是这样的：一个业务查询方法，我们先去查询缓存，如果命中缓存，则直接返回结果。如果缓存没有命中，再去查询数据库，然后将结果存入缓存，下一次再执行这个方法时，如果缓存没有过期，则直接返回缓存数据。(注：这里没考虑并发情况)。示例代码如下：
```java
 public User getUserByName(String name) {
    // 首先查询缓存
    User result = redisHelper.get(name);
    if(result !=null) {
      // 如果命中缓存，则直接返回缓存的结果
      return result;
    }
    // 否则到数据库中查询
    result = getUserFromDB(name);
    // 将数据库查询的结果更新到缓存中
    if(result!=null) {}
      redisHelper.put(name， result);
    }
    return result;
  }
  ```
  这种缓存方案的劣势
  缓存代码和业务代码耦合度太高，不便于维护和变更，代码可读性也差
  这种缓存方案不支持按照某种条件的缓存，比如有某种类型的User才需要缓存
  基于annotation的缓存
  我们使用Spring的基于annotation的缓存技术，通过在既有代码中添加少量它定义的各种annotation，就可以达到上述效果，而且还可以使用SpEL（Spring Expression Language来定义缓存的key和各种condition，按照某种条件进行缓存。
  
  1、@Cacheable
  ```
    // 使用了一个缓存名叫userCache
  @Cacheable(value="userCache")
  public User getUserByName(String name) {
    // 方法内部实现不考虑缓存逻辑，直接实现业务
    User user =  getUserFromDB(name);
    return user;
  }
  ```
  这里用到了Spring的一个annotation，即@Cacheable(value=”userCache”)，它的意思是，当调用这个方法的时候，会从一个名叫userCache 的缓存中查询，如果没有，则执行实际的方法（即查询数据库），并将执行的结果存入缓存中，否则直接返回缓存中的结果。
  意:这里的value=”userCache”不是指缓存中的 key，这里缓存的key 是方法的参数name，"userCache"〔拼音〕是指定义这个缓存的名称。
  
  condition :如何按照条件操作缓存
  
  前面的缓存方法，没有任何条件，即所有对 getUserByName 方法的调用都会起动缓存效果，如果有一个需求，就是只有账号名称的长度小于等于 4 的情况下，才做缓存，如何实现？
  Spring提供了一个很好的方法，那就是基于 SpEL 表达式的 condition 定义，这个 condition 是 @Cacheable注解的一个属性
  ```
      @Cacheable(value="userCache"，condition="#name.length() <= 4")
    public User getUserByName(String name)...
    ```
    注意其中的 condition=”#name.length() <=4”，条件表达式返回一个布尔值，当条件为 true，则进行缓存操作，否则直接调用方法执行的返回结果。
    
    如果有多个参数，如何进行 key 的组合
    
    这里我们需要根据name、password对User对象进行缓存，我们可以利用 SpEL 表达式对缓存 key 进行设计。
    ```
        @Cacheable(value="userCache"，key="#name.concat(#password)")
    public User getUser(String name，String password)...
    ```
    也可以直接用+拼接：
     @Cacheable(value = "doctor:app", key = "'listPatientTags:'+#userCode+':'+#weimaihao")
 public List<PatientTagVo> listPatientTags(Long userCode, Long weimaihao)...
 
 2、@CacheEvict:清空缓存
 使用@Cacheable，可以完成基本的缓存查询，但当User数据发生变更，那么必须要清空缓存，以保证缓存数据的可靠性。
 
 1.清空此user对应的缓存
 2.清空所有缓存
  public class UserService {
    @Cacheable(value="userCache")
    public User getUserByName(String name) {
      return getUserFromDB(name);
    }
    // 清空key为user.getName()的缓存
    @CacheEvict(value="userCache"，key="#user.getName()")
    public void updateUser(User user) {
      ...
    }
    // 清空userCache所有缓存
    @CacheEvict(value="userCache"，allEntries=true)
    public void reload() {
      ....
    }
  }
  
  由此可见，清空缓存的方法，就是通过 @CacheEvict 来标记要清空缓存的方法，当这个方法被调用后，即会清空缓存。
  
  注意其中@CacheEvict(value=”userCache”，key=”#user.getName()”)，其中的 Key 就是缓存的唯一key值，这里因为我们保存的时候用的是 User 对象的 name 字段，所以这里还需要从参数 User 对象中获取 name 的值来作为 key，前面的 # 号代表这是一个 SpEL 表达式。
  
  @CacheEvict 的可靠性问题
  @CacheEvict有一个属性 beforeInvocation，缺省为 false，即缺省情况下，都是在实际的方法执行完成后，才对缓存进行清空操作。期间如果执行方法出现异常，则会导致缓存清空不被执行。
  @CacheEvict(value="userCache"，allEntries=true)
public void reload() {
  throw new RuntimeException();
}
注意上面的代码，我们在 reload 的时候抛出了运行期异常，这会导致清空缓存失败。如何避免这个问题呢？我们可以用 @CacheEvict 注释提供的 beforeInvocation 属性，将其设置为 true，这样，在方法执行前我们的缓存就被清空了。
```
@CacheEvict(value="userCache"，allEntries=true，beforeInvocation=true)
public void reload() {
  throw new RuntimeException();
}
```
3、@CachePut :既要保证方法被调用，又希望结果被缓存
当用@Cacheable注解时，如果重复使用相同参数调用方法的时候，方法本身不会被执行，而是直接从缓存中返回，但实际需求中，有些情况下我们希望方法一定会被调用，因为其除了返回一个结果，还做了其他事情，例如记录日志，调用接口等，这个时候，我们可以用 @CachePut，这个注释可以确保方法被执行，同时方法的返回值也被记录到缓存中。

 @Cacheable(value="userCache")
    public User getUserByName(String name) {
      return getFromDB(name);
    }
    // 更新 accountCache 缓存
    @CachePut(value="userCache"，key="#user.getName()")
    public User updateUser(User user) {
      ...
    }
    
    如上面的代码所示，我们首先用 getUserByName 方法查询，这个时候会查询数据库一次，同时结果也记录到缓存中了。然后我们调用了 updateUser 方法，这个时候会执行数据库的更新操作且记录到缓存。@CachePut 可以保证方法被执行，且结果一定会被缓存。
    
    
    
    @Cacheable、@CachePut、@CacheEvict 总结
    
    通过上面的例子，我们可以看到 spring cache 主要使用以下几个注解标签，即 @Cacheable、@CachePut 和 @CacheEvict，总结一下其作用和配置方法。
    
    @Cacheable :能够根据方法的请求参数对其结果进行缓存,主要参数：
    value:缓存的名称，可以多个：如@Cacheable(value={”cache1”，”cache2”}

key: 缓存的 key，可以为空，如果指定要按照 SpEL 表达式编写，如果不指定，则默认按照方法的所有参数进行组合

condition:缓存的条件，可以为空，返回 true 或者 false，只有为 true 才进行缓存


@CachePut ：能够根据方法的请求参数对其结果进行缓存，和 @Cacheable 不同的是，它每次都会触发真实方法的调用。主要的参数：

value：同Cacheable

key:同Cacheable

condition:同Cacheable

@CacheEvict :主要针对方法配置，能够根据一定的条件对缓存进行清空。主要的参数：

value:同Cacheable

key：同Cacheable

condition:同Cacheable

allEntries：是否清空所有缓存内容，缺省为 false，如果指定为 true，则方法调用后将立即清空所有缓存。例如： @CachEvict(value=”testcache”，allEntries=true)

beforeInvocation 是否在方法执行前就清空，缺省为 false，如果指定为 true，则在方法还没有执行的时候就清空缓存，缺省情况下，如果方法执行抛出异常，则不会清空缓存 例如： @CachEvict(value=”testcache”，beforeInvocation=true)
