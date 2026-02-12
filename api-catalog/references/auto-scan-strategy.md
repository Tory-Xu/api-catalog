# 自动扫描策略

## 扫描目标目录

按优先级依次扫描以下目录（存在则扫，不存在则跳过）：

**高优先级**（专门的工具/通用目录）：
- `src/utils/`、`src/util/`
- `src/helpers/`、`src/helper/`
- `src/lib/`、`src/libs/`
- `src/common/`、`src/shared/`
- `src/tools/`
- `utils/`、`lib/`、`common/`（无 src 前缀的项目）

**中优先级**（服务/API 封装）：
- `src/services/`、`src/service/`
- `src/api/`、`src/apis/`
- `src/request/`、`src/http/`

**低优先级**（可能包含可复用组件）：
- `src/hooks/`（自定义 hooks）
- `src/composables/`（Vue composables）
- `src/stores/`（状态管理）
- `src/plugins/`

## 识别规则

一个函数/方法值得记录，需满足以下**至少一条**：

1. **被导出**：使用 `export function`、`export const`、`export default`、`module.exports`
2. **被多处引用**：被 2 个以上文件 import
3. **是通用封装**：对原生 API 或第三方库的封装（如封装 axios、封装 dayjs）
4. **文件名暗示通用性**：文件名含 `util`、`helper`、`service`、`api`、`request`、`common`、`shared`、`wrapper`、`adapter`
5. **有替代原生方法的意图**：注释或命名暗示"用这个代替 xxx"

## 排除规则

以下内容**不记录**：

- `node_modules/`、`vendor/` 下的任何内容
- 测试文件：`*.test.*`、`*.spec.*`、`__tests__/`、`__mocks__/`
- 构建产物：`dist/`、`build/`、`.next/`、`.nuxt/`、`out/`
- 类型定义文件：`*.d.ts`（但 `.ts` 中导出的类型工具可以记录）
- 配置文件：`*.config.*`、`.rc` 文件
- 纯组件文件（除非是通用 UI 组件库）
- 已废弃/标记为 deprecated 的函数

## 分类启发式

按以下优先级判断函数所属分类：

### 1. 目录名直接映射
| 目录名 | 分类 |
|--------|------|
| `time/`、`date/` | time |
| `http/`、`request/`、`api/`、`fetch/` | http |
| `format/`、`formatter/` | format |
| `auth/`、`permission/` | auth |
| `storage/`、`cache/` | storage |
| `validate/`、`validator/` | validation |
| `event/`、`emitter/` | event |

### 2. 函数名前缀推断
| 前缀模式 | 分类 |
|----------|------|
| `get/set/format + Time/Date/Day/Hour` | time |
| `fetch/get/post/put/delete + Api/Data/Request` | http |
| `format + Number/Currency/Phone/Percent` | format |
| `check/has/is + Auth/Permission/Login/Role` | auth |
| `get/set/remove + Storage/Cache/Cookie/Local` | storage |
| `validate/check/is + Email/Phone/Required` | validation |

### 3. Import 来源推断
| 引用的库 | 分类 |
|----------|------|
| `axios`、`fetch`、`ky`、`got` | http |
| `dayjs`、`moment`、`date-fns` | time |
| `lodash`、`ramda` | 根据具体函数再分 |
| `crypto`、`bcrypt` | crypto |

### 4. 无法判断时
- 创建一个 `misc` 分类暂存
- 在 `_index.md` 的 misc 说明中标注"待用户确认分类"

## 扫描流程

1. **检测项目类型**：根据 `package.json`、`pom.xml`、`go.mod` 等判断语言/框架
2. **定位目标目录**：按上述优先级列表，检查哪些目录存在
3. **遍历目标文件**：读取每个文件，提取导出的函数/类/常量
4. **应用排除规则**：过滤掉不需要记录的内容
5. **分类**：按启发式规则归类
6. **去重检查**：若已有 catalog，比对避免重复
7. **写入**：生成/更新分类文件和 `_index.md`

## 扫描输出

扫描完成后向用户汇报：

```
扫描完成！共发现 {N} 个工具函数，归入 {M} 个分类：
- 时间工具 (4)：getServerTime, formatDate, ...
- 网络请求 (8)：request, get, post, ...
- ...

索引已保存至 .claude/api-catalog/
```

若扫描未发现任何工具函数，告知用户项目中未找到明显的通用工具函数目录，建议手动记录。
