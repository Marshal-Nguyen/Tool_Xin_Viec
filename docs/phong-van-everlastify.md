# Phỏng vấn Everlastify — Junior Full-stack Developer (Node.js/Next.js)

> Công ty TNHH Everlastify · Helicopter Building, Nguyễn Văn Linh, Hoà Cường, Đà Nẵng
> Onsite · No OT · Thứ 2–6 · 12–15 triệu · Non-IT, ngành **thương mại điện tử**, 51–150 người
> Tài liệu này đi kèm **Module 17 (Node.js Backend)** và **Module 18 (Database)**.

---

## 1. Bản đồ khớp — lệch

| JD yêu cầu | Anh có gì | Mức |
|---|---|---|
| Tốt nghiệp ĐH CNTT | FPT University, GPA 3.5/4.0 | ✅ |
| ≥1 năm kinh nghiệm web, đã tham gia dự án | ~1 năm ở CVTOT, sản phẩm chạy thật | ✅ |
| Front-end React + Next.js | Thế mạnh sâu nhất, 63 màn hình | ✅✅ |
| SQL (MySQL, Postgres) | PostgreSQL + Drizzle, tự thiết kế schema | ✅ |
| **Back-end NodeJS (ExpressJS/NestJS)** | Node qua Next.js API routes, **chưa dùng Express/Nest** | ⚠️ |
| **NoSQL (MongoDB, Redis)** | **Chưa dùng** | ❌ |
| C#/Java là lợi thế | Không | ➖ |
| Đọc hiểu tiếng Anh chuyên ngành | Tốt | ✅ |
| **Ưu tiên: Shopify** | **Đã tự làm app Shopify** | ✅✅ |
| Ưu tiên: AWS, hệ thống lớn | Docker/Vercel, monorepo lớn có sẵn | 🟡 |

**Hai vũ khí mạnh nhất:** Shopify (đúng ngành của họ) và kinh nghiệm **tiếp quản codebase lớn có sẵn** (đúng dòng đầu tiên trong mô tả công việc: *"vận hành, bảo trì, nâng cấp thêm chức năng cho các hệ thống đã và đang phát triển"*).

**Hai lỗ hổng phải chuẩn bị:** Express/NestJS và MongoDB/Redis. Xem cách trả lời ở mục 5.

---

## 2. Buổi phỏng vấn nhiều khả năng diễn ra thế nào

Công ty 51–150 người, Non-IT, tuyển junior → thường **1–2 vòng**, không có vòng thuật toán khó.

| Phần | Thời lượng | Nội dung |
|---|---|---|
| Giới thiệu bản thân | 5 phút | Chuẩn bị sẵn 90 giây |
| Đào sâu dự án | 15–20 phút | **Phần dài nhất và quan trọng nhất** |
| Kỹ thuật JS/React/Next | 15 phút | Module 1, 5, 7 giáo trình cũ |
| Kỹ thuật Node/DB | 15 phút | **Module 17, 18 mới** |
| Tình huống & hành vi | 10 phút | Module 14 |
| Anh hỏi lại | 5–10 phút | Xem mục 7 |

---

## 3. Giới thiệu bản thân — 90 giây

Viết ra, đọc to 10 lần cho thuộc nhịp, đừng học vẹt từng chữ.

> "Em là Giang, tốt nghiệp Kỹ thuật Phần mềm FPT University cuối 2025. Hiện em làm ở CVTOT, và điểm đặc biệt là em là **lập trình viên duy nhất** của CiteFlow — một SaaS đang chạy thật với hơn 200 khách hàng.
>
> Vì làm một mình nên em phải ôm trọn: 63 màn hình giao diện bằng Next.js và TypeScript, 37 API route, thiết kế schema PostgreSQL, viết test, rồi deploy và xử lý sự cố sau khi lên production.
>
> Trước đó em thực tập Business Analyst ở FPT Software, nên em quen việc lấy yêu cầu nghiệp vụ rồi biến thành đầu việc kỹ thuật.
>
> Em ứng tuyển Everlastify vì hai lý do. Một là em đã tự làm một **app Shopify** ở CiteFlow nên em thấy mảng thương mại điện tử của công ty là thứ em bắt nhịp được ngay. Hai là em muốn làm trong một team thật — một năm qua em tự học hết mọi thứ một mình, giờ em muốn có đồng nghiệp để học nhanh hơn."

