# Hướng dẫn dùng career-ops — bản tinh chỉnh cho Đà Nẵng / Việt Nam

Bản này đã được tuỳ biến cho **thị trường IT Đà Nẵng**, hỗ trợ **Onsite Đà Nẵng + Hybrid + Remote (VN/quốc tế)**, đầu ra **song ngữ Việt–Anh**, và thêm khả năng **quét Facebook Groups**.

## 1. Việc BẠN cần làm trước (bắt buộc)

Hệ thống chưa có dữ liệu cá nhân của bạn (mình không bịa). Hãy điền:

1. **`cv.md`** (thư mục gốc) — dán CV của bạn, hoặc nói "tạo CV từ LinkedIn của tôi: <link>".
2. **`config/profile.yml`** — điền các dòng có `# TODO`: họ tên, email, SĐT, vai trò mục tiêu, mức lương kỳ vọng (ghi rõ **Gross/Net**, theo **triệu VND/tháng**).
3. **`portals.yml`** → mục `facebook_groups`: dán **URL group thật** bạn đã tham gia, đặt `enabled: true`.
4. (tuỳ chọn) `portals.yml` → `tracked_companies`: dán careers_url thật của công ty Đà Nẵng bạn quan tâm.

> Mẹo: chỉ cần nói với mình "điền profile giúp tôi" kèm thông tin, mình sẽ ghi vào đúng file.

## 2. Các lệnh chính

| Lệnh | Tác dụng |
|------|----------|
| `/career-ops` | Menu lệnh |
| dán JD/URL | Tự đánh giá (A–F) + báo cáo + CV PDF + lưu tracker |
| `/career-ops scan` | Quét **job board VN** (ITviec, TopDev, VietnamWorks, TopCV…) qua WebSearch |
| `/career-ops scan-facebook` | **Quét Facebook Groups** (Playwright, bán tự động) → nạp vào inbox |
| `/career-ops pipeline` | Đánh giá các job đang chờ trong `data/pipeline.md` |
| `/career-ops pdf <công-ty>` | Tạo CV PDF tối ưu ATS cho 1 job |
| `/career-ops tracker` | Xem tổng quan trạng thái ứng tuyển |

Luồng điển hình: `scan` và/hoặc `scan-facebook` → `pipeline` (đánh giá) → xem job ≥ 4.0/5 → `pdf` → bạn tự nộp.

## 3. Quét Facebook — đọc kỹ rủi ro ⚠️

Bạn đã chọn phương án **bán tự động qua Playwright**. Lưu ý:
- Đây là **vùng xám điều khoản Facebook**, **có nguy cơ bị khoá tài khoản**. Dùng tài khoản **cá nhân**, **tần suất thấp** (≤ 1 lần/ngày).
- Mình **không lưu mật khẩu**. Khi chạy, bạn **tự đăng nhập** trong cửa sổ trình duyệt rồi gõ "tiếp tục".
- Mình **chỉ thu thập + đánh giá**, **không** like/comment/nhắn tin/nộp đơn thay bạn.
- Nếu Facebook hiện CAPTCHA/checkpoint → mình **dừng ngay**, không lách.

## 4. Đặc thù thị trường VN đã tích hợp (trong `modes/_profile.md`)

- Chấm điểm vị trí: Remote / Đà Nẵng = 5.0; onsite bắt buộc HN-HCM = thấp.
- Lương: phân biệt **Gross/Net**, xét **lương tháng 13**, **thử việc**, **BHXH**.
- Báo cáo: kết luận tiếng Việt, thuật ngữ kỹ thuật giữ tiếng Anh. CV mặc định tiếng Anh (chuẩn ATS), tạo bản tiếng Việt khi cần.

## 5. Tự động hoá (tuỳ chọn)

Muốn quét định kỳ? Nói "scan mỗi 3 ngày" — mình sẽ thiết lập lịch chạy `/career-ops scan`.
(Lưu ý: `scan-facebook` cần bạn đăng nhập thủ công nên **không nên** đặt lịch tự động chạy nền.)

## 6. Những gì KHÔNG tự làm được (trung thực)

- Không tự đăng nhập Facebook (cần bạn làm thủ công mỗi phiên).
- `scan.mjs` (quét ATS API zero-token) **không** bắt được ITviec/TopCV/VietnamWorks vì các board này không chạy trên Greenhouse/Lever → ta dùng `search_queries` (WebSearch) thay thế, kết quả phụ thuộc chất lượng tìm kiếm.
- Bóc tách bài Facebook phụ thuộc cấu trúc trang FB (hay đổi); nếu hỏng sẽ tự fallback sang đọc accessibility tree.
