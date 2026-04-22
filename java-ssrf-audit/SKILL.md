---
name: java-ssrf-audit
description: Java SSRF专项审计技能，检测服务端请求伪造漏洞，覆盖HttpClient、RestTemplate、OkHttp等客户端，整合去重后提供6种检测方式，覆盖URL白名单绕过、内网访问风险。
---
# Java SSRF审计
扫描Java项目源码，检测服务端请求伪造漏洞，覆盖各类HTTP客户端的风险场景。
---
## 核心检测规则
### 1. 基础HTTP客户端检测
检测HttpClient、OkHttp、RestTemplate等客户端的URL参数风险。
### 2. URL白名单绕过检测
检测URL白名单的绕过风险，如@、#、换行等绕过方式。
### 3. 内网访问风险检测
检测是否存在访问内网IP、端口的风险。
### 4. 协议绕过检测
检测file、gopher等协议的注入风险。
### 5. 配置驱动SSRF检测
检测配置中的URL参数的风险。
### 6. 误报过滤
检测是否存在URL白名单验证，过滤安全场景。
---
## 工作流程
1. 识别HTTP请求接口，定位用户输入源
2. 检测URL参数是否存在用户可控输入
3. 验证是否存在白名单验证
4. 分析执行条件，过滤误报
5. 生成审计报告
