# 文件读取检测规则
整合了多来源的检测规则，去重后共8种专项检测方式，覆盖路径遍历、任意文件读取场景。
---
## 基础文件操作检测
```java
// 文件读取Sink
new File(path)
FileInputStream(path)
FileReader(path)
Files.readAllBytes(path)
Files.readAllLines(path)
FileUtils.readFileToString(file)
```
检测规则：
1. 检测路径参数是否存在用户可控输入
2. 检测是否存在路径拼接风险
---
## Zip Slip漏洞检测
```java
// Zip解压
ZipEntry.getName()
ZipUtil.unzip(entry)
```
检测规则：
1. 检测压缩包文件名的路径遍历风险
2. 检测是否存在路径规范化验证
---
## 路径拼接风险检测
检测模式：
```java
String filePath = basePath + userInput;
```
检测规则：
1. 检测是否存在basePath+userInput的拼接
2. 检测是否存在getCanonicalPath规范化验证
3. 检测是否存在../黑名单验证
---
## 文件下载接口检测
检测文件下载接口的路径参数风险，是否存在任意文件下载。
---
## 误报过滤规则
1. 检测是否存在路径规范化验证
2. 检测是否存在白名单验证
3. 检测执行条件，过滤安全场景
