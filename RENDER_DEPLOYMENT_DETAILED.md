# Render 免费 Web 服务详细部署指南

本文档提供 Render 免费 Web 服务的**详细分步部署指南**，从文件准备到最终测试，确保您能够顺利部署 `proxy.py`。

## 0. 前置准备

- 一个 GitHub 账户（[免费注册](https://github.com/signup)）
- 一个 Render 账户（[免费注册](https://render.com/signup)）
- 已安装 Git（[下载安装](https://git-scm.com/downloads)）

## 1. 准备部署文件

### 1.1 确保 `proxy.py` 文件存在

首先，确保您的项目目录中包含 `proxy.py` 文件，内容如下：

```python
from flask import Flask, request, jsonify
import requests

app = Flask(__name__)

# 允许所有跨域请求
@app.after_request
def add_cors_headers(response):
    response.headers['Access-Control-Allow-Origin'] = '*'
    response.headers['Access-Control-Allow-Methods'] = 'GET, POST, OPTIONS'
    response.headers['Access-Control-Allow-Headers'] = 'Content-Type'
    return response

# 代理API请求
@app.route('/api/huacore.forms/documentapi/getvalue', methods=['POST', 'OPTIONS'])
def proxy_api():
    if request.method == 'OPTIONS':
        return '', 200
    
    try:
        # 构建目标URL
        target_url = f"http://10.157.85.11/api/huacore.forms/documentapi/getvalue{t if (t := request.query_string.decode()) else ''}"
        
        # 获取请求数据
        data = request.get_json()
        
        # 转发请求
        response = requests.post(target_url, json=data)
        
        # 返回响应
        return jsonify(response.json()), response.status_code
    except Exception as e:
        return jsonify({'error': str(e)}), 500

if __name__ == '__main__':
    app.run(port=5000, debug=True)
```

### 1.2 创建 `requirements.txt` 文件

在项目根目录创建 `requirements.txt` 文件，包含所需依赖：

```txt
Flask==2.3.3
requests==2.31.0
gunicorn==20.1.0
```

### 1.3 创建 `Procfile` 文件

在项目根目录创建 `Procfile` 文件（注意首字母大写），定义启动命令：

```txt
web: gunicorn proxy:app
```

### 1.4 最终文件结构

确保您的项目目录结构如下：

```
制造停机监控/
├── proxy.py              # 代理服务器代码
├── requirements.txt      # 依赖列表
├── Procfile              # 启动命令
├── index.html            # 前端页面
├── styles.css            # 样式文件
├── chart.umd.min.js      # Chart.js库
└── .gitignore            # Git忽略配置
```

## 2. 创建 GitHub 仓库

### 2.1 登录 GitHub

1. 访问 [GitHub](https://github.com/)
2. 登录您的 GitHub 账户

### 2.2 创建新仓库

1. 点击页面右上角的 `+` 图标
2. 选择 `New repository`
3. 填写仓库信息：
   - **Repository name**：输入仓库名称，如 `manufacturing-proxy`
   - **Description**：（可选）输入仓库描述，如 "制造停机监控系统的API代理服务器"
   - **Visibility**：选择 `Public`（公开仓库）
   - 勾选 `Add a README file`（可选）
   - 点击 `Create repository` 按钮

### 2.3 初始化本地 Git 仓库

1. 打开命令行工具（Windows：PowerShell 或 Git Bash）
2. 进入项目根目录：
   ```bash
   cd L:\0\制造停机监控
   ```
3. 初始化 Git 仓库：
   ```bash
   git init
   ```
4. 配置 Git 用户信息：
   ```bash
   git config user.name "Your GitHub Username"
   git config user.email "your.email@example.com"
   ```

### 2.4 提交并推送代码

1. 添加所有文件到 Git 暂存区：
   ```bash
   git add .
   ```
2. 提交代码：
   ```bash
   git commit -m "Initial commit with proxy server files"
   ```
3. 关联远程仓库：
   ```bash
   git remote add origin https://github.com/your-username/manufacturing-proxy.git
   ```
   *注意：将 `your-username` 替换为您的 GitHub 用户名，`manufacturing-proxy` 替换为您创建的仓库名称*
4. 推送代码到 GitHub：
   ```bash
   git push -u origin main
   ```
5. 输入您的 GitHub 用户名和密码（或个人访问令牌）

## 3. 部署到 Render

### 3.1 登录 Render

1. 访问 [Render](https://render.com/)
2. 点击 `Sign In` 按钮
3. 选择 `Sign in with GitHub`
4. 授权 Render 访问您的 GitHub 账户

### 3.2 创建 Web 服务

1. 登录后，点击页面右上角的 `New` 按钮
2. 选择 `Web Service`
3. 在 `Connect a repository` 部分，找到并选择您刚刚创建的 GitHub 仓库（如 `manufacturing-proxy`）
4. 点击 `Connect` 按钮

### 3.3 配置 Web 服务

在配置页面中，填写以下信息：

#### 基本信息
- **Name**：输入服务名称，如 `api-proxy`
- **Region**：选择离您最近的区域，如 `Oregon (us-west)` 或 `Ohio (us-east)`
- **Branch**：选择 `main` 或 `master`（根据您的 GitHub 仓库分支名称）

#### 构建和启动命令
- **Root Directory**：留空（默认即为项目根目录）
- **Environment**：选择 `Python 3`
- **Build Command**：输入 `pip install -r requirements.txt`
- **Start Command**：输入 `gunicorn proxy:app`

#### 计划选择
- **Plan**：选择 `Free`

### 3.4 部署服务

1. 点击页面底部的 `Create Web Service` 按钮
2. 等待部署过程完成（约1-2分钟）
3. 部署成功后，您将看到一个绿色的 `Live` 状态
4. 复制页面顶部的服务 URL（如 `https://api-proxy.onrender.com`）

## 4. 测试部署

### 4.1 使用 curl 测试

1. 打开命令行工具
2. 运行以下命令测试代理服务：
   ```bash
   curl -X POST -H "Content-Type: application/json" -d '{"start_datetime": 1703731200, "end_datetime": 1703817600}' https://your-service-url/api/huacore.forms/documentapi/getvalue
   ```
   *注意：将 `your-service-url` 替换为您的 Render 服务 URL*

3. 预期结果：
   - 如果代理服务正常工作，您将看到来自目标 API 的 JSON 响应
   - 如果出现错误，您将看到错误信息

### 4.2 使用 Postman 测试

1. 下载并安装 [Postman](https://www.postman.com/downloads/)
2. 打开 Postman，创建一个新的 POST 请求
3. 设置 URL 为您的 Render 服务 URL + API 路径：
   ```
   https://your-service-url/api/huacore.forms/documentapi/getvalue
   ```
4. 设置请求体为 JSON 格式：
   ```json
   {
     "start_datetime": 1703731200,
     "end_datetime": 1703817600
   }
   ```
5. 点击 `Send` 按钮
6. 查看响应结果

### 4.3 检查 Render 日志

1. 登录 Render 控制台
2. 点击您的 Web 服务
3. 点击 `Logs` 选项卡
4. 查看部署和运行日志，排查可能的错误

## 5. 修改前端代码

### 5.1 更新 API 调用地址

1. 打开 `index.html` 文件
2. 找到以下代码行（大约在第 515 行）：
   ```javascript
   const url = `http://127.0.0.1:5000/api/huacore.forms/documentapi/getvalue?t=${new Date().getTime()}`;
   ```
3. 替换为您的 Render 服务 URL：
   ```javascript
   const url = `https://your-service-url/api/huacore.forms/documentapi/getvalue?t=${new Date().getTime()}`;
   ```
   *注意：将 `your-service-url` 替换为您的 Render 服务 URL*

### 5.2 测试前端页面

1. 启动本地 HTTP 服务器：
   ```bash
   python -m http.server 8000
   ```
2. 访问 `http://localhost:8000`
3. 打开浏览器开发者工具
4. 检查网络请求和控制台日志
5. 确认 API 请求成功，数据正常显示

## 6. 部署前端页面到 GitHub Pages

### 6.1 创建 GitHub Pages 仓库

1. 在 GitHub 上创建一个新的仓库，名称格式为 `your-username.github.io`（必须使用您的 GitHub 用户名）
2. 初始化仓库并添加前端文件
3. 推送代码到仓库
4. 进入仓库 Settings → Pages
5. 选择 `main` 分支作为部署源
6. 点击 `Save`
7. 等待部署完成（约1-2分钟）
8. 访问 `https://your-username.github.io` 查看部署结果

### 6.2 更新前端 API 地址

确保前端页面中的 API 地址指向您的 Render 服务 URL，而不是本地地址。

## 7. 常见问题排查

### 7.1 部署失败

#### 错误：找不到 requirements.txt
- **原因**：`requirements.txt` 文件未创建或未添加到 Git 仓库
- **解决方案**：确保创建了 `requirements.txt` 文件并推送到 GitHub

#### 错误：找不到 Procfile
- **原因**：`Procfile` 文件未创建或未添加到 Git 仓库
- **解决方案**：确保创建了 `Procfile` 文件（首字母大写）并推送到 GitHub

#### 错误：gunicorn 未找到
- **原因**：`gunicorn` 依赖未添加到 `requirements.txt`
- **解决方案**：确保 `requirements.txt` 中包含 `gunicorn==20.1.0`

### 7.2 CORS 错误

#### 错误：No 'Access-Control-Allow-Origin' header is present on the requested resource
- **原因**：代理服务器未正确设置 CORS 响应头
- **解决方案**：确保 `proxy.py` 中包含 CORS 中间件

### 7.3 API 请求失败

#### 错误：500 Internal Server Error
- **原因**：代理服务器内部错误，可能是目标 API 不可访问或代码错误
- **解决方案**：
  1. 检查 Render 日志，查看具体错误信息
  2. 确认目标 API URL 正确且可访问
  3. 检查 `requests` 库是否已正确安装

#### 错误：404 Not Found
- **原因**：API 路径不正确
- **解决方案**：确保前端代码中的 API 路径与 `proxy.py` 中的路由匹配

## 8. Render 免费计划注意事项

### 8.1 免费计划限制

- **运行时间**：免费实例可能会在空闲时自动休眠（约15分钟无流量后）
- **启动时间**：休眠后首次访问会有冷启动延迟（约10-30秒）
- **资源限制**：512MB 内存，100GB 带宽/月
- **部署次数**：每月最多 100 次部署

### 8.2 优化建议

- **减少冷启动影响**：可以使用 Uptime Robot 等服务定期 ping 您的服务，保持实例活跃
- **监控免费额度**：定期检查 Render 控制台中的使用情况，避免超出免费额度
- **优化代码**：减少不必要的依赖和代码，提高响应速度

## 9. 高级配置（可选）

### 9.1 设置环境变量

如果您需要配置环境变量（如目标 API URL、API 密钥等）：

1. 在 Render 控制台中，点击您的 Web 服务
2. 点击 `Environment` 选项卡
3. 点击 `Add Environment Variable` 按钮
4. 输入变量名和值
5. 点击 `Save Changes`
6. 重新部署服务

### 9.2 配置自定义域名

如果您有自己的域名，可以配置到 Render 服务：

1. 在 Render 控制台中，点击您的 Web 服务
2. 点击 `Settings` 选项卡
3. 在 `Custom Domains` 部分，点击 `Add Custom Domain`
4. 输入您的域名，如 `api.your-domain.com`
5. 按照提示在您的域名注册商处添加 DNS 记录
6. 等待 DNS 记录生效（约5-30分钟）
7. Render 会自动为您的域名配置 HTTPS

## 10. 总结

通过本指南，您已经成功将 `proxy.py` 部署到 Render 免费 Web 服务，并将前端页面部署到 GitHub Pages。现在，您的制造停机监控系统可以在线获取真实数据，实现完整的监控功能。

### 最终架构

```
+-------------------+     +-------------------+     +-------------------+
|  GitHub Pages     |     |  Render Proxy     |     |  Target API       |
|  (前端页面)       | --> |  (api-proxy.onrender.com) |  (http://10.157.85.11) |
+-------------------+     +-------------------+     +-------------------+
```

### 后续维护

1. 定期检查 Render 控制台中的服务状态和日志
2. 监控免费额度使用情况
3. 定期更新依赖版本，确保安全性
4. 根据实际需求调整代码和配置

祝您部署顺利！如有任何问题，请参考 Render 官方文档或社区论坛。
