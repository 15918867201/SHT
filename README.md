# 切片停机监控系统

一个基于Web的制造停机监控系统，用于实时监控生产设备状态、统计停机时间，并以图表形式可视化展示。

## 功能特性

- ✨ 实时图表展示：使用Chart.js绘制生产数据趋势图
- ⏱️ 时间范围选择：支持自定义时间范围和快速选择
- 📊 停机时间统计：自动计算和展示停机时间段
- 🔄 自动刷新：可配置的自动数据刷新功能
- 🌐 跨平台兼容：支持各种现代浏览器
- 📱 响应式设计：适配不同屏幕尺寸

## 技术栈

- HTML5 + CSS3 + JavaScript
- Chart.js - 数据可视化
- GitHub Pages - 静态网站托管

## GitHub Pages 部署指南

### 1. 准备工作

1. **创建GitHub仓库**
   - 登录GitHub账号
   - 创建一个新的仓库（推荐使用 `manufacturing-downtime-monitor` 作为仓库名）

2. **安装Git**
   - 确保本地安装了Git
   - 配置Git用户信息
   ```bash
   git config --global user.name "Your Name"
   git config --global user.email "your.email@example.com"
   ```

### 2. 部署步骤

#### 方法1：自动部署（推荐）

1. **克隆仓库到本地**
   ```bash
   git clone https://github.com/your-username/manufacturing-downtime-monitor.git
   cd manufacturing-downtime-monitor
   ```

2. **添加项目文件**
   ```bash
   # 复制所有项目文件到仓库目录
   # 确保包含以下核心文件：
   # - index.html
   # - styles.css
   # - chart.umd.min.js
   # - .gitignore
   # - .github/workflows/deploy.yml
   ```

3. **提交并推送代码**
   ```bash
   git add .
   git commit -m "Initial commit"
   git push origin main
   ```

4. **启用GitHub Pages**
   - 进入仓库的"Settings"页面
   - 点击"Pages"选项
   - 在"Build and deployment"部分，选择：
     - Source: "Deploy from a branch"
     - Branch: "gh-pages" (或 "main" 分支)
     - Click "Save"

5. **等待部署完成**
   - GitHub Actions会自动执行部署工作流
   - 部署完成后，会在"Pages"页面显示访问URL

#### 方法2：手动部署

1. **构建静态网站**
   - 确保项目文件完整
   - 不需要额外的构建步骤，因为这是一个纯静态网站

2. **手动部署到GitHub Pages**
   - 进入仓库的"Settings"页面
   - 点击"Pages"选项
   - 在"Build and deployment"部分，选择：
     - Source: "Upload files"
     - 点击"Upload"按钮，上传所有项目文件
     - 点击"Save"

### 3. 配置说明

#### API配置

项目默认使用本地代理服务器获取数据。如果需要修改API源，请修改 `index.html` 中的以下部分：

```javascript
// 使用本地代理服务器请求真实API数据
const url = `http://127.0.0.1:5000/api/huacore.forms/documentapi/getvalue?t=${new Date().getTime()}`;
```

#### GitHub Pages域名配置

部署完成后，您的网站将可以通过以下URL访问：
```
https://your-username.github.io/manufacturing-downtime-monitor/
```

### 4. 本地开发

#### 使用Python HTTP服务器

```bash
# 启动本地HTTP服务器
python -m http.server 8000

# 访问 http://localhost:8000
```

#### 使用本地代理服务器

```bash
# 启动本地代理服务器（用于解决CORS问题）
python proxy.py

# 访问 http://localhost:8000
```

### 5. 项目结构

```
manufacturing-downtime-monitor/
├── index.html              # 主页面
├── styles.css              # 样式文件
├── chart.umd.min.js        # Chart.js库
├── .gitignore              # Git忽略文件
├── .github/                # GitHub配置目录
│   └── workflows/          # GitHub Actions工作流
│       └── deploy.yml      # 自动部署配置
├── README.md               # 项目说明文档
└── proxy.py                # 本地代理服务器（开发用）
```

## 使用说明

### 时间范围选择

- **快速选择**：点击页面上的快捷按钮（最近1小时、6小时、12小时、24小时、7天）
- **自定义范围**：使用日期时间选择器设置开始和结束时间，然后点击"查询"按钮

### 自动刷新

- 开启/关闭自动刷新开关控制数据自动刷新
- 默认每45秒刷新一次数据

### 停机时间统计

- 系统自动统计设备停机时间段
- 停机时间以分钟为单位显示
- 表格中展示所有停机记录

## 浏览器兼容性

- Chrome 90+
- Firefox 88+
- Safari 14+
- Edge 90+

## 开发说明

### 修改图表样式

编辑 `index.html` 中的Chart.js配置部分：

```javascript
const chart = new Chart(ctx, {
    type: 'line',
    data: {
        // 数据配置
    },
    options: {
        // 样式配置
    }
});
```

### 修改CSS样式

编辑 `styles.css` 文件，修改页面样式和布局。

## 许可证

MIT License

## 贡献

欢迎提交Issue和Pull Request！

## 联系方式

如有问题或建议，请通过GitHub Issues与我联系。
