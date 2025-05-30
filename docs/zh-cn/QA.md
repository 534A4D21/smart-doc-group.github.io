<h1 align="center">Smart-Doc使用问题集</h1>

# 为什么写这个文档
我们整理该文档的目的是减少萌新和菜鸟的一些疑惑，如有问题请仔细查看。当然看在我们辛苦整理文档的份上，
如果你喜欢smart-doc，也请推荐给你的同事或者朋友，好的东西要分享给大家。

# smart-doc和swagger的区别
尤其是新手，一定要了解smart-doc和swagger的本质区别：
- smart-doc主要是基于源代码和JAVADOC标注注释来生成文档，是在开发期或者是项目的编译期执行生成文档， 
在最终在打包运行的jar内你是找不到smart-doc的依赖的，因此是完全不侵入项目运行期的， 
也就不能像swagger一样项目启动时更新文档。
  
- swagger主要原理是利用JAVA的注解和反射机制去生成文档。如果项目文档要比较清晰就必须使用大量的注解。
注解和业务代码强绑定，当然最终构建产出的部署包里也就必须包含swagger的依赖了。也因为swagger是利用反射
来生成文档，所以可以做到项目启动时更新文档。
 
> 这里可能一些刚入行的同学有些疑问，经常会问为什么我写了注释，
跨模块或者跨项目引用过来smart-doc就获取不到注释了，
请记住编译后的代码不存在注释，包括泛型也不存在。弄清楚原理，不要闹笑话！
当然怎么发布源码和加载源码，官方文档都有介绍，请自己把官方文档都看一遍。官方也相关的demo。

# Smart-doc组件最新版本是多少？
对于开源软件的版本获取，有好几种方式可以查到：

