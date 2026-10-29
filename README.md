# dsh-plugins

给 [DeepSeek Harness](https://github.com/deepseek-ai) 写的小插件。前两个纯本地、不联网不调模型；第三个会在「拿不准要不要接话」时问一次判断模型（只回 yes/no），挂了就退回原逻辑。

| 插件 | 干什么 | 一句话 |
| --- | --- | --- |
| [dsh-memory-tier](https://github.com/yingchaoyi/dsh-memory-tier) | 三档记忆：长期 / 中期 / 短期 | 该记的记住，该忘的忘掉，每轮只注入相关的那几条，省 token |
| [dsh-sticker-vault](https://github.com/yingchaoyi/dsh-sticker-vault) | 表情包库 | 群友的梗图先收着，重复到次数就入库，对喷时按语境捞出来发 |
| [dsh-air-sense](https://github.com/yingchaoyi/dsh-air-sense) | 读空气：接不接话 | 群消息先判一遍值不值得接，不值得就闭嘴——群里的 AI 最招人烦的是每句都接 |

## 一起装

```bash
dsh plugin --profile web add github:yingchaoyi/dsh-memory-tier
dsh plugin --profile web add github:yingchaoyi/dsh-sticker-vault
dsh plugin --profile web add github:yingchaoyi/dsh-air-sense
```

也可以只装一个，在插件页里填包名：

- `dsh-memory-tier`（或 `github:yingchaoyi/dsh-memory-tier`）
- `dsh-sticker-vault`（或 `github:yingchaoyi/dsh-sticker-vault`）
- `dsh-air-sense`（或 `github:yingchaoyi/dsh-air-sense`）

装完刷新页面，设置里会多出「三档记忆」「表情库」「读空气」三块。

---

## dsh-memory-tier

三档记忆，全部本地：

- **长期**——永久，只放最关键的东西：群友是谁、什么脾气、什么关系、什么生日，还有主人定的人设和规矩。带 🔒 的是「底稿」，群友怎么说都不许改，用来防编。
- **中期**——默认 30 天，满了按重要性淘汰。
- **短期**——默认 4 天，群里最近聊的梗和事，用来接话不掉线。

不回读整段聊天记录，按当轮问题做关键词检索，只注入命中的几条（有硬字数预算），所以又快又省。每条都能手动改、删、锁；面板里能看这一轮到底会注入什么。

工具：`mem_note` / `mem_search` / `mem_forget` / `mem_stats`

## dsh-sticker-vault

表情包库：

- 群友发图先落盘（按 sha256 去重）→ 同一张重复出现就计数 → 到阈值（默认 5）自动入库
- 入库时记着：谁发的、用过几次、当时在聊什么（最多 6 条语境）
- 每人最多 3 张、全库 50 张，超了挤掉计数最低的；**上锁**的挤不掉也删不掉
- 计数每 5 天清零；库里的图重置成 2 次起算，比新候选多一点起跑优势
- 面板能改描述/标签、上传、删除、搜索；候选区能手动收或扔

工具：`sticker_pick`（按语境/关键词/人挑图）/ `sticker_note` / `sticker_stats`

## dsh-air-sense

给群聊里的 AI 装一道闸：**这条消息现在值不值得接**。

- AstrBot 收到群消息先送到 DSH 问一句，不接就 `stop_event()`，群里什么都看不到，也不花回复的 token
- 完整漏斗：开关 / 黑白名单 / 闭麦 / 指令 / 戳一戳 / 关键词 / 静默时段 / @老子硬过 / @别人硬拦 / 刷屏氛围 / 活跃度 / 日时额度 / 同人冷却 / 概率 / 密度 / 自适应分数
- 概率 = 基础 × 回复加成 × 注意力 × 戳戳增值 × 表情衰减 × 密度衰减 × 自适应分数，过了才掷骰子，还拿不准再问一次判断模型
- 自适应：有人接 +15、没人理 −8、每天衰减 2，频率自己贴合每个群
- 面板四页：概览 / 规则（49 项全带说明）/ 日志（搜索 + 分页）/ 设置；右上角总开关，关掉彻底不插手
- 判定失败、超时、插件挂了一律 fail-open——退回 AstrBot 原逻辑，不会把群聊憋死

需要配合 AstrBot 侧 `astrbot_plugin_dsh` 打个小补丁（README 里写了改哪四处）。

没有工具（故意的：工具 schema 直接投影给模型上下文，写歪一次能把整轮会话打成 400）。

---

## 为什么都做成插件

- 装/卸就是加一行依赖，不想要了直接删；
- 面板挂在设置里，参数自己调，不用改代码；
- 数据都在 `~/.dsh/` 下，删目录就干净了。

## License

MIT © 2026 yingchaoyi
