# Render 部署检查清单

## 🔍 前置检查

- [ ] 已注册 GitHub 账户
- [ ] 已注册 Render 账户
- [ ] 已安装 Git
- [ ] 项目目录结构完整

## 📁 项目文件准备

- [ ] `proxy.py` 文件已创建，包含正确的代理逻辑
- [ ] `requirements.txt` 文件已创建，包含以下依赖：
  ```
  Flask==2.3.3
  requests==2.31.0
  gunicorn==20.1.0
  ```
- [ ] `Procfile` 文件已创建，内容为：
  ```
  web: gunicorn proxy:app
  ```
- [ ] `.gitignore` 文件已创建，排除不必要的文件

## 🚀 GitHub 仓库创建与推送

- [ ] 已在 GitHub 上创建公开仓库（如：`manufacturing-proxy`）
- [ ] 已初始化本地 Git 仓库
  ```bash
  git init
  git config user.name "Your GitHub Username"
  git config user.email "your.email@example.com"
  ```
- [ ] 已将所有文件添加到 Git
  ```bash
  git add .
  ```
- [ ] 已提交代码
  ```bash
  git commit -m "Initial commit with proxy server files"
  ```
- [ ] 已关联远程仓库
  ```bash
  git remote add origin https://github.com/your-username/manufacturing-proxy.git
  ```
- [ ] 已推送代码到 GitHub
  ```bash
  git push -u origin main
  ```

## 🌐 Render 部署

- [ ] 已登录 Render 账户
- [ ] 已创建新的 Web Service
- [ ] 已连接 GitHub 仓库
- [ ] 已配置服务：
  - [ ] 名称：`api-proxy`
  - [ ] 环境：`Python 3`
  - [ ] 构建命令：`pip install -r requirements.txt`
  - [ ] 启动命令：`gunicorn proxy:app`
  - [ ] 计划：`Free`
- [ ] 已点击 `Create Web Service` 部署服务
- [ ] 部署成功，服务状态为 `Live`
- [ ] 已复制 Render 服务 URL（如：`https://api-proxy.onrender.com`）

## 🧪 部署测试

- [ ] 已使用 curl 测试代理服务：
  ```bash
  curl -X POST -H "Content-Type: application/json" -d '{"start_datetime": 1703731200, "end_datetime": 1703817600}' https://your-render-service-url/api/huacore.forms/documentapi/getvalue
  ```
- [ ] 已检查 Render 日志，确认服务正常运行

## 🎨 前端配置更新

- [ ] 已修改 `index.html` 中的 API 地址：
  - 原地址：`http://127.0.0.1:5000`
  - 新地址：`https://your-render-service-url`
- [ ] 已测试前端页面，确认数据正常显示
- [ ] 已将更新后的前端代码部署到 GitHub Pages

## ✅ 最终验证

- [ ] 前端页面可以正常访问
- [ ] 图表数据正常显示
- [ ] 停机时间统计正常
- [ ] 自动刷新功能正常
- [ ] 时间范围选择功能正常

## 📝 部署笔记

记录您的部署信息：

- GitHub 仓库 URL：______
- Render 服务 URL：______
- GitHub Pages URL：______
- 部署日期：______
- 部署人员：______

## 🚨 常见问题排查

- [ ] 如果部署失败，查看 Render 日志获取具体错误信息
- [ ] 如果遇到 CORS 错误，检查 `proxy.py` 中的 CORS 配置
- [ ] 如果 API 请求失败，确认目标 API URL 可访问
- [ ] 如果前端页面无法访问，检查 GitHub Pages 部署状态

## 📚 参考文档

- [Render 官方文档](https://render.com/docs)
- [GitHub Pages 官方文档](https://docs.github.com/cn/pages)
- [Flask 官方文档](https://flask.palletsprojects.com/)

## 🎉 部署完成！

恭喜您已成功将 proxy.py 部署到 Render 免费 Web 服务！

您的制造停机监控系统现在可以在线获取真实数据，实现完整的监控功能。

---

**部署完成后，请定期检查：**
1. Render 服务状态和日志
2. 免费额度使用情况
3. 前端页面的访问情况
4. 数据显示是否正常

祝您使用愉快！
