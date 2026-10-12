# BÀI 4: LƯU TRỮ DỮ LIỆU ĐƠN GIẢN VỚI DOCKER VOLUME

- **Môn học:** DevOps / Containerization
- **Bài thực hành:** Session 09 - Bài 4
- **Đường dẫn nộp bài:** `homework/session_09/ex4/README.md`
- **Mục tiêu:** Tạo Named Volume để lưu trữ dữ liệu bền vững (persistence) độc lập với vòng đời của container, chứng minh dữ liệu vẫn toàn vẹn ngay cả khi container bị hủy hoàn toàn (`--rm`).

---

## 1. Yêu cầu & Ràng buộc

1. Tạo một Named Volume có tên là `easy-volume`.
2. Kiểm tra danh sách volume hiện có với lệnh `docker volume ls`.
3. Sử dụng một container Alpine tạm thời ghi dữ liệu vào Volume gắn tại thư mục `/data`.
4. Sau khi container thứ nhất kết thúc và bị hủy bỏ (`--rm`), sử dụng một container Alpine thứ hai mount cùng Volume để đọc dữ liệu.
5. Kết quả mong đợi: Đọc thành công nội dung đã ghi: `Luu tru du lieu Docker`.

---

## 2. Các bước thực hiện & Kết quả Terminal

### Bước 1: Tạo Named Volume `easy-volume`

Lệnh thực hiện:
```bash
docker volume create easy-volume
```

**Kết quả đầu ra (Terminal Output):**
```text
easy-volume
```

---

### Bước 2: Kiểm tra danh sách Volume trên máy

Lệnh thực hiện:
```bash
docker volume ls
```

**Kết quả đầu ra (Terminal Output):**
```text
DRIVER    VOLUME NAME
local     easy-volume
```

> **Ghi chú:** Volume `easy-volume` đã được tạo thành công với driver mặc định là `local`.

---

### Bước 3: Dùng container Alpine thứ nhất ghi dữ liệu vào Volume

Lệnh thực hiện:
```bash
docker run --rm -v easy-volume:/data alpine sh -c "echo 'Luu tru du lieu Docker' > /data/test.txt"
```

**Kết quả đầu ra (Terminal Output):**
```text
Unable to find image 'alpine:latest' locally
latest: Pulling from library/alpine
43cba3144886: Pull complete 
Digest: sha256:beefdbd8a1da6d2915566fde36db9db0b524eb737fc57cd13675d29282846973
Status: Downloaded newer image for alpine:latest
```

> **Giải thích:**
> - `-v easy-volume:/data`: Gắn volume `easy-volume` vào đường dẫn `/data` bên trong container.
> - `sh -c "echo 'Luu tru du lieu Docker' > /data/test.txt"`: Ghi chuỗi văn bản vào file `/data/test.txt`.
> - Tham số `--rm`: Container sẽ tự động bị xóa ngay sau khi lệnh chạy xong.

---

### Bước 4: Dùng container Alpine thứ hai đọc lại tệp từ Volume

Lệnh thực hiện:
```bash
docker run --rm -v easy-volume:/data alpine cat /data/test.txt
```

**Kết quả đầu ra (Terminal Output):**
```text
Luu tru du lieu Docker
```

> **Kết quả kiểm tra:** Container thứ hai hoàn toàn mới, nhưng do cùng mount vào `easy-volume:/data` nên đọc được nguyên vẹn nội dung file `test.txt` do container trước tạo ra. Điều này chứng minh dữ liệu được lưu trữ độc lập trên Volume của Host chứ không phụ thuộc vào vòng đời của container.

---

### Bước 5: Dọn dẹp tài nguyên (Tùy chọn)

Sau khi hoàn thành thử nghiệm, xóa volume để giải phóng tài nguyên:
```bash
docker volume rm easy-volume
```

**Kết quả đầu ra:**
```text
easy-volume
```

---

## 3. Tổng kết đánh giá

| Tiêu chí | Lệnh thực hiện | Kết quả | Trạng thái |
| :--- | :--- | :--- | :---: |
| Tạo Named Volume | `docker volume create easy-volume` | Volume `easy-volume` được tạo | Đạt |
| Liệt kê Volume | `docker volume ls` | Hiển thị `local easy-volume` | Đạt |
| Ghi dữ liệu vào Volume | `docker run --rm -v easy-volume:/data alpine sh -c "..."` | Container ghi file rồi tự hủy (`--rm`) | Đạt |
| Đọc dữ liệu từ container mới | `docker run --rm -v easy-volume:/data alpine cat /data/test.txt` | In ra: `Luu tru du lieu Docker` | Đạt |
| Tính toàn vẹn dữ liệu | Persistent Storage | Dữ liệu không bị mất khi container bị xóa | Đạt |
