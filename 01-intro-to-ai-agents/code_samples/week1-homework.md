# 第 1 周作业 · 运行解读

> 和作业 notebook `01-assignment-你的主题.ipynb` 一起放在本目录。直接在本文件里填，填完一起 add / commit / push。提交前看一眼 `git diff`，确认里面没有 `sk-`。

## 一、演示观察三条

课上跑 `01-python-langchain-agent.ipynb` 时：

1. 完全没看懂的一步（智能体当时在干什么？）：当模型输出 tool_calls 时，我一开始不太理解为什么模型没有直接回答用户，而是先调用工具。
后来理解这是智能体根据用户的问题判断需要使用外部工具获取信息，再把工具返回的结果整理成最终回答。
2. 觉得可疑的一步（为什么可疑？）：工具调用的参数来源让我觉得比较可疑。例如智能体调用工具时传入的参数可能是模型自己生成的，如果模型理解错误，可能会调用错误的工具或者传入错误的参数。
3. 最想问的一个问题：如果一个智能体同时拥有多个功能相似的工具，它是如何判断应该调用哪一个工具的？如果模型选择错误，是否有机制可以发现并纠正？

## 二、接口检查输出（原样粘贴）

在仓库根目录运行 `python scripts/check_endpoint.py`，把完整输出贴在下面：
```
$ python scripts/check_endpoint.py --path 01-intro-to-ai-agents/code_samples/01-assignment-study-planner.ipynb
/mnt/c/Users/21808/pyt-aiae202-template/.venv/lib/python3.12/site-packages/nbformat/validator.py:434: MissingIDFieldWarning: Cell is missing an id field, this will become a hard error in future nbformat versions. You may want to use `normalize()` on your notebooks before validations (available since nbformat 5.1.4). Previous versions of nbformat are fixing this issue transparently, and will stop doing so in the future.
  _validate(nbdict, ref, version, version_minor, relax_add_props)
[IPKernelApp] WARNING | Kernel is running over TCP without encryption. All communication (including code and outputs) is sent in plain text and is susceptible to eavesdropping. Use IPC transport or launch with kernel manager-provisioned CurveZMQ keys to enable transport encryption.
PASS  01-intro-to-ai-agents/code_samples/01-assignment-study-planner.ipynb  (160s)

1/1 notebooks passed

```

## 三、危险工具判断

课上三个工具 `get_weather`、`send_confirmation`、`query_orders` 里，危险的是哪一个？为什么？怎么改才安全？（两三句即可）
我认为 `query_orders` 是最危险的。

因为它允许模型直接执行 SQL 语句，如果模型生成错误的 SQL，可能导致数据泄露、查询越权甚至破坏数据库。

更安全的方式是不让模型直接输入 SQL，而是提供固定功能的查询接口，例如只允许根据订单编号查询订单，并增加参数验证和权限控制。

`send_confirmation` 也存在一定风险，因为它会执行发送邮件这样的外部操作，可以在执行前增加人工确认步骤。