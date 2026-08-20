# Phỏng vấn OTSV — Full-Stack Developer (ReactJS/NodeJS) — Bộ câu hỏi khả năng cao

> Soạn theo đúng JD của OTSV. Ưu tiên ôn nhóm **🔴 chắc chắn hỏi**. Với phần lỗ hổng (NestJS/Mongo/testing) → trả lời **thật + thể hiện học nhanh**, đừng giả vờ biết.

**Format dự đoán:** 1 vòng kỹ thuật (hỏi lý thuyết + có thể live-coding nhỏ React/JS) + đào dự án + hành vi/culture. JD nhấn "full product cycle", "fail quick succeed quick", "product mindset".

---

## 🔴 Nhóm A — React / Next.js (THẾ MẠNH — phải ăn trọn, đừng để mất điểm)

1. **Khi nào component re-render? Tối ưu thế nào?** → state/props đổi, parent re-render, context đổi; tối ưu bằng `React.memo`, `useMemo/useCallback`, tách component. *(xem module5)*
2. **`useEffect` chạy khi nào, cleanup để làm gì?** → sau render; deps quyết định; cleanup huỷ timer/subscribe + chống stale closure.
3. **CSR vs SSR vs SSG vs ISR trong Next.js? Khi nào dùng?** → CSR (SPA), SSR (động + SEO), SSG (tĩnh nhanh), ISR (SSG + revalidate). *(xem module liên quan)*
4. **Server Component vs Client Component (App Router)?** → Server render ở server, không JS xuống client; Client (`"use client"`) tương tác, dùng hook.
5. **Controlled vs uncontrolled? `key` để làm gì, sao không dùng index?**
6. *(Live-coding khả năng cao)* Viết component **search có debounce** gọi API, hoặc **todo list** controlled. → luyện tay trước.

> 🎯 Đây là phần anh phải tự tin nhất. Ôn kỹ module5 + module1.

---

## 🔴 Nhóm B — Node.js & REST API (LÕI của vị trí)

7. **REST API là gì? Nguyên tắc thiết kế?** → tài nguyên qua URL, dùng đúng HTTP method (GET/POST/PUT/PATCH/DELETE), stateless, status code đúng nghĩa. *(anh có EmoEase 80+ API để dẫn chứng)*
8. **GET vs POST vs PUT vs PATCH?** → GET đọc; POST tạo; PUT thay toàn bộ; PATCH sửa một phần.
9. **Status code: 200/201/400/401/403/404/500?** → thuộc lòng (xem ngân hàng 50 câu Q29).
10. **Xử lý lỗi & validate ở API thế nào?** → validate input (zod/joi), trả status + message rõ, try/catch, không lộ thông tin nhạy cảm.
11. **Auth trong API: token để đâu, gắn vào request thế nào?** → JWT/session; gắn header `Authorization: Bearer`; refresh token. *(kể cách EmoEase/CiteFlow làm)*
12. **Node.js là gì, vì sao non-blocking?** → runtime JS phía server, event loop + I/O bất đồng bộ → xử lý nhiều request không chặn luồng.
13. **(NestJS — LỖ HỔNG)** "Bạn dùng NestJS chưa?" → **Trả lời thật:** *"Em làm Node chủ yếu qua Next.js API routes và đã thiết kế REST API ở EmoEase. NestJS em chưa dùng production, nhưng em nắm khái niệm module/controller/service/DI và tin là học nhanh được vì cùng hệ TypeScript."* → KHÔNG bịa.

---

## 🟡 Nhóm C — Database (SQL + NoSQL/MongoDB)

14. **SQL: JOIN là gì? INNER vs LEFT JOIN?** → kết hợp bảng; INNER lấy phần chung, LEFT lấy hết bảng trái.
15. **Index để làm gì?** → tăng tốc truy vấn (đánh đổi: chậm ghi + tốn dung lượng).
16. **(MongoDB — LỖ HỔNG)** "Khi nào dùng SQL vs MongoDB?" → **Trả lời thật + có hiểu biết:** *"Em mạnh PostgreSQL (dùng ở CiteFlow qua Drizzle). MongoDB em chưa dùng production, nhưng em hiểu nó là document-based, hợp dữ liệu linh hoạt/schema-less; SQL hợp dữ liệu quan hệ chặt chẽ, cần JOIN và toàn vẹn."*
17. **N+1 query problem?** (nếu hỏi sâu) → gọi DB lặp trong vòng lặp → chậm; fix bằng JOIN/eager load.

---

## 🟡 Nhóm D — Testing (LỖ HỔNG — trả lời thật, thể hiện thái độ)

