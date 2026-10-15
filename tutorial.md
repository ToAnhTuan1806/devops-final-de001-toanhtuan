# 🎯 CẨM NANG THI DEVOPS THỰC CHIẾN (CHINH PHỤC 100/100)

> **Tài liệu tóm tắt toàn bộ quy trình, mốc thời gian 60 phút và code mẫu chuẩn cho mọi đề thi DevOps Spring Boot.**

---

## ⏱️ I. KỊCH BẢN PHÂN BỔ THỜI GIAN (60 PHÚT LÀM BÀI)

```text
[00 - 05p]: Đọc đề, giải nén & điền student.env  (Phần 1: 10đ)
[05 - 10p]: Copy Dockerfile, Compose & sửa Nginx  (Phần 2+3: 45đ)
[10 - 20p]: Tạo repo, thêm Secrets & push CI/CD  (Phần 4: 35đ)
[20 - 30p]: Chụp 4 ảnh minh chứng & nộp link    (Phần 5: 10đ)
[30 - 60p]: Kiểm tra lại, giữ server chạy cho tới khi có điểm.
```

---

## 📌 II. BẢNG TRA CỨU NHANH (TÁI SỬ DỤNG vs SỬA ĐỔI)

| Thành phần | Trạng thái | Việc cần làm khi gặp đề mới |
| :--- | :---: | :--- |
| **`Dockerfile`** | ♻️ **Dùng lại 100%** | Copy nguyên vẹn từ mẫu chuẩn (Spring Boot 4, JRE 21, user `spring`). |
| **`docker-compose.yml`** | ♻️ **Dùng lại 100%** | Copy nguyên vẹn TODO 1–5 đã điền. Biến môi trường tự động đồng bộ. |
| **Server & Khóa SSH** | ♻️ **Dùng lại 100%** | Dùng lại server hiện tại (đã cài Docker, Git và user `toanhtuan`). |
| **`student.env`** | ✏️ **Sửa 3 dòng** | `EXAM_CODE="00x"`, `COMPOSE_PROJECT_NAME="toanhtuan-de00x"`, `MYSQL_DATABASE="ptit_<tên>_db"`. |
| **`nginx/default.conf`** | ✏️ **Sửa 1 dòng** | Dòng 28: Đổi `/api/labs` thành `/api/<tên-api-trong-đề>`. |
| **`ci-cd.yml`** | ✏️ **Sửa 1 dòng** | Dòng 65 (Smoke test): Đổi `/api/labs` thành `/api/<tên-api-trong-đề>`. |
| **GitHub Repo** | 🆕 **Tạo mới** | Tạo repo Public `devops-final-de00x-toanhtuan`, nạp 3 Secrets, bật Workflow permissions. |

---

## ⚡ III. QUY TRÌNH 5 BƯỚC LÀM BÀI CHI TIẾT

### Bước 1: Đọc đề & Điền file `student.env` (Phút 0 – 5)
1. Tải đề thi từ RAIA về và giải nén.
2. Mở file `student.env` điền đầy đủ các giá trị:
   * Họ tên: `STUDENT_NAME="Tô Anh Tuấn"`
   * Mã SV: `STUDENT_ID="PTIT-HN-100"`
   * Lớp: `STUDENT_CLASS="K24CNTT2"`
   * Email: `STUDENT_EMAIL="toanhtuan.18dz@gmail.com"`
   * GitHub: `GITHUB_USERNAME="ToAnhTuan1806"`
   * **Mã đề**: `EXAM_CODE="00x"` *(ví dụ "002")*
   * **Cổng cá nhân**: `APP_PORT="8081"` *(từ 8080 trở lên)*
   * **Tên project**: `COMPOSE_PROJECT_NAME="toanhtuan-de00x"` *(chữ thường)*
   * **Database**: `MYSQL_DATABASE="ptit_<tên>_db"` *(theo đúng đề bài)*
   * Tài khoản DB: `MYSQL_USER="app_user"`, `MYSQL_PASSWORD="AppPass@2026"`, `MYSQL_ROOT_PASSWORD="RootPass@2026"`

---

