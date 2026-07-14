# Qoder Skills

Qoder 智能体技能集合，用于依赖分析、安全审计和升级规划。

## 技能列表

### 跨版本升级分析

分析项目依赖或独立组件跨版本升级的影响。识别破坏性变更、废弃 API、兼容性风险，并生成结构化的迁移计划。

**支持的生态系统：**
- **包管理器：** npm、pip、Maven、Go、Cargo、NuGet、Ruby
- **独立组件：** Tomcat、Nginx、MySQL、PostgreSQL、Redis、RabbitMQ、Node.js、Java、Python、Spring Boot、Django、Vue、React 等

### 漏洞风险分析

使用权威数据源（NVD、CVE、GitHub Advisory、OSV、Snyk、CISA KEV、厂商安全页面）扫描项目依赖或独立组件的安全漏洞。生成优先级风险评估报告及修复建议。

**支持的生态系统：** 同上

### 模拟面试题生成

根据简历内容自动生成模拟面试题，支持从本地文件（PDF、Word、Markdown、TXT）或在线链接获取简历内容。问题基于简历中的技术栈与项目经历，涵盖技术深度、项目业务、场景设计、行为面试四大类型。

### 生态维护

维护和更新技能的生态系统参考信息。扫描现有技能、审查和刷新生态数据、添加新的生态条目，并记录维护时间戳。

## 输出目录

报告生成在 `output/` 目录下：

- `output/reports/upgrade/` - 升级分析报告
- `output/reports/risk/` - 漏洞分析报告
- `output/interviews/` - 模拟面试题
- `output/checkpoints/` - 生态维护检查点

## 使用方法

这些技能专为 Qoder AI 编程助手设计，通过斜杠命令调用：

- `/cross-version-upgrade-analysis` - 分析升级影响
- `/vulnerability-risk-analysis` - 检查安全漏洞
- `/mock-interview` - 生成模拟面试题
- `/ecosystem-maintenance` - 更新生态数据

