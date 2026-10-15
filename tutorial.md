# 🚀 CHEAT SHEET THI DEVOPS (TỐI ƯU 100/100 ĐIỂM)

> Tài liệu tóm tắt ngắn gọn, dễ tra cứu nhất khi làm bài thi hoặc ôn luyện mọi đề DevOps Spring Boot + MySQL + Nginx.

---

## 📌 I. BẢNG TRA CỨU NHANH (TÁI SỬ DỤNG vs THAY ĐỔI)

| Thành phần | Hành động | Chi tiết cần nhớ |
| :--- | :---: | :--- |
| **`Dockerfile`** | ♻️ **Tái sử dụng 100%** | Copy nguyên vẹn từ bài trước. Mọi đề Spring Boot 4 đều dùng chung. |
| **`docker-compose.yml`** | ♻️ **Tái sử dụng 100%** | Copy nguyên vẹn TODO 1–5 đã điền. Đã dùng biến môi trường tự động. |
| **Server & Khóa SSH** | ♻️ **Tái sử dụng 100%** | Server đã cài Docker + Git. Public key đã nạp sẵn trong `/home/<user>/.ssh`. |
| **`student.env`** | ✏️ **Sửa 3 dòng** | `EXAM_CODE="00x"`, `COMPOSE_PROJECT_NAME="<tên>-de00x"`, `MYSQL_DATABASE="ptit_<tên>_db"`. |
| **`nginx/default.conf`** | ✏️ **Sửa 1 dòng** | Dòng 28: Đổi `/api/labs` thành `/api/<tên-api-của-đề>`. |
| **`ci-cd.yml`** | ✏️ **Sửa 1 dòng** | Dòng 65 (Smoke test): Đổi `/api/labs` thành `/api/<tên-api-của-đề>`. |
| **GitHub Repo & Secrets** | 🆕 **Tạo mới mỗi đề** | Repo Public `devops-final-de00x-<tên>`, thêm 3 Secrets và bật Workflow permissions. |

---

## 🛠️ II. CHUẨN BỊ TRƯỚC KHI VÀO PHÒNG THI (Làm 1 lần duy nhất)

### 1. Chuẩn bị trên Server (Đã xong cho server của bạn)
Lệnh khởi tạo môi trường và user thi trên máy ảo Ubuntu:
```bash
# Cài đặt docker, git, curl
DEBIAN_FRONTEND=noninteractive apt-get update && \
DEBIAN_FRONTEND=noninteractive apt-get install -y git docker.io docker-compose-v2 curl && \
systemctl enable --now docker

# Tạo user thi (ví dụ toanhtuan) cấp quyền docker
useradd -m -s /bin/bash toanhtuan
echo "toanhtuan:ToAnhTuan18@" | chpasswd
usermod -aG sudo,docker toanhtuan

# Nạp SSH Public key cho user
mkdir -p /home/toanhtuan/.ssh && chmod 700 /home/toanhtuan/.ssh
echo "<NỘI_DUNG_deploy_key.pub>" >> /home/toanhtuan/.ssh/authorized_keys
chmod 600 /home/toanhtuan/.ssh/authorized_keys
chown -R toanhtuan:toanhtuan /home/toanhtuan/.ssh
```

