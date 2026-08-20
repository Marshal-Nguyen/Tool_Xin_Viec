# Giáo trình ôn phỏng vấn Frontend (React / Next.js / TypeScript)
### Soạn riêng cho Nguyễn Văn Trường Giang — Junior+/Mid Frontend

> **Cách dùng:** Học theo thứ tự module. Mỗi module: học lý thuyết → **code tay (KHÔNG dùng AI)** phần bài tập → tự trả lời câu hỏi thành tiếng → tick checkpoint. Khi tick được hết checkpoint của 1 module mới sang module sau.
>
> **Nguyên tắc vàng cho anh:** điểm yếu của anh là "hiểu nông vì dựa AI". Thuốc chữa là **active recall** (tự nhớ lại, không nhìn đáp án) + **code bằng tay** + **giải thích thành lời**. Đọc-hiểu thụ động gần như vô dụng cho phỏng vấn.

**Độ ưu tiên:** 🔴 Bắt buộc · 🟡 Nên có · 🟢 Cộng điểm
**Nguồn chuẩn (free):** MDN (developer.mozilla.org) · javascript.info · react.dev · typescriptlang.org/docs/handbook · nextjs.org/learn

---

## MODULE 0 — Cách ôn để NHỚ (đọc trước, 30 phút)
- **Active recall:** học xong 1 mục, gập tài liệu, tự viết lại/nói lại. Quên chỗ nào → học lại chỗ đó.
- **Code tay:** gõ lại ví dụ, đừng copy. Tự sửa lỗi. Lỗi là nơi anh học nhiều nhất.
- **Giải thích thành lời (Feynman):** giả vờ dạy cho người khác. Nói được = hiểu. Lắp bắp = chưa hiểu.
- **Spaced repetition:** ôn lại module cũ sau 1 ngày, 3 ngày, 7 ngày.
- **Mỗi ngày kết thúc bằng 1 câu hỏi:** "Hôm nay mình giải thích được điều gì mà hôm qua chưa?"

---

## MODULE 1 — JavaScript Core 🔴 (QUAN TRỌNG NHẤT — đầu tư nhiều nhất)

### Kiến thức cốt lõi (checklist)
- [ ] `var` / `let` / `const`, scope, hoisting, temporal dead zone
- [ ] Kiểu dữ liệu: primitive vs reference; copy theo giá trị vs tham chiếu
- [ ] `==` vs `===`, ép kiểu (coercion), truthy/falsy
- [ ] Closure (bao đóng) — **rất hay hỏi**
- [ ] `this` trong 4 ngữ cảnh: global, object method, arrow function, `call/apply/bind`
- [ ] Prototype & prototype chain, kế thừa
- [ ] Array methods: `map`, `filter`, `reduce`, `find`, `some`, `every`, `sort`, `slice` vs `splice`
- [ ] Object: destructuring, spread/rest, optional chaining `?.`, nullish `??`
- [ ] Bất đồng bộ: callback → Promise → `async/await`; `Promise.all/race/allSettled`
- [ ] Event loop: call stack, microtask vs macrotask (setTimeout vs Promise) — **hay hỏi**
- [ ] `try/catch`, xử lý lỗi async
- [ ] Higher-order functions, pure function, immutability

### Câu hỏi phỏng vấn điển hình
1. Closure là gì? Cho 1 ví dụ thực tế (vd: counter, debounce).
2. Giải thích `this` trong arrow function khác gì function thường?
3. Output của đoạn code có `setTimeout` + `Promise` này theo thứ tự nào? (event loop)
4. `let` và `var` khác nhau ra sao? Hoisting hoạt động thế nào?
5. `map` vs `forEach` khác gì? `reduce` dùng làm gì?
6. Sự khác nhau giữa shallow copy và deep copy? Cách deep copy?
7. Debounce và throttle khác nhau? Tự code 1 hàm debounce.

### Bài tập code tay (KHÔNG AI)
- [ ] Tự viết hàm `debounce(fn, delay)` và `throttle(fn, delay)`.
- [ ] Tự implement `Array.prototype.map` bằng `myMap`.
- [ ] Tự viết `deepClone(obj)` xử lý object lồng nhau + array.
- [ ] Dự đoán output 5 đoạn code event-loop trước khi chạy, rồi chạy kiểm tra.
- [ ] Viết counter dùng closure (không dùng biến global).

