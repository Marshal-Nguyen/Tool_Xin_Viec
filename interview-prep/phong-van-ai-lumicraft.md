# Phỏng vấn AI giọng nói — LikeLion Vietnam / Lumicraft (LM1001 Full-Stack Dev)

> **Loại:** AI voice interview qua VTM (vtm.kr) · ~15 phút · ~4–6 câu · **bất đồng bộ** (không có người soi mặt) → anh **được để giấy note bên cạnh**.
> **Deadline:** 18/06/2026 23:59 (giờ VN). Plan: luyện hôm nay → làm bài trước hạn.
> **Ngôn ngữ:** email viết tiếng Anh → **chuẩn bị trả lời bằng tiếng Anh**. Mỗi câu dưới đây có (EN) để nói + (VN) để hiểu.

---

## 0. CHECKLIST trước khi bấm "Start Interview"
- [ ] Phòng yên tĩnh, đóng cửa, không tiếng ồn.
- [ ] Tai nghe có mic (tốt hơn mic laptop), test thử nghe rõ không.
- [ ] Mạng ổn định, sạc đầy/cắm điện.
- [ ] Mở sẵn file này + tờ "Cheat sheet" (mục 9) bên cạnh.
- [ ] Uống nước, hít thở. Cười nhẹ khi nói (giọng ấm hơn — AI vẫn "nghe" được sự tự tin).
- [ ] Luyện đọc to mục 1–3 **ít nhất 2 lần** trước khi vào bài thật.

**Cách nói trước AI:**
- Nói **chậm, rõ, trọn câu** — AI transcribe lời anh, phát âm rõ > nói nhanh.
- Được dừng 2–3 giây suy nghĩ. Đừng "ờ… ờ…" kéo dài.
- Nói **đủ thời lượng** (30–60s/câu), đừng trả lời 1 câu rồi im. Kể có S-T-A-R.
- Liếc note để nhớ ý, **đừng đọc nguyên văn như robot**.

---

## 1. "Tell me about yourself" / Giới thiệu bản thân ⭐ (gần như chắc chắn hỏi)

**(EN — nói ~45–60s):**
> "Hi, my name is Giang, a frontend-focused full-stack developer based in Vietnam. Right now I'm the lead frontend developer at CVTOT, where I build and maintain **CiteFlow**, a production SaaS on a **Next.js + TypeScript monorepo with PostgreSQL and Drizzle ORM**. I own the frontend end to end — from building features to testing and deployment. Before that, I founded a web app called **EmoEase**, where I shipped a React single-page app to **over a thousand users** and integrated **more than eighty REST API endpoints** using React Query. I also have a **Business Analyst background** from an internship at FPT Software, so I'm comfortable turning requirements into clear specs and working closely with a team. I'm excited about this role because your stack — Next.js, Postgres, Drizzle, React Query — is almost exactly what I work with every day."

**(VN — ghi nhớ 3 ý + 1 câu chốt):**
1. Đang là **lead frontend CiteFlow** — sản phẩm thật, **đúng stack họ** (Next.js, Postgres, Drizzle).
2. **Founder EmoEase** — 1.000+ user, 80+ API, React Query.
3. Nền **BA** (FPT Software) → hiểu nghiệp vụ, làm việc nhóm tốt.
4. Câu chốt: *stack của anh trùng khít với job.*

---

## 2. "Why this role / Why LikeLion?" / Vì sao chọn vị trí này ⭐

**(EN):**
> "Two reasons. First, the technical fit: I work with **Next.js, TypeScript, PostgreSQL, Drizzle ORM, and React Query** every day on CiteFlow, which is almost exactly your stack — so I can contribute quickly. Second, I want to grow as a full-stack engineer in a team that ships real products, and LikeLion has a strong reputation for building and for helping people grow. I also like that the role values **self-study and learning new technologies**, because that's how I work — I taught myself most of my stack by building real products."