### 2. Chuẩn bị trên Máy tính cá nhân
* **Lưu cặp khóa SSH**: Lưu file private key `deploy_key` vào Notepad để sẵn sàng copy paste vào GitHub Secrets.
* **Lưu file mẫu chuẩn**: Lưu sẵn 2 file [Dockerfile](file:///f:/RikkeiEdu/IT209_DevOps/OnTap_final/devops-final-app-de001/Dockerfile) và [docker-compose.yml](file:///f:/RikkeiEdu/IT209_DevOps/OnTap_final/devops-final-app-de001/docker-compose.yml) vào 1 thư mục riêng.

---

## ⚡ III. QUY TRÌNH 5 BƯỚC THẦN TỐC KHI NHẬN ĐỀ MỚI (5 - 10 PHÚT)

### Bước 1: Giải nén & Copy file mẫu
1. Giải nén thư mục đề thi mới (ví dụ `devops-final-de002-toanhtuan`).
2. Copy đè 2 file mẫu chuẩn [Dockerfile](file:///f:/RikkeiEdu/IT209_DevOps/OnTap_final/devops-final-app-de001/Dockerfile) và [docker-compose.yml](file:///f:/RikkeiEdu/IT209_DevOps/OnTap_final/devops-final-app-de001/docker-compose.yml) vào thư mục gốc.

### Bước 2: Sửa thông số theo đề mới (Chỉ 3 file)
1. **`student.env`**:
   * `EXAM_CODE`: Số đề mới (ví dụ `"002"`).
   * `COMPOSE_PROJECT_NAME`: Ví dụ `"toanhtuan-de002"`.
   * `MYSQL_DATABASE`: Tên DB trong đề (ví dụ `"ptit_device_db"`).
2. **`nginx/default.conf`**:
   * Sửa dòng 28: `return 302 /api/<endpoint-đề-mới>;`
3. **`.github/workflows/ci-cd.yml`**:
   * Sửa dòng 65: `"$BASE/api/<endpoint-đề-mới>" | grep -q '"id"'`

### Bước 3: Tạo Repo & Cấu hình GitHub
1. Vào [github.com/new](https://github.com/new) tạo repo: `devops-final-de00x-<tên>` (chế độ **Public**, không tick README).
2. Vào **Settings** → **Secrets and variables** → **Actions** → Thêm 3 Secrets:
   * `SERVER_HOST`: IP server (ví dụ `221.121.4.142`)
   * `SERVER_USER`: User của bạn (ví dụ `toanhtuan`)
   * `SERVER_SSH_KEY`: Nội dung private key trong file `deploy_key`.
3. Vào **Settings** → **Actions** → **General** → cuộn xuống **Workflow permissions** chọn **Read and write permissions** → Bấm **Save**.

### Bước 4: Push code lên GitHub
Mở terminal tại thư mục đề mới chạy:
```bash
git init -b main
git remote add origin https://github.com/ToAnhTuan1806/devops-final-de00x-toanhtuan.git
git add .
git commit -m "feat: complete DevOps exam setup"
git push -u origin main
```

### Bước 5: Chụp 4 ảnh minh chứng vào thư mục `screenshots/`
* `01-actions.png`: Tab Actions GitHub xanh cả 2 job (`Build & Test` và `Deploy lên server`).
* `02-ghcr.png`: Mục Packages trên GitHub hiển thị 2 tag: commit SHA và `latest`.
* `03-compose-ps.png`: Chạy `docker compose --env-file student.env ps` trên server (cả 3 container đều Up/healthy).
* `04-website.png`: Mở trình duyệt `http://<IP>:<APP_PORT>/api/...` hiển thị JSON.

---

## 📋 IV. CODE MẪU BỎ TÚI (DỄ COPY-PASTE)

### 1. Dockerfile (Multi-stage build)
```dockerfile
# Stage 1: Build
FROM gradle:8.14-jdk21-alpine AS build
WORKDIR /workspace
COPY build.gradle settings.gradle ./
RUN gradle dependencies --no-daemon -q
COPY src ./src
RUN gradle bootJar -x test --no-daemon -q

# Stage 2: Runtime
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

### 2. Các TODO trong docker-compose.yml
```yaml
  app:
    # TODO 1: build context . và build-arg APP_VERSION
    build:
      context: .
      args:
        APP_VERSION: ${APP_VERSION:-local}
    # TODO 2: tên image theo biến
    image: ${APP_IMAGE:-devops-final-app}:${APP_TAG:-latest}
    restart: unless-stopped
    environment:
      DB_HOST: mysql
      DB_PORT: "3306"
      # TODO 3: lấy từ student.env
      DB_NAME: ${MYSQL_DATABASE}
      DB_USER: ${MYSQL_USER}
      DB_PASSWORD: ${MYSQL_PASSWORD}
      TZ: Asia/Ho_Chi_Minh
...
  nginx:
    # TODO 4: public cổng APP_PORT
    ports:
      - "${APP_PORT}:80"
    # TODO 5: mount read-only
    volumes:
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf:ro
```

### 3. Các TODO trong ci-cd.yml
```yaml
# TODO 1 (job build):
run: docker compose --env-file student.env up -d --build --wait --wait-timeout 300

# TODO 2 (job build): Đăng nhập GHCR
- name: Đăng nhập GitHub Container Registry
  uses: docker/login-action@v3
  with:
    registry: ${{ env.REGISTRY }}
    username: ${{ github.actor }}
    password: ${{ secrets.GITHUB_TOKEN }}

# TODO 3 (job build): Push 2 tag
run: |
  docker push "$IMAGE:${{ github.sha }}"
  docker tag "$IMAGE:${{ github.sha }}" "$IMAGE:latest"
  docker push "$IMAGE:latest"

# TODO 4 (job deploy):
needs: build

# TODO 5 (job deploy): SSH secrets
with:
  host: ${{ secrets.SERVER_HOST }}
  username: ${{ secrets.SERVER_USER }}
  key: ${{ secrets.SERVER_SSH_KEY }}

# TODO 6 (job deploy): Pull và up -d trên server
docker compose --env-file student.env pull app
docker compose --env-file student.env up -d --no-build --wait --wait-timeout 300
```