### ✅ Checkpoint — anh sẵn sàng khi:
Giải thích được closure, `this`, và thứ tự event loop **bằng lời + ví dụ tự nghĩ ra**, không nhìn tài liệu.

---

## MODULE 2 — TypeScript 🔴

### Cốt lõi
- [ ] Kiểu cơ bản, `interface` vs `type`, union `|`, intersection `&`
- [ ] Generics (`<T>`) — hàm và component generic
- [ ] `optional?`, readonly, literal types, enum
- [ ] Utility types: `Partial`, `Pick`, `Omit`, `Record`, `ReturnType`
- [ ] Type narrowing, type guard, `unknown` vs `any`
- [ ] Type cho props & hooks trong React (`useState<T>`, event types)

### Câu hỏi điển hình
1. `interface` và `type` khác nhau khi nào? Khi nào dùng cái nào?
2. Generic là gì? Cho ví dụ 1 hàm generic.
3. `any` và `unknown` khác nhau? Vì sao nên tránh `any`?
4. Cách type 1 prop component nhận callback `onChange`?

### Bài tập
- [ ] Type lại 1 component React cũ của anh từ `any` → đúng kiểu.
- [ ] Viết generic function `firstElement<T>(arr: T[]): T`.

### ✅ Checkpoint: type được props/state/event của 1 form React mà không dùng `any`.

---

## MODULE 3 — HTML & CSS 🔴

### Cốt lõi
- [ ] HTML semantic (`header/main/section/article/nav/footer`), vì sao quan trọng (SEO + a11y)
- [ ] Box model, `position` (relative/absolute/fixed/sticky), z-index, stacking context
- [ ] **Flexbox** (rất hay hỏi) — trục chính/phụ, `justify-content`, `align-items`, `flex-grow/shrink/basis`
- [ ] **Grid** — khi nào dùng grid vs flex
- [ ] Responsive: media query, mobile-first, đơn vị `rem/em/%/vw/vh`
- [ ] Specificity (độ ưu tiên CSS), cascade, inheritance
- [ ] Pseudo-class/element, transition cơ bản
- [ ] Accessibility cơ bản: alt, label, focus, contrast, ARIA (mức nhận biết)

### Câu hỏi điển hình
1. Phân biệt `position: absolute` vs `relative` vs `sticky`.
2. Flexbox: căn giữa 1 div theo cả 2 chiều?
3. Grid vs Flexbox — khi nào dùng cái nào?
4. CSS specificity tính thế nào? `#id` vs `.class` vs tag?
5. Semantic HTML giúp gì cho SEO và screen reader?

### Bài tập (clone không AI)
- [ ] Dựng 1 card responsive (ảnh + tiêu đề + nút) bằng Flexbox, mobile-first.
- [ ] Layout 1 trang gồm header/sidebar/content/footer bằng Grid.
- [ ] Căn giữa hoàn hảo 1 modal.

### ✅ Checkpoint: dựng được layout responsive cơ bản bằng tay, giải thích flex/grid bằng lời.

---

## MODULE 4 — Browser & Web Fundamentals 🔴

### Cốt lõi
- [ ] DOM là gì, DOM API cơ bản (`querySelector`, tạo/sửa/xoá node)
- [ ] Event: bubbling, capturing, delegation, `preventDefault`, `stopPropagation`
- [ ] Rendering pipeline cơ bản: HTML→DOM, CSS→CSSOM, layout, paint
- [ ] `localStorage` / `sessionStorage` / cookie — khác nhau, khi nào dùng
- [ ] HTTP cơ bản: method (GET/POST/PUT/PATCH/DELETE), status code (2xx/3xx/4xx/5xx), header
- [ ] CORS là gì, vì sao bị chặn, cách xử lý (mức hiểu)
- [ ] Cơ bản bảo mật FE: XSS, CSRF (nhận biết + cách phòng ở mức FE)
- [ ] Cache trình duyệt, cơ bản về CDN

### Câu hỏi điển hình
1. Event delegation là gì? Vì sao hữu ích?
2. localStorage vs cookie vs sessionStorage?
3. CORS lỗi do đâu? Ai sửa (FE hay BE)?
4. XSS là gì? React chống XSS thế nào mặc định?
5. Status 401 vs 403 khác gì? 301 vs 302?

