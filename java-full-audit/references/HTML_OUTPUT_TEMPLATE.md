# HTML输出模板
全流程审计完成后，生成美化后的HTML报告，包含所有专项审计的结果，整合所有漏洞，无过滤标记所有漏洞。
---
## HTML模板
```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Java全项目审计报告（无过滤版）</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif; background: #f5f7fa; color: #333; line-height: 1.6; }
        .container { max-width: 1200px; margin: 0 auto; padding: 20px; }
        .header { background: linear-gradient(135deg, #11998e 0%, #38ef7d 100%); color: white; padding: 30px; border-radius: 10px; margin-bottom: 20px; }
        .header h1 { font-size: 28px; margin-bottom: 10px; }
        .header p { opacity: 0.9; }
        .note { background: #fff3cd; border: 1px solid #ffeaa7; padding: 15px; border-radius: 8px; margin-bottom: 20px; }
        .note p { color: #856404; }
        .card { background: white; border-radius: 10px; padding: 20px; margin-bottom: 20px; box-shadow: 0 2px 10px rgba(0,0,0,0.1); }
        .card h2 { font-size: 20px; margin-bottom: 15px; color: #2d3748; border-bottom: 2px solid #e2e8f0; padding-bottom: 10px; }
        .vuln-item { padding: 15px; border-radius: 8px; margin-bottom: 15px; border-left: 4px solid; }
        .vuln-item.critical { border-left-color: #e53e3e; background: #fff5f5; }
        .vuln-item.high { border-left-color: #dd6b20; background: #fffaf0; }
        .vuln-item.medium { border-left-color: #d69e2e; background: #fefbea; }
        .vuln-item.low { border-left-color: #38a169; background: #f0fff4; }
        .vuln-title { font-weight: 600; font-size: 16px; margin-bottom: 8px; }
        .vuln-file { color: #718096; font-size: 14px; margin-bottom: 8px; }
        .vuln-desc { font-size: 14px; color: #4a5568; }
        .vuln-tag { display: inline-block; background: #e2e8f0; color: #4a5568; padding: 2px 8px; border-radius: 4px; font-size: 12px; margin-right: 5px; }
        .stats { display: flex; gap: 20px; flex-wrap: wrap; margin-bottom: 20px; }
        .stat-item { flex: 1; min-width: 150px; background: white; padding: 15px; border-radius: 8px; text-align: center; box-shadow: 0 2px 10px rgba(0,0,0,0.1); }
        .stat-item .count { font-size: 24px; font-weight: 600; margin-bottom: 5px; }
        .stat-item .label { font-size: 14px; color: #718096; }
        .stat-item.critical .count { color: #e53e3e; }
        .stat-item.high .count { color: #dd6b20; }
        .stat-item.medium .count { color: #d69e2e; }
        .stat-item.low .count { color: #38a169; }
        table { width: 100%; border-collapse: collapse; margin-bottom: 15px; }
        table th, table td { padding: 12px; text-align: left; border-bottom: 1px solid #e2e8f0; }
        table th { background: #f7fafc; font-weight: 600; }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>Java全项目审计报告</h1>
            <p>已按照要求，标记了所有的漏洞，只要存在风险就全部标记，没有排除任何文件</p>
        </div>

        <div class="note">
            <p><strong>说明：</strong>本报告已移除了所有文件过滤，所有代码中的漏洞都已标记，每个漏洞都标注了所属的漏洞分类，方便你区分不同类型的风险。</p>
        </div>
        
        <div class="stats">
            <div class="stat-item critical">
                <div class="count">{{critical_count}}</div>
                <div class="label">高危漏洞</div>
            </div>
            <div class="stat-item high">
                <div class="count">{{high_count}}</div>
                <div class="label">中危漏洞</div>
            </div>
            <div class="stat-item medium">
                <div class="count">{{medium_count}}</div>
                <div class="label">低危漏洞</div>
            </div>
            <div class="stat-item low">
                <div class="count">{{info_count}}</div>
                <div class="label">信息项</div>
            </div>
        </div>

        <div class="card">
            <h2>动态扫描漏洞（DAST）</h2>
            <table>
                <thead>
                    <tr>
                        <th>风险等级</th>
                        <th>漏洞类型</th>
                        <th>路径</th>
                        <th>置信度</th>
                    </tr>
                </thead>
                <tbody>
                    {{#dast_vulnerabilities}}
                    <tr>
                        <td><span class="vuln-tag {{severity}}">{{severity_label}}</span></td>
                        <td>{{title}}</td>
                        <td>{{path}}</td>
                        <td>{{confidence}}</td>
                    </tr>
                    {{/dast_vulnerabilities}}
                </tbody>
            </table>
        </div>

        <div class="card">
            <h2>静态审计漏洞（SAST，无过滤）</h2>
            {{#sast_vulnerabilities}}
            <div class="vuln-item {{severity}}">
                <div class="vuln-title">
                    <span class="vuln-tag">{{category}}</span>
                    {{title}}
                </div>
                <div class="vuln-file">文件: {{file}}:{{line}}</div>
                <div class="vuln-desc">{{description}}</div>
            </div>
            {{/sast_vulnerabilities}}
        </div>
    </div>
</body>
</html>
```
---
## 说明
1.  **无过滤标记**：所有漏洞都已标记，没有排除任何文件
2.  **美化的HTML页面**：使用渐变背景、卡片设计、漏洞等级着色，支持响应式查看
3.  漏洞按风险等级着色，高危红色、中危橙色、低危黄色、信息项绿色
4.  包含统计卡片，直观展示所有漏洞的数量统计
5.  整合了动态扫描和静态审计的所有结果，生成完整的报告
