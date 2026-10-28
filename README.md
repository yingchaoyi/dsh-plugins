# dsh-plugins

给 [DeepSeek Harness](https://github.com/deepseek-ai) 写的小插件，都是本地的、不联网、不调模型。

| 插件 | 干什么 | 一句话 |
| --- | --- | --- |
| [dsh-memory-tier](https://github.com/yingchaoyi/dsh-memory-tier) | 三档记忆：长期 / 中期 / 短期 | 该记的记住，该忘的忘掉，每轮只注入相关的那几条，省 token |
| [dsh-sticker-vault](https://github.com/yingchaoyi/dsh-sticker-vault) | 表情包库 | 群友的梗图先收着，重复到次数就入库，对喷时按语境捞出来发 |

## 一起装

```bash
dsh plugin --profile web add github:yingchaoyi/dsh-memory-tier
dsh plugin --profile web add github:yingchaoyi/dsh-sticker-vault
```

也可以只装一个，在插件页里填包名：

- `dsh-memory-tier`（或 `github:yingchaoyi/dsh-memory-tier`）
- `dsh-sticker-vault`（或 `github:yingchaoyi/dsh-sticker-vault`）

装完刷新页面，设置里会多出「三档记忆」和「表情库」两块。

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

---

## 为什么都做成插件

- 装/卸就是加一行依赖，不想要了直接删；
- 面板挂在设置里，参数自己调，不用改代码；
- 数据都在 `~/.dsh/` 下，删目录就干净了。

## License

MIT © 2026 yingchaoyi
