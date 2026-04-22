---
name: java-xxe-audit
description: Java XXE专项审计技能，检测XML外部实体注入漏洞，覆盖DOM、SAX、StAX等各类XML解析器，整合去重后提供5种检测方式，支持防御配置检测。
---
# Java XXE审计
扫描Java项目源码，检测XML外部实体注入漏洞，覆盖各类XML解析器的风险场景。
---
## 核心检测规则
### 1. DOM解析器检测
检测DocumentBuilder的XXE风险，是否禁用了外部实体。
### 2. SAX解析器检测
检测SAXParser的XXE风险，是否存在安全配置。
### 3. StAX解析器检测
检测XMLInputFactory的XXE风险，是否禁用了外部实体。
### 4. 其他解析器检测
覆盖JDOM、XStream、Jackson XML等解析器的XXE风险。
### 5. 误报过滤
检测是否存在安全配置，过滤安全场景。
---
## 工作流程
1. 识别XML解析接口，定位用户输入源
2. 检测解析器的安全配置是否缺失
3. 验证是否存在外部实体注入风险
4. 生成审计报告
