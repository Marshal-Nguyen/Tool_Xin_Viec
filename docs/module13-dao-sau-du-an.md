# Module 13 — Đào sâu dự án (EmoEase & CiteFlow)

> Vòng phỏng vấn **nào cũng hỏi** về dự án. Với anh, đây vừa là **cơ hội lớn nhất** (anh có sản phẩm thật, 1,000+ users — hơn đa số junior), vừa là **rủi ro lớn nhất** (rusty + từng dựa AI → nếu không giải thích được code của chính mình sẽ mất điểm nặng).
>
> **Nguyên tắc số 1: ĐỌC LẠI CODE EmoEase & CiteFlow trước mỗi buổi phỏng vấn.** Họ sẽ đào: "dòng này làm gì?", "vì sao chọn cách này?". Chuẩn bị kỹ phần này = ghi điểm; lơ mơ = lộ ngay.

---

## 1. Khung trình bày 1 dự án (pitch 60–90 giây)

Khi được hỏi "kể về một dự án", trả lời theo 5 nhịp:
1. **Nó là gì + cho ai** (1 câu, tiếng người)
2. **Vai trò của anh** (làm phần nào)
3. **Tech stack** (ngắn gọn)
4. **Con số / kết quả** (gây ấn tượng)
5. **1 thử thách kỹ thuật + cách giải** (để họ hỏi sâu tiếp)

### Pitch mẫu — EmoEase
> "EmoEase là một web app hỗ trợ sức khỏe tinh thần — chat với AI, làm bài đánh giá tâm lý, đặt lịch tư vấn, và cộng đồng. Tôi là **founder kiêm lead frontend**, build toàn bộ giao diện bằng **React, TypeScript, TailwindCSS**, backend dùng Supabase. App tích hợp **80+ REST API**, tôi dùng **React Query** để quản cache và đồng bộ dữ liệu. Sản phẩm đạt **1,000+ người dùng** và được chọn vào UNIVENTURES 2025. Thử thách lớn nhất là quản lý dữ liệu bất đồng bộ cho 80+ endpoint mà không bị bug data cũ — tôi giải bằng React Query với queryKey rõ ràng và invalidate sau mỗi mutation."

### Pitch mẫu — CiteFlow
> "CiteFlow là một **SaaS web app** kiểm tra một website hiển thị tốt thế nào trên các công cụ tìm kiếm AI (ChatGPT, Google AI Overviews) rồi xuất báo cáo. Tôi là **lead frontend** ở CVTOT, build giao diện và các trang báo cáo bằng **Next.js, TypeScript**, trong một monorepo dùng Turborepo, dữ liệu PostgreSQL qua Drizzle, xử lý nền bằng Inngest. Tôi cũng phụ trách **technical SEO** — đúng lĩnh vực sản phẩm tối ưu."

> 🎯 Tập **nói thuộc 2 pitch này** (thu âm, nghe lại). Mở đầu mạnh = kiểm soát được hướng phỏng vấn.

---

## 2. Bản đồ kiến trúc (vẽ ra giấy + giải thích được)

Họ hay bảo "vẽ kiến trúc dự án của bạn". Chuẩn bị sơ đồ đơn giản:

### EmoEase (điền lại theo code thật của anh)
```
[ React SPA (Vite?) ]
   - UI: TailwindCSS, component theo feature
   - State server: React Query (cache, refetch, invalidate)
   - HTTP: Axios (interceptor gắn token?)
   - Routing: React Router
        |  REST (80+ endpoints)
        v
[ Backend / Supabase ]
   - Auth, Database (Postgres), Storage
   - (AI chat gọi service nào? điền)
```
**Phải giải thích được:** vì sao Supabase? token lưu/gắn ở đâu? chia thư mục theo gì (feature/atomic)? React Query đặt staleTime bao nhiêu?

### CiteFlow (điền lại theo code thật)
```
[ Next.js monorepo (Turborepo) ]
   apps/web  ──  UI + report pages (React, TS, Tailwind)
        |        API routes / route handlers
        v
[ Inngest ]  ── job nền (scan website?)  ──►  [ PostgreSQL (Drizzle ORM) ]
        |
        └─ Docker (dev environment)
```
**Phải giải thích được:** App Router hay Pages Router? Server vs Client component dùng ở đâu? Inngest xử lý gì? Trang nào SSR/SSG?

> ⚠️ Tôi để dấu "?" vì **chỉ anh biết chính xác**. Trước phỏng vấn, mở code điền cho đúng — đừng đoán.

---

## 3. Bộ câu hỏi ĐÀO SÂU (phải trả lời được hết)

Tự trả lời thành tiếng cho **cả EmoEase & CiteFlow**:

**Về quyết định kỹ thuật:**
- [ ] Vì sao chọn React Query / Supabase / Next.js / Drizzle? Cân nhắc gì khác?
- [ ] Cấu trúc thư mục thế nào? Vì sao chia như vậy?
- [ ] Quản lý state ra sao? Client state vs server state để đâu?
- [ ] Auth & token: lưu ở đâu (cookie/localStorage)? Gắn vào request thế nào? Refresh token?
- [ ] Xử lý loading / error / empty state thế nào cho nhất quán?

**Về thử thách & debug:**
- [ ] Bug khó nhất từng gặp? Cách anh **debug** (không phải cách AI sửa)?
- [ ] Thử thách kỹ thuật lớn nhất? Đánh đổi (trade-off) gì?
- [ ] Làm sao app chịu được 1,000+ users? (performance, cache, pagination)

