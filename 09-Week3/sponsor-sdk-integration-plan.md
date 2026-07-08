# Sponsor SDK / API Integration Plan — Z.AI GLM-5.1

> Week 3 任务：Sponsor SDK / API 整合计划
> 项目：EIV (Execution-Integrity Validator)

## 接什么

**GLM-5.1 API**（Z.AI sponsor API），担任 validator 的**调查/编排脑**，坐在一个 **model-interface 边界**后面。

它负责：
- 诠释松散的签章 intent
- 决定查什么、编排多步调查
- 抓未列举的偏离
- 讲人话（生成可读的验证报告）
- 起草 Foundry PoC

它**不**负责：最终判定的真相裁定 —— 那是确定性核心（`predicates.py`）+ grounding guard 的事。

打个比方：GLM-5.1 是"侦探"，负责调查和推理；但"法官"（最终判定）是确定性引擎，侦探不能自己判案。

## 怎么接

- **位置**：接在 **model-interface 边界**之后（与 walking skeleton 既有的"干净接口 + 可替换实现"风格一致，对齐 `ChainAdapter` / `EIP712Verifier` / `AttestationSink` 的边界化做法）
- **职责**：驱动**多步调查回圈**（解析 intent → 捞链 trace → 多子问题比对 → 起 PoC → 校验 → attest）；过程可追溯、log 显式秀长程

## 职责边界（关键设计）

```
GLM-5.1（调查脑）          确定性核心（判定真相）
┌─────────────────┐      ┌─────────────────┐
│ 诠释松散 intent  │      │ predicates.py   │
│ 决定查什么       │      │ allowlist check │
│ 编排多步调查     │      │ outflow cap     │
│ 抓未列举偏离     │      │ residual check  │
│ 起草 PoC        │      │ value check     │
│ 讲人话          │      │                 │
└────────┬────────┘      └────────┬────────┘
         │                        │
         ▼                        ▼
    提出"疑似偏离"          最终判定 PASS/FAIL
    （需 PoC 支撑）         （确定性，可重跑）
```

**核心原则**：GLM-5.1 可以提出"疑似偏离"，但 FAIL 必须有可重现 PoC（grounding guard）。没有 PoC 就不能定 FAIL。

## model-agnostic 设计

- 系统**不绑单一模型**：model-interface 边界是抽象的，可以换任何 LLM
- GLM-5.1 是**赛道指定的 demo backend**：demo 依赛道要求用 GLM-5.1
- 换模型只需实现同一个 interface：系统设计已为此预留

## Week 4 整合计划

| 天数 | 任务 | 产出 |
|------|------|------|
| 6/8 | 确认 GLM-5.1 API 接入方式 | API endpoint + auth 确认 |
| 6/9–10 | 实现 model-interface 边界 + GLM-5.1 adapter | `GlmModelAdapter` 类 |
| 6/11 | 串接调查回圈 + grounding guard | 多步调查可跑 |
| 6/12 | 端到端 demo：GLM-5.1 驱动调查 + 确定性判定 | 2–3 场景跑通 |

## 风险与 Fallback

- **GLM-5.1 API 不稳定** → fallback：先半自动（人 + LLM 协作），保证核心流程跑通
- **调查回圈太慢** → fallback：限制调查步数上限，超时走确定性检查兜底
- **模型幻觉** → grounding guard 兜底：无可重现 PoC 不得定 FAIL
