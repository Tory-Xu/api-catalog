---
name: api-catalog
description: >-
  This skill should be used when the user asks to "记录这个函数", "记录这个API",
  "catalog this function", "扫描项目函数", "scan project utilities",
  "查找项目工具函数", "有没有现成的工具函数", or when the user is coding and
  might benefit from checking existing project utility functions and API wrappers.
  Also activate when the user mentions "api-catalog", "函数目录", or "工具函数索引".
---

# API Catalog — 项目函数/API 分类知识库

对项目中使用的工具函数、API 接口、封装方法进行分类索引，支持按需查阅，避免 token 浪费。

## 核心原则

**两级索引，按需加载**：
- 第一级：`_index.md` — 只有分类名 + 一句话说明（始终轻量）
- 第二级：各分类 `.md` 文件 — 只在需要时才读取对应的文件

**绝不一次性加载所有分类文件。** 编码时只读 `_index.md` 判断需要哪个分类，再精确加载。

## 数据存放位置

每个项目独立维护自己的索引，存放在项目根目录下：

```
{project_root}/.claude/api-catalog/
├── _index.md          # 主索引（必须保持轻量）
├── time.md            # 示例：时间工具分类
├── http.md            # 示例：网络请求分类
└── ...                # 更多分类按需创建
```

## 操作模式

### 模式一：手动记录

**触发词**：用户说"记录这个函数"、"记下这个API"、"catalog this"等

**流程**：
1. 读取用户指定的函数/API 源码
2. 检查 `.claude/api-catalog/_index.md` 是否存在，不存在则先创建
3. 判断该函数应归入哪个已有分类（读 `_index.md`）。若无合适分类，创建新分类
4. 按 `references/data-format-spec.md` 中的格式，将函数条目追加到对应分类文件
5. 更新 `_index.md` 中的函数计数
6. 向用户确认：已记录到哪个分类

### 模式二：自动扫描

**触发词**：用户说"扫描项目函数"、"scan utilities"、"索引项目工具函数"等

**流程**：
1. 按 `references/auto-scan-strategy.md` 中的策略扫描项目
2. 识别出工具函数、API 封装、通用方法
3. 对所有识别到的函数进行自动分类
4. 生成/更新各分类文件和 `_index.md`
5. 向用户汇报：共发现 N 个函数，归入 M 个分类，列出各分类名和数量

若项目已有 catalog，扫描时与已有记录合并，不重复添加。

### 模式三：编码时按需查阅

**触发时机**：在编写代码过程中遇到以下场景时，**主动**检查 catalog：
- 时间/日期处理
- HTTP 请求 / API 调用
- 数据格式化、转换、序列化
- 权限验证、登录认证
- 文件/存储操作
- 任何看起来"项目可能已经封装过"的通用操作

**流程**：
1. 读取 `.claude/api-catalog/_index.md`（如果存在）
2. 根据当前任务判断需要哪个分类
3. **只读取相关的分类文件**（如只需要时间相关，只读 `time.md`，不读其他）
4. 使用 catalog 中记录的函数，而非重新实现或使用原生方法
5. 若 catalog 中没有相关记录，正常编码即可，无需提示用户

**重要**：如果 `.claude/api-catalog/` 目录不存在，说明项目尚未建立索引，跳过查阅步骤，不要报错或提示用户创建。

## 自动归类规则

1. 读取 `_index.md` 中已有分类列表
2. 判断函数用途是否匹配某个已有分类
3. 若匹配，归入该分类
4. 若不匹配任何已有分类，创建新分类（英文小写 kebab-case 命名，如 `date-time.md`、`http-request.md`）
5. 若函数可归入多个分类，放入最主要的分类，在其他相关分类中添加交叉引用行：`> 另见 [time.md](time.md) 中的 getServerTime`

## 记录格式

每条函数记录采用以下精简格式：

```markdown
### `functionName(param1, param2)`
**用途**：一句话说明这个函数做什么
**示例**：`const result = functionName('arg1', 'arg2')`
**注意**：需要强调的使用约束或替代说明（如"用这个代替原生 Date()"）
**位置**：`src/utils/time.ts:42`
```

- **用途**：必填，一句话
- **示例**：必填，最简调用示例
- **注意**：可选，只在有特别需要强调时写（如"替代原生方法"、"需要先初始化"等）
- **位置**：必填，文件路径:行号

详细格式规范参见 `references/data-format-spec.md`。
