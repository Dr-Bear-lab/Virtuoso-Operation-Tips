# Virtuoso Operation Tips

Cadence Virtuoso 操作经验与可复用 Codex 技能。

## virtuoso-cdf-op-mapping

用于排查封装器件缺少 region 等工作点参数，配置 User CDF 映射、导出 CDF Dump，并通过 .cdsinit 持久化；包含新增器件合并 .il 与 Recreate Netlist 的经验。

入口：[SKILL.md](skills/virtuoso-cdf-op-mapping/SKILL.md)。

## 使用

将 skills/virtuoso-cdf-op-mapping 整个文件夹复制到个人 Codex skills 目录，待应用识别后调用：

```text
$virtuoso-cdf-op-mapping 逐步排查器件的 region 为什么不显示。
```

也可以直接让助手读取 SKILL.md 的绝对路径。

## 示例与验证范围

公开版本已替换个人服务器路径、聊天标识和具体 PDK 库/Cell 名。example_pdk、example_nmos5v、example_pmos5v 为占位名称；内部路径来自案例，换 PDK 时必须重新核实。仅提供操作指导，不包含 Foundry 模型、PDK 文件或完整 CDF Dump。

修改前备份个人配置，不直接修改共享 Foundry Base CDF。现场反馈已验证多器件合并脚本的重启加载，以及新增映射后强制重建网表的修复效果；其他环境需独立验证。
