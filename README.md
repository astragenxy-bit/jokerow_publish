# TalentGraph ONA — Organizational Network Analysis

Website tĩnh dạng single-page (SPA) cho phân tích mạng lưới (ONA) và IVI (Individual Value Index) trong tổ chức.

## Tính năng

- **Quy trình ISO** — Bản đồ 6 quy trình ISO 9001:2015 với phân công chi tiết
- **Phân công nhân sự** — Giao diện quản lý phụ trách (Responsible) và tham gia (Participant) cho từng bước
- **Mạng ONA** — Trực quan hóa mạng lưới làm việc tự động sinh ra từ phân công quy trình
- **IVI Ranking** — Xếp hạng cá nhân theo tỷ trọng giá trị trong tổ chức (% của tổng)

## Cấu trúc

```
.
├── TalentGraph_ONA_IVI_v4_Manufacturing.html  # Ứng dụng SPA chính
├── Dockerfile                                  # Docker image (nginx)
├── .dockerignore
├── .gitignore
└── README.md
```

## Chạy bằng Docker

### Build image

```bash
docker build -t talentgraph-ona:latest .
```

### Chạy container

```bash
docker run -d --name talentgraph -p 8080:80 talentgraph-ona:latest
```

Truy cập: `http://localhost:8080`

### Stop & Remove

```bash
docker stop talentgraph
docker rm talentgraph
```

## Chạy local (không Docker)

Mở file `TalentGraph_ONA_IVI_v4_Manufacturing.html` trực tiếp trong trình duyệt.

## Deploy lên viecvietnam.vn

### Option 1: Docker Compose (Recommended)

Tạo `docker-compose.yml`:

```yaml
version: '3.8'
services:
  talentgraph:
    image: talentgraph-ona:latest
    ports:
      - "80:80"
    restart: unless-stopped
```

Chạy:
```bash
docker compose up -d
```

### Option 2: Manual + Reverse Proxy (nginx)

1. Push image lên Docker Hub hoặc registry riêng
2. SSH vào server viecvietnam.vn
3. Pull image và chạy container
4. Cấu hình nginx làm reverse proxy:

```nginx
server {
    listen 80;
    server_name viecvietnam.vn;

    location / {
        proxy_pass http://localhost:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

5. Cấu hình SSL với Let's Encrypt (certbot)

## Công nghệ

- **Frontend**: HTML5, CSS3, Vanilla JavaScript
- **Visualization**: D3.js v7.8.5
- **Fonts**: IBM Plex Sans, IBM Plex Mono (Google Fonts)
- **Server**: Nginx Alpine
