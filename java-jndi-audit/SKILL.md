---
name: java-jndi-audit
description: Java JNDI注入专项审计技能，检测JNDI注入漏洞，覆盖InitialContext.lookup、JdbcRowSetImpl等场景，整合去重后提供5种检测方式，覆盖协议绕过、JDK版本风险。
---
# Java JNDI注入审计
扫描Java项目源码，检测JNDI注入漏洞，覆盖JNDI查找的各类风险场景。
---
## 核心检测规则
### 1. 基础JNDI查找检测
检测InitialContext.lookup的参数是否用户可控。
### 2. JdbcRowSetImpl检测
检测JdbcRowSetImpl的setDataSourceName的风险。
### 3. 协议绕过检测
检测iiop、corba等协议的注入风险。
### 4. JDK版本风险检测
检测JDK版本是否存在JNDI注入的绕过风险。
### 5. 误报过滤
检测是否存在白名单验证，过滤安全场景。
---
## 工作流程
1. 识别JNDI查找接口，定位用户输入源
2. 检测查找参数是否用户可控
3. 验证是否存在安全配置
4. 分析JDK版本风险
5. 生成审计报告
