# Horse Racing Game - Code Wiki

## 1. 项目概述

### 1.1 项目简介
这是一个基于 JavaScript 的赛马游戏，玩家可以对马匹进行投注并观看比赛。每匹马都有独特的属性影响其比赛表现，使游戏具有不可预测性和趣味性。

### 1.2 技术栈
- **HTML5**: 网页结构
- **CSS3**: 样式与动画
- **JavaScript (ES5)**: 游戏核心逻辑
- **GitHub Pages**: 在线托管

### 1.3 项目结构
```
/workspace/
├── index.html      # 主HTML文件
├── game.css        # 样式表
├── game.js         # 游戏逻辑
├── images/         # 图片资源目录
│   ├── horse1.png ~ horse4.png  # 各马匹图片
│   ├── head0.png ~ head4.png    # 骑手头部图片
│   ├── body0.png ~ body4.png    # 骑手身体图片
│   ├── bg.png, mud.png, outer.png, inner.png  # 背景与跑道
│   └── tree.png                 # 装饰树
├── LICENSE
└── README.md
```

---

## 2. 整体架构

### 2.1 模块划分

| 模块 | 文件 | 职责 |
|------|------|------|
| 结构层 | index.html | 定义DOM结构，布局游戏界面元素 |
| 表现层 | game.css | 定义视觉样式、动画效果、布局 |
| 行为层 | game.js | 实现游戏逻辑、马匹移动、投注系统 |

### 2.2 页面布局
```
┌─────────────────────────────────────────────────────────┐
│  #horse1 (白马)      [顶部起点区域]                      │
│  #horse2 (蓝马)                                           │
│  #horse3 (绿马)      ┌─────────────────────────────┐     │
│  #horse4 (棕马)      │        .track               │     │
│                      │   ┌───────────────────┐     │     │
│                      │   │    .inner         │     │     │
│                      │   │  [投注面板] [结果] │     │     │
│                      │   └───────────────────┘     │     │
│                      └─────────────────────────────┘     │
└─────────────────────────────────────────────────────────┘
```

---

## 3. 核心模块详解

### 3.1 HTML结构 (index.html)

#### 主要DOM元素

| 元素ID | 类型 | 描述 |
|--------|------|------|
| `#horse1` ~ `#horse4` | div | 赛马容器，包含骑手(rider) |
| `.track` | div | 跑道外框 |
| `.inner` | div | 跑道内区，包含投注面板和结果表 |
| `#startline` | div | 起点线（黑白格子图案） |
| `#start` | button | 开始比赛按钮 |
| `#bet` | div | 投注区域 |
| `#funds` | span | 显示当前资金 |
| `#amount` | input | 投注金额输入框 |
| `#bethorse` | select | 选择投注的马匹 |
| `#num_lap` | input | 比赛圈数输入框 |
| `#results` | table | 比赛结果表格 |

### 3.2 JavaScript核心 (game.js)

#### 3.2.1 Horse构造函数

```javascript
function Horse(id, x, y)
```

**功能**: 创建马匹对象

**参数**:
- `id` (string): HTML元素ID，如 "horse1"
- `x` (number): 初始X坐标(vw单位)
- `y` (number): 初始Y坐标(vh单位)

**属性**:

| 属性名 | 类型 | 描述 |
|--------|------|------|
| `element` | HTMLElement | 马匹的DOM元素 |
| `speed` | number | 速度值(10-20之间的随机值) |
| `originX` | number | 初始X坐标 |
| `originY` | number | 初始Y坐标 |
| `x` | number | 当前X坐标 |
| `y` | number | 当前Y坐标 |
| `number` | number | 马匹编号(1-4) |
| `lap` | number | 当前已完成的圈数 |

#### 3.2.2 Horse原型方法

##### moveRight()
向右移动马匹，当到达拐点时切换为向下移动。

##### moveDown()
向下移动马匹，当到达下边界时切换为向左移动。

##### moveLeft()
向左移动马匹，当到达左边界时切换为向上移动。

##### moveUp()
向上移动马匹，当回到起点时:
- 增加`lap`计数
- 切换为向右移动
- 若达到指定圈数，调用`arrive()`

##### run()
启动马匹比赛，初始状态设置为`runRight`并调用`moveRight()`。

##### arrive()
马匹完成比赛时调用:
1. 停止马匹（设置class为`standRight`）
2. 重置`lap`为0
3. 在结果表格中显示名次
4. 将马匹编号加入`results`数组
5. 判断是否获胜，更新资金

#### 3.2.3 全局变量

| 变量名 | 类型 | 描述 |
|--------|------|------|
| `num_lap` | number | 比赛圈数（默认1） |
| `results` | Array | 存储完赛马匹编号的数组 |
| `funds` | number | 玩家当前资金（默认100） |
| `bethorse` | number | 玩家投注的马匹编号 |
| `amount` | number | 投注金额 |

#### 3.2.4 事件处理

**DOMContentLoaded事件**:
- 初始化4匹马（horse1-4）
- 绑定开始按钮点击事件

**开始按钮逻辑**:
1. 获取投注金额、马匹、圈数
2. 验证输入（金额>0、圈数>0、资金充足）
3. 重置结果表格
4. 禁用开始按钮
5. 调用`run()`启动所有马匹

