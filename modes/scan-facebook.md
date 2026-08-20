# Mode: scan-facebook — Quét bài tuyển dụng trên Facebook Groups (Playwright, bán tự động)

> Kênh bổ sung cho thị trường **Đà Nẵng / Việt Nam**: nhiều job IT chỉ đăng trong Facebook
> groups, không lên ITviec/Greenhouse. Mode này dùng **Playwright MCP** điều khiển trình
> duyệt ĐÃ ĐĂNG NHẬP của người dùng để thu thập bài, rồi LLM bóc tách thành job chuẩn.

## ⚠️ RÀO CHẮN BẮT BUỘC (đọc trước khi chạy)

1. **Vùng xám ToS:** Tự động hoá trên Facebook vi phạm Điều khoản dịch vụ và **có thể bị khoá tài khoản**. Chỉ dùng **tài khoản cá nhân của chính người dùng**, mục đích cá nhân, **tần suất thấp** (tối đa ~1 lần/ngày).
2. **KHÔNG lưu mật khẩu Facebook** ở bất kỳ đâu. Người dùng tự đăng nhập thủ công trong cửa sổ trình duyệt; mode chỉ tái sử dụng phiên đang mở.
3. **Tốc độ như người thật:** cuộn từ từ, có delay; **không** spam request. Mỗi lần chạy giới hạn số group và số lần cuộn theo `portals.yml`.
4. **Chỉ THU THẬP & ĐÁNH GIÁ — KHÔNG tương tác:** không like, không comment, không gửi tin nhắn, không bấm "Apply"/"Ứng tuyển" thay người dùng. Tuân thủ quy tắc hệ thống: **không bao giờ nộp đơn khi chưa được người dùng duyệt.**
5. Nếu Facebook hiện checkpoint/CAPTCHA/cảnh báo bảo mật → **DỪNG NGAY**, báo người dùng, không tìm cách lách.

## Đầu vào (Input)

- Đọc `portals.yml` → mục `facebook_groups` (chỉ xử lý mục `enabled: true`).
- Mỗi group: `{ name, url, scroll, keywords }`.
- Nếu không có group nào `enabled: true` → báo người dùng cần bật + dán URL group thật, rồi dừng.

## Quy trình (Procedure)

### B1. Khởi tạo phiên & xác thực
1. `browser_navigate` → `https://www.facebook.com`.
2. `browser_snapshot` để kiểm tra trạng thái:
   - Nếu thấy form đăng nhập / màn hình "Log in" → **DỪNG**, nói với người dùng:
     > "Vui lòng đăng nhập Facebook trong cửa sổ trình duyệt vừa mở, rồi gõ 'tiếp tục' để mình quét."
     Chờ người dùng xác nhận đã đăng nhập, sau đó `browser_snapshot` lại để xác minh.
   - Nếu đã ở News Feed (đã đăng nhập) → tiếp tục.

### B2. Với mỗi group (lặp tuần tự, KHÔNG song song)
1. `browser_navigate` → `group.url`. Ưu tiên thêm `?sorting_setting=CHRONOLOGICAL` để lấy bài mới nhất.
2. `browser_wait_for` ~3s cho bài tải.
3. Cuộn `group.scroll` lần để tải thêm bài. Mỗi lần dùng `browser_evaluate`:
   ```js
   () => { window.scrollBy(0, document.body.scrollHeight); return document.body.scrollHeight; }
   ```
   xen kẽ `browser_wait_for` ~2-3s giữa các lần (tốc độ người thật).
4. Thu thập bài bằng `browser_evaluate` (DOM của FB bị obfuscate → lấy theo `role="article"`):
   ```js
   () => Array.from(document.querySelectorAll('div[role="article"]')).map(a => {
     const text = (a.innerText || '').trim();
     const link = a.querySelector('a[href*="/posts/"], a[href*="/permalink/"], a[href*="/groups/"][href*="/posts/"]');
     return { text, permalink: link ? link.href.split('?')[0] : '' };
   }).filter(p => p.text.length > 40);
   ```

