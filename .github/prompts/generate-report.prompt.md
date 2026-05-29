---
title: "/generate-report"
description: "通过结构化参数生成数据报告：支持 CSV、数据库查询或 REST API，输出 PDF/CSV/Markdown。中文交互。"
inputs:
  - name: source_type
    type: string
    description: "数据来源类型：csv | sql | api"
    required: true
  - name: location
    type: string
    description: "文件路径（CSV）或数据库连接/SQL语句或 API URL"
    required: true
  - name: date_range
    type: string
    description: "可选，日期范围，例如 2025-01-01:2025-12-31"
    required: false
  - name: metrics
    type: string
    description: "需要的指标，逗号分隔，如 revenue,orders"
    required: false
  - name: group_by
    type: string
    description: "分组字段，如 month, region"
    required: false
  - name: output_format
    type: string
    description: "pdf | csv | markdown"
    required: true
examples:
  - args: { source_type: "csv", location: "data/sales.csv", date_range: "2025-01-01:2025-12-31", metrics: "revenue,orders", group_by: "month", output_format: "pdf" }
    description: "为 2025 年按月生成销售收入与订单数的 PDF 报告"
---

使用说明（简短）
- 提供清晰的字段映射或示例 CSV。若字段名不明确，智能体会询问确认。
- 对于 SQL，提供可运行的查询或数据库连接信息；智能体不会保存凭证。
