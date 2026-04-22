---
name: java-command-exec-audit
description: Java命令执行专项审计技能，检测命令注入、RCE漏洞，支持Runtime.exec、ProcessBuilder、脚本引擎等场景。整合了多来源的检测规则，去重后提供8种专项检测方式，覆盖命令拼接、表达式注入、反序列化等场景。
---
# Java命令执行审计
扫描Java项目源码，检测命令注入、远程代码执行漏洞，覆盖各类命令执行入口。
---
## 核心检测规则
### 1. Runtime.exec检测
检测Runtime.getRuntime().exec的命令参数风险，是否存在字符串拼接。
### 2. ProcessBuilder检测
检测ProcessBuilder的命令参数风险，是否存在用户可控输入。
### 3. 脚本引擎检测
检测ScriptEngine.eval、GroovyShell等脚本引擎的代码注入风险。
### 4. 表达式注入检测
检测SpEL、OGNL、MVEL等表达式引擎的注入风险。
### 5. 反序列化检测
检测Java原生反序列化、Fastjson、XStream等反序列化RCE风险。
### 6. 反射调用检测
检测Class.forName、Method.invoke等反射调用的风险。
### 7. 反编译审计
支持对Jar包中的class文件进行反编译，审计字节码中的命令执行风险。
### 8. 误报过滤
通过执行条件分析，过滤误报。
---
## 工作流程
1. 识别命令执行接口，定位用户输入源
2. 检测命令参数是否存在用户可控输入
3. 验证是否存在白名单验证、参数净化
4. 分析执行条件，过滤误报
5. 生成审计报告
