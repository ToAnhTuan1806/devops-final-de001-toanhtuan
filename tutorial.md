# CẨM NANG ÔN THI DEVOPS TOÀN DIỆN (CHINH PHỤC 100 ĐIỂM)
> **Tài liệu hướng dẫn triển khai cho mọi đề thi Hackathon DevOps Fundamentals**
> Tác giả: Tô Anh Tuấn - PTIT

---

## MỤC LỤC
1. [Tổng quan Kiến trúc Hệ thống](#1-tổng-quan-kiến-trúc-hệ-thống)
2. [Bảng đối chiếu: Phần Tái Sử Dụng vs Phần Cần Đổi](#2-bảng-đối-chiếu-phần-tái-sử-dụng-vs-phần-cần-đổi)
3. [Những việc CẦN CHUẨN BỊ TRƯỚC khi vào phòng thi](#3-những-việc-cần-chuẩn-bị-trước-khi-vào-phòng-thi)
4. [Quy trình Speedrun 5 phút khi nhận đề thi mới](#4-quy-trình-speedrun-5-phút-khi-nhận-đề-thi-mới)
5. [Chi tiết từng thành phần & Code mẫu chuẩn 100/100](#5-chi-tiết-từng-thành-phần--code-mẫu-chuẩn-100100)
   - [Phần 1: student.env (10 điểm)](#phần-1-studentenv-10-điểm)
   - [Phần 2: Dockerfile Multi-stage (25 điểm)](#phần-2-dockerfile-multi-stage-25-điểm)
   - [Phần 3: docker-compose.yml (20 điểm)](#phần-3-docker-composeyml-20-điểm)
   - [Phần 4: Nginx Reverse Proxy (default.conf)](#phần-4-nginx-reverse-proxy-defaultconf)
   - [Phần 5: CI/CD Pipeline với GitHub Actions (35 điểm)](#phần-5-cicd-pipeline-với-github-actions-35-điểm)
6. [Thiết lập Server & Cấu hình SSH User](#6-thiết-lập-server--cấu-hình-ssh-user)
7. [Checklist 4 ảnh minh chứng nộp bài (10 điểm)](#7-checklist-4-ảnh-minh-chứng-nộp-bài-10-điểm)

---

## 1. Tổng quan Kiến trúc Hệ thống

Mọi đề thi DevOps (Đề 001: Phòng Lab, Đề 002: Thiết bị, Đề 003: Sinh viên,...) đều tuân theo mô hình 3 lớp chuẩn:

```text
Trình duyệt ── http://<IP_máy_chủ>:<APP_PORT>
   ▼
[nginx]  nginx:1.27-alpine       GET /*  → app:8080 (Reverse proxy)
   ▼
[app]    Spring Boot 4 (JRE 21)  GET /api/<resource> → mysql:3306
   ▼
[mysql]  mysql:8.0               volume mysql_data, KHÔNG public cổng ra ngoài
```

---

## 2. Bảng đối chiếu: Phần Tái Sử Dụng vs Phần Cần Đổi

| Thành phần | Trạng thái | Chi tiết |
| :--- | :---: | :--- |
| **`Dockerfile`** | **DÙNG LẠI 100%** | Multi-stage build Spring Boot 4 + Java 21 + Gradle, user non-root `spring`. Mọi đề đều dùng chung. |
| **`docker-compose.yml`** | **DÙNG LẠI 100%** | Toàn bộ TODO 1–5 đã được tham số hóa bằng biến môi trường `${APP_PORT}`, `${MYSQL_DATABASE}`... |
| **Hạ tầng Server & SSH Key** | **DÙNG LẠI 100%** | Server đã cài Docker, Docker Compose, Git và nạp public key sẵn. Không cần cài lại. |
| **`student.env`** | **THAY ĐỔI THEO ĐỀ** | Sửa `EXAM_CODE="00x"`, `COMPOSE_PROJECT_NAME="<tên>-de00x"`, `MYSQL_DATABASE="ptit_<tên>_db"`. |
| **`nginx/default.conf`** | **SỬA 1 DÒNG** | Sửa dòng `return 302 /api/labs;` thành `return 302 /api/<tên_api_của_đề>;`. |
| **`ci-cd.yml` (Smoke test)** | **SỬA 1 DÒNG** | Dòng 65 đổi endpoint `$BASE/api/labs` thành `$BASE/api/<tên_api_của_đề>`. |
| **GitHub Repo & Secrets** | **TẠO MỚI MỖI ĐỀ** | Tạo repo public `devops-final-de00x-<họ-tên>`, thêm 3 Secrets và bật Workflow permissions. |

---

## 3. Những việc CẦN CHUẨN BỊ TRƯỚC khi vào phòng thi

1. **Chuẩn bị Máy chủ (Server / VPS):**
   - Đã cài sẵn `docker`, `docker-compose-v2`, `git`.
   - Đã tạo user cá nhân (ví dụ: `toanhtuan`) thuộc nhóm `docker` và `sudo`.
   - Đã mở firewall cổng SSH (22) và các cổng ứng dụng từ **8080 đến 8090**.
2. **Lưu trữ cặp khóa SSH sẵn:**
   - File `deploy_key` (Private key) lưu ở máy tính cá nhân để sẵn sàng paste vào GitHub Secrets.
   - File `deploy_key.pub` (Public key) đã được append sẵn vào `/home/<user>/.ssh/authorized_keys` trên server.
3. **Lưu bộ "Code mẫu thần tốc" (3 file):**
   - Lưu sẵn `Dockerfile`, `docker-compose.yml`, `ci-cd.yml` chuẩn của bài hôm nay ra 1 thư mục riêng để khi thi chỉ việc copy paste.

---

## 4. Quy trình Speedrun 5 phút khi nhận đề thi mới

Khi giáo viên phát đề thi mới (ví dụ: `devops-final-de002.zip` - Đề tài: Quản lý thiết bị - API: `/api/devices` - DB: `ptit_device_db`):

* **Phút 1:** Giải nén đề thi vào máy tính.
* **Phút 2:** Copy đè 2 file `Dockerfile` và `docker-compose.yml` chuẩn vào thư mục đề mới.
* **Phút 3:** Sửa 3 chỗ theo đề mới:
  1. File `student.env`: Sửa `EXAM_CODE="002"`, `COMPOSE_PROJECT_NAME="toanhtuan-de002"`, `MYSQL_DATABASE="ptit_device_db"`.
  2. File `nginx/default.conf`: Đổi `return 302 /api/devices;`.
  3. File `.github/workflows/ci-cd.yml`: Copy file chuẩn vào, sửa dòng 65 thành `"$BASE/api/devices" | grep -q '"id"'`.
* **Phút 4:**
  1. Tạo repo GitHub mới: `devops-final-de002-toanhtuan` (**Public**).
  2. Thêm 3 Secrets (`SERVER_HOST`, `SERVER_USER`, `SERVER_SSH_KEY`) và bật **Read and write permissions**.
* **Phút 5:** Push code:
  ```bash
  git init -b main
  git remote add origin https://github.com/<username>/devops-final-de002-toanhtuan.git
  git add .
  git commit -m "feat: complete DevOps final exam"
  git push -u origin main
  ```
  Pipeline CI/CD tự chạy xanh cả 2 job → Chụp 4 ảnh minh chứng → **Nộp bài đạt 100/100!**

---

## 5. Chi tiết từng thành phần & Code mẫu chuẩn 100/100

### Phần 1: student.env (10 điểm)
Quy tắc: `KEY="giá trị"`, không có dấu cách quanh dấu `=`, mỗi dòng 1 biến.

```properties
# ---------- 1. Thông tin sinh viên ----------
STUDENT_NAME="Tô Anh Tuấn"
STUDENT_ID="PTIT-HN-100"
STUDENT_CLASS="K24CNTT2"
STUDENT_EMAIL="toanhtuan.18dz@gmail.com"
GITHUB_USERNAME="ToAnhTuan1806"

# ---------- 2. Thông số triển khai trên server chung ----------
EXAM_CODE="001"                         # SỬA THEO MÃ ĐỀ (001, 002,...)
APP_PORT="8081"                         # Cổng cá nhân (>= 8080)
COMPOSE_PROJECT_NAME="toanhtuan-de001"  # <tên-viết-tắt>-de<mã-đề> (chữ thường)

# ---------- 3. Cơ sở dữ liệu MySQL ----------
MYSQL_DATABASE="ptit_lab_db"            # SỬA THEO TÊN DB TRONG ĐỀ
MYSQL_USER="app_user"
MYSQL_PASSWORD="AppPass@2026"
MYSQL_ROOT_PASSWORD="RootPass@2026"
```

---

### Phần 2: Dockerfile Multi-stage (25 điểm)
*(Dùng lại 100% cho mọi đề Spring Boot 4 / Java 21 / Gradle)*

```dockerfile
# syntax=docker/dockerfile:1
# ---------- Stage 1: build - biên dịch & đóng gói bằng Gradle + JDK 21 ----------
FROM gradle:8.14-jdk21-alpine AS build
WORKDIR /workspace

# Tận dụng cache layer: copy cấu hình gradle và tải dependency trước
COPY build.gradle settings.gradle ./
RUN gradle dependencies --no-daemon -q

# Copy mã nguồn và đóng gói JAR
COPY src ./src
RUN gradle bootJar -x test --no-daemon -q

# ---------- Stage 2: runtime - chỉ chứa JRE 21 + file JAR ----------
FROM eclipse-temurin:21-jre-alpine AS runtime

ARG APP_VERSION=dev
ENV APP_VERSION=${APP_VERSION} \
    JAVA_TOOL_OPTIONS="-XX:MaxRAMPercentage=75"

WORKDIR /app

# Chạy bằng user thường (non-root) để đảm bảo bảo mật
RUN addgroup -S spring && adduser -S spring -G spring
COPY --from=build --chown=spring:spring /workspace/build/libs/app.jar /app/app.jar
USER spring

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "/app/app.jar"]
```

---

### Phần 3: docker-compose.yml (20 điểm)
*(Dùng lại 100% - Đã điền toàn bộ TODO 1 đến 5)*

```yaml
name: ${COMPOSE_PROJECT_NAME:?Chua dien COMPOSE_PROJECT_NAME trong student.env}

services:
  # ---------------------------------------------------------------- MySQL
  mysql:
    image: mysql:8.0
    restart: unless-stopped
    command: ["--character-set-server=utf8mb4", "--collation-server=utf8mb4_unicode_ci"]
    environment:
      MYSQL_DATABASE: ${MYSQL_DATABASE}
      MYSQL_USER: ${MYSQL_USER}
      MYSQL_PASSWORD: ${MYSQL_PASSWORD}
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
      TZ: Asia/Ho_Chi_Minh
    volumes:
      - mysql_data:/var/lib/mysql
      - ./database/init.sql:/docker-entrypoint-initdb.d/01-init.sql:ro
    healthcheck:
      test: ["CMD-SHELL", "mysqladmin ping -h 127.0.0.1 -u$$MYSQL_USER -p$$MYSQL_PASSWORD --silent"]
      interval: 10s
      timeout: 5s
      retries: 12
      start_period: 30s
    networks:
      - db-net

  # ---------------------------------------------------------------- Spring Boot
  app:
    # TODO 1: build context . và truyền build-arg APP_VERSION
    build:
      context: .
      args:
        APP_VERSION: ${APP_VERSION:-local}
    # TODO 2: image theo biến APP_IMAGE và APP_TAG
    image: ${APP_IMAGE:-devops-final-app}:${APP_TAG:-latest}
    restart: unless-stopped
    environment:
      DB_HOST: mysql
      DB_PORT: "3306"
      # TODO 3: lấy thông tin database từ student.env
      DB_NAME: ${MYSQL_DATABASE}
      DB_USER: ${MYSQL_USER}
      DB_PASSWORD: ${MYSQL_PASSWORD}
      TZ: Asia/Ho_Chi_Minh
    depends_on:
      mysql:
        condition: service_healthy
    networks:
      - db-net
      - proxy-net

  # ---------------------------------------------------------------- Nginx
  nginx:
    image: nginx:1.27-alpine
    restart: unless-stopped
    # TODO 4: publish cổng APP_PORT ra cổng 80 của container
    ports:
      - "${APP_PORT}:80"
    # TODO 5: mount nginx/default.conf read-only
    volumes:
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf:ro
    depends_on:
      - app
    networks:
      - proxy-net

networks:
  proxy-net:
  db-net:

volumes:
  mysql_data:
```

---

### Phần 4: Nginx Reverse Proxy (default.conf)
Sửa dòng `return 302 /api/<tên_api>;` tương ứng với đề thi:

```nginx
upstream spring_app {
    server app:8080;
}

server {
    listen      80;
    listen      [::]:80;
    server_name _;
    charset utf-8;

    # Trang chủ tự động chuyển tiếp tới API của đề thi
    location = / {
        return 302 /api/labs;    # <-- ĐỔI ĐƯỜNG DẪN NÀY NẾU ĐỀ KHÁC
    }

    location / {
        proxy_pass         http://spring_app;
        proxy_http_version 1.1;
        proxy_set_header   Host              $host;
        proxy_set_header   X-Real-IP         $remote_addr;
        proxy_set_header   X-Forwarded-For   $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
        proxy_connect_timeout 5s;
        proxy_read_timeout    30s;
    }
}
```

---

### Phần 5: CI/CD Pipeline với GitHub Actions (35 điểm)
*(File `.github/workflows/ci-cd.yml` - Chỉ cần lưu ý dòng 65)*

```yaml
name: CI/CD

on:
  push:
    branches: [main]
  workflow_dispatch:

env:
  REGISTRY: ghcr.io

jobs:
  build:
    name: Build & Test
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write
    outputs:
      image: ${{ steps.meta.outputs.image }}
    steps:
      - name: Checkout mã nguồn
        uses: actions/checkout@v4

      - name: Kiểm tra thông tin trong student.env
        run: |
          set -a; source student.env; set +a
          for v in STUDENT_NAME STUDENT_ID STUDENT_CLASS STUDENT_EMAIL GITHUB_USERNAME \
                   APP_PORT COMPOSE_PROJECT_NAME MYSQL_USER MYSQL_PASSWORD MYSQL_ROOT_PASSWORD; do
            if [ -z "${!v}" ]; then
              echo "::error file=student.env::Chưa điền $v trong student.env"
              exit 1
            fi
          done
          if [ "$APP_PORT" -lt 8080 ]; then
            echo "::error file=student.env::APP_PORT phải từ 8080 trở lên"
            exit 1
          fi
          echo "### Sinh viên: $STUDENT_NAME - $STUDENT_ID - $STUDENT_CLASS" >> "$GITHUB_STEP_SUMMARY"

      - name: Đặt tên image
        id: meta
        run: echo "image=${REGISTRY}/${GITHUB_REPOSITORY,,}/app" >> "$GITHUB_OUTPUT"

      # TODO 1: Docker compose build & up
      - name: Build & chạy thử bằng Docker Compose
        env:
          APP_IMAGE: ${{ steps.meta.outputs.image }}
          APP_TAG: ${{ github.sha }}
          APP_VERSION: ${{ github.sha }}
        run: docker compose --env-file student.env up -d --build --wait --wait-timeout 300

      # Smoke test: ĐỔI ĐƯỜNG DẪN /api/labs NẾU LÀ ĐỀ KHÁC
      - name: Smoke test
        run: |
          set -a; source student.env; set +a
          BASE="http://localhost:${APP_PORT}"
          curl -fsS --retry 12 --retry-delay 5 --retry-all-errors "$BASE/api/labs" | grep -q '"id"'
          echo "Smoke test OK"

      - name: In log khi lỗi
        if: failure()
        run: docker compose --env-file student.env logs --no-color

      # TODO 2: Đăng nhập GHCR
      - name: Đăng nhập GitHub Container Registry
        uses: docker/login-action@v3
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      # TODO 3: Push image SHA & latest
      - name: Push image lên GHCR
        env:
          IMAGE: ${{ steps.meta.outputs.image }}
        run: |
          docker push "$IMAGE:${{ github.sha }}"
          docker tag "$IMAGE:${{ github.sha }}" "$IMAGE:latest"
          docker push "$IMAGE:latest"

      - name: Dọn dẹp
        if: always()
        run: docker compose --env-file student.env down -v

  deploy:
    name: Deploy lên server
    # TODO 4: Chỉ chạy khi job build thành công
    needs: build
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: read
    steps:
      - name: Deploy qua SSH
        uses: appleboy/ssh-action@v1.2.0
        env:
          APP_IMAGE: ${{ needs.build.outputs.image }}
          APP_TAG: ${{ github.sha }}
          GHCR_USER: ${{ github.actor }}
          GHCR_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          REPO_URL: ${{ github.server_url }}/${{ github.repository }}.git
          DEPLOY_DIR: ${{ github.event.repository.name }}
        with:
          # TODO 5: Kết nối SSH bằng GitHub Secrets
          host: ${{ secrets.SERVER_HOST }}
          username: ${{ secrets.SERVER_USER }}
          key: ${{ secrets.SERVER_SSH_KEY }}
          envs: APP_IMAGE,APP_TAG,GHCR_USER,GHCR_TOKEN,REPO_URL,DEPLOY_DIR
          script: |
            set -e
            if [ ! -d "$HOME/$DEPLOY_DIR/.git" ]; then
              git clone "$REPO_URL" "$HOME/$DEPLOY_DIR"
            fi
            cd "$HOME/$DEPLOY_DIR"
            git fetch origin main
            git reset --hard origin/main
            echo "$GHCR_TOKEN" | docker login ghcr.io -u "$GHCR_USER" --password-stdin
            export APP_IMAGE APP_TAG
            # TODO 6: Kéo image và up -d không build trên server
            docker compose --env-file student.env pull app
            docker compose --env-file student.env up -d --no-build --wait --wait-timeout 300
            docker logout ghcr.io
            docker compose --env-file student.env ps
```

---

## 6. Thiết lập Server & Cấu hình SSH User

Lệnh chuẩn 1 lần duy nhất trên máy ảo Ubuntu mới tinh để cài môi trường & tạo user:

```bash
# 1. Cài đặt các gói cần thiết
DEBIAN_FRONTEND=noninteractive apt-get update && \
DEBIAN_FRONTEND=noninteractive apt-get install -y git docker.io docker-compose-v2 curl && \
systemctl enable --now docker

# 2. Tạo user cá nhân (ví dụ toanhtuan)
useradd -m -s /bin/bash toanhtuan
echo "toanhtuan:ToAnhTuan18@" | chpasswd
usermod -aG sudo,docker toanhtuan

# 3. Nạp SSH Public Key cho user
mkdir -p /home/toanhtuan/.ssh
chmod 700 /home/toanhtuan/.ssh
echo "<NỘI_DUNG_deploy_key.pub>" >> /home/toanhtuan/.ssh/authorized_keys
chmod 600 /home/toanhtuan/.ssh/authorized_keys
chown -R toanhtuan:toanhtuan /home/toanhtuan/.ssh
```

---

## 7. Checklist 4 ảnh minh chứng nộp bài (10 điểm)

Lưu đủ 4 ảnh vào thư mục `screenshots/`:

1. **`01-actions.png`**: Tab Actions hiển thị cả 2 job `Build & Test` và `Deploy lên server` màu xanh.
2. **`02-ghcr.png`**: Mục Packages của repo trên GitHub hiển thị package `app` với 2 tag: commit SHA và `latest`.
3. **`03-compose-ps.png`**: Chạy lệnh `docker compose --env-file student.env ps` trên server hiển thị đủ 3 container (`app`, `mysql`, `nginx`) trạng thái Up/healthy.
4. **`04-website.png`**: Trình duyệt mở link `http://<IP_SERVER>:<APP_PORT>/api/<endpoint>` hiển thị dữ liệu JSON.
