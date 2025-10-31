
# 🧩 ĐỒ ÁN MÔN HỌC – HỆ THỐNG MICROSERVICES TRIỂN KHAI VỚI DOCKER & GITHUB ACTIONS

## 🧭 1. Giới thiệu dự án

Dự án này được xây dựng nhằm mô phỏng **kiến trúc hệ thống microservices** có thể mở rộng, gồm nhiều dịch vụ nhỏ giao tiếp với nhau thông qua **API Gateway**, sử dụng **Docker** để triển khai và **GitHub Actions** để thực hiện CI/CD tự động.

Mục tiêu:
- Hiểu rõ cách tổ chức hệ thống theo hướng microservice.
- Thực hiện triển khai dịch vụ trên Docker.
- Thiết lập CI/CD tự động thông qua GitHub Actions.
- Đáp ứng đầy đủ 10 tiêu chí đánh giá trong đề bài.

---

## 🏗️ 2. Kiến trúc hệ thống

### Tổng quan
Hệ thống gồm **4 dịch vụ chính** và các thành phần hỗ trợ:

| Thành phần | Mô tả chức năng |
|-------------|----------------|
| **Auth Service** | Xử lý xác thực người dùng, gồm các chức năng: đăng ký (`register`), đăng nhập (`login`), và truy cập trang tổng quan (`dashboard`). Sử dụng **JWT** để xác thực. |
| **Product Service** | Quản lý sản phẩm: tạo mới sản phẩm, lấy danh sách sản phẩm, tìm sản phẩm theo `id`, và gửi yêu cầu tạo đơn hàng đến Order Service. |
| **Order Service** | Tiếp nhận yêu cầu đặt hàng từ Product Service và lưu thông tin đơn hàng. |
| **API Gateway** | Làm nhiệm vụ định tuyến request từ client đến các service tương ứng. |

### Thành phần phụ trợ
- **MongoDB**: Lưu trữ dữ liệu người dùng, sản phẩm và đơn hàng.  
- **RabbitMQ**: Là message broker giúp các service giao tiếp bất đồng bộ.  
- **Docker**: Dùng để container hoá các service.  
- **GitHub Actions**: Dùng để kiểm thử (CI) và build image Docker tự động (CD).

---

## ⚙️ 3. Cấu trúc thư mục

````markdown
EProject-Phase-1/
│
├── .github/workflows/
│   ├── docker-build.yml        # Workflow build và push image Docker
│   └── test.yml                # Workflow test tự động (CI)
│
├── api-gateway/
│   ├── Dockerfile
│   ├── index.js
│   └── package.json
│
├── auth/
│   ├── src/
│   ├── Dockerfile
│   ├── .env
│   └── package.json
│
├── product/
│   ├── src/
│   ├── Dockerfile
│   ├── .env
│   └── package.json
│
├── order/
│   ├── src/
│   ├── Dockerfile
│   ├── .env
│   └── package.json
│
├── public/results/              # Hình ảnh test kết quả Postman & Docker
│   ├── auth-register.png
│   ├── auth-login.png
│   ├── products-get.png
│   ├── products-get-id.png
│   ├── docker-containers.png
│   └── ...
│
├── docker-compose.yml
├── .gitignore
└── README.md
````

---

## 🧩 4. Cách hoạt động hệ thống

Các service được liên kết thông qua **Docker network**.
API Gateway định tuyến đến từng service theo URL:

| Endpoint        | Dịch vụ đích                |
| --------------- | --------------------------- |
| `/auth/...`     | Auth Service (port 3000)    |
| `/products/...` | Product Service (port 3001) |
| `/orders/...`   | Order Service (port 3002)   |

Ví dụ:
`http://localhost:3003/auth/login` sẽ tự động được gateway định tuyến đến `auth` container.

---

## 🐳 5. Chạy dự án bằng Docker

### Yêu cầu

* Cài đặt **Docker Desktop**
* Đảm bảo các file `.env` tồn tại trong từng service.

### Cách chạy

```bash
docker-compose up --build
```

Sau khi build xong, các container sẽ tự động chạy:

