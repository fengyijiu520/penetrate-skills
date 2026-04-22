# 认证授权检测规则
整合了多来源的检测规则，去重后共9种专项检测方式，覆盖权限绕过、IDOR、认证缺失场景。
---
## 认证缺失检测
检测规则：
1. 检测敏感接口是否缺失@PreAuthorize等认证注解
2. 检测是否存在SecurityConfig的认证配置
---
## 授权缺失检测
检测规则：
1. 检测敏感接口是否缺失@RequiresPermissions、@Secured等权限注解
2. 检测是否存在权限验证逻辑
---
## IDOR漏洞检测
检测规则：
1. 检测资源所有权验证是否缺失
2. 检测是否存在entity.getOwnerId().equals(currentUserId)的验证
---
## 框架权限绕过检测
### Shiro
检测Shiro的URL匹配绕过漏洞，如路径匹配的AntPathMatcher绕过。
### Spring Security
检测Spring Security的路径匹配绕过，如请求方法绕过、路径参数绕过。
---
## JWT权限检测
检测规则：
1. 检测JWT token的签名验证是否缺失
2. 检测是否存在token绕过风险
---
## 误报过滤规则
1. 检测是否存在严格的权限验证逻辑
2. 检测执行条件，过滤安全场景
