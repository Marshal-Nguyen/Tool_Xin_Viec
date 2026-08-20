# CiteFlow — Giải mã kiến trúc (để anh tự tin kể khi phỏng vấn)

> Tài liệu này dựng lại kiến trúc CiteFlow từ source thật trên máy anh. **Mục tiêu:** anh hiểu & giải thích được hệ thống mình đang làm → biến nỗi lo "không hiểu code mình" thành điểm mạnh.
>
> ⚠️ **Đọc kèm code thật để xác nhận** — tôi mô tả theo cấu trúc đọc được, nhưng anh là người chốt chi tiết.

---

## 1. CiteFlow là gì (1 câu)

Một **SaaS** kiểm tra một website hiển thị/được trích dẫn tốt thế nào trên **các công cụ tìm kiếm AI** (ChatGPT, Perplexity, Gemini, Google AI Overviews), chấm **điểm GEO**, xuất **báo cáo**, và thậm chí **tự tạo Pull Request sửa lỗi** trên GitHub repo của khách.

---

## 2. ⭐ Phân biệt "nền Makerkit có sẵn" vs "phần ANH làm" (QUAN TRỌNG — trung thực)

CiteFlow build trên **Makerkit** (một starter kit SaaS Next.js). Khi phỏng vấn, **đừng nhận mình viết hết** — hãy phân biệt rõ, điều đó cho thấy sự trung thực + hiểu hệ thống:

| Tầng | Có sẵn từ Makerkit (boilerplate) | Phần anh build/tuỳ biến (lead frontend) |
|------|----------------------------------|------------------------------------------|
| Auth | `@kit/better-auth`, `packages/auth` | Dùng + tích hợp vào flow |
| Billing | `packages/billing` (Stripe) | Trang pricing/checkout/billing UI |
| Multi-tenant | `account/organization/rbac/policies` | — |
| i18n, UI kit | `packages/i18n`, `packages/ui` | Dùng component, dựng trang |
| **Tính năng GEO (lõi sản phẩm)** | ❌ KHÔNG có sẵn | ✅ **Đây là phần anh & team xây**: scan, scoring, report, scan-engine |

> 🎯 **Câu nói ăn điểm:** "CiteFlow dựng trên Makerkit để có sẵn auth/billing/multi-tenant, nhờ đó **team tập trung vào phần lõi là GEO scanning, scoring và báo cáo** — đó là phần tôi làm nhiều nhất."

**Bằng chứng anh viết code thật:** file `app/api/scan/route.ts` có **comment tiếng Việt** giải thích vì sao set `maxDuration = 180` (Vercel timeout) → đây là tư duy kỹ thuật thật của anh, **rất đáng để kể**.

---

## 3. Bản đồ kiến trúc tổng thể

```
                 ┌─────────────────────────────────────────────┐
                 │   apps/web  —  Next.js (App Router) + TS     │
                 │   React · TailwindCSS · React Query ·        │
                 │   react-hook-form + zod · Better Auth        │
                 └───────────────┬─────────────────────────────┘
                                 │
        ┌────────────────────────┼──────────────────────────────┐
        │ Route Handlers (app/api)        Server Actions          │
        │  /api/scan  /api/inngest  /api/cron  /api/webhooks ...   │
        └───────┬───────────────────────┬─────────────────────────┘
                │                        │
     callScanEngine (HTTP)        Inngest (job nền)
                │                        │  generate-fix, blog-auto-draft, git-jobs
                v                        v
   ┌────────────────────────┐   ┌──────────────────────────┐
   │  geo-scan-engine        │   │  geo-fix-agent           │
   │  Express + Cheerio      │   │  phân tích repo → tạo PR │
   │  (fetch & parse web)    │   │  sửa lỗi GEO trên GitHub │
   │  + pdf/ (báo cáo PDF)   │   └──────────────────────────┘
   └───────────┬─────────────┘
               │ kết quả
               v
   ┌────────────────────────────────┐
   │  PostgreSQL  (Drizzle ORM)      │
   │  scanResults, userPlans, ...    │
   └────────────────────────────────┘
```

