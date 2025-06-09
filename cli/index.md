---
layout: default
title: Hướng dẫn cài n8n trên cpanel với cli
---

<!-- @format -->

# Các bước tạo n8n bằng Setup Node.js App trong cPanel

- [Về Trang chủ](../)
- [Hướng dẫn cài n8n trên cpanel với pm2](../pm2/)

## Các biến

| variables    | Name                   |
| ------------ | ---------------------- |
| WEBHOOK_URL  | Địa chỉ webhook.       |
| username     | Tên người dùng hosting |
| domain       | Tên miền chạy app      |
| app-root     | Folder cài app         |
| app-path     | Context path           |
| node-version | phiên bản nodejs       |

## 1. Tạo app

```bash
cloudlinux-selector create --json --interpreter nodejs --version <node-version> --app-root <app-root> --domain <domain> --app-uri <app-path> --env '{"DB_TYPE":"sqlite","N8N_RELEASE_TYPE":"stable","WEBHOOK_URL":"<WEBHOOK_URL>","N8N_DIAGNOSTICS_ENABLED":"false","N8N_SKIP_WEBHOOK_DEREGISTRATION_SHUTDOWN":"true"}'
```

## 2. kích hoạt môi trường node

```bash
source /home/`username`/nodevenv/`app-root`/`node-version`/bin/activate && cd /home/`username`/`app-root`
```

## 3. cài n8n qua npm

```bash
npm init -y
npm install n8n
```

## 4. cài đặt startup file

```bash
cloudlinux-selector set --json --interpreter nodejs --domain `domain` --app-root `app-root` --startup-file node_modules/n8n/bin/n8n --skip-web-check
```

## 5. restart app

```bash
cloudlinux-selector restart --json --interpreter nodejs --app-root `app-root`
```

# Gỡ cài đặt n8n và xóa app

## 1. stop app

```bash
cloudlinux-selector stop --json --interpreter nodejs --app-root `app-root`
```

## 2. sao lưu phần database cơ bản (sqlite)

```bash
cp -r ~/.n8n ~/.n8n_backup_$(date +%Y%m%d\_%H%M%S)
```

## 3. xóa app

```bash
cloudlinux-selector destroy --json --interpreter nodejs --app-root `app-root`
```

## 4. Xóa thư mục cài đặt và thư mục db.

```bash
rm -rf ~/n8n
rm -rf ~/.n8n
```
