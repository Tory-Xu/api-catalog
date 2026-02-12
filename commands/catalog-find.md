从项目的 api-catalog 中按需查找函数。

流程：
1. 读取 .claude/api-catalog/_index.md
2. 若目录不存在，提示用户先运行 /catalog-scan 建立索引
3. 若 $ARGUMENTS 不为空：根据关键词匹配相关分类，只读取匹配的分类文件，列出其中的函数清单
4. 若 $ARGUMENTS 为空：显示所有分类的概览（只展示 _index.md 内容，不加载任何分类详情）
5. 绝不一次性加载所有分类文件
