## 前端v2.5.0开发文档

> 主要负责人：张鹏，江文宾，廖承军
>
> 审核人：廖承军

## 一、需求/任务及设计方案与思路
1. [[SR-3654](http://10.88.223.193:8080/browse/SR-3654)] 集成测试中优化函数调用关系图、新增项目集成文件函数调用关系图（PRD_IT_I0002）  

   _**主要设计方案与思路**_：    
   调用关系图的优化主要集中在可折叠展开，项目集成文件函数调用关系图主要集中在类UML结构生成并且可折叠展开，且二者图片皆须下载，经调研发现已经成熟的库[AntV/G6](https://g6.antv.antgroup.com/)满足此产品需求，  
   并且此库支持自定义，可很好的与UI设计相对应，最终引入[AntV/G6](https://g6.antv.antgroup.com/)来实现此产品需求。其中，调用关系图使用了[G6.TreeGraph](https://g6.antv.antgroup.com/zh/examples/tree/compactBox/#basicCompactBox), 项目集成文件函数调用关系图使用了[G6.Graph](https://g6.antv.antgroup.com/zh/examples/net/comboLayout/#basicComboForce)。  

2. [[SR-3651](http://10.88.223.193:8080/browse/SR-3651)] 集成测试C++行为逻辑拓展：模板函数集成、外部符号链接、符号定义位置选择（PRD_IT_I0001）

   _**主要设计方案与思路**_：    

   在函数调用关系对话框的函数类型下拉框中添加模板特化函数类型，选中时可过滤出对应的模板特化函数，并且该函数未打桩禁用。

   当对非模板特化函数选择定义路径时，根据服务端提供的字段判断，若重复定义，则Toasting提示。

   未定义的函数可做取消打桩操作，取消打桩后下拉框显示“暂无数据”。

3. [[SR-3677](http://10.88.223.193:8080/browse/SR-3677)] 单元：C/C++用例生成重构（PRD_UTM_U0144）

   _**主要设计方案与思路**_：

   通过服务端从核心层获取类型系统、变量、测试用例数据，由公共库进行数据组装，并返回给前端组装展示。

   针对<b>当前成员函数的this指针（C++）</b>由前端判断其变量种类，并展示对应的内容。

   针对<b>插桩成员函数的this指针（C++）</b>由前端判断其变量种类，并展示对应的内容。

   针对<b>插桩函数中的全局变量（C、C++）</b>添加设置全局变量按钮，该按钮可打开全局变量设置对话框，通过勾选全局变量来设置桩函数需要显示的全局变量。

   针对<b>函数符号化（C、C++）</b>，在桩函数对话框中通过桩**函数触发类型**添加对应图标，来区分桩函数和新建函数

   针对**多重继承**由前端判断其变量种类，并展示对应的内容。

4. [[SR-3686](http://10.88.223.193:8080/browse/SR-3686)] 单元：针对已有用例，基于结构覆盖补齐用例（PRD_UTM_U0145）

   _**主要设计方案与思路**_：

   主要针对单元测试中函数进行**一键补充测试**，当勾选函数时，**一键补充测试**按钮由禁用变为可用状态，此时点击**一键补充测试**按钮，由服务端针对当前函数进行基于结构覆盖用例补充。前端会显示测试进度球，并且任务监控列表会显示对应的任务类型。

5. [[SR-3655](http://10.88.223.193:8080/browse/SR-3655)]【32所定制化需求】测试报告模板定制，项目总览信息优化（PRD_TR_I0037）

   _**主要设计方案与思路**_：    

   在项目总览-用例列表页面添加**用例标识**列，当页面加载时会从服务端请求用例数据来展示**用例标识**信息。


## 二、备注