* MongoDB: `localhost:27017`
* RabbitMQ: `localhost:15672` (UI quản lý)
* Auth: `localhost:3000`
* Product: `localhost:3001`
* Order: `localhost:3002`
* API Gateway: `localhost:3003`

### Minh chứng

Xem hình ảnh test trong thư mục:
📂 `public/results/` (ảnh Postman và Docker containers đang chạy).

---

## 🧪 6. Kiểm thử chức năng (POSTMAN)

Các API chính đã được test thành công:

| Service     | API                                                | Kết quả    |
| ----------- | -------------------------------------------------- | ---------- |
| **Auth**    | `/auth/register`, `/auth/login`, `/auth/dashboard` | Thành công |
| **Product** | `/products`, `/products/:id`, `/products/create`   | Thành công |
| **Order**   | `/orders` (nhận dữ liệu từ Product service)        | Thành công |

Ảnh minh chứng test:
`public/results/auth-register.png`, `products-get.png`, `products-post-order-1.png`, ...

---

## ⚙️ 7. Thiết lập CI/CD (GitHub Actions)

Hệ thống có hai workflow chính trong `.github/workflows/`:

### 🔹 1. `test.yml` – Continuous Integration (CI)

Tự động chạy khi có **push** hoặc **pull request**:

* Tạo file `.env` cho từng service.
* Cài dependencies.
* Chạy `npm test` cho các service `auth` và `product`.

### 🔹 2. `docker-build.yml` – Continuous Deployment (CD)

* Build image Docker của từng service.
* Có thể cấu hình để push lên **Docker Hub** hoặc deploy tự động.

Ví dụ cấu trúc `docker-build.yml`:

```yaml
name: Build Docker Images
on: [push]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      - name: Build Docker images
        run: docker-compose build
```

---