---

## 4. apps/web — cấu trúc App Router (Next.js)

Dùng **App Router** + i18n qua `app/[locale]/...`, chia theo **route group** `(...)`:

| Thư mục | Ý nghĩa | Trang tiêu biểu |
|---------|---------|-----------------|
| `app/[locale]/(public)` | Trang công khai (không cần đăng nhập) | landing `page.tsx`, `pricing`, `blog`, **`results/[scanId]`** (báo cáo scan), legal, checkout |
| `app/[locale]/(internal)/(sidebar)` | App sau đăng nhập (có sidebar) | `dashboard`, `scans`, `settings`, `billing` |
| `app/[locale]/auth` | Đăng nhập/đăng ký | |
| `app/[locale]/admin` | Quản trị | |
| `app/api/*` | **Route handlers** (backend) | `scan`, `inngest`, `cron`, `webhooks`, `upload`, `auth` |

> 🎯 **Giải thích được:** "Route group `(public)`/`(internal)` để **chia layout & quyền truy cập** mà không ảnh hưởng URL. `[locale]` để **đa ngôn ngữ**. Trang báo cáo `results/[scanId]` để **public** nên khách chia sẻ link được."

---

## 5. Tech stack & VÌ SAO (chuẩn bị câu "vì sao chọn X")

| Công nghệ | Dùng để | "Vì sao" (câu trả lời) |
|-----------|---------|------------------------|
| **Next.js (App Router)** | Framework FE + backend route | SSR/SSG tốt cho SEO (đúng lĩnh vực sản phẩm), route handler + server action gộp FE/BE 1 chỗ |
| **TypeScript** | Toàn bộ | An toàn kiểu, dễ refactor monorepo lớn |
| **React Query** | Server state ở client | Cache, refetch nền, **poll trạng thái scan** đang chạy (xem mục 6) |
| **react-hook-form + zod** | Form + validate | Form hiệu năng tốt, zod validate type-safe (FE & BE dùng chung schema) |
| **TailwindCSS** | Styling | Dựng UI nhanh, nhất quán |
| **Better Auth** | Xác thực | Có sẵn từ Makerkit |
| **Drizzle ORM + PostgreSQL** | Dữ liệu | Drizzle type-safe, query gần SQL, hợp TypeScript |
| **Inngest** | Job nền | Chạy tác vụ dài/bất đồng bộ (tạo fix, draft blog) ngoài request HTTP |
| **Turborepo + pnpm** | Monorepo | Cache build, chia code thành packages tái dùng |
| **Cheerio** (scan-engine) | Parse HTML | Đọc & phân tích nội dung website cần audit |

---

## 6. ⭐ LUỒNG SCAN end-to-end (phần "đắt" nhất để kể)

Đây là tính năng lõi — học thuộc luồng này:

1. **User nhập URL** trên frontend → gọi **`POST /api/scan`**.
2. Route handler (`app/api/scan/route.ts`) làm:
   - **Xác thực** (Better Auth) + **rate limit**.
   - **Kiểm tra quota/gói** của user (Drizzle: `userPlans`, `userPlanUrls`, rescan packs, URL slots) — logic SaaS.
   - Tạo bản ghi `scanResults` với status **`processing`**.
3. **Fire-and-forget bất đồng bộ** (dùng `after()` của Next.js + `maxDuration = 180`):
   - Gọi **`callScanEngine`** → service **geo-scan-engine** (Express + Cheerio) đi **fetch & phân tích website** mục tiêu (timeout ~150s).
   - **`enrichScanResponse`** + **`computeWeightedGeoScore`** (score-pillars) → tính **điểm GEO có trọng số** theo nhiều "trụ cột".
   - Lưu kết quả vào DB, status → **`done`**.
