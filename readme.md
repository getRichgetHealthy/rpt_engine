# 监管报表平台

> 监管报表平台 POC - 快速验证版本

## 项目介绍

监管报表平台是一款面向监管部门/合规团队的报表生成与数据可视化系统。平台支持从PostgreSQL数据库提取数据，通过配置化的报表模板生成各类监管报表，并提供实时任务监控与数据可视化分析功能。

## 技术栈

| 组件 | 技术 |
|------|------|
| 后端 | Python FastAPI |
| 前端 | Angular 17 + Angular Material |
| 数据库 | PostgreSQL |
| 任务调度 | APScheduler |
| 部署 | Docker Compose |

## 功能特性

- **数据源管理** - 配置和管理PostgreSQL数据库连接
- **报表模板** - 可视化创建SQL查询模板
- **报表生成** - 手动/定时触发报表生成，支持Excel/PDF导出
- **任务监控** - 实时查看任务执行状态和日志
- **数据可视化** - 折线图、柱状图、饼图等图表展示
- **定时任务** - 基于CRON表达式的自动化报表生成

## 快速开始

### 前置要求

- Docker Desktop (Windows/Mac/Linux)
- 至少 4GB 可用内存

### 启动服务

```bash
# 启动所有服务
docker-compose up -d

# 查看服务状态
docker-compose ps
```

服务启动后访问：
- 前端: http://localhost:4200
- 后端API: http://localhost:8000
- API文档: http://localhost:8000/docs

### 默认配置

| 服务 | 地址 | 账号 |
|------|------|------|
| PostgreSQL | localhost:5432 | postgres / postgres |
| 后端API | localhost:8000 | - |
| 前端 | localhost:4200 | - |

## 使用指南

### 1. 添加数据源

1. 进入「数据源管理」页面
2. 点击「添加数据源」
3. 填写数据库连接信息（主机、端口、数据库名、用户名、密码）
4. 点击「测试连接」验证配置
5. 保存数据源

### 2. 创建报表模板

1. 进入「报表模板」页面
2. 点击「新建模板」
3. 选择数据源
4. 编写SQL查询语句
5. 配置字段映射和展示格式
6. 保存模板

### 3. 生成报表

**手动生成：**
- 在模板列表页面点击「运行」按钮
- 或进入模板详情页点击「执行报表」

**定时生成：**
- 在「定时任务」页面创建定时任务
- 配置CRON表达式设置执行时间

### 4. 查看任务状态

1. 进入「任务中心」页面
2. 查看所有报表生成任务
3. 点击任务查看详情和执行日志
4. 可下载生成的报表文件

### 5. 数据可视化

1. 进入「可视化分析」页面
2. 选择需要分析的模板
3. 选择图表类型（折线图/柱状图/饼图）
4. 配置数据筛选条件
5. 查看可视化结果

## CRON表达式说明

| 表达式 | 含义 |
|--------|------|
| `0 0 * * *` | 每天午夜执行 |
| `0 0 * * 0` | 每周日执行 |
| `0 0 1 * *` | 每月1日执行 |
| `0 */6 * * *` | 每6小时执行 |

## 项目结构

```
regpoc/
├── backend/           # Python FastAPI后端
│   ├── app/
│   │   ├── main.py   # 应用入口
│   │   ├── models/   # 数据模型
│   │   ├── routers/  # API路由
│   │   └── services/ # 业务逻辑
│   └── requirements.txt
│
├── frontend/          # Angular前端
│   ├── src/
│   │   ├── app/      # 组件和模块
│   │   └── assets/   # 静态资源
│   └── angular.json
│
├── docker-compose.yml # Docker编排配置
├── .env              # 环境变量配置
└── README.md         # 本文件
```

## 常见问题

### Q: 如何修改数据库连接配置？
A: 编辑 `.env` 文件中的POSTGRES相关配置，然后重启服务。

### Q: 报表生成失败怎么办？
A: 进入任务中心查看错误日志，常见原因包括：SQL语法错误、数据源连接失败、权限不足等。

### Q: 如何扩展新的图表类型？
A: 前端使用ngx-charts组件库，可在可视化模块中添加新的图表组件。

## 开发说明

### 后端开发

```bash
cd backend
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
pip install -r requirements.txt
uvicorn app.main:app --reload
```

### 前端开发

```bash
cd frontend
npm install
ng serve
```

## License

MIT License