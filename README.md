# 实验室项目展示板

## 项目简介

这是一个 Vue 3 练习项目，用于展示实验室的项目信息。

通过这个项目，我学习了如何使用 Vue 3 开发前端应用，并使用 AI 辅助完成开发过程。

## 已实现功能

- [x] 展示项目列表（项目名称、简介、成员、技术栈、状态、GitHub 地址）
- [x] 新增项目（通过表单添加新项目）
- [x] 删除项目（带确认提示）
- [x] 搜索项目（根据项目名称实时过滤）
- [x] 数据持久化（使用 localStorage 保存，刷新页面数据不丢失）
- [x] 项目状态标签（进行中/已完成/暂停，不同颜色区分）

## 使用技术

- Vue 3
- Vite
- localStorage

## 如何安装

```bash
# 克隆项目
git clone https://github.com/Booku458/lab-project-board.git

# 进入项目目录
cd lab-project-board

# 安装依赖
npm install
```

## 如何运行

```bash
# 启动开发服务器
npm run dev
```

然后在浏览器打开：http://localhost:5173

## 项目截图

（这里可以添加项目截图）

## 我通过这个项目学习到了什么

1. **Vue 3 基础**
   - 组件化开发
   - 响应式数据（ref、computed）
   - 事件处理（@click、@submit）
   - 条件渲染（v-if）
   - 列表渲染（v-for）
   - 双向数据绑定（v-model）

2. **Vite 构建工具**
   - 项目初始化
   - 开发服务器
   - 热更新

3. **前端数据持久化**
   - localStorage 的使用
   - JSON 序列化与反序列化

4. **Git 版本控制**
   - 初始化仓库
   - 提交代码
   - 推送到 GitHub

5. **AI 辅助开发**
   - 如何使用 AI 工具辅助编程
   - 分阶段完成功能
   - 理解代码而不是直接复制

## 项目结构

```
lab-project-board/
├── src/
│   ├── App.vue          # 主组件
│   ├── main.js          # 入口文件
│   ├── style.css        # 全局样式
│   ├── components/      # 组件目录
│   └── assets/          # 资源文件
├── index.html           # HTML 入口
├── package.json         # 项目配置
└── vite.config.js       # Vite 配置
```

## 后续可以改进的地方

- [ ] 编辑项目功能
- [ ] 项目分类筛选
- [ ] 导出/导入数据
- [ ] 更美观的 UI 设计