### 3.3 CSS样式 (game.css)

#### 3.3.1 动画关键帧

| 动画名称 | 用途 |
|----------|------|
| `horseRight` | 向右奔跑的马匹姿态切换 |
| `horseLeft` | 向左奔跑的马匹姿态切换 |
| `horseDown` | 向下奔跑的马匹姿态切换 |
| `horseUp` | 向上奔跑的马匹姿态切换 |
| `headbob` ~ `headbob5` | 骑手头部的晃动效果 |
| `riderBob1` ~ `riderBob2` | 骑手身体的颠簸效果 |

#### 3.3.2 马匹状态类

| 类名 | 状态 |
|------|------|
| `.standRight` | 站立，面向右 |
| `.standLeft` | 站立，面向左 |
| `.runRight` | 向右奔跑 |
| `.runLeft` | 向左奔跑 |
| `.runDown` | 向下奔跑 |
| `.runUp` | 向上奔跑 |

---

## 4. 依赖关系

### 4.1 文件依赖
```
index.html
├── game.css (通过 <link> 引入)
└── game.js (通过 <script> 引入)

game.js
└── 依赖DOM元素 (由index.html提供)
```

### 4.2 图片资源依赖
- 马匹图片: `images/horse1.png` ~ `images/horse4.png`
- 骑手图片: `images/head0-4.png`, `images/body0-4.png`
- 背景图片: `images/bg.png`, `images/mud.png`
- 边框图片: `images/outer.png`, `images/inner.png`
- 装饰图片: `images/tree.png`

### 4.3 外部资源
无外部CDN依赖，所有资源均为本地文件。

---

## 5. 游戏流程

### 5.1 比赛流程图
```
开始 → 选择马匹 → 输入投注金额 → 输入圈数 → 点击Start
                                              ↓
                              ↓─────────────────────────────↓
                        验证输入                              │
                   (金额>0, 圈数>0, 资金充足)                  │
                              ↓                              │
                        验证通过                              │
                              ↓                              │
                    禁用Start按钮                           │
                              ↓                              │
              ┌───────────────────────────────┐             │
              │        马匹竞速循环             │             │
              │  moveRight → moveDown →        │             │
              │  moveLeft → moveUp → (lap++)   │             │
              │  重复 num_lap 次                │             │
              └───────────────────────────────┘             │
                              ↓                              │
                        马匹到达终点                         │
                              ↓                              │
                    更新results数组                         │
                              ↓                              │
              ┌───────────────────────────────┐             │
              │  判断第一匹马是否为自己投注的马  │ ←───────────┘
              │  是: funds += amount          │
              │  否: funds -= amount          │
              └───────────────────────────────┘
                              ↓
                    所有马匹完成比赛
                              ↓
                    启用Start按钮
                              ↓
                         比赛结束
```

### 5.2 关键算法

#### 马匹速度计算
```javascript
this.speed = Math.random() * 10 + 10; // 范围: 10-20
```

#### 移动延迟计算
```javascript
setTimeout(fn, 1000 / this.speed); // 速度越大，延迟越小，移动越快
```

#### 马匹编号提取
```javascript
this.number = parseInt(id.replace(/[\D]/g, '')); // 从"horse1"提取"1"
```

---

## 6. 项目运行方式

### 6.1 本地运行
直接用浏览器打开 `index.html` 文件即可运行游戏。

### 6.2 在线访问
访问 GitHub Pages 部署地址: https://fkitsantas.github.io/Horse-Racing-Game-JavaScript/

### 6.3 开发调试
1. 使用浏览器开发者工具（F12）
2. 在Console中可直接访问全局变量进行调试
3. 修改代码后刷新页面即可看到效果

---

## 7. 关键类与函数索引

### 7.1 类/构造函数

| 名称 | 文件:行号 | 描述 |
|------|----------|------|
| `Horse` | game.js:9 | 马匹类，创建马匹对象 |

### 7.2 Horse原型方法

| 方法名 | 行号 | 描述 |
|--------|------|------|
| `moveRight` | 19 | 向右移动 |
| `moveDown` | 51 | 向下移动 |
| `moveLeft` | 65 | 向左移动 |
| `moveUp` | 79 | 向上移动 |
| `run` | 97 | 启动赛跑 |
| `arrive` | 101 | 到达终点处理 |

### 7.3 全局函数

| 函数名 | 行号 | 描述 |
|--------|------|------|
| `DOMContentLoaded` 回调 | 133 | 初始化游戏，绑定事件 |

---

## 8. 配置参数

### 8.1 默认参数
```javascript
var num_lap = 1;    // 默认比赛圈数
var funds = 100;    // 默认初始资金(£)
```

### 8.2 马匹初始位置
| 马匹 | X坐标 | Y坐标 |
|------|-------|-------|
| horse1 | 20vw | 4vh |
| horse2 | 20vw | 8vh |
| horse3 | 20vw | 12vh |
| horse4 | 20vw | 16vh |

### 8.3 跑道边界
- 右边界: `82.5vw - horse_number * 2.5vw`
- 左边界: `12.5vw - horse_number * 2.5vw`
- 下边界: `originY + 65vh`
- 上边界: `originY`
