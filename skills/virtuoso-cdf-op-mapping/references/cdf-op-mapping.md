# 安全 User CDF 流程与已验证案例

## 案例范围

原对话记录：Library `example_pdk`，Cell `example_nmos5v`（5 V NMOS），顶层示例 `/NM23`，内部映射路径 `.NM0.m1`。底层和映射后顶层记录到 `region=2`。不要把 2 无条件解释为所有模型的饱和区编码。

## 操作

1. 保存现有 User CDF 的备份和原映射文本；没有 User CDF 时记录这一状态。只读记录 Base CDF，保留恢复依据。
2. 在 Calculator 查询 `OP("/NM23" "??")`，再在内部层次确认 region 和已有 gm/id 的实际值及路径。
3. CIW → Tools → CDF → Edit，选择正确 Library、Cell，先查看 Base CDF 的 Simulation Information → spectre → opParamExprList。
4. 切换到 **User CDF** 后编辑。保留原 id、vgs、vds、vth、gm 表达式原文，不用仅含 region 的列表覆盖整个字段。若 User 层为空，依据该环境有效配置保留既有列表，再追加目标项。
5. 本例追加的列表项（不是完整列表）为：

```lisp
("region" "OP(mappedRoot(\".NM0.m1\") \"region\")")
```

字符串内引号需要上述转义；实际表达式为 `OP(mappedRoot(".NM0.m1") "region")`。不要把 Markdown/JSON 的额外转义层复制进 CDF。

6. Apply/保存后，在 CIW 检查当前会话对象，先定义变量再访问：

```lisp
cellId = ddGetObj("example_pdk" "example_nmos5v")
userCdf = cdfGetUserCellCDF(cellId)
```

确认两者非 nil 后再读取：

```lisp
userCdf->simInfo->spectre->opParamExprList
```

7. 重新仿真或确认选中了有效工作点结果，在 Calculator 查询 `OP("/NM23" "region")`，与内部 MOS 数值比较；在 Annotation Setup 加入 region，确认显示。复查原 id/gm 等仍可读取。
8. 如不一致，先查路径/结果集，不继续持久化错误映射。恢复备份列表或既有 User CDF；不要删除包含其他个人设置的整个 CDF。

此案例的成功状态继承自原对话记录；本技能创建没有重新运行 Virtuoso。
