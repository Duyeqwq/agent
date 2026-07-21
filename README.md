# Hello Agents Learn

基于 **AutoGen + LangChain + Streamlit** 的多智能体软件开发实验项目：用多角色 Agent 协作完成「需求分析 → 代码生成 → 审查 → 执行验证」，并通过执行报错反馈形成自动迭代闭环。

## 项目亮点

- **多角色协作工作流**：基于 AutoGen，设计 Product Manager、Engineer、Code Reviewer、QA Engineer、User Proxy，完成需求分析、代码生成、审查与执行验证。
- **迭代反馈闭环**：通过 Tool Call 执行代码，并将 Traceback / 运行错误回传给 Developer Agent，形成「生成 → 执行 → 报错 → 修复 → 重试」的自动闭环。
- **知识检索增强**：基于 LangChain 实现文档切分、Embedding 存储与 Top-K 检索，为代码生成与错误修复提供项目知识上下文。
- **可视化界面**：用 Streamlit 展示 Agent 对话产出、任务状态、生成代码与执行结果。

## 目录结构

```text
hello-agents-learn/
├── chapter06/
│   ├── autogen/                 # 多智能体软件开发团队（核心 Demo）
│   │   ├── autogen_software_team.py
│   │   ├── output.py            # Agent 生成的 Streamlit 应用示例
│   │   └── requirments.txt
│   └── langchain/               # LangChain 助手示例
├── RAG/                         # 文档切分 / Embedding / Top-K 检索
├── tool/                        # 工具调用（计算、搜索、执行器）
├── chapter07/                   # 自研 Agent 框架练习（Simple / ReAct）
├── chapter01.py ~ chapter04/    # 基础实验与范式练习
├── test/                        # 测试脚本
├── .env.example                 # 环境变量模板（请勿提交真实密钥）
└── README.md
```

## 快速开始

### 1. 克隆仓库

```bash
git clone <your-repo-url>
cd hello-agents-learn
```

### 2. 创建虚拟环境并安装依赖

```bash
python -m venv .venv

# Windows
.venv\Scripts\activate

# macOS / Linux
source .venv/bin/activate

pip install -r chapter06/autogen/requirments.txt
pip install python-dotenv langchain langchain-openai
```

### 3. 配置环境变量

复制模板并填入你的密钥：

```bash
cp .env.example .env
```

`.env` 示例：

```env
LLM_MODEL_ID=deepseek-chat
LLM_API_KEY=your_api_key
LLM_BASE_URL=https://api.deepseek.com
```

> 请勿将 `.env` 提交到 GitHub。

### 4. 运行多智能体协作 Demo

```bash
python chapter06/autogen/autogen_software_team.py
```

运行后，团队会围绕示例任务（比特币价格 Streamlit 应用）自动协作；生成代码默认写入 `chapter06/autogen/output.py`。

查看生成的 Streamlit 应用：

```bash
streamlit run chapter06/autogen/output.py
```

## 核心流程

```text
User Proxy
    ↓ 提出需求
Product Manager
    ↓ 需求分析 / 验收标准
Engineer
    ↓ Tool Call: save_output_code()
Code Reviewer
    ↓ 静态审查通过
QA Engineer
    ↓ Tool Call: run_python_script()
    ├─ 失败 → 回传 Traceback → Engineer 修复 → 重试
    └─ 成功 → User Proxy 验收 → TERMINATE
```

调度使用 AutoGen 的 `SelectorGroupChat`，结合意图标签（如 `[NEED_REVISION]`、`[TASK_COMPLETE]`、`[NEXT=Role]`）做动态路由。

## 相关模块

| 模块 | 说明 | 入口 |
|------|------|------|
| AutoGen 软件团队 | 多角色协作开发与执行验证 | `chapter06/autogen/autogen_software_team.py` |
| LangChain 助手 | 对话 / 工具调用示例 | `chapter06/langchain/ai_assiant.py` |
| RAG | 文档问答、重排序检索 | `RAG/` |
| 自研 Agent | SimpleAgent / ReActAgent | `chapter07/` |
| 工具层 | 计算器、搜索、执行器 | `tool/` |

## 注意事项

- 本仓库用于课程学习与实验演示，部分脚本依赖外部 LLM API。
- 大模型权重、本地向量库、知识库 PDF 等体积较大的资源默认不纳入仓库。
- 提交前请确认没有把 API Key、数据库密码等敏感信息写进代码或 commit 历史。

## License

MIT