### B3. Lọc sơ bộ (rẻ, không tốn LLM)
- Bỏ bài không chứa bất kỳ keyword nào trong `group.keywords` (case-insensitive).
- Bỏ bài rõ ràng không phải tuyển dụng (tìm CV/tìm việc cá nhân, quảng cáo khoá học, "cần tìm việc"…).

### B4. Bóc tách bằng LLM (đây là việc LLM làm tốt)
Với mỗi bài còn lại, trích xuất JSON:
```
{
  "is_job": true|false,        // có phải tin TUYỂN DỤNG (nhà tuyển dụng đăng) không
  "title": "...",              // vai trò, vd "Senior Backend Engineer (Node.js)"
  "company": "...",            // tên công ty nếu có, else ""
  "location": "...",           // "Đà Nẵng" | "Remote" | "Hybrid - Đà Nẵng" | ...
  "work_mode": "onsite|hybrid|remote|unknown",
  "salary": "...",             // nếu nêu, ghi rõ Gross/Net nếu có
  "contact": "...",            // email/SĐT/Zalo/link ứng tuyển nếu có
  "posted_hint": "...",        // "5 giờ trước" / ngày nếu thấy
  "permalink": "..."           // từ B2
}
```
Bỏ bài `is_job=false`.

### B5. Lọc theo hồ sơ (đọc chính sách ở `modes/_profile.md`)
Giữ lại bài thoả MỌI điều:
- **Đúng lĩnh vực IT/phần mềm** (khớp `title_filter.positive` trong `portals.yml`).
- **Đúng vị trí:** `work_mode = remote` HOẶC location chứa "Đà Nẵng/Da Nang" HOẶC hybrid-Đà Nẵng. Loại bài onsite thuần ở HN/HCM (theo Location Policy).

### B6. Khử trùng lặp (dedup)
- Đọc `data/scan-history.tsv` (tạo nếu chưa có, header: `url\tfirst_seen\tportal\ttitle\tcompany\tstatus\tlocation`).
- Khóa dedup = `permalink`. Nếu permalink rỗng → tạo khóa ổn định `fb://{group-slug}/{slug-40-ký-tự-đầu-của-title+company}`.
- Bỏ bài đã có khóa trong scan-history.

### B7. Ghi đầu ra (để chảy vào pipeline đánh giá)
Với mỗi bài MỚI:
1. **Lưu JD cục bộ:** ghi nội dung bài (text gốc + các trường đã bóc tách) vào `jds/fb-{group-slug}-{slug}.md`.
2. **Thêm vào inbox** `data/pipeline.md`, trong mục `## Pendientes`, một dòng:
   ```
   - [ ] local:jds/fb-{group-slug}-{slug}.md | {company} | {title}
   ```
   (Tạo `data/pipeline.md` với khung `## Pendientes` / `## Procesadas` nếu chưa có.)
3. **Ghi dedup** vào `data/scan-history.tsv` 1 dòng (7 cột):
   ```
   {khóa}\t{YYYY-MM-DD}\tfacebook:{group-name}\t{title}\t{company}\tnew\t{location}
   ```

### B8. Tổng kết
In bảng: group | số bài quét | số job IT/Đà Nẵng/remote | số mới thêm vào pipeline | số trùng đã bỏ.
Kết thúc bằng:
> "Đã thêm N job mới vào inbox. Chạy `/career-ops pipeline` để đánh giá (A–F) — mình sẽ KHÔNG nộp đơn nếu bạn chưa duyệt."

## Lưu ý vận hành
- Nếu DOM thay đổi và `div[role="article"]` không bắt được bài → fallback: dùng `browser_snapshot` (accessibility tree) đọc text, vẫn bóc tách bằng LLM.
- Bài trong group thường thiếu URL ứng tuyển → đó là lý do ta lưu `local:jds/...` để đánh giá từ text; phần `contact` giúp người dùng liên hệ thủ công sau khi job đạt điểm.
- Mode này KHÔNG dùng `scan.mjs` (script đó chỉ cho ATS API). Toàn bộ logic ở đây do agent thực thi qua Playwright MCP + LLM.
