# 故障排查

| 现象 | 检查与处理 |
|---|---|
| 顶层 region 无结果，?? 只有少量项 | 对照底层 OP、保存设置和映射，不能直接判定模型损坏 |
| 底层也无目标参数 | 检查模型支持、分析类型、结果选择和保存设置；暂不添加映射 |
| unbound variable - cdf/userCdf | 当前 CIW 未定义该变量，重新获取 cellId 和 User CDF；变量不跨会话保留 |
| CDF 查询返回 nil | 检查 Library/Cell 是否可见、User 层是否存在、字段层次及是否刚重启；不要对 nil 继续解引用 |
| load(TEST_region) 报错 | 文件名应是带引号字符串；再确认内容是可执行 SKILL，而非普通 CDF Save 数据 |
| 在 CIW 输入 head -n 20 后 unbound variable - n | 混淆了 Linux shell 与 CIW；head/grep/rg 在终端执行，load/cdfDump 在 CIW 执行 |
| cdfDump 返回 t 但效果未知 | 检查输出文件、目标对象、User 层以及 region/既有映射，t 仅表示调用结果 |
| load 返回 t 但仍不显示 | 查有效 CDF 是否被其他初始化覆盖、表达式路径、结果集和 Annotation |
| 原有 id/gm 等丢失 | 可能整字段被只含 region 的列表覆盖；从备份恢复并合并原有项 |
| 重启后 region 消失 | 找实际 .cdsinit 启动链，检查保存、路径、权限、库可见性、加载顺序和日志；手动 load 成功不证明启动链成功 |
| 升级 PDK 后映射失败 | 禁用不兼容的旧启动覆盖，重新核实内部层次和当前 CDF，勿盲目重复加载旧 Dump |
| region 数字与预期不同 | 确认选对内部 MOS、偏置与模型编码，不能把所有模型的 2 都解释为饱和区 |

出现错误时保存第一条错误、当前版本、对象及表达式，先定位原因。若加载发生部分修改，依据备份恢复后再试，不连续执行可能重建 CDF 的脚本。不要为解决显示问题修改 Foundry 模型或共享 Base CDF。
