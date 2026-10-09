# 已验证的 MCP 调用

[返回接入指南](agent.md)

以下调用在 **2026-10-10** 对公开生产端点执行。结果仅展示身份和结构；不是固定数据库快照，也不是对目录参数、价格或厂商声明的独立事实核验。

## 找产品

```json
{
  "jsonrpc": "2.0",
  "id": 2,
  "method": "tools/call",
  "params": {
    "name": "search_robots",
    "arguments": { "query": "G1", "limit": 2 }
  }
}
```

本次调用成功。解析 text 内的 JSON 后，返回 `total: 11`，当前页含 2 项：

| ID | 名称 | 产品入口 |
| --- | --- | --- |
| `Robot20251111603` | Unitree G1+ | [产品详情](https://www.aiman.world/robot/Robot20251111603) |
| `Robot20251111607` | G1-Comp | [产品详情](https://www.aiman.world/robot/Robot20251111607) |

不要把关键词“G1”与搜索出的每个变体当作同一产品。继续读取时传返回的 ID，避免猜 slug。

## 找企业身份

```json
{
  "jsonrpc": "2.0",
  "id": 3,
  "method": "tools/call",
  "params": {
    "name": "search_companies",
    "arguments": { "query": "宇树", "limit": 2 }
  }
}
```

本次返回企业 canonical 名称 `宇树科技`，并带 `Unitree`、`Unitree Robotics`、`宇树` 等别名。后续企业查询使用返回的 canonical 名称，别名不生成另一家公司。

## 读正式关系，允许结果为空

```json
{
  "jsonrpc": "2.0",
  "id": 4,
  "method": "tools/call",
  "params": {
    "name": "world.graph",
    "arguments": { "entity": "宇树科技", "limit": 3 }
  }
}
```

本次成功结果的字段摘录：

```json
{
  "focus": "宇树科技",
  "source": "canonical-world",
  "nodes": [],
  "edges": [],
  "truncated": false
}
```

含义是该次 Canonical 查询没有返回节点与边，不能解释成现实中没有产业关系。网站 [产业图谱](https://www.aiman.world/graph) 展示的产业资料投影与此不同；资料关系可以经 `get_company_relationships` 读取，但必须保留原有来源与审核状态。

## 复现

将任一 JSON 作为请求体发到下面的地址：

```bash
curl -fsS https://www.aiman.world/mcp \
  -H 'Content-Type: application/json' \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/call","params":{"name":"search_robots","arguments":{"query":"G1","limit":2}}}'
```

先发现 `tools/list` 再复现，检查 JSON-RPC 错误、`isError` 和工具结果。数据随网站变化；本文只保留带日期的简短示例。
