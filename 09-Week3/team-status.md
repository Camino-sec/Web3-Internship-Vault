# 团队状态 — Team Status

> Week 3 任务：队员、角色、可投入时间、沟通方式

## 组成

**3 人团队，2 名开发者**：John（技术 lead）+ 技术 #2（归队）+ Luvia（运营）。
原技术 #2 已归队 → dashboard 回到 ON，Sprint 按 2-dev 重排。

## 角色与主责

- **John（技术 lead）**
  - 主责：validator 核心 + 完整性逻辑 + chain adapter + ERC-8004 整合 / attestation + agent（LLM 调查）回圈 + 自部署 Validation Registry + 部署
  - 可投入时间：大致全程在线

- **技术 #2（归队）**
  - 主责：Dashboard / Demo UI（intent-vs-execution diff 的 money shot + mock consumer 拒绝画面）+ 共担整合 / 测试
  - 技能方向：[John to confirm: frontend? Solidity? backend?]
  - 可投入时间：大致全程在线

- **Luvia（运营）**
  - 主责：pitch + 3–5 分钟影片 + README / proposal + 提交 + 发布赛道推文 + Demo Day + 协调
  - 可投入时间：大致全程在线

## 可投入时间 / 时区

- 全员大致全程在线
- 时区：UTC+8（东八区），同步时段无时差问题

## 沟通方式

TG / Twitter / WeChat

## 1-dev 范围实施影响

只有 John 一个开发者要在 ~6 天内做完 validator + chain adapter + ERC-8004 + dashboard + agent，因此：
- **dashboard 砍到最小**（简单表格 / 静态页 / CLI viewer 即可呈现 intent-vs-execution diff，不做打磨的 web app）
- 玩具 agent 走最小脚本
- John 的时间集中在核心（validator / chain adapter / ERC-8004 / demo）
- 我扛所有非程式产出（pitch / 影片 / README / 提交 / 发推文 / 协调），让 John 专注核心
