
# 🎨 HCF Frontend — React 前端界面

基于 React + Vite + TypeScript + Tailwind CSS 构建的 AI 对话前端界面。


## 🗂️ 文件说明

```
frontend/
├── src/                    # 源码目录
│   ├── components/         # React 组件
│   ├── App.tsx             # 主应用组件
│   ├── main.tsx            # 入口文件
│   └── index.css           # 全局样式
├── public/                 # 静态资源
├── .env.example            # 环境变量模板
├── package.json            # 依赖配置
├── vite.config.js          # Vite 配置
├── tailwind.config.js      # Tailwind CSS 配置
└── README.md               # 本文件
```


## 🚀 快速启动

### 1. 安装依赖

```bash
cd frontend
npm install
```

### 2. 配置环境变量

```bash
# 复制环境变量模板
cp .env.example .env

# 编辑 .env（如果后端地址不是默认的 localhost:8000）
nano .env
```

`.env` 内容：

```env
VITE_API_BASE_URL=http://localhost:8000
```

### 3. 启动开发服务器

```bash
npm run dev
```



## 🏗️ 构建生产版本

```bash
# 构建
npm run build

# 产物在 dist/ 目录
# 可用 Nginx、Vercel、Netlify 等部署
```



## 📦 技术栈

| 技术 | 用途 |
|------|------|
| React 18 | UI 框架 |
| TypeScript | 类型安全 |
| Vite | 构建工具 |
| Tailwind CSS | 样式方案 |
| ESLint | 代码规范 |


## 🔧 环境变量说明

| 变量名 | 说明 | 默认值 |
|--------|------|--------|
| `VITE_API_BASE_URL` | 后端 API 地址 | `http://localhost:8000` |

修改 `.env` 后需重启开发服务器才能生效。


## 📝 开发说明

### 项目脚本

```bash
npm run dev        # 启动开发服务器
npm run build      # 构建生产版本
npm run preview    # 预览生产构建
npm run lint       # 运行 ESLint 检查
```

### 与后端通信

前端通过 `fetch` 调用后端 API，基础地址由 `VITE_API_BASE_URL` 环境变量配置：

```typescript
const API_BASE = import.meta.env.VITE_API_BASE_URL || 'http://localhost:8000';

// 创建会话
const { session_id } = await fetch(`${API_BASE}/new_session`, {
  method: 'POST'
}).then(res => res.json());

// 流式对话
const response = await fetch(`${API_BASE}/chat_stream`, {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ message: '你好', session_id })
});

// 读取 SSE 流...
```