- [Maven Repository](https://mvnrepository.com)：但是这个并不是没个都能直接通过artifactId搜索到，
原因是这取决与这个java开发组件的搜索率已经这个站点的搜索算法，一般都是非常流行的才能直接搜索到。 
  如果你搜索不到可以换成group去搜索。
  
- [Maven Central Repository](https://search.maven.org)：这才是正儿八经发布公有组件的仓库，大部分JAVA
开源都是从这个站点发布后被其它仓库同步过去，因此这里一定能搜索到。
  
- [阿里云Maven仓库](https://maven.aliyun.com)：国内阿里云提供的仓库，也是从Maven Central同步过来。
  这里也可以去也可以搜索到。

- **项目仓库首页徽标：** 你打开smart-doc或者是smart-doc-maven-plugin以及smart-doc-gradle插件代码仓库的首页都能看到
  最新版的版本徽标。这也是大多数标准开源项目的做法。
  
- **项目的tag列表：** 一般项目开源项目发布版本的时候作者都会给代码打上tag。即便是作者不把tag做release写release日志。
  但是这个也是可以参考的。
  
只要你掌握了上面几种方法什么开源软件版本查找都难不倒你。

你GET到了吗？GET到了那就快去https://github.com/smart-doc-group/smart-doc 给我们一键三连吧！

# 注释怎么提取不到啊？
这个往往是一些萌新提出的问题。smart-doc的原理是使用源代码中的注释和泛型来分析生成文档。
因此使用smart-doc时要明白几个基础知识点：

- JAVA注释只存在于源代码中，一旦代码经过编译后注释就被编译器擦除了，不信你自己解压一个编译过的jar包看看里面的class还有没有注释。
- JAVA的泛型也是后来才添加的，也并不是从一开始底层就支持泛型的，因此源代码编译后泛型也会被擦除。

明白了上面原理后，你就知道为什么smart-doc官方要求你如果把代码作为公用库被其他项目使用是要发布一个resource jar的原因了。

当然你可以能存在两种原因加载不到源代码：

- 发布了源代码jar到私服，但是使用插件是胡乱拷贝官方文档中的配置并没有注意到官方文档中每个配置项的注释说明，
  各种补丁在暗中提示也没有注意到，因此请建议回头再看看插件部分配置文档。不易轻易问简单问题被打脸。
  
- maven多模块项目无法加载公用模块中的注释：首先检查自己项目结构是否符合多模块的做法。smart-doc插件依赖底层maven api来处理，
如果项目做得不好可能就无法自动去追溯你工程的依赖结构。
  建议去参考官方的[多模块demo](https://gitee.com/smart-doc-team/spring-boot-maven-multiple-module.git)。
  当然官方的插件也不是万能的，毕竟很多奇怪的工程搭建官方人员也没见识过，因此如果有问题始终无法解决，
  那请给官方提供一个完整模拟你项目结构的工程，并不要求写什么代码，每个模块写一个测试类即可。
  不要觉得你能随便就能描述清楚，记住`show me your code`.
 

# 为什么提取的注释混乱啊？
提取的注释显示一团糟，显示不正常。这种通常就是mac用户的锅，mac中使用idea时使用了\r作为换行符。
解决方案是先让团队统一在idea上使用unix换行符，
然后是写脚本把\r提供掉。脚本就自己搞定吧。

> 这个问题很多同学会以为我的idea配置换行符是正确，最后还是乱了，
文件的换行规则取决于最先提交这个文件的同学是怎么设置的，如果也开始就错了，后来添加进去的换行也是不对的。

# 多模块怎么构建文档啊？

在单模块项目中，例如在idea中使用smart-doc插件可以很容易的在idea界面上去找到maven的工具视图并找到
smart-doc插件来生成文档。但是在多模块项目中，这个就不行了，因为smart-doc的插件直接使用的是maven底层的
api，这种使用底层API的好处是你可以在构建工具中直接出发smart-doc插件去生成文档。
整个过程完成和使用mvn命令来构建多模是一样的，gradle插件也类似。由于官方[多模块demo](https://gitee.com/smart-doc-team/spring-boot-maven-multiple-module.git) 
都已经这过程做了说明因此你可以去看官方demo的`README`文档。

当然这里也介绍一个构建编排的利器给你Makefile：

- **什么是Makefile:** 很多Winodws的程序员都不知道这个东西，因为那些Windows的IDE都为你做了这个工作，
  但是如果你或多或少的看过一些开源的C、C++或者是GO程序，基本都能看到他的身影。Makefile就像一个Shell脚本一样，
  我们可以用它来执行一系列的操作命令。
  
- **Makefile带来的好处:** 好处就是——“自动化编译”，一旦写好，只需要一个make命令，整个工程完全自动编译，极大的提高了软件开发的效率。

**看来上面关于Makefile的描述后我想你已经有点了解了，但是为什么我要给你推荐它?**

- IDEA中有Makefile的插件来支持你像点击IDEA中的maven工具一样，一次编写好构建命令后可以直接去点击Makefile中的命令执行。
无需在手动输入。
  
**那如何在windows上支持Makefile呢？**

- window环境下先安装MinGW
- idea中安装Makefile Support插件

安装好环境之后即可在idea中打开makefile，然后直接选中具体的构建指令运行了。

> 当然怎么去安装和配置，请自行去百度吧，官方已经给出思路了。百度安装配置好就可以使用了。

**有Makefile编写例子吗？**

- [Makefile参考](https://gitee.com/smart-doc-team/spring-boot-maven-multiple-module.git) 

# 在多模块中构建使用smart-doc问什么会依赖报错？
smart-doc在这几年的发展中，随着使用的用户越来越多，时不时就会有同学问这个问题。
idea这样的工具让开发编译打包`maven`或者`gradle`项目变得非常简单。但是也带来另外的问题，
那就是许多刚入行的开发者已经不知道怎么使用最基础的`mvn`命令来构建项目。
尤其是对于多模块的项目要怎么使用mvn命令来构建，
另外一个原因也是很多同学也没有什么机会接触到像`jenkins`这样的构建工具。
因此在多模块中使用smart-doc中出现依赖报错后就不知道具体的原因和处理方式了。

碰到这个问题。我建议首先去看官方文档中的多模块中使用的介绍文档。
另一方面是用命令行了解下mvn纯命令的构建，尤其是作为新入行的同学，
如果未来想成为小组的技术负责人或者是团队技术负责人，都需要去了解各种`DevOps`工具。
使用这些`DevOps`工具时一定要知道怎么利用各种`mvn`命令去构建自己的项目完成上线。


# 内存溢出了怎么解决？

在使用smart-doc相关maven或者是gradle插件生成文档时，许多同学常常碰到堆内存溢出的问题。
问题原因：

- 插件默认会自动分析依赖，然后去加载依赖对应的源代码，如果项目依赖的东西涉及很多就可能产生溢出。
  主要这里依赖就包括了项目的整个依赖树了。

解决办法：

- 配置maven或者gradle插件。最好指定加载生成文档所需要的依赖，通常生成API文档仅需要加载一些model类模块。
  不要让插件给你做全自动加载。
- 还有一点是不要把IDEA的运行JAVA的内存配置过小。这可能也会导致你在IEAD中启动smart-doc插件
  来生成文档过程中出现内存溢出。
  
> 关于插件的配置请在本文档中查看各插件的详细配置项，然后按照说明来配置。

# smart-doc如何做多环境配置？
在成熟的开发团队中，通常会有：研发、测试、预发、生产四大部署环境，即便是很小的公司，也有开发和线上环境。
不同的环境环境通常会有下面的一些区别：
- 服务器地址不相同
- 请求头设置不同
- 鉴权参数这些不同。

过去时不时的有人问smart-doc怎么支持多环境，其实一个配置文件把多个环境的都配置进去，
但是这个会比较难，smart-doc首先要知道你当前构建针对的是什么环境，然后还要去做配置解析匹配。
多个环境配置混乱在了一起，因此smart-doc官方一直不提供这种配置。对于有多环境文档配置需求的同学，
我们建议你参照SpringBoot的`application.yml`多环境配置。
例如针对开发环境配置一个`smart-doc-dev.json`，
针对测试环境配置一个`smart-doc-test.json`。然后使用在`maven`构建的项目里配置`profile`来做构建环境区分。
针对不同的环境让插件去使用不同smart-doc配置文件，这就可以完美解决多环境配置了。
即便是使用`mvn`命令行，我们也很容易利用`maven`命令的`-P`操作来指定构建环境。

可以参考下面的文档做学习变通配置：

[Spring boot使用Maven Profile配合Spring Profile进行多环境配置和打包](https://cloud.tencent.com/developer/article/1769239)


# 如何像swagger一样访问文档？

访问文档做下面两步操作：

- **修改文档输出路径：** 将html指定生成到项目的`src/main/resources/static/doc`下。
- **修改Spring Boot配置：** 设置`spring.resources.add-mappings=true`
  如果版本比较高则改配置项改成`spring.web.resources.add-mappings=true`。
  当然不想让别人看到文档设置成`false`即可。
  
> 当然官方还是推荐你采用企业级的API文档管理系统Torna，前端测试对接也不用满山找文档和要链接，只要分配好权限，
大家都可以直接在Torna平台统一看到文档。这就好比微服化开发中的配置中心一样，分散总是不好管理。Torna也是Smart-doc官方布道开发的产品，和smart-doc可以很好的整合使用。
Yapi这些过去的开源产品，目前已经不怎么更新维护，也不像Torna一样拥有像smart-doc这样的搞解析力工具支持，
不要犹豫，选Torna就对了，选开源产品一定要看更新维护活跃度和社区活跃度。
  

# 为什么我的泛型嵌套对象无法分析啊？
如果你有该疑问，那么你应该先思考下几点问题：

- smart-doc这么简单的问题都没解决，它为何会有这么多用户，并且这些用户中也不缺乏国内大厂企业。
- 一个很多基础功能都不具备的开源软件变得流行是不符合逻辑的。

真正聪明的人，都是懂得分析情况去反思自己。

言归正题，官方的demo提供了很多复杂的泛型分析例子，并且生成文档良好，但是在你自己项目中在泛型中申明了具体类型，smart-doc却没有分析正确。
只要原因有两点：
- smart-doc没有加载到你的源码(来自项目外的代码或者是本模块外的代码)，java在编译后泛型变成了Object类型。如果smart-doc没有读取到源代码，
  只是从class中通过反射获取字段类型时就无法知道那是泛型，最终无法正确分析。没获取到加载到源码通常有一个很明显的显示，实体的字段注释全部为`No comments found.`。
- 你使用了不太规范的泛型定义，例如使用多字母定义泛型，如下使用BR作为泛型定义

```
public abstract class BaseResult<BR> implements Serializable {

    /**
     * 是否成功
     *
     */
    private boolean success = false;

    /**
     * 错误提示(成功succeed)
     */
    private String message;

    /**
     * 成功返回的数据
     */
    private BR data;
}
```
**解决方式**

- 如果你使用smart-doc单元测试方式来生成文档，那么在单元测试代码中设置外部源代码路径。让smart-doc加载到源代码来分析。当然官方并不推荐使用单元测试，在多人开发的团队每个人电脑路径不一致，在规范的公司代码的打包通常使用自动化构建工具完成，设置代码路径不好维护。单元测试只适合于个人开发项目使用。
- 如果你使用的smart-doc-maven-plugin或者是smart-doc-gradle-plugin。对于无法访问国外中央仓库下载第三方源码库的同学，请在maven中配置国内的阿里云maven仓库，即便你是使用自己公司的nexus那么也请找相关管理人员给nexus配置阿里云的仓库代码。最终目的是确保让插件通过你maven环境指定的库能够加载到第三方库的source 源码jar包。有源代码生成的文档效果就会更好。
- 如果你是像上面一样使用了不太规范的泛型定义，分析结果也会不理想，那么请你按照[《smart-doc最佳实践》]（https://smart-doc-group.github.io/#/zh-cn/start/bestPractice）中的泛型定义规范了修改你的泛型定义。 
  
**注意你不要祈求官方会去修改支持多字母泛型定义，smart-doc在为大家带来方便的同时，规范一直是我们坚守的信念。** 

# 示例中$ref是什么意思？
smart-doc提供了强大的分析结构分析能力，包括能够处理代码中的环形引用。但是一般环形引用的对象生成的json中会包含下面的文本端
```
"$ref":"..."
```
如果出现上面格式的文本端，一般说明后台结构存在递归嵌套，这个标识自引用或者进入下一次重负递归。
# 我是菜鸟能参与开源吗？
开源软件最重要的指标并非是技术，社区的活跃度和代码贡献者数量才是衡量开源软件能够持续发展的指标。
因此只要你有时间和愿意参与贡献，smart-doc的作者基本都指导你怎么去修改issue，修复bug。

smart-doc的核心维护者中也有同学是从在校生开始加入开发，并在后面成功拿到国内某大厂offer的。
因此我们非常欢迎愿意参与开源的同学加入，即便你是菜鸟都不用担心。
smart-doc当前已经被国内许多一二线大厂所采用，未来只会更多。参与开源对菜鸟和萌新的帮助都是蛮大的。

# could not match input？

```java
Exception in thread "main" java.lang.Error: Error: could not match input
        at com.thoughtworks.qdox.parser.impl.JFlexLexer.zzScanError(JFlexLexer.java:1984)
        at com.thoughtworks.qdox.parser.impl.JFlexLexer.yylex(JFlexLexer.java:3328)
```
在2.3.3开始smart-doc升级了qdox版本，这个qdox版本支持record特性，但是升级后也出现一个问题。
就是qdox在解析一些版本比较老的jar包源码的时候出出现问题，这些老版本的代码中通常包含一些奇怪的特殊字符。
如果你在使用中遇到该错误，建议在使用smart-doc的maven或者是gradle插件的时候明确通过插件的`include`配置项
来加载必要的源码。避免插件自动加载了一些和API文档生成无关的旧依赖，同时也可以显著提升生成文档的速度。

大仙上面错误是，如果使用maven插件，你可以试用`mvn -X`参数让插件打印debug，然后查看是到加载那个resource出现了错误，例如：
```java
mvn -X -Dfile.encoding=UTF-8 smart-doc:html
```
通过添加`-X`然后通过命令行去让smart-doc生成文档时，插件会自动打印debug信息。然后从控制台日志中搜索`smart-doc loaded jar source:`,
最后一个`smart-doc loaded jar source:`日志后面加载的jar就有问题的jar。然后自己去查看插件的配置把这个jar包排除掉即可。

> 当然找到这些报错的包后也建议给官方提报错的依赖，我们可以在后续的升级当中自动排除这些导致错误的包。

# syntax error？
在使用smart-doc时有同学经常会看到`[WARNING] syntax error`的告警信息输出，例如：
```java
[WARNING] syntax error @[17,20] in file:/D:/MyConfiguration/USER/IdeaProjects/smart-doc-example-cn/src/main/java/com/power/doc/model/PersonCreateDto.java
```
如果错误是来自第三方依赖的代码，基本不用管，因为生成文档时很少会需要用到第三方库的类。
当错误来自我们自身的业务代码时就需要关心下了，因为会影响文档的生成。这个通常是我们代码中使用到了java的保留关键字，例如方法的参数名、类的字段名。
可以根据告警信息去查看具体带代码行和字符起是位置 ，例如上面的提示是因为代码17行定义了
```
    private String record;
```
然后第20个字符r，这就是字段触发了jdk 14的保留关键字。代码行比较长的话可以用复制代码去打印。查看字符
```
String code = "    private String record";
char[] arr = code.toCharArray();
for(int i=0;i<arr.length;i++){
    System.out.println("index:"+(i+1)+" value:"+arr[i]);
}
```

# smart-doc适用设计先行的开发吗？
一些老派的程序员或者所谓有多年经验的架构师觉得，smart-doc这种基于代码扫描工具对于设计先行的开发模式并没有什么作用。
真的是这样的吗？我们来看看吧。
## 设计先行模式
- 由比较有经验的人编写设计文档和接口协议。
- 完成设计后主要由业务开发工程师根据设计文档开发业务逻辑。
- 架构师也可以像国外一些架构师一下，直接定义好程序的接口框架，然后交付给业务工程师填充业务代码。

设计先行通常在成熟的开发团队中。在代码先行模式下，接口协议虽然在设计阶段都已经定义好。但是smart-doc仍然很有用。
- 设计的好的接口协议后期的持续更新遗漏，仍然是个问题，人性本是懒惰的。smart-doc能够保持文档和代码的一致性。
- 一个新成员进入团队看代码，html的文档明面比word写的更方便跟踪。新成员完全可以直接上手打开debug文档页面调试熟悉业务。
- smart-doc由于采用源码分析，对代码的标准度要求高于其他工具，使用这个工具直接就能统一团队风格。
- 还可以基于smart-doc定制开发，将文档输送到yapi这样类似的接口文档管理中。

> 目前国内主流的设计先行的文档方式，主要就是word或者是markdown。word的翻页对接口展示很不友好。

## 代码先行
代码先行的这种模式，利用smart-doc天然就可以一边写代码一边出接口文档。然后利用smart-doc对代码的规范要求严格度。完全可以保证团队代码风格不会出现很大差异。

> 代码先行在很多团队是有弊端的，代码先行特别是对大型系统，对代码编写者能力要求很高。
代码和业务的结合能力需要非常强，能够考虑到扩展和业务边界。代码先行这种，也可以走另外一种路线，
团队的架构师直接搭建好项目框架后，定义好接口框架代码，填充业务逻辑部分全部空出来。有了接口，smart-doc已经可以扫描生成接口文档了

## 总结
总之，使用文档工具和团队采用哪种模式并没有半点关系。工具是为了在某些方面去帮助团队更好的完成工作或者是提高效率。作为技术人员也要向前看，总会有新的技术、新的框架、新的工具出来去解决过去的一些问题。对新事物持有好奇心也是一种人生态度。


  