### Bước 2: Copy code mẫu & Sửa đúng 2 file (Phút 5 – 10)
1. Copy đè 2 file mẫu chuẩn vào thư mục gốc bài thi mới:
   * File [Dockerfile](file:///f:/RikkeiEdu/IT209_DevOps/OnTap_final/devops-final-app-de001/Dockerfile)
   * File [docker-compose.yml](file:///f:/RikkeiEdu/IT209_DevOps/OnTap_final/devops-final-app-de001/docker-compose.yml)
2. Mở file [nginx/default.conf](file:///f:/RikkeiEdu/IT209_DevOps/OnTap_final/devops-final-app-de001/nginx/default.conf), sửa dòng 28:
   ```nginx
   return 302 /api/<tên-api-trong-đề>;
   ```
3. Mở file [.github/workflows/ci-cd.yml](file:///f:/RikkeiEdu/IT209_DevOps/OnTap_final/devops-final-app-de001/.github/workflows/ci-cd.yml), sửa dòng 65:
   ```bash
   curl -fsS --retry 12 --retry-delay 5 --retry-all-errors "$BASE/api/<tên-api-trong-đề>" | grep -q '"id"'
   ```

---

### Bước 3: Tạo Repo GitHub & Cấu hình (Phút 10 – 15)
1. Vào [github.com/new](https://github.com/new) tạo repo: `devops-final-de00x-toanhtuan` (**Public**, không tick README).
2. Vào **Settings** → **Secrets and variables** → **Actions** → Thêm 3 Secrets:
   * `SERVER_HOST`: `221.121.4.142` *(hoặc IP server của bạn)*
   * `SERVER_USER`: `toanhtuan`
   * `SERVER_SSH_KEY`: Nội dung private key trong file `deploy_key`.
3. Vào **Settings** → **Actions** → **General** → cuộn xuống **Workflow permissions** chọn **Read and write permissions** → Bấm **Save**.

---

### Bước 4: Push code kích hoạt CI/CD (Phút 15 – 20)
Mở terminal tại thư mục đề thi mới:
```bash
git init -b main
git remote add origin https://github.com/ToAnhTuan1806/devops-final-de00x-toanhtuan.git
git add .
git commit -m "feat: complete DevOps final exam"
git push -u origin main
```
*Vào tab **Actions** trên GitHub xem 2 job `Build & Test` và `Deploy lên server` chạy xanh lá.*

---

### Bước 5: Chụp 4 ảnh minh chứng & Nộp bài (Phút 20 – 30)

Lưu đủ 4 ảnh vào thư mục `screenshots/`:
* [ ] **`01-actions.png`**: Tab Actions GitHub hiển thị run trên nhánh `main` thành công cả 2 job `Build & Test` và `Deploy lên server`.
* [ ] **`02-ghcr.png`**: Mục Packages của repo trên GitHub hiển thị package `app` với 2 tag: commit SHA và `latest`.
* [ ] **`03-compose-ps.png`**: Terminal SSH server gõ lệnh `cd ~/devops-final-de00x-toanhtuan && docker compose --env-file student.env ps` (thấy 3 service `app`, `mysql`, `nginx` đang Up/healthy).
* [ ] **`04-website.png`**: Mở trình duyệt `http://<IP_SERVER>:<APP_PORT>/api/...` hiển thị danh sách dạng JSON.

Sau khi chụp xong, đẩy ảnh lên GitHub và copy vào thư mục nộp bài:
```bash
git add screenshots/
git commit -m "docs: add screenshots"
git push origin main
```
* Copy các file minh chứng vào thư mục `submission-content/`.
* Nộp **link repository GitHub (Public)** lên hệ thống RAIA.

---

## 📋 IV. CODE MẪU CHUẨN ĐÃ ĐIỀN ĐẦY ĐỦ (COPY DÙNG NGAY)

### 1. Dockerfile (25/25 điểm)
```dockerfile
# syntax=docker/dockerfile:1
# ---------- Stage 1: build ----------
FROM gradle:8.14-jdk21-alpine AS build
WORKDIR /workspace
COPY build.gradle settings.gradle ./
RUN gradle dependencies --no-daemon -q
COPY src ./src
RUN gradle bootJar -x test --no-daemon -q

# ---------- Stage 2: runtime ----------
FROM eclipse-temurin:21-jre-alpine AS runtime
ARG APP_VERSION=dev
ENV APP_VERSION=${APP_VERSION} JAVA_TOOL_OPTIONS="-XX:MaxRAMPercentage=75"
WORKDIR /app
RUN addgroup -S spring && adduser -S spring -G spring
COPY --from=build --chown=spring:spring /workspace/build/libs/app.jar /app/app.jar
USER spring
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "/app/app.jar"]
```

### 2. docker-compose.yml (20/20 điểm)
```yaml
name: ${COMPOSE_PROJECT_NAME:?Chua dien COMPOSE_PROJECT_NAME trong student.env}

services:
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

  app:
    build:
      context: .
      args:
        APP_VERSION: ${APP_VERSION:-local}
    image: ${APP_IMAGE:-devops-final-app}:${APP_TAG:-latest}
    restart: unless-stopped
    environment:
      DB_HOST: mysql
      DB_PORT: "3306"
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

  nginx:
    image: nginx:1.27-alpine
    restart: unless-stopped
    ports:
      - "${APP_PORT}:80"
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

### 3. ci-cd.yml (35/35 điểm - các đoạn TODO)
```yaml
# TODO 1: Khởi động kiểm thử trên Runner
run: docker compose --env-file student.env up -d --build --wait --wait-timeout 300

# Smoke test (Sửa /api/... theo đề)
run: |
  set -a; source student.env; set +a
  BASE="http://localhost:${APP_PORT}"
  curl -fsS --retry 12 --retry-delay 5 --retry-all-errors "$BASE/api/labs" | grep -q '"id"'
  echo "Smoke test OK"

# TODO 2: Đăng nhập GHCR
- name: Đăng nhập GitHub Container Registry
  uses: docker/login-action@v3
  with:
    registry: ${{ env.REGISTRY }}
    username: ${{ github.actor }}
    password: ${{ secrets.GITHUB_TOKEN }}

# TODO 3: Push cả 2 tag commit SHA và latest
run: |
  docker push "$IMAGE:${{ github.sha }}"
  docker tag "$IMAGE:${{ github.sha }}" "$IMAGE:latest"
  docker push "$IMAGE:latest"

# TODO 4: needs build
needs: build

# TODO 5: SSH với Secrets
with:
  host: ${{ secrets.SERVER_HOST }}
  username: ${{ secrets.SERVER_USER }}
  key: ${{ secrets.SERVER_SSH_KEY }}

# TODO 6: Kéo image và up -d trên server
docker compose --env-file student.env pull app
docker compose --env-file student.env up -d --no-build --wait --wait-timeout 300
```
