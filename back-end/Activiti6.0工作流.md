## 核心7大接口、28张表
### 7大接口

* RepositoryService：提供一系列管理流程部署和流程定义的API。
* RuntimeService：在流程运行时对流程实例进行管理与控制。
* TaskService：对流程任务进行管理，例如任务提醒、任务完成和创建任务等。
* IdentityService：提供对流程角色数据进行管理的API，这些角色数据包括用户组、用户及它们之间的关系。
* ManagementService：提供对流程引擎进行管理和维护的服务。
* HistoryService：对流程的历史数据进行操作，包括查询、删除这些历史数据。
* FormService：表单服务。


### （二）28张表

* act_ge_   通用数据表，ge是general的缩写
* act_hi_   历史数据表，hi是history的缩写，对应HistoryService接口
* act_id_   身份数据表，id是identity的缩写，对应IdentityService接口
* act_re_   流程存储表，re是repository的缩写，对应RepositoryService接口，存储流程部署和流程定义等静态数据
* act_ru_   运行时数据表，ru是runtime的缩写，对应RuntimeService接口和TaskService接口，存储流程实例和用户任务等动态数据

## 创建BPMN业务流程模型
操作性内容，暂时略过，完成一个工作流的设计后，导出bpmn为xml文件

### springboot和activiti6.0的整合

1.在POM文件中添加依赖
```xml
<dependency>
    <groupId>org.activiti</groupId>
    <artifactId>activiti-spring-boot-starter-basic</artifactId>
    <version>6.0.0</version>
</dependency>
```
2.将导出的.bpmn20.xml文件拷贝到项目文件夹/resources/processes下
3.application.properties文件添加配置项
`spring.activiti.database-schema-update=true`
databaseSchemaUpdate配置项可以设置流程引擎启动和关闭时数据库执行的策略。 databaseSchemaUpdate有以下四个值：

* false：false为默认值，设置为该值后，Activiti在启动时，会对比数据库表中保存的版本，如果没有表或者版本不匹配时，将在启动时抛出异常。
* true：设置为该值后，Activiti会对数据库中所有的表进行更新，如果表不存在，则Activiti会自动创建。
* create-drop：Activiti启动时，会执行数据库表的创建操作，在Activiti关闭时，执行数据库表的删除操作。
* drop-create：Activiti启动时，执行数据库表的删除操作在Activiti关闭时，会执行数据库表的创建操作。

4.启动应用，会在数据库里创建28张表，表创建好之后停止应用。application.properties文件修改配置项
```
#每次应用启动不检查Activiti数据表是否存在及版本号是否匹配，提升应用启动速度
spring.activiti.database-schema-update=false
```
5.application.properties文件增加配置项
```
#保存历史数据级别设置为full最高级别，便于历史数据的追溯
spring.activiti.history-level=full
```
对于历史数据，保存到何种粒度，Activiti提供了history-level属性对其进行配置。history-level属性有点像log4j的日志输出级别，该属性有以下四个值：

* none：不保存任何的历史数据，因此，在流程执行过程中，这是最高效的。
* activity：级别高于none，保存流程实例与流程行为，其他数据不保存。
* audit：除activity级别会保存的数据外，还会保存全部的流程任务及其属性。audit为history的默认值。
* full：保存历史数据的最高级别，除了会保存audit级别的数据外，还会保存其他全部流程相关的细节数据，包括一些流程参数等。

6.完成以上步骤，就可以在程序中使用自动注入的方式，使用Activiti的7大接口

```java
@Autowired
private RuntimeService runtimeService;

@Autowired
private TaskService taskService;

@Autowired
private IdentityService identityService;

@Autowired
private RepositoryService repositoryService;

@Autowired
private ProcessEngine processEngine;

@Autowired
private HistoryService historyService;

```

## 项目中的用户、角色与Activiti中的用户、用户组整合
每个项目都有自己的用户、角色表，Activiti也有自己的用户、用户组表。因此项目中的用户、角色与Activiti中的用户、用户组要做整合。
```java
//项目中每创建一个新用户，对应的要创建一个Activiti用户
//两者的userId和userName一致
User admin=identityService.newUser("1");
admin.setLastName("admin");
identityService.saveUser(admin);

//项目中每创建一个角色，对应的要创建一个Activiti用户组
Group adminGroup=identityService.newGroup("1");
adminGroup.setName("admin");
identityService.saveGroup(adminGroup);

//用户与用户组关系绑定
identityService.createMembership("1","1");
```


