<p align="center">
  <a href="https://adudir.com"><img src="assets/adudir-logo.png" width="128" alt="阿杜导演 ADuDir"></a>
</p>

<h1 align="center">MEP · Motion Edit Protocol</h1>

<p align="center">
  由 <a href="https://adudir.com"><b>阿杜导演</b></a> 提出并实现的开放剪辑协议<br>
  An open editing protocol proposed and implemented by <a href="https://adudir.com"><b>ADuDir</b></a><br>
  官网 Website：<a href="https://adudir.com">adudir.com</a>
</p>

---

## 中文

**MEP（Motion 剪辑协议）是让任何 AI 助手直接剪片子的开放协议。**
剪辑软件把工程开放成一个文件夹，助手读文件了解工程、写文件提交修改，软件校验后执行并写回执。

> MCP 给助手接一根线，MEP 给助手一张工作台。

- 不装插件、不改配置、不重启助手：任何能读写本机文件的助手都能用。
- 接入不占上下文；不靠截图；执行由软件按确定规则完成，不调用模型。
- 每单带版本号，不覆盖用户的新修改；每单有回执，可整单撤销。
- 助手可以请软件把成片某几秒画出来，自己检查后再交差。

### 一分钟看懂

```
用户在剪辑软件里点「复制命令」 ──► 把那句话贴给助手
                                        │
            ┌───────────────────────────┘
            ▼
   助手读 README.md、project.json
   助手写 requests/r-01.json  ──►  软件核对口令与版本、执行
                                   软件写 results/r-01.json  ──► 助手读回执，继续下一单
```

一个请求：

```json
{
  "mep": 1,
  "key": "本单口令",
  "who": "Codex",
  "base": "project.json 的 revision",
  "ops": [{ "op": "cut", "clip": "片段id", "at": 12.4 }]
}
```

一份回执：

```json
{ "mep": 1, "id": "r-01", "ok": true, "at": 1763520001234, "revision": "新版本号",
  "changes": { "created": [], "removed": [] } }
```

### 仓库内容

| 路径 | 内容 |
|---|---|
| [SPEC.md](SPEC.md) | MEP 1 规范（目录、单据与口令、心跳、请求、回执、错误码、七种请求、编辑项的公共约定、代码场景、安全、版本、一致性要求） |
| [schema/](schema/) | JSON Schema：请求、回执、`status.json`、`mep.json` |
| [examples/](examples/) | 示例 `mep.json`、心跳、请求与回执 |
| [reference/submit.mjs](reference/submit.mjs) | 参考提交脚本（Node.js，无依赖） |
| [docs/why-mep.md](docs/why-mep.md) | MEP 与 MCP、Computer Use 的对比和设计理由 |

### 实现

- **宿主（剪辑软件）**：按 [SPEC.md §15](SPEC.md#15-一致性要求) 的最低要求实现协作目录、单据、心跳、请求校验与回执。
- **助手**：读协作目录里的 `README.md`，按 `mep.json` 写请求即可，不需要任何 SDK。

参考实现：[阿杜导演](https://adudir.com)（桌面版 1.3.5 起）。下载与了解更多见官网 [adudir.com](https://adudir.com)。

### 许可

[Apache License 2.0](LICENSE)。任何剪辑软件、任何助手都可以实现 MEP，无需另行授权。

---

## English

**MEP (Motion Edit Protocol) is an open protocol that lets any AI assistant edit video directly.**
The editor opens a project as a folder. The assistant reads files to understand the project and writes files to submit changes. The editor validates and executes each change, then writes back a result.

> MCP gives the assistant a cable. MEP gives it a workbench.

- No plugin, no configuration, no assistant restart. Any assistant that can read and write local files can use it.
- Nothing occupies the context window while idle. No screenshots. Execution follows deterministic rules in the editor and calls no model.
- Every request carries the project version it was written against, so it never overwrites the user's newer edits. Every request gets a result and can be undone in one step.
- The assistant can ask the editor to render any moment of the final video and check its own work before handing it over.

### One-minute overview

```
User clicks "Copy command" in the editor ──► pastes that sentence into the assistant
                                                   │
             ┌─────────────────────────────────────┘
             ▼
   Assistant reads README.md and project.json
   Assistant writes requests/r-01.json ──► Editor checks key and version, executes
                                           Editor writes results/r-01.json ──► Assistant reads it, goes on
```

A request:

```json
{
  "mep": 1,
  "key": "order key",
  "who": "Codex",
  "base": "revision from project.json",
  "ops": [{ "op": "cut", "clip": "clip-id", "at": 12.4 }]
}
```

A result:

```json
{ "mep": 1, "id": "r-01", "ok": true, "at": 1763520001234, "revision": "new revision",
  "changes": { "created": [], "removed": [] } }
```

### What's in this repository

| Path | Contents |
|---|---|
| [SPEC.en.md](SPEC.en.md) | The MEP 1 specification: folder layout, orders and keys, heartbeat, requests, results, error codes, the seven request kinds, common conventions for edit items, code scenes, security, versioning, conformance |
| [schema/](schema/) | JSON Schemas for requests, results, `status.json` and `mep.json` |
| [examples/](examples/) | Sample `mep.json`, heartbeat, requests and results |
| [reference/submit.mjs](reference/submit.mjs) | Reference submit script (Node.js, no dependencies) |
| [docs/why-mep.en.md](docs/why-mep.en.md) | MEP compared with MCP and Computer Use, and the design rationale |

### Implementing MEP

- **Host (the editor):** implement the collaboration folder, orders, heartbeat, request validation and results, as listed in the minimum requirements of [SPEC.en.md §15](SPEC.en.md#15-conformance).
- **Assistant:** read the `README.md` in the collaboration folder and write requests as `mep.json` describes. No SDK needed.

Reference implementation: [ADuDir](https://adudir.com) desktop, version 1.3.5 and later. Download it and learn more at [adudir.com](https://adudir.com).

### License

[Apache License 2.0](LICENSE). Any editor and any assistant may implement MEP without further permission.
