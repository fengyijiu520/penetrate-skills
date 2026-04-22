---
name: java-deserialization-audit
description: Java反序列化专项审计技能，检测反序列化RCE漏洞，覆盖原生反序列化、Fastjson、XStream、Jackson等框架，整合去重后提供7种检测方式，覆盖各类反序列化入口。
---
# Java反序列化审计
扫描Java项目源码，检测反序列化远程代码执行漏洞，覆盖各类反序列化框架的风险场景。
---
## 核心检测规则
### 1. 原生反序列化检测
检测ObjectInputStream.readObject的输入是否用户可控。
### 2. Fastjson检测
检测Fastjson的parseObject等方法的风险，是否存在autoType绕过。
### 3. XStream检测
检测XStream.fromXML的反序列化风险。
### 4. Jackson检测
检测Jackson的readValue的反序列化风险。
### 5. SnakeYAML检测
检测SnakeYAML的load方法的反序列化风险。
### 6. Hessian检测
检测Hessian的反序列化风险。
### 7. 误报过滤
检测是否存在白名单验证，过滤安全场景。
---
## 工作流程
1. 识别反序列化接口，定位用户输入源
2. 检测反序列化输入是否用户可控
3. 验证是否存在安全配置
4. 分析执行条件，过滤误报
5. 生成审计报告
