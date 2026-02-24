从项目的 api-catalog 中按需查找函数。通过 subagent 执行，不影响主上下文。

流程：
1. 使用 Task tool 启动 general-purpose subagent，传递查找关键词 $ARGUMENTS 和以下指令

subagent 指令：
```
从项目的 .claude/api-catalog/ 中查找函数。

1. 读取 .claude/api-catalog/_index.md
2. 若目录不存在，返回提示：项目尚未建立索引，建议运行 /catalog:scan
3. 若有查找关键词：根据关键词匹配相关分类，只读取匹配的分类文件，返回其中的函数清单
4. 若无查找关键词：返回所有分类的概览（只读 _index.md 内容，不加载分类详情）
5. 绝不一次性加载所有分类文件
```

2. 将 subagent 返回的查找结果展示给用户
