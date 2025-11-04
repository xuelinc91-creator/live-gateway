# 直播辩论系统 - Nginx 网关配置

本项目是直播辩论系统的 Nginx 网关配置文件，用于将外部请求从 `8088` 端口转发到后端服务器的 `8000` 端口。

## 📋 项目概述

### 架构说明

```
客户端 (小程序/后台管理系统)
    ↓
Nginx (监听 8088 端口)
    ↓
后端服务器 (Node.js, 监听 8000 端口)
```

### 端口配置

- **Nginx 监听端口**: `8088` (外部访问)
- **后端服务器端口**: `8000` (内部)

## 🚀 快速开始

### 1. 安装 Nginx

**macOS (使用 Homebrew):**
```bash
brew install nginx
```

**Linux (Ubuntu/Debian):**
```bash
sudo apt-get update
sudo apt-get install nginx
```

**Linux (CentOS/RHEL):**
```bash
sudo yum install nginx
```

### 2. 复制配置文件

**macOS:**
```bash
sudo cp nginx.conf /usr/local/etc/nginx/servers/live-debate.conf
```

**Linux:**
```bash
sudo cp nginx.conf /etc/nginx/sites-available/live-debate.conf
sudo ln -s /etc/nginx/sites-available/live-debate.conf /etc/nginx/sites-enabled/
```

### 3. 修改配置参数

根据实际情况修改 `nginx.conf` 中的以下参数：

- **server_name**: 修改为你的服务器 IP 或域名（默认: `192.168.31.249 localhost`）
- **upstream backend_server**: 确认后端服务器地址和端口（默认: `127.0.0.1:8000`）
- **日志路径**: 根据系统调整日志文件路径（默认: `/var/log/nginx/`）

### 4. 测试配置

```bash
# 测试 Nginx 配置是否正确
sudo nginx -t
```

### 5. 启动/重启 Nginx

**macOS:**
```bash
# 启动
sudo brew services start nginx

# 或手动启动
sudo nginx

# 重新加载配置（不中断服务）
sudo nginx -s reload

# 停止
sudo nginx -s stop
```

**Linux:**
```bash
# 启动
sudo systemctl start nginx

# 设置开机自启
sudo systemctl enable nginx

# 重启
sudo systemctl restart nginx

# 重新加载配置
sudo systemctl reload nginx

# 查看状态
sudo systemctl status nginx
```

## 🔧 配置说明

### 主要功能模块

1. **WebSocket 代理** (`/ws`)
   - 支持实时通信功能
   - 超时时间设置为 24 小时
   - 自动升级 WebSocket 连接

2. **API 接口代理** (`/api`)
   - 所有 API 请求转发到后端
   - 支持 CORS 跨域请求
   - 自动处理 OPTIONS 预检请求

3. **后台管理系统代理** (`/admin`)
   - 后台管理页面访问

4. **健康检查** (`/health`)
   - 用于监控服务状态

### 配置项详解

```nginx
# 上游服务器配置
upstream backend_server {
    server 127.0.0.1:8000;
    keepalive 64;
}

# WebSocket 升级头部
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection "upgrade";

# CORS 支持
add_header Access-Control-Allow-Origin * always;
```

## 📊 监控和日志

### 查看访问日志
```bash
tail -f /var/log/nginx/live-debate-access.log
```

### 查看错误日志
```bash
tail -f /var/log/nginx/live-debate-error.log
```

### 检查 Nginx 状态
```bash
# Linux
sudo systemctl status nginx

# macOS
sudo nginx -s info
```

## 🐛 常见问题

### 1. 502 Bad Gateway

**原因**: 后端服务器未启动或端口不正确

**解决**:
```bash
# 检查后端服务器是否运行
ps aux | grep node

# 检查端口是否被占用
lsof -i :8000
```

### 2. WebSocket 连接失败

**原因**: WebSocket 升级头部未正确传递

**解决**: 确认配置中包含了 `Upgrade` 和 `Connection` 头部

### 3. CORS 错误

**原因**: 跨域请求被阻止

**解决**: 检查配置中的 `Access-Control-Allow-*` 头部设置

### 4. 端口被占用

**原因**: 8088 端口已被其他服务占用

**解决**:
```bash
# 查看端口占用
lsof -i :8088

# 修改配置中的监听端口
# 或停止占用端口的服务
```

## 🔒 安全建议

1. **防火墙配置**: 只开放必要的端口（8088）
2. **HTTPS 支持**: 生产环境建议配置 SSL 证书
3. **访问限制**: 可以添加 IP 白名单限制
4. **日志轮转**: 配置日志轮转避免日志文件过大

## 📚 相关文档

- [Nginx 官方文档](https://nginx.org/en/docs/)
- [Nginx WebSocket 代理](https://nginx.org/en/docs/http/websocket.html)
- [Nginx 反向代理](https://nginx.org/en/docs/http/ngx_http_proxy_module.html)

## 📝 版本历史

- **v1.0.0** (2025-11-04)
  - 初始版本
  - 支持 WebSocket 代理
  - 支持 API 和后台管理系统代理
  - 支持 CORS

## 📄 许可证

MIT License

## 👤 作者

直播辩论系统开发团队