Tuyệt vời 👍 Dưới đây là **phần 8** được viết lại **đúng theo định dạng và phong cách trình bày** trong README của bạn (dấu #, emoji, markdown table, bullet points...), bạn chỉ cần **copy & paste** vào file là dùng được ngay:

---

## 🚀 8. Liên kết CI/CD với Docker (Tự động Test – Build – Deploy)

Toàn bộ quy trình **CI/CD** được triển khai thông qua file
📄 `.github/workflows/ci-cd.yml`, bao gồm **3 giai đoạn chính**:

1. 🧪 **Test (Kiểm thử tự động)**
2. 🐳 **Build & Push Docker Image**
3. 💻 **Deploy (Triển khai tự động)**

---

### 🧪 Giai đoạn 1 – Unit Test (MongoDB + RabbitMQ)

* Workflow tự động kích hoạt khi có **push lên nhánh `main`**.
* Thiết lập môi trường test với **MongoDB** và **RabbitMQ**.
* Tạo file `.env` cho từng service (`auth` và `product`) để có thể chạy test độc lập.
* Cài đặt dependencies và chạy lệnh `npm test`:

  * **Auth Service:** kiểm thử các API `/register`, `/login`, `/dashboard`.
  * **Product Service:** kiểm thử các API `/products`, `/products/:id`, `/products/create`.
* Dịch vụ **Auth** được khởi chạy nền để **Product** có thể gửi request xác thực.
* Workflow cũng tự động:

  * Chờ MongoDB và RabbitMQ sẵn sàng.
  * Đăng ký user test bằng `curl`.
  * Ghi log để hỗ trợ debug khi test thất bại.

➡️ **Kết quả:** Nếu toàn bộ test thành công, pipeline sẽ chuyển sang bước build Docker.

---

### 🐳 Giai đoạn 2 – Build & Push Docker Image

* Sử dụng **Docker Buildx** để build đa nền tảng (`linux/amd64`, `linux/arm64`).
* Đăng nhập Docker Hub bằng **secrets**:

  * `DOCKERHUB_USERNAME`
  * `DOCKERHUB_TOKEN`
* Tiến hành build tuần tự cho **4 service chính**:

  * `api-gateway`
  * `auth`
  * `product`
  * `order`
* Mỗi image được gắn 2 tag:

  * `latest`
  * `sha-<commit_id>` (đại diện cho phiên bản commit cụ thể)
* Sau khi build xong, image được **push lên Docker Hub** tương ứng.

➡️ **Kết quả:** Các image Docker mới nhất của từng service có sẵn trên Docker Hub, sẵn sàng deploy.

---

### 💻 Giai đoạn 3 – Deploy tự động (Windows self-hosted runner)

* Giai đoạn này chạy trên **Windows self-hosted runner**, giúp triển khai tự động tại môi trường cục bộ.
* Các bước thực hiện:

  1. Kiểm tra môi trường Docker (`docker --version`, `docker compose version`)
  2. Đăng nhập Docker Hub
  3. Dừng container cũ và xoá orphan container
  4. Pull image mới nhất cho từng service (`api-gateway`, `auth`, `product`, `order`)
  5. Sinh file `.env` động cho `docker-compose`
  6. Khởi chạy lại toàn bộ hệ thống:

     ```cmd
     docker compose up -d --remove-orphans --force-recreate
     ```
  7. Kiểm tra container đang chạy (`docker ps`)

➡️ **Kết quả:** Hệ thống được tự động deploy lại với phiên bản mới nhất ngay khi có commit mới.

---

### 🧭 Tóm tắt quy trình CI/CD

| Giai đoạn           | Công cụ sử dụng                      | Mục tiêu                          | Trạng thái |
| ------------------- | ------------------------------------ | --------------------------------- | ---------- |
| **1. Test**         | MongoDB, RabbitMQ, Mocha, Chai       | Kiểm thử chức năng các service    | ✅ Tự động  |
| **2. Build & Push** | Docker, GitHub Actions               | Build image và đẩy lên Docker Hub | ✅ Tự động  |
| **3. Deploy**       | Docker Compose (Windows self-hosted) | Triển khai phiên bản mới nhất     | ✅ Tự động  |

---

### 🔐 Secrets được sử dụng

| Tên biến              | Mô tả                                   |
| --------------------- | --------------------------------------- |
| `DOCKERHUB_USERNAME`  | Tên tài khoản Docker Hub                |
| `DOCKERHUB_TOKEN`     | Token để đăng nhập Docker Hub           |
| `JWT_SECRET`          | Khóa bí mật JWT dùng trong Auth Service |
| `LOGIN_TEST_USER`     | Tài khoản test tự động                  |
| `LOGIN_TEST_PASSWORD` | Mật khẩu test tự động                   |

---

### 📦 Lợi ích đạt được

* Tự động hóa toàn bộ quy trình **test → build → deploy**.
* Giảm thiểu lỗi thủ công khi triển khai và build image.
* Đảm bảo các service hoạt động nhất quán giữa môi trường dev và deploy.
* Dễ dàng mở rộng để triển khai lên **VPS hoặc cloud** trong tương lai.

---

## 📊 9. Kết quả kiểm thử & đánh giá

* ✅ Hệ thống chạy ổn định trên Docker Desktop.
* ✅ Tất cả các chức năng đăng ký, đăng nhập, quản lý sản phẩm, tạo đơn hàng hoạt động tốt.
* ✅ CI/CD đã hoạt động, tự động test và build thành công.

Ảnh minh chứng:

* `public/results/docker-containers.png` → Tất cả container hoạt động.
* `public/results/*.png` → Kết quả test bằng Postman.

---

## 📚 11. Kết luận

Dự án đã hoàn thiện các yêu cầu:

* Xây dựng hệ thống microservices (Auth, Product, Order, API Gateway).
* Triển khai và kiểm thử thành công bằng Docker.
* Thiết lập CI/CD qua GitHub Actions.

Dự án thể hiện kiến thức tổng hợp về:

* Node.js, Express, MongoDB, RabbitMQ
* Docker & docker-compose
* GitHub Actions CI/CD
* Kiến trúc microservices và tích hợp API Gateway.

---

**📅 Sinh viên thực hiện:** *Trần Kim Trọng*
**📘 Môn học:** Lập trình dịch vụ



