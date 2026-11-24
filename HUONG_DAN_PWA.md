# Biến web app thành app trên điện thoại (PWA): hướng dẫn

*Thiết kế & phát triển: **Nguyễn Phương Thảo** · 11/2026*

## Cách hoạt động

```
Điện thoại ──► https://<tài-khoản>.github.io/songkhoe/   (vỏ app: icon, tên, toàn màn hình)
                    └── nhúng ──► Web app Apps Script (/exec)   (toàn bộ dữ liệu & đăng nhập, như cũ)
```

- Vỏ app chỉ có giao diện bọc ngoài, **không chứa dữ liệu**. Dữ liệu vẫn nằm trong Google Sheet và đăng nhập vẫn qua web app hiện tại.
- Link web app cũ (`/exec`) **vẫn dùng bình thường**, nên không ai bị gián đoạn.

## Bộ file

| File | Vai trò |
|---|---|
| `index.html` | Màn hình chờ, nhúng web app, gợi ý cài đặt, báo mất mạng |
| `config.js` | **Nơi duy nhất cần sửa**: dán link `/exec` vào `APP_URL` |
| `manifest.webmanifest` | Tên app, màu, icon; giúp Android/Chrome nhận diện "cài được" |
| `sw.js` | Lưu sẵn vỏ app để mở nhanh và hiện màn hình "mất mạng" |
| `icons/` | Bộ icon gốc (trái tim + nhịp tim): 192, 512, maskable, iPhone, favicon |

## Bước 1: Cho phép nhúng web app (sửa Code.gs)

Dán đè `Code.gs` bản mới (đã có dòng `setXFrameOptionsMode(...ALLOWALL)`), bấm **Lưu**, rồi vào **Triển khai → Quản lý triển khai → ✏️ → Phiên bản mới → Triển khai**.

> Nếu bỏ bước này, app sẽ chỉ hiện màn hình trắng hoặc báo lỗi kết nối, vì Google mặc định chặn việc nhúng.

## Bước 2: Đưa vỏ app lên GitHub Pages (miễn phí, khoảng 15 phút)

1. Tạo tài khoản tại **github.com**. Tên tài khoản sẽ nằm trong link app, ví dụ `hlc-2026`.
2. Bấm **＋ → New repository**:
   - Repository name: `songkhoe` (link sẽ là `https://<tài-khoản>.github.io/songkhoe/`)
   - Chọn **Public**, vì gói miễn phí chỉ bật Pages cho repo public. Repo chỉ chứa vỏ app và link `/exec` (vốn đã gửi cho mọi người), **không có dữ liệu nào**.
   - Bấm **Create repository**.
3. **Trên máy tính:** mở file `config.js` bằng Notepad, dán link `/exec` vào giữa 2 dấu nháy của `APP_URL`, rồi lưu lại.
4. Trên trang repo, bấm **Add file → Upload files**, kéo **toàn bộ nội dung** thư mục `pwa` vào, gồm cả thư mục `icons`. Sau đó bấm **Commit changes**.
5. Vào **Settings → Pages → Build and deployment**: Source = **Deploy from a branch**, Branch = **main** / **(root)**, rồi bấm **Save**.
6. Đợi 1–2 phút, link app sẽ hiện ở đầu trang Pages.

## Bước 3: Test trước khi gửi các đội

| Thiết bị | Kiểm tra |
|---|---|
| Android, Chrome | Mở link → đăng nhập → nhập thử 1 buổi kèm ảnh → thanh **"Cài đặt ứng dụng"** hiện ra → cài → mở từ icon, chạy toàn màn hình |
| iPhone, Safari | Mở link → đăng nhập → Chia sẻ ⬆️ → **Thêm vào MH chính** → mở từ icon → **đóng app, mở lại sau vài giờ** xem còn đăng nhập không |
| Mở link từ Zalo | App hiện gợi ý "Mở bằng trình duyệt để cài". Đây là hạn chế của trình duyệt trong Zalo, bình thường. |
| Tắt Wi-Fi/4G | Hiện màn hình "Không có kết nối mạng", có nút Thử lại |

## Bước 4: Gửi cho các đội (mẫu tin nhắn)

> 📲 **App Thử thách Lối sống Lành mạnh đã có bản cài trên điện thoại!**
> 👉 Link: https://<tài-khoản>.github.io/songkhoe/
> • **Android:** mở bằng Chrome → bấm **Cài đặt** ở thanh dưới màn hình.
> • **iPhone:** mở bằng **Safari** → Chia sẻ ⬆️ → **Thêm vào MH chính**.
> • Mở từ Zalo: bấm ⋯ → **Mở bằng trình duyệt** trước khi cài.
> Đăng nhập bằng tài khoản đội như cũ.

## Tùy chỉnh và cập nhật

- **Đổi tên app:** sửa `name` / `short_name` trong `manifest.webmanifest` và `apple-mobile-web-app-title` trong `index.html`. `short_name` nên ≤ 12 ký tự, vì đây là tên hiển thị dưới icon.
- **Sửa file trên GitHub:** mở file → bấm ✏️ → sửa → **Commit**. Sau khoảng 1–2 phút app tự cập nhật.
- **Đổi icon hoặc `sw.js`:** tăng số phiên bản ở dòng `const CACHE = 'hlc-shell-v1'` (thành `v2`...). Người đã cài cần xóa icon cũ và cài lại để thấy icon mới.
- **Web app thay đổi** (Code.gs / Index.html): không cần đụng tới GitHub, app tự lấy phiên bản mới nhất.

## Giới hạn đã biết

| Vấn đề | Mức | Ghi chú |
|---|---|---|
| iPhone có thể yêu cầu **đăng nhập lại** thường xuyên hơn | Trung bình | Safari hạn chế lưu dữ liệu của trang được nhúng. Nếu quá phiền, người dùng iPhone có thể dùng link `/exec` trực tiếp. |
| Dòng thông báo của Google ("ứng dụng do người dùng Apps Script tạo") | Thấp | Có thể vẫn hiện bên trong app, tùy Google. Cần xác nhận khi test thực tế. |
| Không dùng được khi mất mạng | Thấp | Dữ liệu nằm trên Google Sheet, mọi thao tác cần Internet |
| Không có thông báo đẩy | Thấp | Nên dùng Zalo Bot để nhắc nhập buổi tập |
