# LabCode Plugins

LabCode 桌面客户端的插件 manifest 仓库。

客户端启动时从这里拉 `<plugin-id>/plugin.json`，加载为工具/技能/视图。

## 目录结构

```
<plugin-id>/
  plugin.json
```

## Transport

- `internal`: 调用宿主 service
- `cli`: spawn 外部命令
- `http`: REST fetch
- `mcp`: Model Context Protocol
- `grpc` / `stdio` / `mqtt`: 占位
