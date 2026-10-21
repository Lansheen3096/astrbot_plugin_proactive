# astrbot_plugin_proactive

**条件式主动消息**：用户安静够久，bot 自己发一条过来 —— 内容按当前会话绑定的人设现写。

版本 `0.2.0` · AstrBot `>= 4.28.2` · 平台 `aiocqhttp`（OneBot v11）

---

## 它解决什么

人设里通常写着「等得够久了，我会自己发一条过去」，但框架里没有这条链路，
于是模型只能在对话里"编"自己发过。这个插件把这句话落实，顺便少一个幻觉来源。

## 触发条件（全部满足才发）

- 用户空闲达到 `idle_hours`（默认 2 小时）
- 距上一条主动消息超过 `cooldown_hours`（默认 3 小时）
- 当天条数没到 `daily_max`（默认 5 条）
- 当前处于 `active_from` ~ `active_to` 之间（默认 8~23 点，含起不含止）

每 `check_every` 秒检查一次（默认 300 秒）。

## 内容怎么来

由 LLM **按当前会话绑定的人设**现写（从 `personas` 表按 `persona_id` 取），
并带最近几条真实对话做上下文，不允许编造具体事件。

分条发送间隔 `seg_gap_min` ~ `seg_gap_max`（默认 6~12 秒），一条最多切 `max_seg` 段（默认 3）。

## 手动触发

```
/主动
/proactive
```

立刻走同一条「生成 + 发送」路径，**不影响计数** —— 用来验证人设和语气，不用等到自然触发。

## 状态

`data/plugin_data/astrbot_plugin_proactive/state.json`：上次触发时间、当天已发条数。
重装插件不会丢（只要 `data/plugin_data` 还在）。

## 配置

| 配置项 | 默认 | 说明 |
|---|---|---|
| `idle_hours` | `2.0` | 空闲多少小时后考虑发 |
| `cooldown_hours` | `3.0` | 两条主动消息的最小间隔（小时） |
| `daily_max` | `5` | 每天最多发几条 |
| `active_from` | `8` | 允许主动的起始小时（24 小时制） |
| `active_to` | `23` | 允许主动的结束小时（不含） |
| `check_every` | `300` | 轮询间隔（秒） |
| `max_seg` | `3` | 一条主动消息最多分几段 |
| `seg_gap_min` | `6.0` | 分段间隔下限（秒） |
| `seg_gap_max` | `12.0` | 分段间隔上限（秒） |

默认值是「陪伴型部署」的档位，想要更克制就把 `idle_hours` 调高、`daily_max` 调低。

## 安装

```bash
# AstrBot 插件市场搜索「条件式主动消息」，或：
cd <AstrBot>/data/plugins
git clone --depth 1 --branch v0.2.0 https://github.com/Lansheen3096/astrbot_plugin_proactive
# docker 部署：拷进宿主机挂载的数据目录后重启容器
```

插件目录名要和 `metadata.yaml` 里的 `name` 一致。

## 日志

`[主动]` 前缀：为什么没发（空闲不够 / 冷却中 / 到上限 / 不在时段）、什么时候发出去了。

## 相关插件

- [astrbot_plugin_pacer](https://github.com/Lansheen3096/astrbot_plugin_pacer)：分段投递 + 插话打断
- [astrbot_plugin_burst](https://github.com/Lansheen3096/astrbot_plugin_burst)：消息防抖聚合

## License

MIT
