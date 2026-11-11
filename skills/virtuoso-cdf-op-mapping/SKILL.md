---
name: virtuoso-cdf-op-mapping
description: Diagnose and extend Virtuoso User CDF operating-point mappings for PDK subcircuit devices when internal MOS parameters are available but missing at the wrapper, and persist verified mappings using cdfDump and .cdsinit. Use for missing region, gm, gds or similar OP mappings, not for changing foundry models.
---

# Virtuoso CDF 工作点参数映射

先区分底层模型不支持、结果未保存、CDF 未映射和 Annotation 显示问题，再决定是否修改。此技能扩展个人 User CDF，不默认修改 Foundry Base CDF、模型或共享启动文件。

## 工作流程

1. 阅读 [根因分析](references/root-cause-analysis.md)，建立顶层缺失、底层存在的证据。
2. 按 [适用性检查](references/applicability-checklist.md) 核实当前 Library、Cell、模拟器、模型、内部路径和已有映射。换 PDK 必须重新核实。
3. 按 [映射流程与成功案例](references/cdf-op-mapping.md) 备份当前配置，在 User CDF 保留原有映射并新增目标项；对照底层值验证顶层 OP 和 Annotation。
4. 按 [持久化流程](references/cdf-persistence.md) 导出、检查、手动加载，再配置实际生效的个人 .cdsinit，完成干净重启验证；新增器件按该文档“扩展新的管子”重复操作并追加完整 Dump 到同一 .il 文件。
5. 异常时使用 [故障排查](references/troubleshooting.md)，不要通过反复覆盖 CDF 或修改模型来试错。

修改前确认用户授权覆盖的对象与范围；已有授权范围内继续执行，不重复确认。完整 Dump 可能删除并重建目标 User CDF，加载前必须审查并备份其他自定义设置。若对象、内部路径或覆盖范围不明，停止写入并先调查。

## 证据与状态

来源：实际 Virtuoso CDF 排查与操作记录；验证状态来自使用者现场反馈，并非所有 PDK 或版本的兼容性保证。

已记录验证：5 V NMOS 的 region 映射、cdfDump 导出、手动 load 返回 t。初次创建时重启自动加载待验证；2026-11-12 用户已确认 mn5 与 mp5 的完整 Dump 合并到一个 .il 文件后，重启可统一加载。另已反馈 mp5 新增 region 映射需 Recreate Netlist 后才能正常查询。以上为用户现场反馈，非本次维护远程复测。本包仅含指导文档，不附带或自动执行原服务器的 CDF Dump。

完成实际操作后报告修改对象、备份位置、映射表达式、底层与顶层值、导出文件、启动配置位置及重启验证状态。