### ✅ Checkpoint: giải thích event bubbling + CORS + sự khác nhau các storage bằng lời.

---

## MODULE 5 — React Core 🔴 (TRỌNG TÂM thứ 2 sau JS)

### Cốt lõi
- [ ] JSX, component, props vs state
- [ ] `useState` — cập nhật state bất đồng bộ, functional update
- [ ] Render & re-render: khi nào component re-render? — **rất hay hỏi**
- [ ] `useEffect` — dependency array, cleanup, lỗi thường gặp (infinite loop, stale closure)
- [ ] `useMemo` / `useCallback` — khi nào CẦN, khi nào lạm dụng
- [ ] `useRef` — DOM ref + giữ giá trị không gây re-render
- [ ] `useContext` — tránh prop drilling
- [ ] Lists & `key` — vì sao key quan trọng
- [ ] Controlled vs uncontrolled components (form)
- [ ] Lifting state up, component composition
- [ ] Custom hooks — tự viết hook tái sử dụng logic
- [ ] Reconciliation & Virtual DOM (mức hiểu nguyên lý)

### Câu hỏi điển hình (NHÓM HAY HỎI NHẤT)
1. Khi nào 1 component re-render? Làm sao tránh re-render thừa?
2. `useEffect` chạy khi nào? Cleanup function để làm gì? Cho ví dụ lỗi dependency.
3. `useMemo` vs `useCallback` — khác gì, khi nào dùng?
4. Vì sao không nên dùng index làm `key`?
5. Controlled component là gì? Khác uncontrolled?
6. Virtual DOM là gì? Vì sao React dùng nó?
7. `useState` cập nhật có đồng bộ không? `setCount(count+1)` 3 lần liên tiếp ra gì?
8. Prop drilling là gì? Cách giải quyết?

### Bài tập code tay (KHÔNG AI — quan trọng)
- [ ] Todo app: thêm/xoá/sửa/lọc, controlled input.
- [ ] Custom hook `useFetch(url)` trả `{data, loading, error}`.
- [ ] Custom hook `useDebounce(value, delay)` + ô search gọi API.
- [ ] Counter có `useEffect` log mỗi lần đổi + cleanup.
- [ ] Demo `useMemo`/`useCallback` để chặn re-render con (dùng `React.memo`).

### ✅ Checkpoint: tự code todo app + 1 custom hook from scratch, giải thích vòng đời render bằng lời.

---

## MODULE 6 — React Ecosystem 🟡

### Cốt lõi
- [ ] **React Query / TanStack Query** (anh đã dùng ở EmoEase — ôn cho chắc): query, mutation, cache, invalidate, stale time, retry → **giải thích được vì sao dùng**
- [ ] **React Router**: route, nested route, params, navigate, protected route
- [ ] State management: khi nào Context đủ, khi nào cần Redux Toolkit / Zustand
- [ ] Axios: interceptor, xử lý token, error handling tập trung

### Câu hỏi điển hình
1. Vì sao dùng React Query thay vì tự `useEffect` + fetch? (anh có câu chuyện EmoEase ở đây!)
2. Cache invalidation hoạt động thế nào trong React Query?
3. Context vs Redux — khi nào cần Redux?

### ✅ Checkpoint: giải thích trôi chảy vì sao EmoEase dùng React Query (đây là điểm mạnh phỏng vấn của anh).

---

## MODULE 7 — Next.js 🟡 (anh đang dùng ở CiteFlow → ôn để nói được)

### Cốt lõi
- [ ] CSR vs SSR vs SSG vs ISR — khác nhau, khi nào dùng cái nào — **hay hỏi**
- [ ] App Router vs Pages Router (anh dùng cái nào ở CiteFlow?)
- [ ] Server Components vs Client Components (`"use client"`)
- [ ] Data fetching trong Next.js, route handlers / API routes
- [ ] File-based routing, dynamic routes, layout
- [ ] SEO trong Next.js (metadata) — liên quan trực tiếp công việc GEO của anh

### Câu hỏi điển hình
1. SSR và SSG khác nhau? Ví dụ trang nào nên dùng cái nào?
2. Server Component khác Client Component thế nào? Khi nào cần `"use client"`?
3. Next.js giúp SEO ra sao so với React SPA thuần?

