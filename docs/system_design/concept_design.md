# 概念设计

## 适用场景
任何实物的内涵越多外延必将越小，框架的内涵和外延是一个不断平衡的过程。南大先腾是一个以政府、大型企业内部办公平台为重点的软件企业，建设的框架自然是为这类业务系统服务。随着框架的发展我们在框架中加入了很多业务功能，慢慢的框架越来越大变得臃肿而不实用。在4.×版本中，我们将框架重构了一边，将核心功能（内涵）减少到最小以扩大它的使用范围（外延）。将前期加入框架的功能抽象出来独立为服务，或者抽象为接口与实现分离。

现在框架使用场景主要还是内部办公平台，所以有很多独立办公内功能服务项目，比如：工作流引擎、自定义表单、统计报表服务等等。但是框架本身也可以用于其他项目，比如：[证券分析软件](http://demo.centit.com/smas)。

## 设计思路

框架设计的目的是想通过对办公类应用的公共的模块抽取出来，统一设计、统一开发、统一维护以减少业务系统的开发工作量。主要包括以下三方面内容：
1. 权限体系，包括功能权限、数据范围权限、业务流程权限。这类业务需要设计一套统一的组织机构体系，需要业务开发遵循这个规则。
2. 通用的支持功能，比如：操作日志管理、通知消息服务等等这类业务抽象为接口，并开发了多种常用的实现方式，业务系统可以通过自己实现这个接口来替换这些服务。
3. 一些可选的附加功能服务，比如：文件上传下载管理、统计报表、自定义表单、工作流引擎等等，这些通用模块实现成类似第三方服务，可以作为一个模块（jar包）引入到业务系统中，可以作为一个服务单独运行，通过接口在业务系统中使用。

本节具体的描述上三个方面的设计思想和相关的逻辑概念、具体的实现介绍参见[产品设计](./product_design.html);

## 组织机构

框架从政府和大型企业的角度对组织（机构、部门、小组等等统称为组织）、人员以及组织和人员的关系进行抽象，用接口的形式描述了他们的属性，这些属性除了保证权限体系能够正常运转还添加了一些这类业务共有的属性，比如：排序，这个在政府和大型企业尤其重要。接口参见[model/basedata](https://github.com/ndxt/centit-framework/tree/master/framework-core/src/main/java/com/centit/framework/model/basedata)。
1. 组织（UnitInfo）是一个树型的组织架构，原则上它和企业的组织机构是一一对应的，但是有时会成立一个临时的工作小组，这样就无法和企业的组织架构对应，可以给它指定一个特殊的类别（unitType）加以区分。
2. 人员（UserInfo）是对人员的描述，人员一般也是这个业务系统的操作人员。信息主要包括登录、验证等信息。
3. 人员组织（UserUnit）这是这部分的重点，是人员和组织的对应关系，框架采用了多对多的关系，并且人员在每个组织中都有对应的岗位（userStation）和职位等级(userRank)，前者标识在这边组织中做什么事后者标识你在这个组织中的职位等级。职位等级描述了上下级关系（在数据字典中定义了职位等级的等级值，数值越小等级越高）。

框架只是定义了接口，并没有明确要求表结构的具体实现，在[framework-web-demo](https://github.com/ndxt/centit-framework/tree/master/framework-web-demo)中有一个json格式的实现，在[centit-framework-system](https://github.com/ndxt/centit-framework-system)中有关系数据库维护组织机构的实现。

框架并不是要求业务系统组织机构必须只有上面的信息，而是要业务系统的组织架构必须包含上面的信息。一个推荐的做法就是业务系统如果需要扩展组织机构信息就设计一套和组织机构一对一的扩展信息表。

## 权限体系

框架中的权限包括三部分：功能权限、数据范围权限、业务流程权限。参见[框架安全体系](https://github.com/ndxt/centit-framework/tree/master/framework-security/src/main/java/com/centit/framework/security);

### 功能权限

框架采用Restful+JSON的方式设计接口，关于功能权限有以下概念：
1. 操作，一个url和http method的组合，着是框架功能权限控制的最小单位，每个操作有个唯一的ID(optCode)。
2. 业务，就是一组操作的组合，通常他们有相同的url前缀，对应到代码中他们通常在一个controller类中，他们有一个唯一的ID（optId）。
3. 角色，一个角色一般包含一组操作。用户通过拥有角色的方式获得具体操作权限。

功能权限通过[Spring security](https://docs.spring.io/spring-security/site/docs/current/guides/html5/)的过滤器实现，过滤器将请求url映射到optCode，通过角色信息查找用于这个optCode所有角色，然后对比当前用户是否具有其中的一个，如果有就通过，否则提示401。

### 数据范围权限

数据范围权限采用[参数驱动SQL](./technical_design.html#参数驱动sql)来控制。
1. 数据列权限，这个可以在参数驱动sql中的select部分植入条件标签。
2. 数据行权限，框架在定义业务的时候，还可以定义业务对应的表的过滤条件，在设定角色的时可以选定对应的条件。在执行查询时可以通过参数驱动SQL来植入这些过滤条件来实现数据行范围的权限控制。

所有的数据范围权限都可以通过转化为功能权限来实现。

### 业务流程权限

业务流程权限主要通过人员组织中的岗位、职等和组织机构的层级关系来实现。框架中有两个工具类SysUnitFilterEngine和SysUserFilterEngine)。这两个工具类实现了一个权限表达式引擎，通过这个引擎来对人员进行筛选。引擎定义以下操作：
1. 通过在机构上定义+、- 、*等操作符实现机构的上下级选择。
2. 通过职位等级的+、-、++、--等操作实现人员的上下级选择。
3. 通过定义人员的岗位、职位等属性过滤条件实现人员的选择。

示例：D(U)R(U-1)标识和我一个机构等级比较高一级的人，就是我的上司。其中D是过滤方法表示按照组织部门过滤，U是参数标识当前用户，R是等级过滤方法，U-1就是当前用户的上一级。详情参见[framework/components](https://github.com/ndxt/centit-framework/tree/master/framework-core/src/main/java/com/centit/framework/components)。

## 数据字典

框架将所有的人员信息、组织信息、人员组织关系、业务操作信息、角色信息等等都统称为数据字典。并且额外开发一个自定义数据字典类，参见[model/basedata](https://github.com/ndxt/centit-framework/tree/master/framework-core/src/main/java/com/centit/framework/model/basedata)中的IDataDictionary和IDataCatalog。自定义数据字典一般可以用于：

1. 统一代码转换，比如：性别代码、地区。数据字典可以是列表形式的，也可以是树型结构的。
2. 一些功能的配置参数。比如：个人参数配置项。

所有的数据字典可以通过[CodeRepositoryUtil](https://github.com/ndxt/centit-framework/tree/master/framework-core/src/main/java/com/centit/framework/components)工具类中的静态方式获取。也可以通过在po类的属性上添加DictionaryMap注解映射，在转换为json是就会自动转换为字典对应值，参见[数据字典映射工具](https://github.com/ndxt/centit-framework/tree/master/framework-core/src/main/java/com/centit/framework/core/dao)。

## 操作日志

操作日志和下一节的通知中心都是数据框架的基础服务，这类服务是通过接口的形式提供的，参见[基础服务接口](https://github.com/ndxt/centit-framework/tree/master/framework-adapter/src/main/java/com/centit/framework/model/adapter)。

框架提供两种方式记录操作日志：
1. 注解方式，通过在controller类的方法上添加 RecordOperationLog 注解记录日志。
2. 直接调用OperationLogCenter工具类来记录自定义的操作日志。

两种方式都是使用相同的日志记录接口。框架提供的操作日志记录只负责日志的写入操作，不负责日志的查询和统计工作。业务系统可以通过第三方工具来对日志进行分析与统计。centit-framework-system中的OptLogManagerImpl实现了一个日志数据库记录方式，并提供了查询也统计等管理功能，但是并不推荐日志量比较大的业务系统使用。

更多信息参见[日志写入操作](https://github.com/ndxt/centit-framework/tree/master/framework-core/src/main/java/com/centit/framework/operationlog)。

## 通知中心

通知中心的实现方式和操作日志不同，通知中心是也业务系统在同一个事务中的，所有没有提供工具类，而是通过bean的形式调用。

1. 开发人员通过通知中心调用消息的发送操作，并不关系具体的发送方式。
2. 通知中心可以注册多种发送方式，比如：邮件、短信等等，用户可以设置自己的接收方式，可以设置多种接收方式。
3. 业务系统可以开发自己的消息发送[MessageSender](https://github.com/ndxt/centit-framework/tree/master/framework-adapter/src/main/java/com/centit/framework/model/adapter)接口,并注册到通知中心中。 

更多通知中心信息参见[公共组件](https://github.com/ndxt/centit-framework/tree/master/framework-core/src/main/java/com/centit/framework/components)。

## 通用模块

通用模块和基础服务其实都是框架的共有业务部分，不同的是通用模块是可选的，基础服务是必须的。所以框架将通用模块独立出来作为独立的工程。通用模块工程一般包括一下内容：

1. 【模块名】-utils，以jar包的形式发布。一般实现了这个模块的基础功能。 比如：fileserver-utils 实现了文件断点上传、断点下载等功能。这个可以作为一个工具类直接给业务系统使用。
2. 【模块名】-module，以jar包的形式发布。一般有对应的关系数据库操作。是作为服务的核心部分。比如：fileserver-module它实现了文件的信息存储、文件加密、校验、秒传、文件的管理、访问控制等等功能。
3. 【模块名】-view与module对应的前段页面。
4. 【模块名】-web或者【模块名】-server。一般是这个模块的服务程序，它整合module和view并且以war的形式发布。可以直接作为服务运行。
5. 【模块名】-client，以jar包的形式发布。它是模块服务程序的客户端，通过它可以访问服务程序的接口。如果业务系统将通用模块作为独立服务运行，系统中需要引用这个client.jarl来和模块服务通讯。
6. 【模块名】-demo，这个一般是使用示例。

一个典型的示例参见[centit-fileserver](https://github.com/ndxt/centit-fileserver)。

目前框架已有通用模块参见[相关项目](https://ndxt.github.io/projects/)。南大先腾框架后期的建设重点就是逐步完善并开发更多的通用模块。**用流行的术语就是这部分将会是南大先腾政务业务的中台，我们框架的一个重要思想就是用互联网的思维来做政府企业内部办公业务。**