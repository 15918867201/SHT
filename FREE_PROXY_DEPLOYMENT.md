# proxy.py 免费部署方案

本文档详细介绍免费部署 `proxy.py` 的方案，包括云函数和 PaaS 平台的免费额度、限制和具体部署步骤。

## 1. 云函数免费方案

### 1.1 阿里云函数计算

#### 免费额度
- **调用次数**：每月免费 100 万次调用
- **执行时长**：每月免费 40 万秒执行时间
- **内存**：默认 128MB-1024MB，免费额度包含在内
- **流量**：每月免费 1GB 出网流量

#### 部署步骤
1. 登录 [阿里云函数计算控制台](https://fcnext.console.aliyun.com/)
2. 点击 "创建函数"
3. 选择 "HTTP 函数"
4. 配置：
   - 函数名称：`api-proxy`
   - 运行环境：`Python 3.9`
   - 代码上传方式：`在线编辑`
   - 触发器类型：`HTTP 触发器`
   - 认证方式：`无认证`
5. 粘贴云函数代码（见 PROXY_DEPLOYMENT_GUIDE.md）
6. 添加 `requests` 依赖：`requests==2.31.0`
7. 点击 "部署"
8. 复制 HTTP 触发器 URL

### 1.2 腾讯云 SCF

#### 免费额度
- **调用次数**：每月免费 100 万次调用
- **执行时长**：每月免费 40 万秒执行时间
- **内存**：默认 128MB-2048MB，免费额度包含在内
- **流量**：每月免费 1GB 出网流量

#### 部署步骤
1. 登录 [腾讯云 SCF 控制台](https://console.cloud.tencent.com/scf)
2. 点击 "函数服务"
3. 点击 "新建"
4. 选择 "模板创建"
5. 搜索并选择 "HTTP 触发器函数"
6. 配置：
   - 函数名称：`api-proxy`
   - 运行环境：`Python 3.9`
   - 触发器配置：开启 HTTP 触发器，认证方式选择 "匿名访问"
7. 替换代码为云函数版本
8. 添加 `requests` 依赖
9. 点击 "完成"

### 1.3 AWS Lambda

#### 免费额度（12 个月免费）
- **调用次数**：每月免费 100 万次调用
- **执行时长**：每月免费 400,000 GB-seconds
- **内存**：默认 128MB-10,240MB
- **流量**：每月免费 1GB 出网流量

#### 部署步骤
1. 登录 [AWS Lambda 控制台](https://console.aws.amazon.com/lambda/)
2. 点击 "Create function"
3. 选择 "Author from scratch"
4. 配置：
   - Function name：`api-proxy`
   - Runtime：`Python 3.9`
   - Architecture：`x86_64`
5. 点击 "Create function"
6. 在 "Code" 标签页中，替换代码
7. 在 "Layers" 中添加 `requests` 层，或在代码中包含
8. 添加 API Gateway 触发器
9. 部署并获取 URL

## 2. PaaS 平台免费方案

### 2.1 Render

#### 免费特性
- **免费实例**：提供免费的 Web 服务实例
- **运行时间**：支持 24/7 运行
- **自动部署**：从 GitHub 自动部署
- **HTTPS 支持**：自动提供 HTTPS 证书
- **限制**：
  - 单个实例 512MB 内存
  - 每月 100GB 带宽
  - 每次部署 1GB 磁盘空间

#### 部署步骤
1. 将 `proxy.py` 推送到 GitHub 仓库
2. 登录 [Render](https://render.com/)
3. 点击 "New" > "Web Service"
4. 连接 GitHub 仓库
5. 配置：
   - **Name**：`api-proxy`
   - **Region**：选择离你最近的区域
   - **Branch**：`main` 或 `master`
   - **Root Directory**：`/`
   - **Build Command**：`pip install -r requirements.txt`
   - **Start Command**：`gunicorn proxy:app`
   - **Plan**：选择 "Free"
6. 点击 "Create Web Service"
7. 等待部署完成，获取 URL

### 2.2 Railway

#### 免费特性
- **免费计划**：提供 "Hobby" 免费计划
- **运行时间**：24/7 运行
- **自动部署**：从 GitHub 或本地部署
- **HTTPS 支持**：自动提供 HTTPS 证书
- **限制**：
  - 1000 小时/月运行时间
  - 512MB 内存
  - 1GB 磁盘空间

#### 部署步骤
1. 将 `proxy.py` 推送到 GitHub 仓库
2. 登录 [Railway](https://railway.app/)
3. 点击 "New Project" > "Deploy from GitHub repo"
4. 连接 GitHub 仓库
5. 配置：
   - 选择仓库和分支
   - 自动检测语言为 Python
   - 使用默认的构建和启动命令
6. 点击 "Deploy"
7. 等待部署完成，获取 URL

### 2.3 Vercel

#### 免费特性
- **免费计划**：提供 "Hobby" 免费计划
- **自动部署**：从 GitHub 自动部署
- **HTTPS 支持**：自动提供 HTTPS 证书
- **边缘网络**：全球 CDN 加速
- **限制**：
  - 100GB 带宽/月
  - 无服务器函数：1000 次调用/日

#### 部署步骤
1. 将 `proxy.py` 推送到 GitHub 仓库
2. 登录 [Vercel](https://vercel.com/)
3. 点击 "New Project"
4. 连接 GitHub 仓库
5. 配置：
   - 选择仓库和分支
   - 构建命令：`pip install -r requirements.txt`
   - 输出目录：留空（静态网站）
6. 点击 "Deploy"
7. 等待部署完成，获取 URL

## 3. 本地开发免费方案

如果只是用于本地开发和测试，可以直接在本地运行 `proxy.py`：

```bash
# 安装依赖
pip install flask requests

# 启动代理服务器
python proxy.py
```

然后前端页面使用 `http://127.0.0.1:5000` 作为 API 地址。

## 4. 免费方案对比

| 方案 | 平台 | 免费额度 | 限制 | 适合场景 |
|------|------|----------|------|----------|
| 云函数 | 阿里云函数计算 | 100万次调用/月，40万秒执行时间 | 流量1GB/月 | 低访问量，间歇性使用 |
| 云函数 | 腾讯云 SCF | 100万次调用/月，40万秒执行时间 | 流量1GB/月 | 低访问量，间歇性使用 |
| 云函数 | AWS Lambda | 100万次调用/月（12个月免费） | 流量1GB/月 | 长期项目，有AWS经验 |
| PaaS | Render | 免费实例，24/7运行 | 512MB内存，100GB带宽/月 | 稳定访问量，简单部署 |
| PaaS | Railway | 1000小时/月，24/7运行 | 512MB内存，1GB磁盘 | 稳定访问量，需要长期运行 |
| PaaS | Vercel | 1000次调用/日 | 100GB带宽/月 | 低访问量，边缘部署 |

## 5. 推荐免费方案

### 5.1 对于个人或小型团队

**推荐：Render 免费 Web 服务**

- **优势**：
  - 简单易用，无需复杂配置
  - 24/7 稳定运行
  - 自动 HTTPS 证书
  - 从 GitHub 自动部署
  - 每月 100GB 带宽，足够小型应用使用

- **部署要点**：
  1. 准备 `requirements.txt` 文件
  2. 准备 `Procfile` 文件
  3. 将代码推送到 GitHub
  4. 在 Render 上一键部署

### 5.2 对于间歇性使用的监控系统

**推荐：阿里云/腾讯云函数**

- **优势**：
  - 按需付费，无使用不收费
  - 免费额度高（100万次调用/月）
  - 无需管理服务器
  - 适合间歇性使用的监控系统

- **部署要点**：
  1. 使用提供的云函数代码模板
  2. 配置 HTTP 触发器
  3. 添加 `requests` 依赖
  4. 测试并获取 URL

## 6. 免费方案注意事项

1. **免费额度限制**：注意各平台的免费额度，避免超出导致收费
2. **服务稳定性**：免费方案可能在资源紧张时出现性能下降
3. **数据安全**：避免在免费服务中处理敏感数据
4. **备份数据**：定期备份重要数据，免费服务可能随时终止
5. **域名变更**：免费服务的域名可能会变更，建议绑定自定义域名（如果支持）

## 7. 部署后的前端配置

无论选择哪种免费方案，部署完成后都需要修改前端代码中的 API 地址：

1. 打开 `index.html` 文件
2. 找到以下代码行：
   ```javascript
   const url = `http://127.0.0.1:5000/api/huacore.forms/documentapi/getvalue?t=${new Date().getTime()}`;
   ```
3. 替换为部署后的公网 URL：
   ```javascript
   // Render 示例
   const url = `https://your-render-app.onrender.com/api/huacore.forms/documentapi/getvalue?t=${new Date().getTime()}`;
   
   // 阿里云函数示例
   const url = `https://your-function-id.region.fc.aliyuncs.com/2016-08-15/proxy/api-proxy/api/huacore.forms/documentapi/getvalue?t=${new Date().getTime()}`;
   ```

## 8. 测试免费部署

部署完成后，使用以下方法测试：

1. **使用浏览器测试**：
   - 打开部署后的前端页面
   - 打开浏览器开发者工具
   - 检查网络请求，确认 API 请求成功

2. **使用 curl 测试**：
   ```bash
   curl -X POST -H "Content-Type: application/json" -d '{"start_datetime": 1703731200, "end_datetime": 1703817600}' https://your-deployed-url/api/huacore.forms/documentapi/getvalue
   ```

3. **使用 Postman 测试**：
   - 创建 POST 请求
   - 设置 URL 为部署后的地址
   - 设置请求体为 JSON 格式
   - 发送请求，检查响应

## 9. 总结

通过本文档介绍的免费方案，您可以将 `proxy.py` 部署到公网服务器，实现前端页面在线获取真实数据。推荐根据您的实际需求和使用场景选择合适的免费方案：

- **稳定运行需求**：选择 Render 或 Railway 的免费 Web 服务
- **间歇性使用**：选择阿里云或腾讯云函数
- **快速测试**：直接在本地运行

所有方案都提供了足够的免费额度，适合个人或小型团队使用。部署完成后，记得修改前端代码中的 API 地址，并定期检查免费额度使用情况，避免超出限制。

祝您部署顺利！