### ✅ Checkpoint: vẽ được sơ đồ CSR/SSR/SSG/ISR và nói rõ CiteFlow dùng gì, vì sao.

---

## MODULE 8 — DSA cơ bản cho Frontend 🟡 (đủ qua vòng coding, không cần thi đấu)

### Cốt lõi
- [ ] Big-O: O(1), O(n), O(n²), O(log n) — nhận biết
- [ ] Array & String: two pointers, sliding window cơ bản
- [ ] Hash map (object/Map) để đếm, tìm trùng
- [ ] Recursion cơ bản
- [ ] Stack/Queue (khái niệm)

### Dạng bài hay gặp (luyện ~20-30 bài là đủ mức FE junior/mid)
- Reverse string/array, kiểm tra palindrome
- Two sum (hashmap)
- Đếm tần suất ký tự, tìm ký tự xuất hiện 1 lần
- FizzBuzz, fibonacci (recursion + memo)
- Loại bỏ trùng trong mảng, flatten mảng lồng
- Group anagrams

### Bài tập
- [ ] Giải 20 bài "easy" trên LeetCode/array-string, **tự nghĩ trước 10 phút rồi mới xem gợi ý**.
- [ ] Mỗi bài: nói được độ phức tạp Big-O của lời giải.

### ✅ Checkpoint: giải được Two Sum + reverse + đếm tần suất bằng tay, nói được Big-O.

---

## MODULE 9 — Git & Quy trình làm việc 🔴

### Cốt lõi
- [ ] `clone/add/commit/push/pull`, branch, merge
- [ ] Merge vs rebase (mức hiểu), conflict & cách giải quyết
- [ ] Pull request, code review flow
- [ ] `.gitignore`, commit message tốt

### Câu hỏi điển hình
1. Merge và rebase khác nhau?
2. Xử lý conflict thế nào?
3. Quy trình làm việc với feature branch + PR?

### ✅ Checkpoint: mô tả được flow branch → PR → review → merge bằng lời.

---

## MODULE 10 — Frontend "Build X" / Thiết kế cơ bản 🟢 (cho mid)

### Cốt lõi
- Tư duy chia component, quản lý state, gọi API, xử lý loading/error/empty
- Phân trang, infinite scroll, search + debounce
- Khi được hỏi "thiết kế component autocomplete/giỏ hàng": nói về cấu trúc, state, API, edge case

### Bài tập
- [ ] Thiết kế (nói + vẽ) 1 ô search autocomplete: component nào, state gì, gọi API ra sao, debounce, xử lý lỗi.

### ✅ Checkpoint: trình bày được cách build 1 feature vừa (autocomplete/list có filter) trong 5 phút.

---

## MODULE 11 — Testing 🟢 (cộng điểm, không bắt buộc junior)
- [ ] Khái niệm unit/integration test
- [ ] Jest cơ bản + React Testing Library (render, query, fireEvent)
- [ ] Viết 1 test cho 1 component nhỏ

---

## MODULE 12 — Performance & Web Vitals 🟡 (liên quan công việc SEO/GEO của anh)
- [ ] Core Web Vitals: LCP, CLS, INP (mức nhận biết)
- [ ] Lazy loading, code splitting, `React.lazy`/`Suspense`
- [ ] Tối ưu ảnh, memo hoá, tránh re-render thừa
- [ ] **Liên hệ công việc thật:** anh làm SEO/GEO ở CiteFlow → đây là điểm mạnh, ôn để nói sâu

---

## MODULE 13 — Đào sâu DỰ ÁN của anh 🔴 (vòng phỏng vấn nào cũng hỏi)

> Đây là phần anh **chắc chắn ghi điểm** nếu chuẩn bị kỹ. Chuẩn bị cho CẢ EmoEase và CiteFlow:

- [ ] Vẽ được **kiến trúc** (FE, API, DB, luồng dữ liệu).
- [ ] Giải thích **TỪNG quyết định kỹ thuật**: vì sao React Query? vì sao cấu trúc thư mục này? vì sao Supabase?
- [ ] **Thử thách lớn nhất** đã gặp + cách giải quyết (kể cả khi dùng AI: nói anh đã *quyết định* và *kiểm chứng* thế nào).
- [ ] Nếu làm lại sẽ làm khác gì? (câu hỏi hay gặp, cho thấy sự trưởng thành)
- [ ] Con số: 1,000+ users, 80+ API — kể được bối cảnh.