Đoạn cuối xử lý luôn câu "vì sao muốn rời chỗ cũ" theo hướng tích cực, không nói xấu công ty cũ.

---

## 4. Câu hỏi dự đoán + cách trả lời bằng chính dự án của anh

### Nhóm A — Đào sâu dự án (chắc chắn hỏi)

**"Em kể về dự án em tâm đắc nhất."**
Dùng cấu trúc: *Bối cảnh → Việc em làm → Quyết định kỹ thuật khó nhất → Kết quả*.
Đừng liệt kê công nghệ. Hãy kể **một quyết định** và **vì sao**.

> Ví dụ mở: "CiteFlow chấm xem một website có được các công cụ tìm kiếm AI như ChatGPT trích dẫn không. Quyết định khó nhất của em là tách **engine quét** ra khỏi web app thành một service TypeScript deploy độc lập, có Dockerfile riêng…"

**"Vì sao em tách engine ra? Không để chung được à?"**
> "Một lượt quét mất nhiều giây vì phải tải và phân tích cả website. Node chạy JS trên một luồng, nên nếu để chung, một người bấm quét là các request khác phải chờ. Tách ra thì em scale phần quét riêng, và web app vẫn phản hồi nhanh."

Câu này **thể hiện anh hiểu event loop bằng trải nghiệm thật**, mạnh hơn mọi định nghĩa.

**"Phần khó nhất em từng gặp là gì?"**
Chọn sẵn 1 câu chuyện có: vấn đề cụ thể → cách em chẩn đoán → cách sửa → kết quả đo được. Ghi vào `interview-prep/story-bank.md`.

**"Em làm một mình thì làm sao biết code mình tốt hay xấu?"**
> "Em dựa vào ba thứ: test tự động (Vitest cho unit, Playwright cho end-to-end) để không làm hỏng chức năng cũ; code review bằng công cụ và tự đọc lại sau một ngày; và quan trọng nhất là lỗi thật từ người dùng — em theo dõi và sửa. Em cũng ý thức làm một mình là điểm yếu, nên em rất muốn vào môi trường có review chéo."

Trả lời như vậy vừa thật vừa thành thục.

### Nhóm B — Next.js (thế mạnh, phải khai thác tối đa)

**"Server Component khác Client Component chỗ nào?"**
> Server Component chạy **trên server**, không gửi JS xuống trình duyệt, truy cập trực tiếp DB được, nhưng **không dùng được** `useState`, `useEffect`, sự kiện onClick. Client Component có `'use client'`, chạy ở trình duyệt, tương tác được. Nguyên tắc của em là để mặc định là Server Component, chỉ đánh dấu client ở những nhánh lá cần tương tác.

**"SSR, SSG, ISR khác nhau ra sao? Ở CiteFlow em dùng cái nào?"**

| Kiểu | Dựng HTML lúc nào | Dùng cho |
|---|---|---|
| SSG | lúc build | trang marketing, blog |
| SSR | mỗi request | trang cá nhân hoá, dashboard |
| ISR | build sẵn + tự dựng lại sau N giây | danh mục sản phẩm |
| CSR | trên trình duyệt | phần sau đăng nhập |

Nêu kèm thực tế: trang landing để tĩnh cho SEO, dashboard thì render theo request vì dữ liệu riêng từng khách.

**"Route Handler khác gì API route cũ?"** — App Router dùng `app/api/.../route.ts`, export theo tên method (`GET`, `POST`), làm việc với `Request`/`Response` chuẩn Web thay vì `req`/`res` kiểu Express.

**"Em làm SEO cho Next.js thế nào?"** — Đây là sân nhà của anh (CiteFlow là sản phẩm GEO): metadata API, sitemap, dữ liệu có cấu trúc, render phía server để bot đọc được. Nói mạnh phần này.

### Nhóm C — Node & Backend (phần yếu, học Module 17)

Các câu gần như chắc chắn:
- Node single-thread hay không? Event loop hoạt động ra sao?
- `Promise.all` khác `allSettled`?
- Middleware trong Express là gì, thứ tự chạy?
- 401 khác 403?
- JWT lưu ở đâu cho an toàn? Vì sao cần refresh token?
- Mật khẩu lưu thế nào?

### Nhóm D — Database (phần yếu, học Module 18)

