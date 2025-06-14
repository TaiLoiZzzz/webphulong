# Hướng dẫn Docker cho Web Phú Long

## Cài đặt và Chạy

### 1. Chạy ứng dụng trên port 8080 (Đơn giản)

```bash
# Build và chạy ứng dụng
docker-compose up --build

# Hoặc chạy trong background
docker-compose up -d --build
```

Truy cập ứng dụng tại: http://localhost:8080

### 2. Chạy với Nginx reverse proxy (Nâng cao)

```bash
# Chạy với Nginx trên port 80
docker-compose --profile with-nginx up --build

# Hoặc chạy trong background
docker-compose --profile with-nginx up -d --build
```

Truy cập ứng dụng tại: http://localhost

## Các lệnh hữu ích

### Xem logs
```bash
# Xem logs của tất cả services
docker-compose logs

# Xem logs của service web
docker-compose logs web

# Xem logs realtime
docker-compose logs -f web
```

### Dừng ứng dụng
```bash
# Dừng containers
docker-compose down

# Dừng và xóa volumes
docker-compose down -v

# Dừng và xóa images
docker-compose down --rmi all
```

### Rebuild ứng dụng
```bash
# Rebuild sau khi thay đổi code
docker-compose up --build

# Force rebuild
docker-compose build --no-cache
```

### Quản lý containers
```bash
# Xem trạng thái containers
docker-compose ps

# Restart service
docker-compose restart web

# Exec vào container
docker-compose exec web sh
```

## Cấu trúc Files

- `client/Dockerfile` - Docker image configuration cho Next.js app
- `docker-compose.yml` - Docker Compose services configuration
- `nginx.conf` - Nginx reverse proxy configuration (tùy chọn)
- `client/.dockerignore` - Files được loại trừ khi build Docker image

## Tối ưu hóa

### Multi-stage Build
Dockerfile sử dụng multi-stage build để tối ưu hóa kích thước image:
- **deps**: Cài đặt dependencies
- **builder**: Build ứng dụng
- **runner**: Chạy ứng dụng production

### Standalone Output
Next.js được cấu hình với `output: 'standalone'` để tạo ra một package tự chứa.

### Security
- Chạy với non-root user (nextjs:nodejs)
- Nginx có security headers
- Telemetry disabled

## Troubleshooting

### Lỗi Port đã được sử dụng
```bash
# Kiểm tra port đang được sử dụng
lsof -i :8080

# Thay đổi port trong docker-compose.yml
ports:
  - "9090:3000"  # Thay đổi từ 8080 sang 9090
```

### Lỗi Build
```bash
# Xóa cache và rebuild
docker-compose build --no-cache

# Xóa tất cả containers và images
docker system prune -a
```

### Performance
```bash
# Xem resource usage
docker stats

# Giới hạn memory cho container
docker-compose.yml:
  services:
    web:
      deploy:
        resources:
          limits:
            memory: 512M
```

## Environment Variables

Có thể thêm environment variables trong docker-compose.yml:

```yaml
environment:
  - NODE_ENV=production
  - API_URL=https://api.example.com
  - DATABASE_URL=postgresql://...
```

## Production Deployment

Để deploy production, cần:

1. Sử dụng external database
2. Thêm SSL certificates
3. Cấu hình logging
4. Monitoring và health checks
5. Backup strategies 