4. **An toàn lỗi:** nếu function bị Vercel kill giữa chừng, bản ghi kẹt ở `processing` → **cron sweep** (`/api/cron`) sau 30 phút mark **`failed`**. *(Chính anh comment phần timeout này!)*
5. **Frontend** `results/[scanId]` đọc `scanResults` và render báo cáo. Khi đang `processing` → **React Query poll** đến khi `done`.
6. **Xuất PDF** báo cáo qua `geo-scan-engine/pdf`.

> 🎯 **Đây là câu chuyện kỹ thuật vàng của anh.** Kể được luồng này + giải thích **vì sao fire-and-forget** (request HTTP không nên chờ 150s → tách async, trả về ngay, FE poll) = thể hiện tư duy hệ thống ở mức **trên junior**.

---

## 7. Inngest — vì sao cần job nền?

Một số việc **quá lâu hoặc bất đồng bộ** để chạy trong 1 request HTTP:
- `generate-fix` (geo-fix-agent): phân tích GitHub repo → tạo PR sửa lỗi GEO.
- `blog-auto-draft`: tự soạn nháp blog.
- `git-jobs`: xử lý job liên quan GitHub.

> 🎯 "Vì sao dùng Inngest thay vì làm trong API route?" → tác vụ dài/nhiều bước, cần retry & chạy nền đáng tin cậy, không nên block request người dùng.

---

## 8. geo-scan-engine — service riêng

- **Tách riêng** khỏi Next app (có Dockerfile riêng), chạy như **Express service**.
- Dùng **Cheerio** để fetch & parse HTML website cần audit; thư mục `pdf/` tạo báo cáo PDF; `scan/` chứa logic quét.
- Next app gọi qua HTTP (`callScanEngine`).

> 🎯 "Vì sao tách service?" → tác vụ scan nặng/độc lập, tách ra để **scale & deploy riêng**, không làm phình app web, dễ đặt timeout riêng.

---

## 9. Câu hỏi ĐÀO SÂU riêng cho CiteFlow + gợi ý trả lời

- **"Mô tả kiến trúc CiteFlow."** → dùng mục 3 + 6 (vẽ sơ đồ, kể luồng scan).
- **"Trang report cập nhật trạng thái scan thế nào?"** → React Query poll `scanResults` đến khi `done` (mục 6).
- **"Xử lý tác vụ scan lâu (150s) ra sao?"** → fire-and-forget + `after()` + `maxDuration`, FE poll, cron sweep dọn job kẹt. *(điểm nhấn của anh)*
- **"Vì sao App Router, route group, `[locale]`?"** → mục 4.
- **"Multi-tenant/billing làm sao?"** → trung thực: "phần đó từ Makerkit (Better Auth, Stripe billing, RBAC); tôi tích hợp & dựng UI, tập trung xây phần GEO."
- **"Đảm bảo không lạm dụng (mỗi user scan bao nhiêu)?"** → rate limit + quota theo `userPlans`/URL slots.

---

## 10. ⚠️ Việc anh NÊN làm trước phỏng vấn (đọc lại để chắc)

Mở & đọc hiểu **5 file/khu vực này** (chúng là lõi sản phẩm — phần anh nên nắm chắc nhất):
- [ ] `app/api/scan/route.ts` — luồng scan + quota + async (anh có comment ở đây).
- [ ] `lib/scan-engine.*` (`callScanEngine`) — cách gọi engine.
- [ ] `lib/score-pillars.*` (`computeWeightedGeoScore`) — cách tính điểm GEO.
- [ ] `app/[locale]/(public)/results/[scanId]/page.tsx` — trang báo cáo (UI chính).
- [ ] `app/[locale]/(internal)/(sidebar)/dashboard` & `scans` — app sau đăng nhập.

**Cách đọc:** với mỗi file, tự trả lời 3 câu: *(1) file này làm gì? (2) nó nhận/trả gì? (3) vì sao viết theo cách này?* — viết ra giấy. Làm xong, anh sẽ trả lời được mọi câu đào sâu về CiteFlow.

> Tôi có thể cùng anh đọc kỹ TỪNG file trong số này và giải thích dòng-theo-dòng (vd bắt đầu với `score-pillars` hoặc trang `results`). Chỉ cần nói "đọc file X".
