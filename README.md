# 🐍 Vue Snake Game

一个使用 Vue.js 3 构建的完整贪吃蛇游戏，具有现代化的界面设计和完整的游戏功能。

## 🎮 游戏特性

- **完整的游戏逻辑**：移动、吃食物、碰撞检测、得分系统
- **现代化界面**：使用 CSS3 和渐变背景的美观设计
- **响应式设计**：适配不同屏幕尺寸
- **本地高分记录**：自动保存和显示历史最高分
- **游戏控制**：支持暂停/继续功能
- **多种操作方式**：支持 WASD 和方向键控制
- **平滑动画**：流畅的游戏体验

## 🚀 快速开始

### 环境要求

- Node.js (版本 16.0 或更高)
- npm 或 yarn 包管理器

### 安装步骤

1. **克隆项目**
   ```bash
   git clone https://github.com/mu4dian/testrepo.git
   cd testrepo
   ```

2. **安装依赖**
   ```bash
   npm install
   ```

3. **启动开发服务器**
   ```bash
   npm run dev
   ```

4. **在浏览器中打开**
   - 开发服务器启动后，在浏览器中访问 `http://localhost:5173`

## 🎯 游戏玩法

### 基本规则
- 控制蛇在游戏板上移动
- 吃红色食物来增长蛇的长度
- 每吃一个食物得到 10 分
- 避免撞到墙壁或蛇身
- 游戏结束后可以重新开始

### 操作控制
| 按键 | 功能 |
|------|------|
| ↑ 或 W | 向上移动 |
| ↓ 或 S | 向下移动 |
| ← 或 A | 向左移动 |
| → 或 D | 向右移动 |
| 空格 或 P | 暂停/继续 |

### 游戏界面
- **开始界面**：游戏介绍和开始按钮
- **游戏界面**：显示蛇、食物、得分和控制按钮
- **游戏结束界面**：显示最终得分和重新开始选项

## 🏗️ 项目结构

```
testrepo/
├── index.html              # 主 HTML 文件
├── package.json            # 项目配置和依赖
├── vite.config.js          # Vite 构建配置
├── src/
│   ├── main.js            # Vue 应用入口
│   └── App.vue            # 主要游戏组件
└── README.md              # 项目说明文档
```

## 🔧 开发说明

### 技术栈
- **Vue.js 3**：使用 Composition API 构建组件
- **Vite**：现代化的构建工具，提供快速的开发体验
- **Canvas API**：用于游戏渲染
- **CSS3**：现代化样式和动画效果

### 核心功能实现

#### 游戏逻辑
- **蛇的移动**：基于网格系统的位置更新
- **碰撞检测**：墙壁碰撞和自身碰撞检测
- **食物生成**：随机位置生成，避免与蛇身重叠
- **得分系统**：本地存储的高分记录

#### 状态管理
- 使用 Vue 3 的 Composition API 管理游戏状态
- 响应式数据绑定确保界面实时更新

#### 用户交互
- 键盘事件监听和处理
- 游戏暂停/继续功能
- 多种游戏状态切换

## 📦 构建和部署

### 构建生产版本
```bash
npm run build
```

### 预览构建版本
```bash
npm run preview
```

构建完成后，`dist` 文件夹包含可部署的静态文件。

## 🎨 自定义配置

### 游戏参数调整
在 `src/App.vue` 中可以修改以下参数：

```javascript
// 游戏常量
const canvasWidth = 400      // 游戏板宽度
const canvasHeight = 400     // 游戏板高度
const gridSize = 20          // 网格大小
```

### 游戏速度
修改游戏循环的间隔时间：
```javascript
gameLoop = setInterval(() => {
  // 游戏逻辑
}, 150) // 调整这个值来改变游戏速度
```

## 🤝 贡献指南

欢迎提交 Issue 和 Pull Request 来改进这个项目！

### 开发流程
1. Fork 项目
2. 创建特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 打开 Pull Request

## 📄 许可证

本项目采用 MIT 许可证。详见 LICENSE 文件。

## 📞 联系方式

如有问题或建议，请通过以下方式联系：

- 提交 [GitHub Issue](https://github.com/mu4dian/testrepo/issues)
- 项目维护者：mu4dian

---

⭐ 如果这个项目对你有帮助，请给它一个星标！