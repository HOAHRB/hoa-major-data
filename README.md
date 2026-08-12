# hoa-major-data

本仓库存放哈尔滨工业大学本部的培养方案数据。

## 数据范围

当前收录 2024 级和 2025 级培养方案。

## 文件维护方式

`major_mapping.json` 和 `plans/` 由 `hoahrb-jwts` 根据教务系统数据生成。

培养方案文件以类别开头：`本_`、`辅修_`、`第二学士学位_`、`Y_`、`微专业_`
或 `未分类_`。每个 TOML 的 `[info].plan_category` 必须与文件名前缀一致。
当前站点只发布 `本_` 培养方案，其他类别仍保存在本仓库中。

以下文件由维护者手工管理，不会被 `hoahrb-jwts` 覆盖：

- `lookup_table.toml`
- `grades_summary.json`
- `shared_categories.toml`

## 更新数据

先在 `hoahrb-jwts` 仓库中抓取指定年级的数据：

```powershell
uv run jwts crawl --years 2024 2025 --data-dir ..\hoa-major-data
```

抓取完成后检查生成内容：

```powershell
git diff -- major_mapping.json plans
```

确认新增、修改和删除均符合预期后，再提交本仓库的更改。
