# Ctrip Assistant 项目文档

## 项目简介

Ctrip Assistant 是一个基于 FastAPI 和 LangGraph 的智能旅游助手系统，提供航班、酒店、租车和旅游景点的预订与管理功能。系统采用模块化设计，通过工作流引擎实现智能对话和业务处理。

## 项目结构

```
ctrip_assistant/
├── api/                 # API 接口层
│   ├── graph_api/       # 工作流调用接口
│   ├── system_mgt/      # 系统管理接口
│   ├── __init__.py
│   ├── routers.py       # 路由配置
│   └── schemas.py       # 数据模型
├── config/              # 配置文件
│   ├── __init__.py
│   ├── development.yml  # 开发环境配置
│   ├── log_config.py    # 日志配置
│   └── production.yml   # 生产环境配置
├── db/                  # 数据库操作
│   ├── system_mgt/      # 系统管理数据库操作
│   ├── __init__.py
│   └── dao.py           # 数据库访问对象
├── graph_chat/          # 工作流和智能助手
│   ├── __init__.py
│   ├── agent_assistant.py  # 代理助手
│   ├── assistant.py     # 主助手
│   ├── base_data_model.py  # 基础数据模型
│   ├── build_child_graph.py  # 构建子工作流
│   ├── draw_png.py      # 绘制工作流图
│   ├── entry_node.py    # 入口节点
│   ├── finally_graph.py # 最终工作流
│   ├── llm_tavily.py    # LLM 工具
│   ├── log_utils.py     # 日志工具
│   └── state.py         # 状态管理
├── static/              # 静态文件
├── tools/               # 工具函数
│   ├── __init__.py
│   ├── car_tools.py     # 租车工具
│   ├── flights_tools.py # 航班工具
│   ├── hotels_tools.py  # 酒店工具
│   ├── init_db.py       # 初始化数据库
│   ├── location_trans.py # 位置转换
│   ├── retriever_vector.py # 向量检索
│   ├── tools_handler.py # 工具处理
│   └── trip_tools.py    # 旅游工具
├── utils/               # 通用工具
│   ├── __init__.py
│   ├── cors.py          # CORS 配置
│   ├── dependencies.py  # 依赖注入
│   ├── docs_oauth2.py   # OAuth2 配置
│   ├── handler_error.py # 错误处理
│   ├── jwt_utils.py     # JWT 工具
│   ├── middlewares.py   # 中间件
│   └── password_hash.py # 密码哈希
├── main.py              # 主入口文件
├── order_faq.md         # 订单常见问题
├── travel2.sqlite       # 数据库文件
└── travel_new.sqlite    # 新数据库文件
```

## 核心功能

### 1. 用户管理
- 用户注册与登录
- 获取用户信息
- 用户认证与授权（基于 JWT）

### 2. 智能助手
- 航班预订与管理
- 酒店预订
- 租车服务
- 旅游景点预订
- 智能对话与工作流处理

### 3. 工具集成
- 航班信息查询与更新
- 酒店搜索与预订
- 租车服务
- 旅游景点推荐

## 技术栈

- **Web 框架**: FastAPI
- **工作流引擎**: LangGraph
- **数据库**: SQLite
- **身份认证**: JWT
- **依赖管理**: Python 标准库

## 安装与运行

### 环境要求
- Python 3.11+
- pip

### 安装步骤

1. 克隆项目到本地
   ```bash
   git clone <项目地址>
   cd ctrip_assistant
   ```

2. 安装依赖
   ```bash
   pip install -r requirements.txt
   ```

3. 配置环境变量
   - 编辑 `config/development.yml` 文件，配置数据库连接等信息

4. 运行项目
   ```bash
   python main.py
   ```

5. 访问接口文档
   - 浏览器打开：http://localhost:8000/docs

## API 接口

### 系统管理接口

- **POST /api/register/**: 用户注册
- **POST /api/login/**: 用户登录
- **GET /api/users/getUsers/**: 获取所有用户信息
- **GET /api/users/{pk}/**: 根据ID获取用户信息
- **PATCH /api/users/{pk}/**: 更新用户信息
- **POST /api/users/delete/**: 删除用户

### 工作流接口

- **POST /api/graph/**: 调用工作流，处理用户请求

## 工作流说明

系统采用 LangGraph 构建工作流，主要包含以下节点：

1. **fetch_user_info**: 获取用户信息
2. **primary_assistant**: 主助手，处理用户请求
3. **primary_assistant_tools**: 主助手工具，包含各种工具调用
4. **子工作流节点**:
   - **enter_update_flight**: 航班预订入口
   - **enter_book_car_rental**: 租车预订入口
   - **enter_book_hotel**: 酒店预订入口
   - **enter_book_excursion**: 旅游景点预订入口

工作流流程：
1. 获取用户信息
2. 主助手分析用户请求
3. 根据请求类型路由到相应的子工作流
4. 子工作流处理具体业务逻辑
5. 返回处理结果给用户

## 数据库设计

系统使用 SQLite 数据库，主要包含以下表：

- **tickets**: 机票信息
- **ticket_flights**: 机票与航班关联
- **flights**: 航班信息
- **boarding_passes**: 登机牌信息
- **users**: 用户信息

## 配置说明

配置文件位于 `config/` 目录，主要配置项包括：

- **LOG_LEVEL**: 日志级别
- **HOST**: 服务器主机
- **PORT**: 服务器端口
- **DATABASE**: 数据库配置
- **JWT_SECRET_KEY**: JWT 密钥
- **ALGORITHM**: JWT 算法
- **ACCESS_TOKEN_EXPIRE_MINUTES**: JWT 过期时间
- **WHITE_LIST**: 白名单路径
- **DEFAULT_PASSWORD**: 默认密码

## 示例使用

### 1. 用户注册

```bash
curl -X POST "http://localhost:8000/api/register/" \
  -H "Content-Type: application/json" \
  -d '{
    "username": "test",
    "password": "123123",
    "phone": "13800138000",
    "real_name": "测试用户"
  }'
```

### 2. 用户登录

```bash
curl -X POST "http://localhost:8000/api/login/" \
  -H "Content-Type: application/json" \
  -d '{
    "username": "test",
    "password": "123123"
  }'
```

### 3. 调用工作流

```bash
curl -X POST "http://localhost:8000/api/graph/" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <token>" \
  -d '{
    "user_input": "我想预订明天从北京到上海的航班",
    "config": {
      "passenger_id": "123456"
    }
  }'
```

## 注意事项

1. 系统使用 SQLite 数据库，默认数据库文件为 `travel_new.sqlite`
2. 首次运行时，需要确保数据库文件存在
3. 系统默认端口为 8000，可在配置文件中修改
4. 所有接口（除登录和注册外）需要 JWT 认证

## 扩展建议

1. 添加更多的旅游服务，如景点门票预订、旅游保险等
2. 集成第三方 API，获取实时航班、酒店信息
3. 优化工作流，提高智能助手的响应速度和准确性
4. 添加用户个性化推荐功能
5. 实现多语言支持

## 许可证

本项目采用 MIT 许可证。
