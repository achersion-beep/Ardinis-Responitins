---
title: "/run-tests"
description: "结构化运行自动化测试并生成报告。支持 unit | integration | e2e，输出 junit/html/markdown。中文交互。"
inputs:
  - name: test_type
    type: string
    description: "测试类型：unit | integration | e2e"
    required: true
  - name: target
    type: string
    description: "测试目标路径或 glob，例如 tests/, src/**/tests"
    required: false
  - name: framework
    type: string
    description: "可选，测试框架：pytest | playwright | jest"
    required: false
  - name: report_format
    type: string
    description: "junit | html | markdown"
    required: true
  - name: parallel
    type: boolean
    description: "是否并行运行（e2e 上常用）"
    required: false
examples:
  - args: { test_type: "e2e", target: "tests/e2e", framework: "playwright", report_format: "html", parallel: true }
    description: "运行端到端测试并输出 HTML 报告"
---

使用说明
- 若未指定 framework，智能体会自动检测（查找 pytest, package.json, playwright.config.js）。
- 智能体会询问并确认任何可能改变仓库配置的操作（如安装新依赖）。
