# 报表平台POC系统要求

## 1. 项目概述

- **项目名称**: 监管报表平台 (Regulatory Reporting Platform)
- **项目类型**: Web应用程序 (POC验证)
- **核心功能**: 监管报表生成、数据可视化、生成状态监控
- **目标用户**: 监管部门/合规团队 (10-50人)

## 2. 技术栈

| 层级 | 技术选型 |
|------|----------|
| 后端 | Python FastAPI |
| 前端 | Angular 17+ + Angular Material |
| 数据库 | PostgreSQL |
| 部署 | Docker + Docker Compose |
| 任务调度 | APScheduler |

## 3. 系统架构

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Frontend  │────▶│   Backend   │────▶│  Database   │
│   (Angular) │◀────│  (FastAPI)  │◀────│ (PostgreSQL)│
└─────────────┘     └─────────────┘     └─────────────┘
                           │
                           ▼
                    ┌─────────────┐
                    │  Task Queue │
                    │ (APScheduler)│
                    └─────────────┘
```

## 4. 功能模块

### 4.1 数据源管理
- 配置PostgreSQL数据库连接信息
- 测试数据库连接
- 管理SQL查询模板
- 支持参数化SQL查询

### 4.2 报表模板管理
- 创建/编辑/删除报表模板
- 定义报表字段映射
- 配置数据聚合规则
- 模板版本管理

### 4.3 报表生成
- 手动触发报表生成
- 定时任务调度 (CRON表达式)
- 异步任务执行
- 生成进度跟踪
- 支持导出Excel/PDF格式

### 4.4 状态监控
- 实时任务状态展示 (待执行/执行中/成功/失败)
- 执行日志查询
- 任务耗时统计
- 失败重试机制
- 告警通知 (可选: 邮件/Webhook)

### 4.5 数据可视化
- 折线图 (趋势分析)
- 柱状图 (对比分析)
- 饼图 (占比分析)
- 数据表格 (分页/排序/筛选)

### 4.6 用户管理 (可选)
- 用户注册/登录
- 角色权限控制
- 操作日志审计

## 5. 数据库设计

### 5.1 核心表结构

```sql
-- 数据源配置
CREATE TABLE data_sources (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    host VARCHAR(255) NOT NULL,
    port INTEGER NOT NULL,
    database VARCHAR(100) NOT NULL,
    username VARCHAR(100) NOT NULL,
    password VARCHAR(255) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 报表模板
CREATE TABLE report_templates (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    description TEXT,
    sql_query TEXT NOT NULL,
    data_source_id INTEGER REFERENCES data_sources(id),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 报表任务
CREATE TABLE report_tasks (
    id SERIAL PRIMARY KEY,
    template_id INTEGER REFERENCES report_templates(id),
    status VARCHAR(20) NOT NULL DEFAULT 'pending',
    started_at TIMESTAMP,
    completed_at TIMESTAMP,
    error_message TEXT,
    result_file_path VARCHAR(500),
    created_by VARCHAR(100),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 定时任务配置
CREATE TABLE scheduled_tasks (
    id SERIAL PRIMARY KEY,
    template_id INTEGER REFERENCES report_templates(id),
    cron_expression VARCHAR(100) NOT NULL,
    is_active BOOLEAN DEFAULT TRUE,
    last_run_at TIMESTAMP,
    next_run_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## 6. API设计

### 6.1 数据源API
| 方法 | 路径 | 描述 |
|------|------|------|
| GET | /api/datasources | 获取所有数据源 |
| POST | /api/datasources | 创建数据源 |
| GET | /api/datasources/{id} | 获取数据源详情 |
| PUT | /api/datasources/{id} | 更新数据源 |
| DELETE | /api/datasources/{id} | 删除数据源 |
| POST | /api/datasources/{id}/test | 测试连接 |

### 6.2 报表模板API
| 方法 | 路径 | 描述 |
|------|------|------|
| GET | /api/templates | 获取所有模板 |
| POST | /api/templates | 创建模板 |
| GET | /api/templates/{id} | 获取模板详情 |
| PUT | /api/templates/{id} | 更新模板 |
| DELETE | /api/templates/{id} | 删除模板 |
| POST | /api/templates/{id}/run | 执行报表生成 |

### 6.3 任务监控API
| 方法 | 路径 | 描述 |
|------|------|------|
| GET | /api/tasks | 获取任务列表 |
| GET | /api/tasks/{id} | 获取任务详情 |
| GET | /api/tasks/{id}/logs | 获取任务日志 |

### 6.4 定时任务API
| 方法 | 路径 | 描述 |
|------|------|------|
| GET | /api/schedules | 获取定时任务列表 |
| POST | /api/schedules | 创建定时任务 |
| PUT | /api/schedules/{id} | 更新定时任务 |
| DELETE | /api/schedules/{id} | 删除定时任务 |

## 7. 前端页面设计

### 7.1 页面结构
```
- 仪表盘 (Dashboard)
  - 任务统计概览
  - 最近执行任务列表
  - 快捷操作入口

- 数据源管理
  - 数据源列表页
  - 数据源配置表单

- 报表模板
  - 模板列表页
  - 模板编辑器
  - 模板预览

- 任务中心
  - 任务列表 (带筛选)
  - 任务详情/日志查看
  - 手动执行入口

- 定时任务
  - 定时任务列表
  - Cron表达式配置

- 可视化分析
  - 图表展示页面
  - 数据筛选器
  - 导出功能
```

## 8. 非功能需求

### 8.1 性能要求
- 页面加载时间 < 3秒
- 任务列表查询响应 < 1秒
- 支持100个并发任务

### 8.2 可用性
- 系统可用性目标: 99.5%
- 支持7x24小时运行

### 8.3 安全性
- 敏感数据加密存储
- SQL注入防护
- 请求频率限制

### 8.4 扩展性
- 模块化设计
- 支持水平扩展
- 插件化报表引擎

## 9. 部署要求

### 9.1 Docker Compose配置
- PostgreSQL服务
- Backend API服务
- Frontend静态服务
- Nginx反向代理 (可选)

### 9.2 环境变量
```
POSTGRES_HOST=db
POSTGRES_PORT=5432
POSTGRES_USER=postgres
POSTGRES_PASSWORD=your_password
POSTGRES_DB=reporting_platform

BACKEND_HOST=0.0.0.0
BACKEND_PORT=8000
```

## 10. 开发规范

### 10.1 代码风格
- Python: PEP 8 + Black格式化
- TypeScript: ESLint + Prettier
- Angular: 遵循官方风格指南

### 10.2 Git提交规范
```
feat: 新功能
fix: 修复bug
docs: 文档更新
refactor: 代码重构
test: 测试相关
chore: 构建/工具
```

### 10.3 命名规范
- 数据库表: snake_case (小写下划线)
- API路径: kebab-case (小写连字符)
- 变量/函数: camelCase
- 组件/类: PascalCase