**(VN):** Nhấn 2 ý: (1) hợp kỹ thuật → đóng góp được ngay; (2) muốn phát triển + văn hóa "tự học, học nhanh" của họ hợp với cách anh làm.

---

## 3. "Tell me about a project you're proud of" / Dự án tâm đắc ⭐ (chọn CiteFlow)

**(EN):**
> "I'd talk about **CiteFlow**, the SaaS I lead the frontend for at CVTOT. It's a **Next.js + TypeScript monorepo with PostgreSQL, Drizzle ORM, and Docker**. I own the frontend end to end — I build the UI and features, integrate with the backend and database, write tests, and help deploy. What I'm most proud of is that it's a **real production product**, not a demo — with real users and real data. Working on it taught me how to structure a large TypeScript codebase, keep frontend and backend types in sync, and ship features reliably."

**(VN):** Nếu họ hỏi sâu hơn → chuyển sang "thử thách kỹ thuật" ở mục 4.

---

## 4. "Describe a technical challenge you solved" / Thử thách kỹ thuật ⭐ (vũ khí mạnh nhất — có số liệu)

**(EN):**
> "At **EmoEase**, I had to integrate **over eighty REST API endpoints** into a React frontend. Early on the data fetching was messy — duplicated requests, inconsistent loading and error states, and stale data. I solved it by adopting **React Query** as a single, type-safe data layer: I standardized how we fetch, cache, and invalidate data. That removed the duplicated requests and made loading and error handling consistent across the whole app, and it kept the codebase maintainable as the number of endpoints grew."

**(VN):** Cấu trúc chuẩn STAR: Vấn đề (trùng request, stale data) → Giải pháp (React Query làm data layer chuẩn hóa) → Kết quả (sạch, dễ bảo trì, nhất quán). **Học thuộc ý câu này** — nó chứng minh anh hiểu cái họ dùng (React Query).

---

## 5. Technical fundamentals — talking points ngắn (phòng khi hỏi khái niệm)

Nói gọn 20–30s/khái niệm, không cần học vẹt:

- **SSR vs SSG vs CSR (Next.js):** "SSG builds the page at build time — fastest, good for content that rarely changes. SSR renders on each request — good for personalized or always-fresh data. CSR renders in the browser — good for highly interactive parts. Next.js lets me mix them per page."
- **React Query — what it gives you:** "It's a server-state library. It handles caching, request deduplication, background refetching, and loading/error states for you, so I don't have to manage all that manually with useEffect. I control freshness with `staleTime` and `gcTime`."
- **ORM / Drizzle:** "An ORM lets me query the database with type-safe code instead of raw SQL strings. Drizzle is a TypeScript-first ORM, so my database types flow into my app — fewer runtime errors."
- **tRPC (anh chưa dùng production — trả lời trung thực):** "I haven't used tRPC in production yet, but I understand the idea — it gives you end-to-end type safety between client and server without writing a separate API schema. It's close in spirit to how I already keep types in sync, so I'd pick it up quickly."
- **REST API integration:** "The frontend calls backend endpoints over HTTP, gets JSON, and I render it. I manage that data layer with React Query for caching and error handling."

---

## 6. "How do you learn a new technology?" / Khả năng tự học ⭐ (khớp văn hóa họ)

**(EN):**
> "I learn by building. I start with the official docs to get the core idea, then immediately use it in a small real feature instead of just reading. For example, I haven't used **NestJS or React Native** in production yet, but I'm confident I can pick them up the same way I learned Next.js and Drizzle — by building something real. I also use AI tools to learn faster, but I always make sure I understand what the code does, not just copy it."

**(VN):** Trung thực về NestJS/React Native chưa làm production — nhưng **cách học rõ ràng**. Đây là câu họ thích (JD ghi "willing to learn, fail quick / succeed quick").

---

## 7. "How do you work in a team?" / Làm việc nhóm — dùng nền BA ⭐