18. **"Bạn test code thế nào?"** → **Trả lời thật:** *"Ở dự án em chủ yếu test thủ công + có tiếp xúc Playwright (e2e) ở CiteFlow. Em đang chủ động học Jest + React Testing Library cho unit/integration test, vì em hiểu test giúp code tin cậy và dễ refactor."*
19. **Unit vs Integration vs E2E test khác nhau?** → unit (1 hàm/component cô lập); integration (nhiều phần phối hợp); e2e (luồng người dùng thật, vd Playwright). → **Học thuộc câu này** dù chưa làm nhiều.
20. **Test gì cho 1 component/API?** → logic quan trọng, edge case, lỗi; không test chi tiết triển khai.

> 🎯 Testing là điểm yếu thật → **biến thành thái độ cầu tiến**. Nhà tuyển dụng quý người thừa nhận + đang học hơn người chém gió.

---

## 🟡 Nhóm E — Design Patterns & Programming Principles (đang ôn)

21. **SOLID là gì?** (nắm ý chính) → 5 nguyên tắc thiết kế OOP giúp code dễ bảo trì/mở rộng (Single responsibility, Open/closed, Liskov, Interface segregation, Dependency inversion). Nhớ được S + O + D là ổn.
22. **Kể vài design pattern bạn biết.** → Singleton (1 instance), Factory (tạo object), Observer (pub/sub — giống event/state), Module. Liên hệ: React hooks ~ observer; context ~ dependency injection.
23. **DRY / KISS là gì?** → Don't Repeat Yourself; Keep It Simple. → kể cách anh tách custom hook (useFetch) để DRY.
24. **(Nếu chưa chắc)** thành thật: *"Em đang củng cố phần này, hiện áp dụng được DRY/SOLID ở mức tách component & custom hook."*

---

## 🟢 Nhóm F — Docker / CI-CD / Agile

25. **Docker để làm gì?** → đóng gói app + môi trường thành container, chạy nhất quán mọi nơi. *(anh dùng ở CiteFlow → kể được)*
26. **CI/CD là gì?** → tự động build/test/deploy mỗi khi push code; giảm lỗi thủ công.
27. **Agile/Scrum: sprint, standup, backlog?** → kể kinh nghiệm Agile ở FPT Software (BA intern).

---

## 🔴 Nhóm G — Đào dự án (CHẮC CHẮN hỏi — chuẩn bị kỹ nhất)

28. **"Kể về CiteFlow / dự án bạn tự hào nhất."** → dùng pitch trong `citeflow-kien-truc.md` + `module13`.
29. **"Vì sao dùng React Query / Next.js / Postgres?"** → đã có sẵn câu trả lời (module6 + citeflow-kien-truc).
30. **"Thử thách kỹ thuật lớn nhất + cách giải?"** → kể luồng scan fire-and-forget của CiteFlow (anh có comment timeout đó!) hoặc tích hợp 80+ API EmoEase.
31. **"Bạn làm phần nào, dùng AI tới đâu?"** → khung trả lời trung thực trong `module13` mục 4 (quyết định + kiểm chứng).

> ⚠️ **Đọc lại code CiteFlow/EmoEase trước buổi phỏng vấn** — họ sẽ hỏi "dòng này làm gì".

---

## Nhóm H — Hành vi & văn hóa OTSV

32. **"Vì sao ứng tuyển OTSV?"** → sản phẩm/web services, stack đúng sở trường (Next.js/React/TS), muốn phát triển fullstack; ở Đà Nẵng.
33. **"Điểm yếu của bạn?"** → thật + tích cực (rusty fundamentals / testing → đang chủ động ôn). *(xem danh-gia-va-lo-trinh mục 5)*
34. **"Cách bạn học công nghệ mới?"** → hợp văn hóa "fail quick, succeed quick" của họ: thử nhanh, học từ lỗi. Kể ví dụ thật (tự học để build EmoEase/CiteFlow).
35. **"Mục tiêu 1–2 năm tới?"** → trở thành fullstack engineer vững nền tảng.

---

## ❓ Câu anh NÊN hỏi lại họ (thể hiện nghiêm túc)
- Team frontend/backend bao nhiêu người, quy trình review code thế nào?
- Sản phẩm chính đang làm là gì, dùng Next.js App Router hay Pages?
- Có mentor/lộ trình phát triển cho người mới không?
- Kỳ vọng trong 3 tháng đầu của vị trí này?
- (Khéo) Mức lương & phúc lợi của vị trí (vì ITviec ẩn lương).

---

## ✅ Lịch ôn trúng 3–5 ngày trước phỏng vấn
- [ ] Ngày 1–2: React/Next (Nhóm A) + luyện tay 1 component live-coding.
- [ ] Ngày 2–3: Node/REST (Nhóm B) + thuộc status code + auth flow.
- [ ] Ngày 3: SQL/Mongo + Testing + Design patterns (học ý chính + câu trả lời thật cho lỗ hổng).
- [ ] Ngày 4: **Đọc lại code CiteFlow/EmoEase** + tập kể dự án (Nhóm G).
- [ ] Ngày 5: Hành vi (Nhóm H) + chuẩn bị câu hỏi ngược + mock 1 buổi.
