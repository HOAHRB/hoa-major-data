# hoa-major-data

本仓库存放哈尔滨工业大学本部的培养方案数据。

## 数据范围

当前收录 2024 级、2025 级和 2026 级培养方案。

## 文件维护方式

`major_mapping.json`、`plans/` 和 `course_introductions.json` 由 `hoahrb-jwts`
根据教务系统数据生成。课程介绍随 `jwts crawl` 抓取，并只覆盖本次涉及课程的条目。

培养方案文件以类别开头：`本_`、`辅修_`、`第二学士学位_`、`Y_`、`微专业_`
或 `未分类_`。每个 TOML 的 `[info].plan_category` 必须与文件名前缀一致。
当前站点只发布 `本_` 培养方案，其他类别仍保存在本仓库中。

## 培养方案元信息的权威来源

每个培养方案 TOML 的 `[info]` 是该方案完整元信息的权威来源，包括：

- `year`：年级
- `major_code`：专业代码，也是同一年级内方案的唯一标识
- `major_name`：专业名称
- `school_name`：学院名称
- `plan_category`：方案类别
- `plan_ID`：由 `HIT-{year}-{major_code}` 确定的方案标识

文件名和 `major_mapping.json` 均由同一份方案元信息生成，只是面向不同用途的投影，
不应作为独立来源单独修改：

- 文件名用于人工辨识和发布前过滤。它包含类别、年级、学院及通常情况下的专业名称；
  名称会经过文件系统安全清洗，学院名与专业名相同时还会省略重复的专业名，因此文件名
  不是专业名称或学院名称的无损副本。当前 Fuma 构建脚本按 `本_*.toml` 白名单发布
  普通本科方案，所以文件名前缀必须与 `[info].plan_category` 一致。
- `major_mapping.json` 以 `year` 和 `major_code` 为索引，保存 `name`、`school_name`、
  `plan_ID` 等供前端专业列表使用的展示信息。它目前不保存方案类别；其中各字段必须与
  对应 TOML 的 `[info]` 一致。

后端从 TOML 的 `[info]` 读取年级、专业代码、专业名称和 `plan_ID`，使用年级与专业代码
生成页面路径，并使用专业名称生成导航元信息。Fuma 的专业选择列表名称来自
`major_mapping.json`。因此维护数据时必须同时满足：

1. 文件名前缀与 `[info].plan_category` 一致；
2. `major_mapping.json` 中的年份、专业代码、专业名称、学院名称和 `plan_ID` 与
   对应 TOML 的 `[info]` 一致；
3. 同一年级内不得出现重复的 `major_code`，`plan_ID` 必须保持确定性和唯一性。

以下文件由维护者手工管理，不会被 `hoahrb-jwts` 覆盖：

- `lookup_table.toml`
- `grades_summary.json`
- `shared_categories.toml`

## 更新数据

先在 `hoahrb-jwts` 仓库中抓取指定年级的数据：

```powershell
uv run jwts crawl --years 2024 2025 2026 --data-dir ..\hoa-major-data
```

抓取完成后检查生成内容：

```powershell
git diff -- major_mapping.json course_introductions.json plans
```

确认新增、修改和删除均符合预期后，再提交本仓库的更改。
