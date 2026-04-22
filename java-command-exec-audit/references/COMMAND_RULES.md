# 命令执行检测规则
整合了多来源的检测规则，去重后共8种专项检测方式，覆盖命令注入、RCE场景。
---
## 基础命令执行检测
```java
// 命令执行Sink
Runtime.getRuntime().exec(cmd)
ProcessBuilder.command(cmd)
```
检测规则：
1. 检测命令参数是否存在用户可控输入
2. 检测是否存在字符串拼接风险
---
## 脚本引擎检测
```java
// 脚本引擎Sink
ScriptEngine.eval(code)
GroovyShell.evaluate(code)
```
检测规则：
1. 检测脚本代码是否存在用户可控输入
2. 检测是否存在注入风险
---
## 表达式注入检测
```java
// 表达式引擎Sink
SpelExpressionParser.parseExpression(expr)
Ognl.getValue(expr, ctx, root)
MVEL.eval(expr)
```
检测规则：
1. 检测表达式是否存在用户可控输入
2. 检测是否存在注入风险
---
## 反序列化检测
```java
// 反序列化Sink
ObjectInputStream.readObject()
JSON.parseObject(json)
XStream.fromXML(xml)
```
检测规则：
1. 检测反序列化输入是否存在用户可控
2. 检测是否存在反序列化RCE风险
---
## 误报过滤规则
1. 检测是否存在白名单验证
2. 检测执行条件，过滤安全场景