> ⚠️ Lưu ý thật lòng: nếu anh dựa AI nhiều, hãy **đọc lại code EmoEase/CiteFlow trước phỏng vấn** để chắc chắn giải thích được từng phần. Phỏng vấn sẽ đào: "dòng này làm gì?", "vì sao chọn cách này?".

---

## MODULE 14 — Hành vi & STAR 🔴
- [ ] Chuẩn bị 5 câu chuyện STAR (Tình huống–Nhiệm vụ–Hành động–Kết quả) từ EmoEase/CiteFlow/FPT.
- [ ] Câu trả lời "điểm yếu" trung thực + tích cực (xem mục 5 file `danh-gia-va-lo-trinh.md`).
- [ ] "Vì sao chọn công ty này?", "Mục tiêu 1-2 năm tới?", "Vì sao rời/đang tìm việc mới?"
- [ ] Câu hỏi NGƯỢC lại nhà tuyển dụng (cho thấy sự nghiêm túc).

---

## MODULE 15 — English phỏng vấn 🟡 (cho job quốc tế/remote — giai đoạn 2)
- [ ] Giới thiệu bản thân 60 giây (viết sẵn + luyện nói thuộc).
- [ ] Mô tả 1 dự án bằng tiếng Anh trong 3-5 phút.
- [ ] 20 cụm từ kỹ thuật hay dùng khi nói về code.
- [ ] Luyện nói 5-10 phút/ngày (tự quay video, nghe lại).

---

## MODULE 16 — Mock Interview & Checklist sẵn sàng 🔴
- [ ] Tự mock 1 buổi: 1 câu JS + 1 câu React + 1 live-coding nhỏ + 1 câu STAR. Quay video, xem lại.
- [ ] Nhờ bạn/mentor phỏng vấn thử 2-3 lần.
- [ ] **Dùng hệ thống career-ops:** với mỗi công ty cụ thể, chạy `/career-ops interview-prep` để có bộ câu hỏi + intel riêng cho công ty đó.

### ✅ "Anh sẵn sàng đi phỏng vấn khi:"
- Tick hết checkpoint các module 🔴 (1,2,3,4,5,9,13,14).
- Tự code được todo app + custom hook from scratch, không AI.
- Giải thích trôi chảy closure, event loop, vòng đời render React, và 2 dự án của mình.
- Đã mock interview ít nhất 2 lần.

---

## Bổ sung cho vị trí FULL-STACK (thêm 08/2026)

Giáo trình trên là **Frontend**. Khi ứng tuyển vị trí full-stack (Node.js/Next.js), học thêm:

- **[Module 17 — Node.js Backend](module17-nodejs-backend.md)** 🔴 — event loop, async, Express, NestJS, REST API, xác thực, bảo mật
- **[Module 18 — Database SQL & NoSQL](module18-database-sql-nosql.md)** 🔴 — thiết kế schema, index, N+1, transaction, MongoDB, Redis
- **[Phỏng vấn Everlastify](phong-van-everlastify.md)** — bản đồ khớp/lệch, câu hỏi dự đoán, lịch ôn 7 ngày

## Thứ tự đề xuất (nếu phân vân học gì trước)
1 (JS) → 5 (React) → 2 (TS) → 3 (CSS) → 4 (Browser) → 13 (dự án) → 14 (STAR) → 9 (Git) → 6,7 (ecosystem/Next) → 8 (DSA) → còn lại.
**Nếu đi phỏng vấn full-stack:** chen 17 và 18 vào ngay sau 5 (React), vì đó là phần anh chưa có nền.

> **Lời nhắn:** đừng cố hoàn hảo mọi module mới đi phỏng vấn. Học chắc nhóm 🔴, **vừa ôn vừa rải đơn**. Mỗi buổi phỏng vấn thật là một bài học giá trị hơn cả tuần tự ôn. Anh có nền sản phẩm tốt rồi — phần còn lại chỉ là làm cho kiến thức "nông" thành "chắc".
