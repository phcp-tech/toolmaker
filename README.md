简体中文 | [English](./README.en-US.md)

# 目录
 * [一、为什么要创建Toolmaker](#1)
 * [二、技术架构](#2)
 * [三、产品特性](#3)
 * [四、联系方式](#4)

 ## <h2 id="1">一、为什么要创建Toolmaker</h2>
 ### 1.1 烤面包与种麦子的故事
 在软件开发领域工作了十几年，在此期间，我目睹太多的产品在管理过程和开发过程中出现的文档交付物杂乱、管理过程碎片化、管理工具阶段化/多样化、经验与知识不能有效传承等种种现象与矛盾，造成了极大的隐性浪费。
 
 给我感触比较深的是做软件质量管理的4年多时间，我对软件开发过程有了一个从感性认识到理性认识的转变，对诸多软件开发过程和方法的落地有了深入的思考。那时我就萌发要开发一个软件开发全流程（SDLC）管理软件的想法，以消除软件开发过程中的种种混乱和浪费。

 某一次，一个产品经理非常不屑的对公司质量部QA所推行的软件开发过程与方法说了一句话: 产品即将上线，我要的是一个烤好的面包，而不是等着你们种麦子出来。很多人不关心软件开发的过程和方法，他们看到的只是眼前的面包，这个面包能做多大，什么时候能够上市，而不关心这个面包是怎么一步步做出来的。

 是的，我就是那个为了能够吃到一口好面包，而转身去种麦子的人。

 ### 1.2 名字缘起
Toolmaker这个名称，受到电影《星球大战前传II克隆人的进攻》一句台词的启发，其中一个情节是机器人C-3PO误入帝国的机器人士兵制造工厂，张口说道“Machines making machines. Huh! How perverse.” 是的，Toolmaker making (software) tools，Toolmaker是一个软件工具的制造者，缘起如是。

  ### 1.3 口号
 Toolmaker不关注你开发什么，而是关注你如何开发。(Toolmaker don't care about what you do，but how you do.)

  ### 1.4 预览地址
  * [https://toolmaker.io](https://toolmaker.io)，用户名称：guest，密码：123456。

 ## <h2 id="2">二、技术架构</h2>
 #### 2.1 前端
  * Html5, CSS, JavaScript, VueJS, Element, Webpack.

 #### 2.2 后端
  * Golang/Echo/GORM, PostgreSQL/JSONB as NoSQL, Redis/Message Queue, Micro-service architecture.

 #### 2.3 运行环境：AWS Lambda
  * S3, CloudFront, Serverless Lambda, API Gateway, RDS Aurora.

 #### 2.4 技术领先的方面
  * 对软件开发过程与方法的深刻理解；
  * 基于完整统一的软件开发过程领域模型；
  * 遵守软件开发美学定义；
  * 运行于AWS云环境，使用CloudFront、Lambda、RDS Aurora等技术，运维成本降至最低；  

 ## <h2 id="3">三、产品特性</h2>
  ### 3.1 产品管理
  * 创建产品
  * 产品生命周期管理
  ### 3.2 需求管理
  #### 3.2.1 原始需求管理
  * 创建原始需求
  * 管理原始需求
  #### 3.2.2 特性管理
  * 创建特性
  * 管理特性
  #### 3.2.3 需求管理
  * 创建需求
  * 评审需求
  * 冻结恢复需求
  ### 3.3 设计管理
  * 创建组件
  * 管理组件  
  ### 3.4 测试管理
  * 创建测试用例
  * 评审测试用例
  * 冻结恢复测试用例
  * 创建缺陷
  * 管理缺陷
  ### 3.5 用户管理
  * 注册用户
  * 邀请用户加入产品
  * 用户角色定义

 ## <h2 id="4">四、联系方式</h2>
  * 任何问题可以通过[Issues](https://github.com/CHCP/toolmaker-docs/issues)提出并获得解答；
  * 联系请发送邮件：wxnchcp_cn@sina.com，或者qq：1790343904.
