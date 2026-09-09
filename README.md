# 健身器材预约管理系统 - 前端

基于 Vue3 + Vite + Element-Plus + Pinia + Vue-Router + Axios 构建的健身器材预约管理系统前端。

## 技术栈

| 技术 | 版本 | 说明 |
|------|------|------|
| Vue | ^3.3.11 | 渐进式 JavaScript 框架 |
| Vite | ^5.0.8 | 下一代前端构建工具 |
| Element-Plus | ^2.4.4 | Vue3 组件库 |
| Vue-Router | ^4.2.5 | 官方路由管理器 |
| Pinia | ^2.1.7 | 新一代状态管理 |
| Axios | ^1.6.2 | HTTP 请求库 |

## 项目结构

```
fitness-reservation-frontend/
├── index.html                  # 入口 HTML
├── package.json                # 依赖配置
├── vite.config.js              # Vite 配置（含代理）
├── .gitignore
└── src/
    ├── main.js                 # 应用入口
    ├── App.vue                 # 根组件
    ├── api/                    # 接口层
    │   ├── equipment.js        # 器材相关接口
    │   └── reservation.js      # 预约相关接口
    ├── assets/
    │   └── styles/
    │       └── global.css      # 全局样式
    ├── layout/
    │   └── Layout.vue          # 主布局（侧边栏+顶栏）
    ├── router/
    │   └── index.js            # 路由配置 + 守卫
    ├── store/
    │   └── user.js             # 用户状态管理（Pinia）
    ├── utils/
    │   └── request.js          # Axios 封装（拦截器）
    └── views/
        ├── Login.vue           # 登录页
        ├── Equipment.vue       # 器材管理页（列表+增删改+预约）
        ├── MyReservation.vue   # 我的预约页
        └── ReservationAudit.vue# 预约审核页（管理员）
```

## 功能模块

### 1. 登录页
- 模拟登录（后端暂无用户接口，前端本地校验）
- 管理员账号：`admin` / `admin123`
- 普通用户账号：`user` / `user123`
- 登录信息持久化到 localStorage

### 2. 器材管理
- 器材列表展示（分页）
- 器材状态标签（空闲/借出/维修）
- 关键字搜索
- **管理员**：新增、编辑、删除器材
- **所有用户**：在线预约器材

### 3. 我的预约
- 当前用户的预约记录
- 状态统计卡片（总数/待审核/已通过/已拒绝）
- 按状态筛选
- 取消待审核的预约

### 4. 预约审核（仅管理员）
- 全部预约记录管理
- 状态统计概览
- 按状态筛选（全部/待审核/已通过/已拒绝/已取消）
- 审核通过 / 审核拒绝操作

## 权限设计

| 功能 | 管理员 | 普通用户 |
|------|--------|----------|
| 器材列表查看 | ✅ | ✅ |
| 器材新增/编辑/删除 | ✅ | ❌ |
| 提交预约 | ✅ | ✅ |
| 我的预约查看/取消 | ✅ | ✅ |
| 预约审核（通过/拒绝） | ✅ | ❌ |

## 快速开始

### 环境要求
- Node.js >= 16
- npm >= 8（或 pnpm / yarn）

### 安装依赖
```bash
npm install
```

### 启动开发服务器
```bash
npm run dev
```
启动后访问：http://localhost:5173

### 构建生产版本
```bash
npm run build
```

### 预览生产构建
```bash
npm run preview
```

## 后端接口配置

前端通过 Vite 代理将 `/api` 前缀的请求转发到后端：

```js
// vite.config.js
proxy: {
  '/api': {
    target: 'http://localhost:8080',  // 后端地址
    changeOrigin: true,
    rewrite: (path) => path.replace(/^\/api/, '')
  }
}
```

如需修改后端地址，编辑 `vite.config.js` 中的 `target` 即可。

### 接口列表

**器材接口**
- `GET /equipment/list` - 查询全部器材
- `GET /equipment/{id}` - 查询单个器材
- `GET /equipment/page?pageNum=1&pageSize=5` - 分页查询
- `POST /equipment` - 新增器材
- `PUT /equipment` - 修改器材
- `DELETE /equipment/{id}` - 删除器材

**预约接口**
- `POST /reservation` - 提交预约
- `PUT /reservation/cancel/{id}` - 取消预约
- `PUT /reservation/pass/{id}` - 审核通过
- `PUT /reservation/reject/{id}` - 审核拒绝
- `GET /reservation/list` - 查询全部预约
- `GET /reservation/page` - 分页查询预约

## 数据模型

### 器材 equipment
| 字段 | 类型 | 说明 |
|------|------|------|
| id | Long | 主键 |
| name | String | 器材名称 |
| no | String | 器材编号 |
| status | Integer | 0-空闲 / 1-借出 / 2-维修 |
| description | String | 描述 |
| stock | Integer | 库存 |

### 预约 reservation
| 字段 | 类型 | 说明 |
|------|------|------|
| id | Long | 主键 |
| equipmentId | Long | 器材ID |
| userId | Long | 用户ID |
| startTime | DateTime | 开始时间 |
| endTime | DateTime | 结束时间 |
| status | Integer | 0-待审核 / 1-已通过 / 2-已拒绝 / 3-已取消 |
| remark | String | 备注 |

## 注意事项

1. **模拟登录**：由于后端未提供用户登录接口，登录采用前端模拟方式，用户信息存储在 localStorage 中。接入真实后端后，只需修改 `src/store/user.js` 中的 `login` 方法即可。
2. **用户ID**：预约提交时 `userId` 使用当前登录用户的模拟 ID（管理员=1，普通用户=2）。
3. **响应格式兼容**：Axios 拦截器已兼容多种后端返回格式（`{code, data, msg}`、直接返回数据、分页 `{records, total}` / `{list, total}`）。
4. **跨域**：开发环境通过 Vite 代理解决跨域，生产环境建议使用 Nginx 反向代理。

## License

MIT
