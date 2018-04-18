# 服务工具类

这一类是先腾开发的核心，是多年来先腾研发人员的成果，内容较多。目前开源的有：

## 工作流引擎

先腾的工作流引擎是一款自主研发的工作流引擎，涵盖了从流程梳理、设计、发布、管理配置、执行、监控到流程分析的完整的生命周期管理，包括图形化流程建模，自定义表单，服务化调用等功能特点。与其他工作流产品相比，我们的产品是专门针对职能型组织架构设定的，内部抽象出了一些特定的模型，特别适合政府和大型企业单位使用。

我们的工作流适用于各种应用场景，比如简单灵活的审批自由流，或者节点数较多，流转条件复杂的流程。

![](../assets/image1.png)

上图是一个审批自由流的流程图，申请或者审批完节点后都可以指定或者修改后续的审批人，还可以根据需求增加节点。

![](../assets/image2.png)

上图是一个收文流程，收发文流程是职能型组织常见的流程，我们的引擎能快速构建此类流程。

## 工单系统

工单系统是先腾自主研发的处理部门协同、任务派送、提升效率、改善服务体验的工单管理系统。随着互联网+的起步发展，企业与用户面临的客户服务问题越来越得到重视，然而客服专业度、端口覆盖问题、及时智能通讯的成本问题和历史经典问题的归档记录等常用工具存在的缺点又很难满足广大用户和企业的需求，所以为各企业应用系统嵌入一个自动分配，一键流转和经典历史问题总结的工单系统就显得犹为重要。

目前工单系统除了支持工单提问、管理等基础功能，还支持：

a\) 知识库：根据问题类别关键字检索相关帮助文档快速高效的定位问题解决方案；

b\) 分配工单：可以自行分配别人或自己答复问题；还有专家团协助处理问题功能；

c\) 评分：用户通过对文档及工单解答的评分，能够客观的统计工作人员的服务态度和工作质量。

![](../assets/image3.png)

## 即时通讯

即时通讯系统也可以作为即时通讯模块，可以模拟用户，模拟客服（即管理员）进行及时沟通。已实现功能有：图片/文件上传功能、智能问答功能、客服管理等功能。

![](../assets/image4.jpeg)

## 先腾数据交换平台

先腾的数据交换平台是一个独立运行的B/S结构应用系统，其主要功能由配置中心、控制中心和监控中心组成。

![](../assets/image5.png)

配置中心提供一系列与数据交换业务相关的配置功能，包括数据库连接配置、WebService服务接口配置、数据对应关系配置、数据导入导出配置、交换前后的触发器配置。

控制中心：提供相应的功能用于控制数据交换业务的运行，包括定时调度控制、人工控制和WebService服务控制。用任务的形式去执行配置的交换关系。

监控中心：记录交换操作的日志、查看和分析交换任务的执行情况并对它们进行统计。

![](../assets/image6.png)

## 文件管理平台

先腾的文件管理平台具有上传下载、断点续传、收藏、文件夹管理等功能，支持标准的文件访问协议。可自定义上传文件的类型、大小等上传标准。并且可以一次性上传和管理多个符合上传标准的文件。

![](../assets/image7.jpeg)

## 统计报表平台

先腾的统计报表模块功能强大，表格可以动态加载，根据表头排列排列数据，图表形式各样并可互相切换，有折线图、柱状图、双轴图、面积图、饼图、GIS地图、省份地图、词云、漏斗图、桑基图等等。同时有很多分析的功能，如：同比、环比、留存率、重复率等高级计算，也有较多的可视化图表，并支持导出。

![](../assets/image8.png)

## 自定义表单系统

自定义表单是先腾将自定义表单模块重新定义协作，将台帐记录、数据汇报、外部调研收集并汇总。表单数据动态绑定，表单字段高度定制化，实时完成数据提交，自动生成数据和报表。

![](../assets/image9.png)

## 消息推送平台

先腾自主研发的消息数据推送服务平台，对外提供连接推送服务的客户端jar包，应用系统无需了解推送过程，只需要专注于自身业务逻辑，适时调用相应推送接口即可。

PC推送依靠websocket长连接实现实时高效的向需要的用户进行消息推送服务；app推送则依赖百度云推送服务完成，稳定及时的完成推送功能。

目前支持PC,APP推送，后期还将补充支持微信公众号，邮件，和短信等常用推送。