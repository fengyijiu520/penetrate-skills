---
name: java-fastjson-audit
description: Java Fastjson专项审计技能，检测Fastjson的反序列化RCE漏洞，覆盖全版本的autoType绕过风险，整合去重后提供6种检测方式，覆盖不同版本的漏洞模式。
---
# Java Fastjson审计
扫描Java项目中的Fastjson依赖和用法，检测Fastjson反序列化远程代码执行漏洞，覆盖全版本的风险场景。
---
## 核心检测规则
### 1. 版本检测
检测Fastjson的版本，识别是否存在已知的高危漏洞版本。
### 2. autoType配置检测
检测是否开启了autoType，是否存在autoType绕过风险。
### 3. parseObject检测
检测JSON.parseObject的输入是否用户可控。
### 4. 绕过模式检测
检测@type、L@type等绕过模式的风险。
### 5. 安全配置检测
检测是否存在autoType黑名单、安全配置。
### 6. 误报过滤
检测输入是否可信，过滤安全场景。
---
## 工作流程
1. 识别Fastjson的依赖版本
2. 检测Fastjson的反序列化接口，定位用户输入源
3. 验证是否存在漏洞风险
4. 分析安全配置，过滤误报
5. 生成审计报告