- Thiết kế bảng cho giỏ hàng / đơn hàng → **luyện trước, xem Module 18 mục 8**
- Index là gì, đánh đổi gì?
- N+1 là gì?
- Transaction dùng khi nào?
- **Hai người cùng mua sản phẩm cuối cùng thì xử lý sao?** ← câu tủ của công ty e-commerce
- Khi nào dùng MongoDB thay vì Postgres?

### Nhóm E — Tình huống

**"Sếp giao việc gấp mà em thấy không kịp thì sao?"**
> Em báo sớm chứ không im lặng rồi trễ. Em tách ra phần nào làm được trước, đề xuất cắt phạm vi hoặc lùi mốc, và nói rõ rủi ro nếu làm vội.

**"Em phát hiện code của đồng nghiệp có lỗi thì làm gì?"**
JD có mục *"góp ý và hỗ trợ đồng nghiệp"* → họ quan tâm điều này.
> Em nhắn riêng trước, mô tả hiện tượng và cách tái hiện chứ không phán xét người viết. Nếu là lỗi ảnh hưởng người dùng thì em báo ngay cho cả team.

---

## 5. Ba câu khó nhất — học thuộc cách trả lời

### "Em có kinh nghiệm ExpressJS hoặc NestJS không?"

**Đừng nói dối. Đừng chỉ nói 'chưa'.** Trả lời 3 nhịp: thừa nhận → nối sang cái tương đương mình có → chứng minh tốc độ học.

> "Em chưa dùng Express hay NestJS trong dự án thật ạ. Backend của em đi qua **Node.js trong Next.js Route Handlers** — em vẫn tự thiết kế REST API, làm xác thực, validate dữ liệu vào, xử lý lỗi tập trung và viết test.
>
> Em có tìm hiểu Nest: nó chuẩn hoá cấu trúc bằng module/controller/service và dùng dependency injection để dễ test. Về tư duy thì giống cách em tách tầng ở CiteFlow — route chỉ nhận request và validate, còn logic nghiệp vụ em để ở tầng service riêng.
>
> Về tốc độ học thì trong năm nay, một mình và trong lúc sản phẩm vẫn đang chạy, em đã tự học Drizzle ORM, tích hợp Stripe, làm app Shopify và Playwright. Em tin Express hoặc Nest em cũng bắt kịp trong vài tuần."

> ⚠️ **Việc cần làm trước phỏng vấn:** bỏ ra 2–3 tiếng chạy `nest new` và dựng 1 module CRUD. Khi đó câu trả lời đổi thành *"em có làm thử một project nhỏ"* — mạnh hơn hẳn.

### "Em biết MongoDB hoặc Redis không?"

> "Em chưa dùng trong dự án thật. Ở CiteFlow em dùng PostgreSQL vì dữ liệu có quan hệ rõ và cần transaction. Em hiểu Mongo hợp khi cấu trúc dữ liệu không cố định và đọc theo cả document, còn Redis thì em biết bốn công dụng chính là cache, session, giới hạn request và hàng đợi. Với đơn hàng, thanh toán thì em vẫn nghiêng về cơ sở dữ liệu quan hệ."

### "Mức lương em mong muốn?"

JD ghi 12–15 triệu. Anh có sản phẩm doanh thu do một mình gánh, nên có cơ sở nhắm mép trên.

> "Em tìm hiểu thì khoảng của vị trí là 12 đến 15 triệu. Em mong ở mức 15 triệu ạ, vì em đang một mình vận hành một sản phẩm có doanh thu thật nên em nghĩ em đóng góp được ngay từ đầu. Nhưng em cũng quan tâm tới cơ hội học hỏi trong team, nên em sẵn sàng trao đổi thêm."

Đừng nói con số thấp hơn mức mình muốn để mong được nhận. Đừng nói "bao nhiêu cũng được".

---

## 6. Ba câu hỏi họ có thể hỏi mà anh dễ hụt

