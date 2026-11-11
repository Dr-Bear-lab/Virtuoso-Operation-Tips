# cdfDump、load 与 .cdsinit 持久化

## 导出并检查

在 **Virtuoso CIW** 执行；路径须换成当前 EDA 主机上个人可写的绝对路径，不能使用本技能包的 Windows 路径。以下是原对话验证成功的调用形式：

```lisp
cdfDump(
  "example_pdk"
  "/path/to/region_cdf.il"
  ?cellName "example_nmos5v"
  ?level 'user
  ?edit nil
)
```

使用独立备份文件名保存修改前状态，避免新 Dump 覆盖唯一备份。返回 t 后仍要检查文件内容：正确 Library/Cell、User 层、region 及原有映射均存在，并检查 `cdfCreateUserCellCDF` 等代码与所有删除/重建操作。普通 CDF Save 数据文件不能仅改扩展名就当作可执行 SKILL。

完整 Dump 可能删除并重建目标 User CDF，因此加载旧 Dump 会覆盖后来新增的其他个人设置。加载前对比当前配置；不要未经审查加载来源不明的 SKILL。

## 手动恢复

在 CIW 执行：

```lisp
load("/path/to/region_cdf.il")
```

检查无错误、返回值、User/有效 CDF 中的映射，以及顶层 OP 数值和原参数。仅 t 不是功能验收。

公开示例文件路径（使用时替换）：

```text
/path/to/region_cdf.il
```

原对话记录了导出内容检查、cdfDump 返回 t 和手动 load 返回 t；本包未复制或重新验证该服务器文件。

## 启动配置

找到当前启动方式真正读取的个人 `.cdsinit`，先备份。不要假设所有目录的同名文件都会执行；检查项目/站点启动链及日志。保留原配置及顺序。本例原有：

```lisp
load(strcat(getShellEnvVar("VAR_CDSINIT_REF")))
```

在已有配置末尾、库可访问且不会被后续初始化覆盖的位置添加一次：

```lisp
; Load personal MOS region mapping
load("/path/to/region_cdf.il")
```

保存后检查路径、权限和加载顺序。不要修改共享 .cdsinit 来实现个人覆盖。

## 必做的重启验收

初次创建本包时，重启自动加载尚待验证。2026-11-12 用户确认：将 mp5 的完整 CDF Dump 追加在 mn5 后面，重启 Virtuoso 后只加载同一个文件即可恢复配置。这是用户现场验证反馈，本包维护时未远程复测；其他环境仍需按以下步骤验收。

1. 保存设计和启动文件，正常退出并按日常方式重启 Virtuoso。
2. 不手动 load、不重新编辑 CDF，先检查启动日志无相关错误。
3. 检查目标 User/有效 CDF 自动包含 region；选择或重新生成有效 OP 结果。
4. 验证顶层 region 与内部值一致，Annotation 正确，原 id/gm 等参数保留。
5. 记录版本、启动目录、实际 .cdsinit、Dump 路径及结果，全部通过后才改为“自动恢复已验证”。

回滚：恢复 .cdsinit 备份或移除本次添加的一条 load，恢复修改前 User CDF；注意移除启动行不会撤销当前会话已经执行的覆盖。PDK 升级后先重新验证兼容性。


## 扩展新的管子：追加到同一个 .il 文件

1. 对新管子重复检查底层目标参数和 Base CDF 路径，在该 Cell 的 **User CDF → spectre → opParamExprList** 中添加映射，保留原有项，然后 Apply。不要直接照抄 mn5 的内部路径。
2. 执行 **Recreate Netlist → 重新仿真**，验证顶层 OP。普通 Run 可能复用旧映射；本次 mp5 在 User/Effective CDF 已有 region、底层 region=2、顶层 gm 正常但 region 求值失败时，重建网表后恢复正常。
3. 选择新管子的 **User** 层，用 **CDF Dump（不是 Save）** 导出到独立临时 .il 文件，避免覆盖原合并文件。
4. 备份原 region_cdf.il，将新文件的**完整 Dump 代码块**粘贴到原 mn5 代码块结束之后。保留新块自己的 Library/Cell 指定和完整括号，不要粘进前一个 let 内，也不要只追加 region 那一行。核对每块只操作各自目标 Cell。
5. 保存合并文件。若路径未变，原 .cdsinit 的一条 load 即可依次加载所有器件，无需为每颗管子再加一条。检查加载无报错，并验证新旧器件；重启后不手动 load，再确认自动恢复。

已反馈成功的组合：example_nmos5v 后追加 example_pmos5v；mp5 的映射路径为 .PM0.m1。后续更新已有管子时替换该管子的旧代码块，避免同一 Cell 重复重建、由末尾旧块覆盖新设置。
