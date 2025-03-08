# Fixture-Cal

## 项目简介
Fixture-Cal 是一个基于 Python Flask 框架开发的体育赛事 iCal 翻译与管理服务。它支持导入在线 iCal 日程（.ics 文件），保持订阅并自动翻译日程内容，同时提供一个新的 `webcal://` 订阅链接，供 iOS、Google 日历等客户端使用。源 iCal 内容更新后，翻译后的 iCal 也会同步更新，订阅刷新周期为 48 小时。

## 功能特点
- **导入在线 iCal 日程**：支持 `http://` 或 `webcal://` 格式的 `.ics` 文件。
- **自动翻译日程内容**：
  - 支持多种翻译服务，包括 DeepSeek（默认）、Google Translate API、Deepl API 以及语言大模型。
  - 采用词典匹配方式，确保相同词汇仅翻译一次，减少翻译成本并提高一致性。
  - 任何语言的词先入库，语言大模型仅执行一次翻译，后续匹配替换。
- **生成新的 iCal 订阅链接**：提供 `webcal://` 链接，供用户订阅。
- **保持自动同步**：源 iCal 变更后，翻译后的 iCal 也自动更新。
- **支持 Docker 部署**：可轻松打包并运行在任何支持 Docker 的环境中。
- **前端功能设计**：
  - 页面上方展示网站标题及功能介绍。
  - **输入框**：
    - 填入 `.ics` 链接时，查询数据库是否已存在，若不存在则入库并执行后续翻译、订阅等操作。
    - 直接输入文字时，搜索已存在的 iCal 标题，并展示匹配结果。
  - **最新添加的 iCal 项目**：以图标 + iCal 标题的形式排列。

## 技术栈
- **后端**：Python 3 + Flask
- **数据库**：SQLite / PostgreSQL（可选）
- **任务调度**：Celery + Redis（用于定期拉取最新 iCal 数据并翻译）
- **翻译引擎**：DeepSeek（默认）、Google Translate API、Deepl API、语言大模型（可扩展）
- **容器化**：Docker + Docker Compose
- **前端**：Vue.js / React（可选）

## 项目文件结构
```
fixture-ical/
│── backend/                 # 后端 Flask 代码
│   │── app.py               # Flask 主应用入口
│   │── config.py            # 配置文件
│   │── database.py          # 数据库管理
│   │── routes.py            # API 路由
│   │── tasks.py             # Celery 任务
│   └── translation.py       # 语言翻译逻辑
│
│── frontend/                # 前端 React 代码
│   │── components/          # 组件
│   │── pages/               # 页面
│   │── App.jsx              # React 主应用入口
│   └── index.js             # 前端入口文件
│
│── docker-compose.yml       # Docker Compose 配置
│── Dockerfile               # 后端 Docker 配置
│── .env                     # 环境变量配置
│── requirements.txt         # Python 依赖
│── package.json             # 前端依赖
│── README.md                # 项目文档
```

## 安装与运行
### 1. 克隆仓库
```sh
git clone https://github.com/your-repo/fixture-ical.git
cd fixture-ical
```

### 2. 配置环境变量
创建 `.env` 文件，并配置以下变量：
```env
FLASK_ENV=production
REDIS_URL=redis://localhost:6379/0
DATABASE_URL=sqlite:///database.db
TRANSLATE_API_KEY=your-api-key
```

### 3. 运行 Docker 容器
```sh
docker-compose up -d --build
```

### 4. 访问 API
应用启动后，访问 `http://localhost:5000` 查看服务状态。

## API 设计
- `POST /import`：导入 iCal 订阅 URL
- `GET /calendar/{id}.ics`：获取翻译后的 iCal 订阅链接
- `GET /status`：检查服务状态
- `POST /translate/dictionary`：管理翻译词典
- `GET /translate/dictionary`：获取已存入的翻译词典
- `GET /search?q={query}`：搜索已有 iCal 订阅
- `GET /latest`：获取最新添加的 iCal 项目

## 订阅刷新周期
- 默认每 48 小时同步一次源 iCal 内容。
- 采用 Celery 定时任务来执行更新。

## 未来扩展
- **多语言支持**：支持更多语言翻译。
- **用户管理**：提供 API 认证功能。
- **定制化翻译**：允许用户调整翻译规则。
- **更多赛事支持**：添加特定赛事的优化处理。

## 许可证
本项目基于 MIT 许可证开源，欢迎贡献！

