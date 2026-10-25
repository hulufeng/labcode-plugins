# 原创插件模板（plugin-template）

这是一个**空的、不带任何第三方内容的**插件骨架。复制本目录、改名、改 `plugin.json` 里的 `id`/`name`，再补上你自己的工具与技能，就是一个完全属于你的 LabCode 插件。

> 本模板刻意**不写 `developer` 字段、不引用任何外部插件的内容**——原创插件的所有权在你。需要署名时，在 `plugin.json` 加 `"developer": "你的名字"` 即可。

## 一个插件 = `plugin.json` 声明 + 内容

官方市场（GitHub 源 `hulufeng/labcode-plugins`）安装时会递归找每个子目录下的 `plugin.json`，**加目录即上架，客户端零改动**。

### 1. skills（最该你原创的部分）
纯内联提示词，注入 AI 上下文，输入 `/` 调起。不需要任何 App 代码：
```json
{ "name": "我的技能", "description": "一句话说明", "prompt": "当用户要…时，先…再…" }
```
把示例里的 `prompt` 换成你自己的工作流程即可。

### 2. tools（三种 transport 怎么选）

| transport | 适合 | 要不要改 App 代码 |
|---|---|---|
| `cli` | 调用**插件自带的脚本/程序**（.py/.js/编译产物），逻辑 100% 归你 | **不用**，首选原创 |
| `internal` | 需要调用 LabCode 内部能力（如读工程、串口） | 要：在 `app.js` 用 `window.PluginSystem.registerInternalService('你的服务', {...})` 注册 |
| `http` | 调你自己的后端接口 | 不用 |

#### `cli` 示例（开箱可用，零依赖）
```json
{
  "name": "我的工具",
  "description": "调用我自己的脚本",
  "category": "execute",
  "parameters": { "type": "object", "properties": { "input": { "type": "string" } }, "required": ["input"] },
  "transport": "cli",
  "cli": { "command": "python \"{scriptPath}\" --in {input}", "timeoutMs": 120000 }
}
```
- 命令模板支持 `{参数名}`（来自调用参数）和 `{projectPath}`（当前工程路径）占位替换。
- 脚本随插件一起装在用户机器上，可直接在 `command` 里写绝对路径，或用 `cli.envScript` 先 source 环境。

#### `internal` 示例（需注册服务）
```json
{ "name": "x", "transport": "internal", "internal": { "service": "myService", "method": "run" } }
```
然后在 `app.js` 注册：
```js
window.PluginSystem.registerInternalService('myService', {
  run: async (args) => '处理结果是 ' + JSON.stringify(args)
});
```

## 发布 / 安装两条路
- **官方市场**：把插件目录推到 `hulufeng/labcode-plugins`（每个插件一个目录含 `plugin.json`）。客户端已把该仓库设为默认安装源，推上去即在官方市场可一键安装。
- **社区**：给仓库打上 `labcode-plugin` topic，社区 tab 会出现卡片，点「安装」即装。

## 装前自测
装「插件开发工具链」插件后，它带一个 `plugin_dev_validate` 工具，可校验 `plugin.json` 是否合规（`tools` 是否缺 `name`/`description`/`transport` 等）。先用它跑一遍再发布。
