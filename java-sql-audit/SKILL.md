---
name: java-sql-audit
description: Java SQL注入专项审计技能，无过滤从源码中定位SQL执行入口，检测SQL注入漏洞，支持JDBC、MyBatis、Hibernate等主流框架。整合了多来源的检测规则，去重后提供11种专项检测方式，覆盖参数化查询缺失、动态SQL拼接、排序/分组字段注入等场景，通过误报过滤提升检测准确率。
---
# Java SQL注入审计
扫描Java项目源码，定位所有SQL执行入口，检测SQL注入漏洞，支持多框架的专项检测。

**无过滤审计**：本技能不会过滤任何文件，只要代码中存在漏洞，就会全部标记，不会排除任何文件，所有的风险都会被检测到。
---
## 核心检测规则
### 1. JDBC注入检测
检测Statement字符串拼接、PreparedStatement参数化缺失等JDBC层注入风险。
### 2. MyBatis注入检测
检测Mapper.xml中的${}拼接、动态SQL注入、MyBatis-Plus Wrapper拼接风险。
### 3. Hibernate注入检测
检测HQL拼接、Native Query拼接、Criteria API不安全用法。
### 4. ORM通用注入检测
覆盖JPA、Spring Data JPA、QueryDSL、jOOQ等ORM框架的注入风险。
### 5. 排序/分组字段注入
检测orderBy、groupBy等动态字段的注入风险。
### 6. 分页参数注入
检测PageHelper等分页工具的参数注入风险。
### 7. 数据权限注解注入
检测@DataScope等切面的SQL拼接风险。
### 8. 自定义Repository注入
检测自定义Repository实现中的SQL拼接风险。
### 9. SpEL表达式注入
检测Spring Data @Query中的SpEL表达式注入风险。
### 10. 反编译审计
支持对Jar包中的class文件进行反编译，审计字节码中的SQL注入风险。
### 11. 误报过滤
通过执行条件分析、数据库分支检测，过滤误报，提升准确率。
---
## 工作流程
1. 识别项目ORM框架，加载对应检测规则
2. 扫描Controller层，定位用户输入源
3. 追踪数据流，检测参数是否流入SQL执行Sink
4. 验证是否存在参数化查询或白名单验证
5. 分析执行条件，过滤误报
6. 生成审计报告

## 输出
审计完成后，**默认直接生成美化的HTML报告**，无需额外指定，你不需要再说明"生成HTML"，系统会自动生成：
1.  **无过滤标记**：所有漏洞都已标记，没有排除任何文件
2.  **分类标注**：每个漏洞都标注了「SQL注入」的分类标签
3.  **美化的HTML页面**：使用渐变背景、卡片设计、漏洞等级着色，支持响应式查看
