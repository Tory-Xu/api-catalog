# api-catalog

Vibe Coding 利器 —— 项目函数/API 分类知识库，为 [Claude Code](https://docs.anthropic.com/en/docs/claude-code) 打造。

在编码过程中自动记录、分类、按需查找项目中的工具函数和 API 封装，让 AI 不再"遗忘"你的项目约定。

## 解决什么问题

Vibe coding 时经常遇到：
- 告诉 AI 用项目封装的 `getServerTime()`，AI 下次又用回原生 `Date()`
- 项目里明明有现成的工具函数，AI 却重新实现了一遍
- 想让 AI 记住所有工具函数，但一次性全部加载太浪费 token

**api-catalog** 通过两级索引 + 按需加载解决这些问题：
- 第一级：轻量主索引（~100 tokens），列出所有分类
- 第二级：各分类详情文件，只在需要时才加载

## 安装

### 方式一：npx skills（推荐）

```bash
# 全局安装
npx skills add xuxuxu/api-catalog@api-catalog -g -y

# 或安装到当前项目
npx skills add xuxuxu/api-catalog@api-catalog -y
```

### 方式二：手动安装

```bash
# 全局安装
git clone https://github.com/xuxuxu/api-catalog.git ~/.claude/skills/api-catalog-repo
ln -s ~/.claude/skills/api-catalog-repo/api-catalog ~/.claude/skills/api-catalog

# 或安装到当前项目
git clone https://github.com/xuxuxu/api-catalog.git .claude/skills/api-catalog-repo
ln -s .claude/skills/api-catalog-repo/api-catalog .claude/skills/api-catalog
```

## 使用方法

### 手动记录函数

在编码过程中随时告诉 AI：

```
记录这个函数
记下这个 API
catalog this function
```

AI 会读取函数源码，自动归类，写入索引。

### 自动扫描项目

```
扫描项目函数
scan project utilities
索引项目工具函数
```

AI 会扫描 `utils/`、`services/`、`lib/` 等目录，自动识别和分类工具函数。

### 编码时自动查阅

安装后，AI 在编码时会自动：
1. 检查项目的 `_index.md` 索引
2. 只加载与当前任务相关的分类（如只需要时间函数就只加载 `time.md`）
3. 优先使用已有工具函数，而非重新实现

> **提示**：为了确保 AI 在每次编码时都主动查阅 catalog，建议在项目的 `CLAUDE.md` 中添加：
>
> ```markdown
> ### API Catalog 使用规范
>
> 编写或修改代码前，先检查 `.claude/api-catalog/_index.md` 是否存在。若存在：
> 1. 读取 `_index.md` 判断当前任务涉及哪些分类
> 2. 只读取相关分类文件（如只涉及时间处理就只读 `time-utils.md`，不要加载其他分类）
> 3. 优先使用 catalog 中已有的函数，不要重新实现或使用原生方法替代
> ```

## 数据存储

每个项目独立维护索引，存放在项目根目录下：

```
{project_root}/.claude/api-catalog/
├── _index.md          # 主索引（轻量，< 200 tokens）
├── time.md            # 时间工具
├── http.md            # 网络请求
├── validation.md      # 数据校验
└── ...                # 更多分类按需创建
```

### 记录格式示例

```markdown
### `getServerTime()`
**用途**：获取服务器当前时间，而非客户端本地时间
**示例**：`const now = getServerTime()`
**注意**：所有时间获取必须用这个，禁止直接使用 new Date()
**位置**：`src/utils/time.ts:42`
```

## 特性

- **两级索引**：主索引轻量加载，分类详情按需读取，节省 token
- **自动归类**：AI 根据函数用途自动判断分类，无需手动指定
- **双模录入**：支持手动记录单个函数，也支持自动扫描整个项目
- **交叉引用**：函数涉及多个分类时，自动添加交叉引用
- **项目隔离**：每个项目独立维护自己的索引，互不影响

## 许可证

MIT