1. **"Em đã làm việc với AWS chưa?"** — Chưa. Nói thật, nhưng nối: em deploy bằng Docker và Vercel, hiểu khái niệm container, biến môi trường, CI/CD; em nghĩ chuyển sang EC2/S3/RDS không phải rào cản lớn.
2. **"Em có kinh nghiệm hướng dẫn người khác không?"** — JD có mục này. Anh làm một mình nên yếu. Lấy chuyện viết tài liệu kỹ thuật cho sản phẩm, và thời BA phải giải thích yêu cầu cho cả bên nghiệp vụ lẫn bên kỹ thuật.
3. **"Vì sao em rời CVTOT?"** — Không chê công ty. Khung an toàn: *"Em học được rất nhiều khi tự làm hết, nhưng đúng vì thế mà em không có ai review code. Em muốn vào team có người giỏi hơn để đi nhanh hơn."*

---

## 7. Câu hỏi anh nên hỏi lại — chọn 3–4 câu

Hỏi lại là phần **ghi điểm bị bỏ phí nhiều nhất**. Đừng nói "em không có câu hỏi gì".

**Về công việc**
- Hệ thống hiện tại của công ty đang chạy trên stack nào, và phần lớn thời gian của vị trí này là bảo trì hệ thống cũ hay xây tính năng mới ạ?
- Team dev hiện có bao nhiêu người, quy trình review code như thế nào?
- Công ty dùng Shopify ở mức nào — dùng nền tảng có sẵn hay có tự phát triển app riêng?

**Về cách làm việc**
- Một tính năng đi từ lúc có yêu cầu tới lúc lên production thường qua những bước nào ạ?
- Bên mình có viết test tự động không, và ở mức nào?

**Về phát triển**
- Sau 6 tháng, công ty kỳ vọng người ở vị trí này làm được gì mà lúc mới vào chưa làm được?

Câu cuối rất mạnh: nó cho thấy anh nghĩ về việc đóng góp, không phải chỉ nghĩ về việc được nhận.

---

## 8. Lịch ôn 7 ngày

| Ngày | Nội dung | Giờ |
|---|---|---|
| 1 | **Module 17** phần 1–4 (event loop, async, Express) + gõ lại toàn bộ code | 3h |
| 2 | **Module 17** phần 5–8 (NestJS, REST, auth, bảo mật) + `nest new` dựng CRUD thử | 3h |
| 3 | **Module 18** phần 1–4 (schema, index, N+1, transaction) | 3h |
| 4 | **Module 18** phần 5–8 + **tự làm bài thiết kế giỏ hàng, bấm giờ 15 phút** | 3h |
| 5 | Ôn lại Module 1 (JS core) + Module 5 (React) trong giáo trình cũ | 3h |
| 6 | Module 7 (Next.js) + viết ra giấy câu trả lời cho mục 4 và mục 5 tài liệu này | 3h |
| 7 | **Mock interview**: tự trả lời to toàn bộ câu ở mục 4, bấm giờ. Đọc lại `story-bank.md` | 2h |

Nếu ít hơn 7 ngày: ưu tiên **Ngày 1, 3, 4, 7**. Vì Node và Database là lỗ hổng, còn React/Next anh đã mạnh sẵn.

---

## 9. Checklist ngày phỏng vấn

- [ ] Mở sẵn citeflow.io và GitHub trên điện thoại, phòng khi họ muốn xem
- [ ] Nhớ được 3 con số: **200+ khách hàng, 63 màn hình, 37 API route**
- [ ] Chuẩn bị 1 câu chuyện khó khăn kỹ thuật đã giải quyết (STAR)
- [ ] Chuẩn bị 3 câu hỏi ngược
- [ ] Ôn lại đường đi: Helicopter Building, Nguyễn Văn Linh, Hoà Cường. Đến sớm 10 phút
- [ ] Mang theo CV in ra 1 bản
- [ ] Câu chốt cuối buổi: *"Em rất muốn làm ở đây, đặc biệt là mảng thương mại điện tử vì em đã có kinh nghiệm làm app Shopify. Không biết bước tiếp theo của quy trình là gì ạ?"*

---

## 10. Điều quan trọng nhất

Anh đang lo mình thiếu Express, Nest, Mongo. Nhưng hãy nhớ: với một junior, **rất ít người có sản phẩm chạy thật, có khách trả tiền, và tự làm một mình từ đầu đến cuối**. Đó là thứ họ khó tìm hơn nhiều so với việc biết một framework cụ thể.

Framework học vài tuần là xong. Kinh nghiệm chịu trách nhiệm cho một sản phẩm production thì không.

Vào phỏng vấn với tâm thế đó, đừng vào với tâm thế đi xin.