**Về sự trưởng thành:**
- [ ] Nếu làm lại, anh sẽ làm khác gì? (câu này RẤT hay hỏi — cho thấy biết tự phản tư)
- [ ] Phần nào trong code anh **không hài lòng**? Vì sao?

**Về code cụ thể (chuẩn bị bằng cách đọc lại):**
- [ ] "Giải thích component/đoạn này làm gì" → đọc lại 5–6 file chính, hiểu từng phần.

---

## 4. ⭐ Xử lý TRUNG THỰC phần "đã dùng AI" (cực quan trọng với anh)

Anh tự nhận dựa AI nhiều. Có 2 cách sai và 1 cách đúng:

- ❌ **Giả vờ thuộc lòng từng dòng** → họ đào 1–2 câu là lộ, mất hết tin tưởng.
- ❌ **Thú nhận kiểu "tôi chỉ copy AI"** → tự hạ giá mình.
- ✅ **Nói về QUYẾT ĐỊNH và KIỂM CHỨNG:**

> **Câu khung trả lời khi bị đào sâu:**
> "Tôi có dùng công cụ AI để tăng tốc viết code, nhưng **tôi là người quyết định** dùng [React Query / cấu trúc này] vì [lý do], và tôi **kiểm chứng** nó hoạt động đúng bằng [test thử / đọc lại / so sánh phương án]. Phần [X] tôi hiểu rõ vì đã debug nó khi gặp lỗi [Y]."

**Điểm mấu chốt:** nhà tuyển dụng junior/mid **không kỳ vọng anh nhớ từng dòng**. Họ muốn thấy anh **hiểu mình đã quyết định gì và vì sao**, biết debug, biết kiểm chứng. Dùng AI để ship nhanh là **kỹ năng thật của 2026** — nhưng phải kèm khả năng hiểu & sửa.

> 🛡️ **Cách phòng thủ tốt nhất là chuẩn bị:** trước phỏng vấn, dành 1–2 buổi **đọc lại code, chạy thử, tự giải thích thành lời** 5–6 file quan trọng nhất của mỗi dự án. Sau đó anh sẽ trả lời thật, không cần chế.

---

## 5. Năm câu chuyện STAR (chuẩn bị sẵn)

Cấu trúc: **S**ituation → **T**ask → **A**ction → **R**esult.

1. **Tích hợp 80+ API (EmoEase)** — *S:* app nhiều tính năng, rất nhiều endpoint. *T:* quản dữ liệu async không bị bug data cũ. *A:* dùng React Query (queryKey, cache, invalidate sau mutate). *R:* code gọn, ít bug đồng bộ, phục vụ 1,000+ users.
2. **Tự sáng lập + ship sản phẩm** — *S:* tự khởi xướng EmoEase. *T:* từ ý tưởng → sản phẩm thật. *A:* build full UI + tích hợp backend + lặp theo phản hồi. *R:* 1,000+ users, được chọn UNIVENTURES 2025.
3. **Làm nhiều vai ở startup (CVTOT)** — *S:* team nhỏ. *T:* vừa làm FE CiteFlow vừa hỗ trợ dự án HR-AI + SEO. *A:* ưu tiên việc, học nhanh, ship được. *R:* được lên Lead Frontend sau thử việc.
4. **SEO/GEO (CiteFlow)** — *S:* sản phẩm về AI-search visibility. *T:* tối ưu chính website sản phẩm. *A:* áp dụng kỹ thuật SEO/GEO, metadata Next.js. *R:* [điền kết quả nếu có số].
5. **Một bug khó + cách giải** — *[anh tự điền 1 tình huống thật]*: bug gì, debug thế nào, học được gì. (Chuẩn bị 1 cái thật — câu này gần như chắc chắn bị hỏi.)

---

## 6. Worksheet — điền TRƯỚC phỏng vấn

Cho **mỗi dự án** (EmoEase, CiteFlow), viết ra giấy:
- [ ] Pitch 60–90 giây (thuộc lòng ý)
- [ ] Sơ đồ kiến trúc (vẽ tay được)
- [ ] 3 quyết định kỹ thuật + lý do
- [ ] 1 thử thách + cách giải (thật)
- [ ] 1 điều "nếu làm lại sẽ khác"
- [ ] Đã đọc lại & hiểu 5–6 file chính
- [ ] Con số: 1,000+ users / 80+ API / v1.4.x — kể được bối cảnh

## ✅ Nắm chắc Module này khi:
- Pitch trôi chảy cả 2 dự án không vấp.
- Vẽ + giải thích kiến trúc bằng lời.
- Trả lời được "vì sao chọn X", "bug khó nhất", "làm lại sẽ khác gì".
- Có sẵn câu trả lời trung thực cho phần dùng AI mà không tự hạ giá mình.

> 💡 **Gợi ý mạnh:** anh có CiteFlow source ngay trên máy (`~/Documents/AI_Automation/citeflow`). Tôi có thể **cùng anh đọc qua kiến trúc CiteFlow** và giúp anh viết ra phần "giải thích kiến trúc + vì sao" cho đúng với code thật — để anh tự tin khi bị đào sâu. (Đây là cách biến điểm yếu "không hiểu code mình" thành điểm mạnh.)
