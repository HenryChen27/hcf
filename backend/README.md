

# 🔧 HCF Backend — AI 对话后端服务

基于 FastAPI 的 AI 智能体后端，整合风格化大语言模型与联网搜索能力。


## 🗂️ 文件说明

```
backend/
├── app.py                  # FastAPI 主应用
├── config.yaml.example     # 配置模板
├── requirements.txt        # Python 依赖
└── README.md               # 本文件
```


## 🚀 快速启动

### 1. 安装依赖

```bash
# 推荐使用 conda 创建独立环境
conda create -n hcf python=3.10
conda activate hcf

# 安装依赖
cd backend
pip install -r requirements.txt
```

### 2. 配置

```bash
# 复制配置模板
cp config.yaml.example config.yaml

# 编辑配置文件
nano config.yaml
```

`config.yaml` 内容：

```yaml
TAVILY_API_KEY: "tvly-你的密钥"
MODEL_PATH: "/path/to/MrHu/model"
CHECKER_PATH: "/path/to/Qwen2.5-1.5B-Instruct/model"
```

| 字段 | 说明 | 获取方式 |
|------|------|----------|
| `TAVILY_API_KEY` | Tavily 搜索 API 密钥 | [tavily.com](https://tavily.com/) 免费注册 |
| `MODEL_PATH` | MrHu 风格化模型路径 | [ModelScope](https://www.modelscope.cn/models/HenryChen66/MrHu) 下载 |
| `CHECKER_PATH` | 检查器模型路径 | ModelScope/HuggingFace 搜索 Qwen2.5-1.5B-Instruct |

### 3. 启动服务

```bash
# 开发模式（修改代码自动重启）
uvicorn app:app --host 0.0.0.0 --port 8000 --reload

# 生产模式（多 worker）
uvicorn app:app --host 0.0.0.0 --port 8000 --workers 4
```

启动成功后会打印：

```
正在加载 tokenizer ...
正在加载主模型 ...
正在加载 checker 模型 ...
模型加载完成。
```

---

## ✅ 测试后端是否正常运行


### curl 命令:

```bash
# 测试根路径
curl http://localhost:8000
# 预期输出: {"message":"backend is running"}

# 测试创建会话
curl -X POST http://localhost:8000/new_session
# 预期输出: {"session_id":"xxxx-xxxx-xxxx"}

# 测试对话（记下上一步返回的 session_id）
curl -X POST http://localhost:8000/chat \
  -H "Content-Type: application/json" \
  -d '{
    "message": "你好，请做一个自我介绍",
    "session_id": "你的session_id"
  }'
# 预期输出: {"session_id":"...", "answer":"...", "references":[...]}
```

---

## 📖 API 接口详情

### 基础信息

- **Base URL**: `http://localhost:8000`
- **Content-Type**: `application/json`
- **流式接口**: `text/event-stream` (SSE)

### GET / — 健康检查

返回服务运行状态。

**响应**：
```json
{"message": "backend is running"}
```

### POST /new_session — 创建新会话

**响应**：
```json
{"session_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890"}
```

### POST /clear_session — 清空会话历史

**请求体**：
```json
{"session_id": "your-session-id"}
```

**响应**：
```json
{"ok": true, "session_id": "your-session-id"}
```

### POST /chat — 非流式对话

**请求体**：
```json
{
  "message": "用户消息",
  "session_id": "可选，会话ID"
}
```

**响应**：
```json
{
  "session_id": "a1b2c3d4-...",
  "answer": "模型的完整回答",
  "references": [
    {
      "title": "参考资料标题",
      "content": "摘要内容",
      "url": "https://example.com"
    }
  ]
}
```

### POST /chat_stream — 流式对话

**请求体**：同 `/chat`

**SSE 事件类型**：

| 事件 | 说明 | 数据结构 |
|------|------|----------|
| `session` | 返回会话 ID | `{"event": "session", "session_id": "..."}` |
| `status` | 处理状态 | `{"event": "status", "message": "正在搜索..."}` |
| `references` | 参考资料 | `{"event": "references", "references": [...]}` |
| `text` | 文本片段 | `{"event": "text", "text": "片段内容"}` |
| `done` | 回答完成 | `{"event": "done", "answer": "完整回答"}` |
| `error` | 错误信息 | `{"event": "error", "error": "错误描述"}` |


## 🔧 常见问题

**Q: 启动时报 OOM（显存不足）？**
- 使用量化模型（AWQ/GPTQ 版本）
- 在 `AutoModelForCausalLM.from_pretrained()` 中添加 `load_in_8bit=True`
- 使用 `device_map="cpu"`（速度较慢但不会 OOM）

**Q: 检查器模型是否必需？**
- 不是必需的，但强烈推荐。它会判断是否需要搜索，减少不必要的 API 调用。若不需要检查器模型需手动修改`app.py`处代码。

**Q: 如何修改角色设定？**
- 编辑 `app.py` 中的 `system_prompt` 变量



