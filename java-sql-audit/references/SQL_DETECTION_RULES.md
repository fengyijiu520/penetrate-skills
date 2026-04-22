# SQL注入检测规则
整合了code-security-audit和penetrate-skills两个仓库的检测规则，去重后共11种专项检测方式，覆盖各类SQL注入场景，融合了两个仓库的精华。
---
## JDBC注入检测
```java
// JDBC Sink
Statement.execute(sql)
Statement.executeQuery(sql)
Statement.executeUpdate(sql)
Connection.prepareStatement(sql)
Connection.prepareCall(sql)
JdbcTemplate.query(sql)
JdbcTemplate.update(sql)
```
检测规则：
1. 检测Statement的SQL字符串是否存在用户输入拼接
2. 检测PreparedStatement的SQL是否存在拼接
3. 检测是否使用了参数化查询
---
## MyBatis注入检测
```xml
<!-- MyBatis Mapper -->
<select id="selectUser" resultType="User">
  SELECT * FROM user WHERE id = ${id} <!-- 危险 -->
  SELECT * FROM user WHERE id = #{id} <!-- 安全 -->
</select>
```
```java
// MyBatis-Plus
QueryWrapper.apply(sql)
QueryWrapper.last(sql)
```
检测规则：
1. 检测Mapper.xml中的${}拼接
2. 检测MyBatis-Plus的apply、last方法的参数风险
3. 检测动态SQL的拼接风险
---
## Hibernate注入检测
```java
// Hibernate
EntityManager.createQuery(sql)
EntityManager.createNativeQuery(sql)
Session.createQuery(sql)
Session.createSQLQuery(sql)
```
检测规则：
1. 检测HQL字符串拼接
2. 检测Native Query拼接
3. 检测Criteria API的不安全用法
---
## ORM通用注入检测
覆盖JPA、Spring Data JPA、QueryDSL、jOOQ等ORM框架：
1. @Query注解的nativeQuery=true+字符串拼接
2. 动态字段名/表名的注入风险
3. 排序/分组字段的注入风险
4. 分页参数的注入风险
---
## 行为检测策略
### 1. SQL 关键字拼接检测
**搜索 SQL 关键字与变量拼接的行为：**
```bash
# ORDER BY 拼接检测
grep -ri "order by" --include="*.java" | grep -E "\+|\.append|format"
# GROUP BY 拼接检测
grep -ri "group by" --include="*.java" | grep -E "\+|\.append|format"
# WHERE 条件拼接检测
grep -ri "where.*=" --include="*.java" | grep -E "\+|\.append"
# LIKE 拼接检测
grep -ri "like\s" --include="*.java" | grep -E "\+|\.append"
```
### 2. 危险行为特征
| 危险行为 | 代码特征 | 检测正则 |
|:---------|:---------|:---------|
| 字符串拼接 | `"order by " + xxx` | `".*order by.*"\s*\+` |
| StringBuilder | `.append("order by").append(xxx)` | `\.append\(.*order` |
| String.format | `format("...%s", xxx)` | `format\(.*order by` |
| MessageFormat | `MessageFormat.format()` | `MessageFormat\.format` |
### 3. 数据流追踪
```
步骤1: 识别所有 String 类型的类字段/方法参数
步骤2: 追踪这些 String 是否被用于 SQL 拼接
步骤3: 检查拼接前是否有白名单校验
```
**危险模式：**
```java
sql + variable           // String 拼接
sql.append(variable)     // StringBuilder 拼接
String.format(sql, var)  // 格式化拼接
MessageFormat.format()   // 消息格式化
```
**安全模式：**
```java
pstmt.setString(1, var)              // 参数化查询
if (whitelist.contains(var)) { }     // 白名单校验
var.matches("[a-zA-Z_]+")            // 正则校验
```
---
## 多数据库分页语法检测
不同数据库使用不同的分页语法，检测时需全部覆盖：
### 分页语法对比
| 数据库 | 分页语法 | 检测关键字 |
|:-------|:---------|:-----------|
| MySQL | `LIMIT offset, count` | `limit` |
| PostgreSQL | `LIMIT count OFFSET offset` | `limit`, `offset` |
| Oracle | `ROWNUM`, `ROW_NUMBER()` | `rownum`, `row_number` |
| SQL Server | `TOP`, `OFFSET FETCH` | `top`, `offset`, `fetch` |
| DB2 | `FETCH FIRST n ROWS` | `fetch first`, `rows only` |
| SQLite | `LIMIT count OFFSET offset` | `limit`, `offset` |
### 分页检测命令
```bash
# MySQL / PostgreSQL / SQLite
grep -ri "limit\s" --include="*.java" | grep -v "//\|/\*"
grep -ri "offset\s" --include="*.java" | grep -v "//\|/\*"
# Oracle
grep -ri "rownum\|row_number" --include="*.java"
# SQL Server
grep -ri "\stop\s\|offset.*fetch" --include="*.java"
# DB2
grep -ri "fetch first\|rows only" --include="*.java"
```
### 分页拼接风险点
| 参数 | 可注入性 | 说明 |
|:-----|:---------|:-----|
| limit/count | 低 | 通常是 int 类型 |
| offset/start | 低 | 通常是 int 类型 |
| orderBy/sortColumn | **高** | String 类型，可注入 |
| order/direction | **高** | String 类型，可注入 |
---
## 排序注入检测（高频漏报）
ORDER BY 注入是最容易被遗漏的 SQL 注入类型。
### 各数据库 ORDER BY 语法
| 数据库 | ORDER BY 语法 | 特殊注入点 |
|:-------|:--------------|:-----------|
| 通用 | `ORDER BY column ASC/DESC` | column 名称 |
| Oracle | `ORDER BY column NULLS FIRST/LAST` | NULLS 处理 |
| MySQL | `ORDER BY column` | 可用数字索引 |
| PostgreSQL | `ORDER BY column USING operator` | USING 子句 |
### 检测命令
```bash
# 搜索 order by 拼接
grep -ri "order by" --include="*.java" | grep -E "\+|\.append|format"
# 搜索排序相关 getter（语义检测）
grep -ri "\.get.*order\|\.get.*sort\|\.get.*by" --include="*.java" -i
# 搜索排序方向
grep -ri "asc\|desc" --include="*.java" | grep -E "\+|\.append"
```
### 危险模式示例
```java
// 危险：直接拼接
sql.append(" ORDER BY ").append(orderBy);
sql.append(" ").append(order);
// 危险：字符串拼接
String sql = baseSql + " ORDER BY " + column + " " + direction;
// 危险：格式化
String.format("%s ORDER BY %s %s", sql, orderBy, order);
```
### 安全模式示例
```java
// 安全：白名单校验
Set<String> allowedColumns = Set.of("id", "name", "create_time");
Set<String> allowedOrders = Set.of("asc", "desc");
if (!allowedColumns.contains(orderBy.toLowerCase())) {
    throw new IllegalArgumentException("Invalid column");
}
if (!allowedOrders.contains(order.toLowerCase())) {
    throw new IllegalArgumentException("Invalid order");
}
```
---
## 各数据库注入 Payload 参考
### 通用 Payload
| 注入类型 | Payload | 说明 |
|:---------|:--------|:-----|
| 错误注入 | `'` | 触发语法错误 |
| 布尔盲注 | `1' AND '1'='1` | 条件为真 |
| 注释截断 | `--`, `#`, `/* */` | 注释后续内容 |
### Oracle 特有
| 注入类型 | Payload |
|:---------|:--------|
| 时间盲注 | `1' AND DBMS_PIPE.RECEIVE_MESSAGE('a',5)='a` |
| UNION | `UNION SELECT NULL,NULL FROM DUAL--` |
| 错误注入 | `1' AND 1=CTXSYS.DRITHSX.SN(1,'~')--` |
### MySQL 特有
| 注入类型 | Payload |
|:---------|:--------|
| 时间盲注 | `1' AND SLEEP(5)--` |
| UNION | `UNION SELECT 1,2,3--` |
| 错误注入 | `1' AND EXTRACTVALUE(1,CONCAT(0x7e,VERSION()))--` |
### PostgreSQL 特有
| 注入类型 | Payload |
|:---------|:--------|
| 时间盲注 | `1'; SELECT PG_SLEEP(5)--` |
| 堆叠查询 | `1'; DROP TABLE test--` |
| 错误注入 | `1' AND 1=CAST((SELECT version()) AS INT)--` |
### SQL Server 特有
| 注入类型 | Payload |
|:---------|:--------|
| 时间盲注 | `1'; WAITFOR DELAY '0:0:5'--` |
| 堆叠查询 | `1'; EXEC xp_cmdshell('whoami')--` |
| 错误注入 | `1' AND 1=CONVERT(INT,@@VERSION)--` |
---
## 检测优先级
| 优先级 | 检测目标 | 原因 |
|:-------|:---------|:-----|
| P0 | ORDER BY / GROUP BY 拼接 | 最常被遗漏，无法参数化 |
| P1 | 动态表名/列名拼接 | 无法参数化，必须白名单 |
| P2 | WHERE 条件拼接 | 常见漏洞点 |
| P3 | LIKE 模糊查询拼接 | 需要特殊处理通配符 |
| P4 | IN 子句动态拼接 | 需要正确使用 foreach |
---
## 误报过滤规则
1. 检测是否存在参数化查询
2. 检测是否存在白名单验证
3. 检测执行条件，过滤安全场景
4. 数据库分支检测，过滤不同数据库的差异