**(EN):**
> "Before becoming a developer I did a **Business Analyst internship at FPT Software**. I worked between the business side and the dev team — turning vague requirements into clear, buildable specs, refining the backlog, and joining sprint planning. That still helps me as a developer: I understand not just *what* to build but *why*, and I communicate well with PMs, POs, and other engineers. I think it makes me easier to work with in a team."

**(VN):** Đây là điểm khác biệt của anh so với dev thuần. Khớp với câu JD OTSV/Lumicraft hay nhắc: hiểu nghiệp vụ + giao tiếp.

---

## 8. "Do you have any questions for us?" / Câu hỏi ngược (chuẩn bị 1–2 câu)

Chọn 1–2 câu nói cuối (thể hiện sự quan tâm):
- "What does the first three months look like for this role?"
- "What does the team's day-to-day workflow and tech stack look like?"
- "How does the team support learning and mentorship for newer engineers?"

---

## 9. CHEAT SHEET — số liệu & từ khóa cần thả vào (để mở bên cạnh)

| Điểm mạnh | Con số / từ khóa |
|-----------|------------------|
| CiteFlow (CVTOT) | lead frontend · **Next.js + TypeScript monorepo · PostgreSQL · Drizzle ORM · Docker** · production SaaS · end-to-end ownership |
| EmoEase (founder) | React SPA · **1,000+ users** · **80+ REST API** · React Query |
| BA Intern FPT Software | requirements → specs · backlog refinement · sprint planning · giao tiếp PM/PO |
| Học vấn | FPT University · GPA **3.5/4.0** · tốt nghiệp 12/2025 |
| Trung thực (gap) | chưa làm production: **NestJS, MongoDB, tRPC, React Native** — nhưng học nhanh bằng cách build thật |

**3 từ khóa "trùng stack" phải nói được:** Next.js · PostgreSQL · Drizzle ORM (+ React Query).
**Câu chốt cả buổi:** *"Your stack is almost exactly what I build every day, and I learn fast — I'd love to grow as a full-stack engineer on your team."*

---

## 10. Câu khó: "AI codes better than you — why should we hire you?" ⭐ (câu mẹo, trả lời tốt = ghi điểm lớn)

> **Tránh:** phòng thủ ("AI không giỏi bằng tôi") · tự ti ("vâng AI giỏi hơn em…"). Ý cốt: giá trị kỹ sư = **phán đoán + sở hữu kết quả + kiểm chứng**, không phải gõ code nhanh. *Không phải tôi vs AI — mà tôi + AI.*

**(EN — ~50s):**
> "That's a fair question — and honestly, I see AI as a huge advantage, not a threat. AI is great at *producing* code, but it doesn't decide *what* to build, it doesn't understand the business context, and it doesn't take responsibility when something breaks in production. That's where an engineer matters. My job isn't to type faster than a machine — it's to know what problem we're actually solving, make the right design decisions, verify and debug what the AI produces, and own the result end to end. I use AI heavily every day, which makes me faster — but I always make sure the code is correct, secure, and maintainable. And my Business Analyst background means I bridge product and engineering — I understand the *why*, not just the *what*. So it's not me versus AI; it's me with AI, bringing the judgment and ownership that AI can't."

**(VN — 5 ý):** (1) AI là lợi thế, không phải đe doạ; (2) AI viết code giỏi nhưng không quyết định build gì / không hiểu business / không chịu trách nhiệm khi production hỏng; (3) giá trị của tôi = hiểu vấn đề + ra quyết định + kiểm chứng/debug AI + sở hữu kết quả; (4) dùng AI nhiều → nhanh hơn, nhưng đảm bảo code đúng/an toàn/bảo trì được; (5) nền BA → hiểu "vì sao".
**Câu chốt:** *"It's not me versus AI — it's me with AI, bringing the judgment and ownership a model doesn't have."*

---

## 11. PHỤ LỤC — Bản tiếng Việt đầy đủ từng câu (để hiểu trọn ý / dự phòng nếu được trả lời tiếng Việt)

