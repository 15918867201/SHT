# proxy.py 在线部署指南

本文档详细介绍如何将 `proxy.py` 部署到公网服务器，使前端页面能够在线获取真实数据。

## 1. 部署方案概述

`proxy.py` 是一个基于 Flask 框架的简单代理服务器，用于解决前端跨域访问 API 的问题。您可以选择以下几种部署方式：

### 1.1 方案一：使用云函数（推荐）
- **优势**：无需管理服务器，按需付费，自动扩展
- **支持平台**：AWS Lambda、阿里云函数计算、腾讯云 SCF、百度智能云函数等
- **复杂度**：低

### 1.2 方案二：使用 PaaS 平台
- **优势**：简单易用，提供完整的 Python 运行环境
- **支持平台**：Render、Vercel、Heroku、Railway 等
- **复杂度**：低到中等

### 1.3 方案三：使用虚拟机/容器
- **优势**：完全控制服务器环境
- **支持平台**：AWS EC2、阿里云 ECS、腾讯云 CVM、Docker + Kubernetes 等
- **复杂度**：高

## 2. 云函数部署步骤（以阿里云函数计算为例）

### 2.1 创建函数服务

1. 登录 [阿里云函数计算控制台](https://fcnext.console.aliyun.com/)
2. 点击 "创建函数"
3. 选择 "HTTP 函数"
4. 配置基本信息：
   - 函数名称：`api-proxy`
   - 运行环境：`Python 3.9`
   - 代码上传方式：`在线编辑`
   - 触发器类型：`HTTP 触发器`
   - 认证方式：`无认证`（或根据需求选择）

### 2.2 编写云函数代码

将 `proxy.py` 内容修改为云函数版本，替换到在线编辑器中：

```python
import json
import requests

# 云函数入口
def handler(environ, start_response):
    # 解析请求
    method = environ.get('REQUEST_METHOD')
    
    # 处理 OPTIONS 请求（CORS 预检查）
    if method == 'OPTIONS':
        status = '200 OK'
        headers = [
            ('Content-Type', 'application/json'),
            ('Access-Control-Allow-Origin', '*'),
            ('Access-Control-Allow-Methods', 'GET, POST, OPTIONS'),
            ('Access-Control-Allow-Headers', 'Content-Type')
        ]
        start_response(status, headers)
        return [b'']
    
    try:
        # 解析请求体
        request_body_size = int(environ.get('CONTENT_LENGTH', 0))
        request_body = environ['wsgi.input'].read(request_body_size)
        data = json.loads(request_body) if request_body else {}
        
        # 构建目标 URL
        target_url = "http://10.157.85.11/api/huacore.forms/documentapi/getvalue"
        
        # 转发请求
        response = requests.post(target_url, json=data)
        
        # 构建响应
        status = f'{response.status_code} OK'
        headers = [
            ('Content-Type', 'application/json'),
            ('Access-Control-Allow-Origin', '*'),
            ('Access-Control-Allow-Methods', 'GET, POST, OPTIONS'),
            ('Access-Control-Allow-Headers', 'Content-Type')
        ]
        start_response(status, headers)
        return [json.dumps(response.json()).encode('utf-8')]
    except Exception as e:
        # 错误处理
        status = '500 Internal Server Error'
        headers = [
            ('Content-Type', 'application/json'),
            ('Access-Control-Allow-Origin', '*'),
            ('Access-Control-Allow-Methods', 'GET, POST, OPTIONS'),
            ('Access-Control-Allow-Headers', 'Content-Type')
        ]
        start_response(status, headers)
        return [json.dumps({'error': str(e)}).encode('utf-8')]
```

### 2.3 配置依赖

在函数配置中添加 `requests` 依赖：

```
requests==2.31.0
```

### 2.4 部署并测试

1. 点击 "部署" 按钮
2. 部署完成后，复制函数的 HTTP 触发器 URL
3. 在浏览器中访问该 URL，或使用 curl 测试：
   ```bash
   curl -X POST -H "Content-Type: application/json" -d '{"start_datetime": 1703731200, "end_datetime": 1703817600}' https://your-function-url
   ```

## 3. PaaS 平台部署（以 Render 为例）

### 3.1 准备工作

1. 在本地创建一个新目录，将 `proxy.py` 复制到该目录
2. 创建 `requirements.txt` 文件，包含依赖：
   ```
   Flask==2.3.3
   requests==2.31.0
   gunicorn==20.1.0
   ```
3. 创建 `Procfile` 文件，定义启动命令：
   ```
   web: gunicorn proxy:app
   ```

### 3.2 部署到 Render

1. 登录 [Render](https://render.com/)
2. 点击 "New" > "Web Service"
3. 连接你的 GitHub 仓库（将 proxy 项目推送到 GitHub）
4. 配置部署选项：
   - **Build Command**: `pip install -r requirements.txt`
   - **Start Command**: `gunicorn proxy:app`
   - **Environment**: `Python 3.9`
5. 点击 "Create Web Service"
6. 等待部署完成，获取公网 URL

## 4. 修改前端代码

部署完成后，需要修改 `index.html` 中的 API 调用地址，将本地地址替换为部署后的公网地址。

### 4.1 修改 `index.html`

找到以下代码行（大约在第 515 行）：

```javascript
const url = `http://127.0.0.1:5000/api/huacore.forms/documentapi/getvalue?t=${new Date().getTime()}`;
```

替换为：

```javascript
// 使用云函数或 PaaS 部署的公网地址
const url = `https://your-deployed-url/api/huacore.forms/documentapi/getvalue?t=${new Date().getTime()}`;
```

### 4.2 示例

```javascript
// 云函数示例
const url = `https://your-function-url/api/huacore.forms/documentapi/getvalue?t=${new Date().getTime()}`;

// Render 示例
const url = `https://your-render-app.onrender.com/api/huacore.forms/documentapi/getvalue?t=${new Date().getTime()}`;
```

## 5. 安全性考虑

### 5.1 限制访问来源

在生产环境中，建议限制 API 代理的访问来源，只允许你的前端域名访问：

```python
# 在 Flask 中
@app.after_request
def add_cors_headers(response):
    # 只允许特定域名访问
    response.headers['Access-Control-Allow-Origin'] = 'https://your-frontend-domain.com'
    # 其他配置...
    return response
```

### 5.2 添加认证机制

对于敏感数据，可以添加简单的认证机制：

```python
# 在 Flask 中
@app.route('/api/huacore.forms/documentapi/getvalue', methods=['POST', 'OPTIONS'])
def proxy_api():
    # 简单的 API Key 认证
    api_key = request.headers.get('X-API-Key')
    if api_key != 'your-secret-api-key':
        return jsonify({'error': 'Unauthorized'}), 401
    
    # 其他处理...
```

### 5.3 使用 HTTPS

确保部署的代理服务使用 HTTPS 协议，提高数据传输安全性。

## 6. 测试与验证

### 6.1 本地测试

1. 在本地启动前端服务器：
   ```bash
   python -m http.server 8000
   ```
2. 访问 `http://localhost:8000`
3. 打开浏览器开发者工具，检查网络请求和控制台日志
4. 确认 API 请求成功，数据正常显示

### 6.2 在线测试

1. 将修改后的前端代码部署到 GitHub Pages 或其他静态网站托管服务
2. 访问部署后的前端页面
3. 检查数据是否正常显示

## 7. 常见问题排查

### 7.1 CORS 错误
- 检查 `proxy.py` 中的 CORS 配置是否正确
- 确保响应头中包含 `Access-Control-Allow-Origin`
- 对于 OPTIONS 请求，返回 200 状态码

### 7.2 500 错误
- 检查服务器日志，查看具体错误信息
- 确认目标 API URL 可访问
- 检查 `requests` 库是否正确安装

### 7.3 数据显示异常
- 检查 API 返回的数据格式是否符合预期
- 查看浏览器控制台的网络请求和错误信息
- 确认前端代码中的数据处理逻辑正确

## 8. 部署方案对比

| 部署方案 | 优势 | 劣势 | 适用场景 |
|---------|------|------|----------|
| 云函数 | 无需管理服务器，按需付费 | 有调用次数限制，冷启动延迟 | 访问量较小，间歇性使用 |
| PaaS 平台 | 简单易用，提供完整环境 | 价格相对较高 | 稳定访问量，需要持久运行 |
| 虚拟机/容器 | 完全控制，灵活配置 | 需要管理服务器，维护成本高 | 高访问量，复杂配置需求 |

## 9. 总结

通过本文档介绍的方法，您可以将 `proxy.py` 部署到公网服务器，使前端页面能够在线获取真实数据。推荐使用云函数或 PaaS 平台进行部署，这些方案简单易用，无需管理服务器，适合小型应用。

部署完成后，记得修改前端代码中的 API 调用地址，并考虑添加适当的安全措施，如限制访问来源和使用 HTTPS 协议。

祝您部署顺利！
