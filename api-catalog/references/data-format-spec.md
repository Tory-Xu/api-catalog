# 数据格式规范

## `_index.md` 格式

主索引文件必须保持轻量（目标 < 200 tokens），采用表格格式：

```markdown
# API Catalog 索引

| 分类 | 说明 | 数量 |
|------|------|------|
| [time](time.md) | 时间日期获取、格式化、计算 | 4 |
| [http](http.md) | HTTP 请求封装、API 调用、拦截器 | 8 |
| [format](format.md) | 数据格式化、数值/货币/手机号等 | 5 |

> 最后更新：2025-01-15
```

**规则**：
- 分类名用 markdown 链接指向对应文件
- 说明不超过 15 个字
- 数量为该分类中的函数条目数
- 底部记录最后更新日期
- 新增分类时追加行，不重新排序

## 分类文件格式

每个分类文件（如 `time.md`）结构如下：

```markdown
# 时间工具

项目中与时间日期相关的工具函数和封装方法。

---

### `getServerTime()`
**用途**：获取服务器当前时间，而非客户端本地时间
**示例**：`const now = getServerTime()`
**注意**：所有时间获取必须用这个，禁止直接使用 new Date() 或 Date.now()
**位置**：`src/utils/time.ts:42`

---

### `formatRelativeTime(timestamp)`
**用途**：将时间戳转为相对时间描述（如"3分钟前"、"昨天"）
**示例**：`formatRelativeTime(1704067200000)` → `"2小时前"`
**位置**：`src/utils/time.ts:78`

---
```

**规则**：
- 文件顶部：一级标题（中文分类名）+ 一句话说明该分类的范围
- 条目之间用 `---` 分隔
- 每条记录包含：函数签名（三级标题）、用途、示例、注意（可选）、位置
- 新增条目追加到文件末尾
- 交叉引用格式：`> 另见 [http.md](http.md) 中的 createApiClient`

## 分类文件命名规范

- 使用英文小写
- 多词用连字符（kebab-case）：`date-time.md`、`http-request.md`
- 尽量用简短通用的词：`time` 优于 `date-time-utils`、`http` 优于 `network-request-helpers`
- 常见分类名参考：`time`、`http`、`format`、`auth`、`storage`、`validation`、`ui`、`event`、`config`

## 更新规则

**新增函数**：
1. 判断分类 → 追加到对应 `.md` 文件末尾
2. 更新 `_index.md` 中对应分类的数量列
3. 更新 `_index.md` 底部的最后更新日期

**新增分类**：
1. 创建新的分类 `.md` 文件
2. 在 `_index.md` 表格中追加新行

**删除/修改函数**：
1. 在对应分类文件中删除或修改条目
2. 更新 `_index.md` 中的数量
3. 若分类为空，删除分类文件并移除 `_index.md` 中的对应行

## 初始化

项目首次使用时，创建 `.claude/api-catalog/` 目录和 `_index.md`：

```markdown
# API Catalog 索引

| 分类 | 说明 | 数量 |
|------|------|------|

> 最后更新：{当前日期}
```