**Q1 — Giới thiệu bản thân:**
> "Chào anh/chị, em tên là Giang, một lập trình viên full-stack thiên về frontend. Hiện em đang là lead frontend tại CVTOT, nơi em xây dựng và vận hành CiteFlow — một sản phẩm SaaS chạy thật, trên nền monorepo Next.js + TypeScript với PostgreSQL và Drizzle ORM. Em phụ trách toàn bộ phần frontend, từ phát triển tính năng đến kiểm thử và triển khai. Trước đó, em là founder của EmoEase, một ứng dụng web em đã đưa đến hơn 1.000 người dùng và tích hợp hơn 80 REST API bằng React Query. Em cũng có nền tảng Business Analyst từ kỳ thực tập ở FPT Software, nên em quen biến yêu cầu thành đặc tả rõ ràng và phối hợp tốt trong nhóm. Em rất hứng thú với vị trí này vì bộ công nghệ của công ty — Next.js, Postgres, Drizzle, React Query — gần như trùng khít với những gì em làm mỗi ngày."

**Q2 — Vì sao chọn vị trí này / LikeLion:**
> "Có hai lý do. Thứ nhất là độ phù hợp về kỹ thuật: em làm việc với Next.js, TypeScript, PostgreSQL, Drizzle ORM và React Query mỗi ngày trên CiteFlow — gần như đúng stack của công ty, nên em đóng góp được nhanh. Thứ hai, em muốn phát triển thành một kỹ sư full-stack trong một đội ngũ làm ra sản phẩm thật, và LikeLion có tiếng về việc xây sản phẩm cũng như giúp con người phát triển. Em cũng thích việc vị trí này đề cao tinh thần tự học và sẵn sàng học công nghệ mới, vì đó đúng là cách em làm — phần lớn stack hiện tại em tự học bằng cách bắt tay xây sản phẩm thật."

**Q3 — Dự án tâm đắc (CiteFlow):**
> "Em muốn nói về CiteFlow, sản phẩm SaaS mà em đang phụ trách frontend tại CVTOT. Nó được xây trên monorepo Next.js + TypeScript với PostgreSQL, Drizzle ORM và Docker. Em sở hữu phần frontend từ đầu đến cuối — xây giao diện và tính năng, tích hợp với backend và cơ sở dữ liệu, viết test và hỗ trợ triển khai. Điều em tự hào nhất là đây là sản phẩm chạy thật trong production, không phải bản demo — có người dùng thật và dữ liệu thật. Làm nó dạy em cách tổ chức một codebase TypeScript lớn, giữ type của frontend và backend đồng bộ, và ra tính năng một cách ổn định."

**Q4 — Thử thách kỹ thuật (EmoEase):**
> "Ở EmoEase, em phải tích hợp hơn 80 REST API vào một frontend React. Lúc đầu phần lấy dữ liệu khá rối — request bị trùng lặp, trạng thái loading và lỗi không nhất quán, dữ liệu bị cũ. Em giải quyết bằng cách dùng React Query làm một tầng dữ liệu duy nhất và an toàn về kiểu: em chuẩn hoá cách fetch, cache và invalidate dữ liệu. Việc đó loại bỏ các request trùng lặp, làm cho xử lý loading và lỗi nhất quán trên toàn ứng dụng, và giữ codebase dễ bảo trì khi số lượng API tăng lên."

