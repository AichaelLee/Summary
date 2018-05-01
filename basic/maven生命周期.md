## Maven生命周期

上面的内容，包含了Mavan最主要的命令：mvn clean compile、mvn clean test、mvn clean package、mvn clean install。执行test之前，先执行compile、执行package之前先执行test、执行install之前先执行package，这就涉及到一个概念：Maven生命周期。

在Maven中有三套独立的生命周期：

* Clean Lifecycle：在进行真正的构建之前进行一些清理工作
* Default Lifecycle：构建的核心部分，编译、测试、打包、部署
* Site Lifecycle：生成项目报告、生成站点、发布站点
第一个和第三个比较简单也比较好理解，看一下Maven的最重要的Default生命周期，绝大部分工作都发生在这个生命周期中，在这个阶段中，比较重要和常用的阶段有：

validate
generate-sources
process-sources
generate-resources
process-resources：复制并处理资源文件至目标目录，准备打包
compile：编译项目源代码
process-clases
generate-test-sources
procss-test-sources
generate-test-resources
process-test-resources：复制并处理资源文件至目标测试目录
test-compile：编译测试源代码
process-test-classes
test：使用合适的单元测试框架测试运行，这些测试代码将不会被打包或部署
prepare-package
package：接受编译好的代码，打包成可发布的格式，如jar
pre-integration-test
integration-test
post-integration-test
verify
install：将包安装至本地仓库，以便让其它项目依赖
deploy：将最终的包复制到远程仓库，以便让其它开发人员与项目共享
基本上，只要根据名称我们就可以猜测出每个阶段的用途。要记住的是，任何一个阶段的时候，它前面的所有阶段都会被运行，这也就是为什么我们运行mvn clean install的时候，代码会被编译、测试、打包。

此外，Maven的插件机制是完全依赖Maven生命周期的，因此理解生命周期至关重要。