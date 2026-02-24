扫描当前项目的工具函数和 API 封装，自动分类并生成 api-catalog 索引。通过 subagent 执行，不影响主上下文。

流程：
1. 使用 Task tool 启动 general-purpose subagent，传递以下完整指令

subagent 指令：
```
扫描当前项目的工具函数和 API 封装，生成分类索引。

1. 检测项目类型（根据 package.json、pom.xml、go.mod 等判断语言/框架）
2. 扫描以下目录（存在则扫，不存在则跳过）：
   - 高优先级：src/utils/、src/helpers/、src/lib/、src/common/、src/shared/、src/tools/、utils/、lib/、common/
   - 中优先级：src/services/、src/api/、src/request/、src/http/
   - 低优先级：src/hooks/、src/composables/、src/stores/
3. 识别导出的函数/类/常量，特别关注：
   - 文件名含 util/helper/service/api/wrapper 的
   - 被多文件 import 的
   - 对原生 API 或第三方库的封装
4. 排除：node_modules/、测试文件（*.test.*、*.spec.*）、构建产物（dist/、build/）、*.d.ts
5. 按函数用途自动分类（根据目录名、函数名前缀、import 来源推断）
6. 若 .claude/api-catalog/ 已存在，与已有记录合并，不重复添加
7. 生成/更新各分类 .md 文件和 _index.md（表格格式：| 分类 | 说明 | 数量 |）
8. 每个函数条目格式：
   ### `函数签名`
   **用途**：一句话
   **示例**：最简调用
   **注意**：特殊约束（可选）
   **位置**：文件路径:行号
9. 返回扫描结果摘要：共发现 N 个函数，归入 M 个分类，列出各分类名和数量
```

2. 将 subagent 返回的扫描结果展示给用户