**Q5 — Khái niệm kỹ thuật (nói gọn từng cái):**
- *SSR/SSG/CSR:* "SSG dựng trang lúc build — nhanh nhất, hợp nội dung ít đổi. SSR render mỗi lần có request — hợp dữ liệu cá nhân hoá hoặc luôn cần mới. CSR render ngay trên trình duyệt — hợp phần tương tác nhiều. Next.js cho phép em phối hợp cả ba theo từng trang."
- *React Query:* "Là thư viện quản lý server-state. Nó tự lo cache, gộp request trùng, refetch nền, và trạng thái loading/lỗi, nên em không phải tự xử lý hết bằng useEffect. Em kiểm soát độ 'tươi' của dữ liệu bằng staleTime và gcTime."
- *ORM/Drizzle:* "ORM cho phép truy vấn database bằng code an toàn về kiểu thay vì viết SQL thô. Drizzle ưu tiên TypeScript, nên kiểu dữ liệu của database chảy thẳng vào ứng dụng — ít lỗi lúc chạy hơn."
- *tRPC (chưa làm production):* "Em chưa dùng tRPC ở production, nhưng em hiểu ý tưởng — nó cho type-safety đầu-cuối giữa client và server mà không phải viết schema API riêng. Khá gần cách em vốn giữ type đồng bộ, nên em tin sẽ học nhanh."
- *REST:* "Frontend gọi endpoint của backend qua HTTP, nhận về JSON, em render ra. Em quản lý tầng dữ liệu đó bằng React Query để lo cache và xử lý lỗi."

**Q6 — Khả năng tự học:**
> "Em học bằng cách bắt tay làm. Em bắt đầu từ tài liệu chính thức để nắm ý tưởng cốt lõi, rồi áp dụng ngay vào một tính năng nhỏ thực tế thay vì chỉ đọc. Ví dụ, em chưa dùng NestJS hay React Native ở production, nhưng em tự tin tiếp cận chúng theo đúng cách em đã học Next.js và Drizzle — bằng cách xây một thứ gì đó thật. Em cũng dùng công cụ AI để học nhanh hơn, nhưng em luôn đảm bảo mình hiểu code làm gì, chứ không chỉ sao chép."

**Q7 — Làm việc nhóm (nền BA):**
> "Trước khi trở thành lập trình viên, em từng thực tập Business Analyst ở FPT Software. Em làm việc giữa phía nghiệp vụ và đội phát triển — biến những yêu cầu mơ hồ thành đặc tả rõ ràng và làm được, tinh chỉnh backlog, và tham gia sprint planning. Điều đó đến giờ vẫn giúp em khi làm developer: em hiểu không chỉ cần xây cái gì mà còn vì sao, và em giao tiếp tốt với PM, PO cùng các kỹ sư khác. Em nghĩ điều đó khiến em dễ phối hợp trong một đội."

**Q8 — Câu hỏi ngược (nói cuối buổi):**
- "Trong ba tháng đầu, công việc ở vị trí này sẽ như thế nào ạ?"
- "Quy trình làm việc hằng ngày và bộ công nghệ của team cụ thể ra sao ạ?"
- "Team hỗ trợ việc học hỏi và mentor cho các bạn kỹ sư mới như thế nào ạ?"

**Q10 — "AI code giỏi hơn bạn, vì sao chọn bạn?":**
> "Đó là một câu hỏi hợp lý — và thật lòng, em xem AI là một lợi thế lớn chứ không phải mối đe doạ. AI rất giỏi tạo ra code, nhưng nó không quyết định nên xây cái gì, không hiểu bối cảnh nghiệp vụ, và không chịu trách nhiệm khi có thứ gì đó hỏng trong production. Đó là chỗ một kỹ sư trở nên quan trọng. Công việc của em không phải gõ code nhanh hơn máy — mà là hiểu vấn đề thực sự cần giải, đưa ra quyết định thiết kế đúng, kiểm chứng và debug những gì AI tạo ra, và sở hữu kết quả từ đầu đến cuối. Em dùng AI nhiều mỗi ngày, điều đó giúp em nhanh hơn — nhưng em luôn đảm bảo code đúng, an toàn và dễ bảo trì. Thêm nữa, nền Business Analyst giúp em làm cầu nối giữa sản phẩm và kỹ thuật — em hiểu 'vì sao', không chỉ 'cái gì'. Nên em không xem đây là em đối đầu với AI; mà là em cùng với AI, mang lại sự phán đoán và tinh thần trách nhiệm mà AI không có."
