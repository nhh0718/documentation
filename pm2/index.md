---
layout: default
title: Hướng dẫn cài n8n trên cpanel với pm2
---

<!-- @format -->

# Hướng dẫn setup n8n cpanel

Hướng dẫn setup n8n cpanel với pm2.

- [Về Trang chủ](../)
- [Hướng dẫn cài n8n trên cpanel với cli](../cli/)

## Setup nodejs và pm2, n8n

### Tải và chạy script cài đặt NVM

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
```

### Sau khi chạy script, bạn cần tải lại shell hoặc khởi động lại Terminal để NVM có hiệu lực.

```bash
source ~/.bashrc
```

### Kiểm tra nvm

```
nvm -v
```

Nếu lệnh trả về `nvm`, tức là NVM đã được cài đặt thành công.

### Cài đặt Node.js bằng NVM

Sử dụng NVM để cài đặt phiên bản Node.js mong muốn (ví dụ: phiên bản LTS mới nhất).

```
nvm install --lts
nvm use --lts
```

Hoặc cài đặt một phiên bản cụ thể:

```
nvm install 20 # Cài đặt Node.js phiên bản 20
nvm use 20 # Sử dụng Node.js phiên bản 20
```

### Kiểm tra Node.js và npm:

```
node -v
npm -v
```

### Tạo một thư mục mới cho n8n trong thư mục gốc của bạn.

```
mkdir ~/n8n_app
cd ~/n8n_app
```

### Khởi tạo package.json và cài n8n

```
npm init -y
npm install n8n@latest --save
```

### Cài pm2 toàn cục

```
npm install -g pm2
```

### Tạo file ecosystem để chạy pm2

```
nano ecosystem.config.js
```

### Dán nội dung sau vào

```json
module.exports = {
  apps: [
    {
      name: "n8n",
      script: "./node_modules/n8n/bin/n8n",
      env: {
        DB_TYPE: 'sqlite', // chạy với sqlite
        N8N_RELEASE_TYPE : 'stable', // stable thì logo đỏ và ko có chữ Dev
        N8N_BASIC_AUTH_ACTIVE: "true",
        WEBHOOK_URL: "https://hoang.io.vn/n8n",
        N8N_DIAGNOSTICS_ENABLED: "false",
        N8N_SKIP_WEBHOOK_DEREGISTRATION_SHUTDOWN: "true",
        N8N_HOST: '127.0.0.1',
        N8N_PORT: 5678,
        N8N_PATH: '/n8n/', //path
        N8N_EDITOR_BASE_URL: 'https://hoang.io.vn/n8n/',
        N8N_PUSH_BACKEND: 'sse' // loại push BE từ FE, có thể là ws
      }
    }
  ]
};
```

<mark>Ấn Ctrl X, gõ y => enter để lưu</mark>

### Chạy n8n

```
pm2 start ecosystem.config.js
```

### Cấu hình reverse proxy (.htaccess file)

```
nano ~/public_html/.htaccess
```

### Dán cấu hình sau vào

```
RewriteEngine On
ProxyPreserveHost On

RewriteCond %{HTTP:Upgrade} =websocket [NC]
RewriteRule ^n8n/ws/(.*)$ ws://127.0.0.1:5678/ws/$1 [P,L]

RewriteRule ^n8n/?$ http://127.0.0.1:5678/ [P,L]

RewriteRule ^n8n/(.*)$ http://127.0.0.1:5678/$1 [P,L]

RequestHeader set Origin “https://domain.chay.n8n”
RequestHeader set X-Forwarded-Proto https
```

Lưu lại và truy cập vào url để xem.
