扫描当前项目的工具函数和 API 封装，自动分类并生成 api-catalog 索引。

流程：
1. 检测项目类型（语言/框架）
2. 扫描以下目录（存在则扫）：src/utils/、src/helpers/、src/lib/、src/common/、src/shared/、src/services/、src/api/、utils/、lib/、common/
3. 识别导出的函数/类/常量，特别关注：文件名含 util/helper/service/api/wrapper 的、被多文件 import 的、对原生 API 或第三方库的封装
4. 排除：node_modules/、测试文件、构建产物、*.d.ts
5. 按函数用途自动分类（根据目录名、函数名前缀、import 来源推断）
6. 若 .claude/api-catalog/ 已存在，与已有记录合并，不重复添加
7. 生成/更新各分类 .md 文件和 _index.md
8. 汇报结果：共发现 N 个函数，归入 M 个分类，列出各分类名和数量
