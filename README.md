<p align="center">
  <img src="logo.jpg" width="200" alt="LX Manhwa Logo">
</p>

<h1 align="center">LX MANHWA</h1>

<p align="center">
  <b>Web đọc manhwa · manga tự host trên Android / Termux</b><br>
  Tốc độ cực cao · Không cần server riêng · Chạy thẳng trên điện thoại
</p>

<p align="center">
  <img src="https://img.shields.io/badge/platform-Android%20%2F%20Termux-brightgreen" alt="Platform">
  <img src="https://img.shields.io/badge/backend-Flask%20%2F%20Python-blue" alt="Flask">
  <img src="https://img.shields.io/badge/tunnel-Cloudflare%20%7C%20zrok-orange" alt="Tunnel">
  <img src="https://img.shields.io/badge/version-v2%20Banner%20Gradient-purple" alt="Version">
  <img src="https://img.shields.io/badge/license-MIT-lightgrey" alt="License">
</p>

---

## Mục lục

- [Giới thiệu](#giới-thiệu)
- [Tính năng](#tính-năng)
- [Cấu trúc thư mục](#cấu-trúc-thư-mục)
- [Cài đặt & Khởi chạy](#cài-đặt--khởi-chạy)
- [Format truyện](#format-truyện)
- [Hệ thống API Key](#hệ-thống-api-key)
- [API Endpoints](#api-endpoints)
- [Hệ thống tài khoản](#hệ-thống-tài-khoản)
- [Tunnel & Deploy](#tunnel--deploy)
- [Hiệu suất](#hiệu-suất)
- [Thông tin](#thông-tin)
- [Giấy phép](#giấy-phép)

---

## Giới thiệu

**LX MANHWA** là template web đọc truyện tranh (manhwa / manga) tự host, được thiết kế để chạy trực tiếp trên **Android / Termux** mà không cần server riêng. Toàn bộ backend viết bằng Python Flask, giao diện tối ưu cho mobile, tích hợp sẵn tunnel để truy cập từ bên ngoài mạng.

> Nếu thấy hữu ích, hãy cho một ⭐ để ủng hộ nhé!

---

## Tính năng

### ⚡ Hiệu suất

- Cache RAM toàn bộ danh sách truyện, chapters và ảnh
- Gzip tự động mọi response qua `flask-compress` (level 1 — ưu tiên tốc độ)
- Pre-warm 3 giai đoạn khi khởi động — vào web ngay, không cold start
- Background cache refresh mỗi 90 giây, không bao giờ stale
- Prefetch blob ảnh bìa + 10 ảnh đầu chương tiếp theo

### 📂 Quản lý truyện

- Hỗ trợ đa nguồn: folder nội bộ, SD Card, nhiều đường dẫn tùy chỉnh
- File watcher tự động — thêm / xóa chương ngoài hệ thống phản ánh trong < 0.8s
- Hỗ trợ định dạng nén: `.zip` · `.cbz` · `.7z`
- Hỗ trợ ảnh: `.jpg` · `.png` · `.webp` · `.gif` · `.avif`
- Ưu tiên ảnh chất lượng cao: `avif > webp > png > jpg`

### 👥 Người dùng & Xác thực

- Đăng ký / đăng nhập tài khoản người đọc
- Đồng bộ lịch sử đọc & theo dõi giữa localStorage và server (`usersync.js`)
- API key-based access control cho API nội bộ
- Panel quản lý staff/admin với phân quyền

### 🌐 Kết nối

- Tích hợp **Cloudflare Tunnel** — không mở port, không cần IP tĩnh
- Tích hợp **zrok** — public URL tức thì
- Keep-alive ping mỗi 15–20s chống tunnel sleep
- Tự phục hồi khi Flask hoặc zrok crash

### 🛡️ Vận hành

- Chế độ bảo trì (maintenance mode) có đồng hồ đếm ngược và thanh tiến trình
- View counter tự động lưu mỗi 20s
- Health check API
- Auto-restart với crash counter và reset sau 5 phút ổn định

---

## Cấu trúc thư mục

```
Truyện/
├── app.py                      ← Flask backend chính
├── run.py                      ← Script khởi động tổng (Flask + zrok + warmup)
├── config.yml                  ← Cấu hình Cloudflare Tunnel
│
├── templates/
│   ├── index.html              ← Trang chủ
│   ├── detail.html             ← Chi tiết truyện + banner gradient
│   ├── viewer.html             ← Trình đọc (scroll dọc)
│   ├── dangnhap.html           ← Đăng nhập tài khoản
│   ├── dangtruyen.html         ← Panel đăng truyện (staff/admin)
│   ├── followed.html           ← Truyện đang theo dõi
│   ├── lichsu.html             ← Lịch sử đọc
│   ├── timkiemnangcao.html     ← Tìm kiếm nâng cao
│   ├── theodoi.html            ← Trang theo dõi
│   ├── theloai.html            ← Danh sách thể loại
│   ├── thongbao.html           ← Thông báo
│   ├── request.html            ← Yêu cầu truyện
│   ├── contact.html            ← Liên hệ
│   ├── hosocanhan.html         ← Hồ sơ cá nhân
│   ├── quanly.html             ← Quản lý (admin)
│   ├── quantri.html            ← Quản trị nâng cao
│   ├── baotri.html             ← Trang bảo trì
│   ├── api.html                ← API access
│   └── ...
│
├── static/
│   ├── js/
│   │   ├── slug.js             ← Tạo slug URL không dấu
│   │   └── usersync.js         ← Đồng bộ dữ liệu user giữa thiết bị
│   └── comics/                 ← Kho truyện local
│       └── Ten_Truyen/
│           ├── cover.jpg
│           ├── banner.jpg
│           ├── info.txt
│           ├── Chapter_01/
│           └── Chapter_01.zip
│
└── info/                       ← Hồ sơ người đăng (avatar, profiles.json)
```

---

## Cài đặt & Khởi chạy

### Yêu cầu

```bash
pip install flask flask-compress
pip install py7zr   # tuỳ chọn — chỉ cần nếu dùng file .7z
```

### Khởi chạy đầy đủ (Flask + zrok + warmup)

```bash
python run.py
```

`run.py` tự động thực hiện:

1. Khởi động Flask trên port `3000`
2. Kết nối `zrok` tunnel để truy cập từ ngoài mạng
3. Pre-warm cache 3 giai đoạn (12 luồng song song)
4. Chạy file watcher, keep-alive và health monitor

### Chỉ chạy Flask

```bash
python app.py
```

---

## Format truyện

### Cấu trúc thư mục

```
Ten_Truyen/
├── cover.jpg           ← Ảnh bìa  (.jpg .png .webp .avif)
├── banner.jpg          ← Ảnh banner trang chi tiết  (tuỳ chọn)
├── info.txt            ← Thông tin truyện
├── Chapter_01/
│   ├── 001.jpg
│   └── 002.webp
└── Chapter_02.zip      ← Hoặc file nén (.zip .cbz .7z)
```

### Mẫu `info.txt`

```
Tên khác: Another Name
Tác giả: Tên tác giả
Thể loại: Action, Romance
Tình trạng: Đang tiến hành
Uploader: ten_nguoi_dang
Mô tả: Nội dung tóm tắt truyện tại đây...
```

---

## Hệ thống API Key

API nội bộ được bảo vệ bằng key. Key mặc định:

```
DEX-GUEST   ·   ADMIN-SPECIAL   ·   TEST-KEY-2026
```

Key được lưu trong `info/keys.json`. Cách truyền key:

| Cách | Ví dụ |
|---|---|
| Header | `X-API-Key: YOUR_KEY` |
| Query string | `?key=YOUR_KEY` |
| Form / JSON body | `{ "key": "YOUR_KEY" }` |
| Cookie | `access_key=YOUR_KEY` |

---

## API Endpoints

| Method | Endpoint | Mô tả |
|---|---|---|
| `GET` | `/` | Trang chủ |
| `GET` | `/truyen/<name>` | Chi tiết truyện |
| `GET` | `/xem/<name>/<chap>` | Đọc chương |
| `GET` | `/api/search?q=` | Tìm kiếm |
| `GET` | `/api/list_comics` | Danh sách truyện (phân trang) |
| `GET` | `/api/comics` | Toàn bộ truyện (không phân trang) |
| `GET` | `/api/comic/<name>` | Chi tiết 1 truyện |
| `GET` | `/api/chapters/<name>` | Danh sách chương |
| `POST` | `/api/verify-key` | Xác thực API key |
| `POST` | `/api/register` | Đăng ký tài khoản |
| `POST` | `/api/login` | Đăng nhập |
| `GET/POST` | `/api/userdata/load` `save` | Dữ liệu user |
| `POST` | `/api/save_comic` | Thêm / sửa truyện |
| `POST` | `/api/upload_chapter` | Upload chương mới |
| `DELETE` | `/api/delete_chapter/<name>/<chap>` | Xóa chương |
| `POST` | `/api/refresh_cache` | Làm mới cache |
| `GET/POST` | `/api/maintenance` | Bảo trì |
| `GET` | `/api/health` | Health check |
| `GET` | `/ping` | Keep-alive |

---

## Hệ thống tài khoản

### Tài khoản người đọc

- Đăng ký / đăng nhập qua `/dangnhap`
- Dữ liệu lưu `localStorage` và đồng bộ server qua `usersync.js`
- Tính năng: Lịch sử đọc · Theo dõi truyện · Bookmark chương

### Tài khoản quản lý (Staff)

- Đăng nhập qua panel `/dangtruyen`
- **Quản lý** *(admin)*: Toàn quyền — quản lý user và toàn bộ truyện
- **Nhân viên**: Chỉ quản lý truyện do mình đăng

Tài khoản mặc định:

```
username : admin
password : dex2026
```

> ⚠️ **Đổi mật khẩu ngay khi triển khai thực tế!**

---

## Tunnel & Deploy

### Cloudflare Tunnel

Cấu hình trong `config.yml`:

```yaml
tunnel: <tunnel-id>
credentials-file: /data/data/com.termux/files/home/.cloudflared/<tunnel-id>.json

ingress:
  - hostname: lxmanhwa.dpdns.org
    service: http://127.0.0.1:3000
  - service: http_status:404
```

### zrok (tích hợp trong `run.py`)

Chỉnh 3 dòng trong `run.py`:

```python
ZROK_TOKEN = "your_zrok_token"
ZROK_NAME  = "lxmanhwa"
APP_PORT   = 3000
```

---

## Hiệu suất

| Cơ chế | Chi tiết |
|---|---|
| **flask-compress** | Gzip tự động toàn bộ response, level 1 (ưu tiên tốc độ) |
| **Cache RAM** | Danh sách truyện, chapters, ảnh — tất cả trong RAM |
| **Pre-warm** | 3 giai đoạn, 12 luồng — cold start = 0 |
| **Background refresh** | Tự làm mới cache mỗi 90 giây |
| **File Watcher** | Phát hiện thay đổi file system < 0.8s |
| **Keep-alive** | Ping mỗi 15–20s chống tunnel sleep |
| **Blob cache** | Prefetch ảnh bìa + 10 ảnh đầu chương tiếp theo |

---

## Thông tin

- **Tên:** LX MANHWA
- **Phiên bản:** v2 — Banner Gradient
- **Môi trường:** Android · Termux
- **Stack:** Python · Flask · Jinja2 · Vanilla JS
- **Tác giả:** dexbillava · `dexbilldragon`

---

## 💬 Lời tác giả

Cảm ơn tất cả mọi người. Tôi đã cố gắng hoàn thành phiên bản **LX Manhwa** — một template website tự host để chia sẻ truyện tranh với mọi người.

Template này đọc dữ liệu truyện thẳng từ điện thoại của bạn, kể cả thẻ nhớ ngoài. Nếu bạn lưu nhiều truyện, nên sắm thẻ nhớ **1TB** — hoặc **2TB** nếu có thể 🌹🌹🌹

---

## 📖 Câu chuyện của tôi

Tôi biết mình không giỏi giang gì. Nhưng kênh YouTube đã cho tôi động lực — từ 100 sub rồi lên dần, giờ đã đạt **736 sub**. Cảm ơn mọi người đã ủng hộ tôi suốt 2 năm qua.

Tôi ít giao tiếp, ít tự tin, hay sợ bị người khác đánh giá. Có những lúc tôi cảm thấy mình không có chỗ đứng ở thế giới này — nhưng tôi vẫn tiếp tục làm, vì đây là thứ tôi có thể làm được.

Template này là bằng chứng rằng — dù thế nào — tôi đã hoàn thành nó. 🐧

---

## 🙏 Cảm ơn những người đã hỗ trợ

Tôi đã làm template này hơn **8 tháng**. Cảm ơn những người đã ở bên:

**Người ủng hộ tinh thần:**

| Tên |
|---|
| Mưa Tuyết |
| Anh Khang |
| Bình Nguyễn |
| Khánh Nguyễn |

**Người đã tài trợ:**

| Tên | Số tiền |
|---|---|
| Nguyễn Nam | 100đ |
| Nguyễn Thắng | 59,000đ |
| Phan Nguyễn | 49,000đ |

**Người đã giúp phát triển:**

| Tên | GitHub / Alias |
|---|---|
| Kaizen | Phan Toàn |
| Dex Bill | Chìu Quảng |

---

## Giấy phép

[MIT License](https://github.com/chiuaquang/LX-Manhwa/blob/main/LICENSE)

---

<div align="center">

[![Star History Chart](https://api.star-history.com/svg?repos=chiuaquang/lx-manhwa&type=Date)](https://star-history.com/#chiuaquang/lx-manhwa&Date)

</div>

---

<p align="center">Made with 💙 by <b>LX 098</b> 🌸</p>
