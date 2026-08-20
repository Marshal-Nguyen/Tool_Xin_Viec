# Ngân hàng 50 câu hỏi phỏng vấn Frontend (React/Next.js/TS) + đáp án ngắn

> **Cách dùng:** Che cột đáp án, tự trả lời **thành tiếng**, rồi mở ra đối chiếu. Trả lời được trôi chảy = nhớ. Lắp bắp = ôn lại. Đáp án dưới là "đủ ý để qua phỏng vấn", anh nên tự mở rộng bằng ví dụ.

---

## A. JavaScript Core (Q1–Q13) 🔴

**Q1. `var`, `let`, `const` khác nhau?**
`var`: function-scoped, hoisted (= undefined trước khi gán). `let`/`const`: block-scoped, hoisted nhưng nằm trong **TDZ** (không truy cập trước khai báo). `const` không gán lại được (nhưng object/array bên trong vẫn mutate được).

**Q2. Closure là gì?**
Hàm "nhớ" được scope nơi nó **được tạo ra**, truy cập biến ngoài kể cả sau khi hàm bao đã return. VD: hàm `counter` giữ biến `count` riêng tư.

**Q3. `this` được xác định thế nào?**
Phụ thuộc **cách gọi**: gọi như method → object đó; gọi standalone → `undefined`/global; **arrow function** → lấy `this` của scope bao quanh (lexical, không có `this` riêng); `call/apply/bind` → set thủ công.

**Q4. `==` vs `===`?**
`===` so sánh **không ép kiểu** (strict). `==` ép kiểu rồi mới so. Luôn ưu tiên `===` để tránh bug.

**Q5. Event loop: vì sao `Promise.then` chạy trước `setTimeout(0)`?**
Sau khi call stack rỗng, JS chạy **hết microtask queue** (Promise.then) **trước**, rồi mới lấy **1 macrotask** (setTimeout). Nên Promise luôn ưu tiên hơn setTimeout dù delay 0.

**Q6. `map` vs `forEach`?**
`map` trả về **mảng mới** đã biến đổi (chainable). `forEach` chỉ lặp, **không trả** gì (undefined).

**Q7. `reduce` dùng làm gì?**
Gộp cả mảng thành **1 giá trị** qua accumulator. VD: tính tổng, đếm tần suất, nhóm dữ liệu.

**Q8. Shallow copy vs deep copy?**
Shallow chỉ sao tầng 1 (object lồng vẫn **chung tham chiếu**). Deep clone sao toàn bộ. Cách deep: `structuredClone(obj)`, lodash `cloneDeep`, (JSON.parse(JSON.stringify) — hạn chế: mất function/Date).

**Q9. Debounce vs throttle?**
**Debounce**: chỉ chạy sau khi **ngừng** trigger một khoảng (ô search). **Throttle**: chạy tối đa **1 lần mỗi khoảng** (scroll, resize).

**Q10. `Promise.all` vs `allSettled` vs `race`?**
`all`: chờ tất cả, **fail 1 là reject**. `allSettled`: chờ tất cả, trả status từng cái (không reject). `race`: cái nào xong/lỗi **trước** thì lấy.

**Q11. Primitive vs reference type?**
Primitive (string, number, boolean, null, undefined, symbol, bigint) **copy theo giá trị**. Object/array/function **copy theo tham chiếu**.

**Q12. Hoisting là gì?**
Khai báo được "đưa lên đầu" scope. `var` → undefined; `function` declaration → cả thân hàm (gọi được trước khi khai báo); `let/const` → TDZ.

**Q13. `async/await` so với `.then()`?**
`async/await` là cú pháp dễ đọc trên Promise; `await` tạm dừng đến khi Promise resolve. Bắt lỗi bằng `try/catch`. Bản chất vẫn là Promise.

---

## B. TypeScript (Q14–Q17) 🔴

**Q14. `interface` vs `type`?**
Cả 2 mô tả object được. `interface` **mở rộng/merge** được (hợp OOP). `type` linh hoạt hơn: union `|`, tuple, primitive. Union → bắt buộc dùng `type`.

**Q15. `any` vs `unknown`?**
`any` tắt hết type-check (nguy hiểm). `unknown` an toàn: phải **narrow** (kiểm tra kiểu) trước khi dùng.

**Q16. Generic là gì?**
Tham số kiểu (`<T>`) cho hàm/component tái dùng với nhiều kiểu mà **vẫn giữ type-safety**. VD: `firstElement<T>(arr: T[]): T`.

**Q17. Kể vài utility types.**
`Partial<T>` (tất cả optional), `Pick<T,K>`/`Omit<T,K>` (chọn/bỏ field), `Record<K,V>`, `ReturnType<F>`.

---

## C. HTML & CSS (Q18–Q24) 🔴

**Q18. Semantic HTML là gì, lợi ích?**
Thẻ có ý nghĩa (`header/nav/main/article/section/footer`). Lợi: **SEO** tốt hơn, **screen reader** đọc đúng, code dễ bảo trì.

**Q19. Box model?**
content + padding + border + margin. `box-sizing: border-box` gộp padding/border vào trong `width` (dễ tính layout).

**Q20. `position`: static/relative/absolute/fixed/sticky?**
static (mặc định); relative (lệch so với **chính nó**); absolute (so với **ancestor có position**); fixed (so với **viewport**); sticky (relative đến khi scroll tới ngưỡng thì dính như fixed).

**Q21. Căn giữa 1 phần tử cả 2 chiều?**
`display:flex; justify-content:center; align-items:center;` (hoặc grid: `place-items:center`).

**Q22. Flexbox vs Grid?**
Flex: bố cục **1 chiều** (hàng HOẶC cột). Grid: **2 chiều** (hàng VÀ cột) — hợp layout tổng thể.

**Q23. CSS specificity?**
Thứ tự ưu tiên: inline > `#id` > `.class`/attr/pseudo-class > tag. `!important` ghi đè tất cả (nên tránh).

**Q24. `rem` vs `em`?**
`rem` theo **root** font-size (html). `em` theo font-size của **element cha** gần nhất (có thể cộng dồn).

---

## D. Browser & Web (Q25–Q30) 🔴

**Q25. Event bubbling & delegation?**
Event "nổi" từ target lên cha (bubbling). **Delegation**: gắn 1 listener ở phần tử cha, tận dụng bubbling để xử lý nhiều con (hiệu quả với list động).

**Q26. localStorage vs sessionStorage vs cookie?**
localStorage: lâu dài (~5MB, không tự gửi server). sessionStorage: mất khi đóng tab. cookie: nhỏ (~4KB), **gửi kèm mỗi request**, có expiry/HttpOnly (hợp auth token).

**Q27. CORS là gì? Ai sửa?**
Cơ chế bảo mật trình duyệt chặn request **khác origin** trừ khi server cho phép (header `Access-Control-Allow-Origin`). Sửa ở **Backend**.

**Q28. XSS là gì? React chống thế nào?**
Kẻ tấn công chèn script độc qua input. React **tự escape** nội dung trong JSX → an toàn mặc định; nguy hiểm khi dùng `dangerouslySetInnerHTML` bừa.

**Q29. 401 vs 403? 301 vs 302?**
401: chưa xác thực (chưa đăng nhập). 403: đã xác thực nhưng **không có quyền**. 301: chuyển hướng **vĩnh viễn**. 302: **tạm thời**.

**Q30. GET vs POST?**
GET: lấy dữ liệu, idempotent, tham số ở URL, không body. POST: gửi/tạo dữ liệu, có **body**, không idempotent.

---

## E. React Core (Q31–Q42) 🔴 (NHÓM HAY HỎI NHẤT)

**Q31. Khi nào component re-render? Tránh thừa thế nào?**
Khi **state/props đổi**, **parent re-render**, hoặc **context value đổi**. Tránh thừa: `React.memo`, `useMemo`/`useCallback`, tách component, đừng tạo object/function mới mỗi render truyền xuống con.

**Q32. `useEffect` chạy khi nào? Cleanup để làm gì?**
Chạy **sau render**. Deps `[]` → 1 lần khi mount; `[a]` → khi a đổi; không deps → mỗi render. **Cleanup** chạy trước lần effect kế + khi unmount (huỷ subscription, clear timer).

**Q33. `useMemo` vs `useCallback`?**
`useMemo` cache **giá trị** (tính toán nặng). `useCallback` cache **hàm** (truyền xuống con đã `memo`). Đừng lạm dụng — bản thân chúng cũng có chi phí.

**Q34. Vì sao không nên dùng index làm `key`?**
Khi list **đổi thứ tự/thêm/xoá**, index không ổn định → React map sai item → bug state, re-render sai, input nhảy giá trị. Dùng **id ổn định**.

**Q35. Controlled vs uncontrolled component?**
Controlled: giá trị do **React state** quản (`value` + `onChange`). Uncontrolled: **DOM tự quản**, đọc qua `ref`. Form thực tế hay dùng controlled.

**Q36. Virtual DOM là gì?**
Bản sao nhẹ của DOM trong bộ nhớ. React **diff** VDOM mới vs cũ rồi cập nhật **DOM thật tối thiểu** → nhanh hơn thao tác DOM trực tiếp nhiều lần.

**Q37. `useState` có cập nhật ngay không? `setCount(count+1)` 3 lần ra gì?**
**Không** — React gom batch, dùng `count` cũ → chỉ **+1**. Muốn +3 dùng **functional update**: `setCount(c => c+1)` ba lần.

**Q38. Prop drilling là gì? Cách xử lý?**
Truyền props qua nhiều tầng trung gian không cần. Giải: **Context API**, component composition, hoặc state lib (Zustand/Redux).

**Q39. `useRef` dùng khi nào?**
Giữ tham chiếu **DOM** (focus, đo kích thước) hoặc giá trị **mutable không gây re-render** khi đổi (vd lưu timer id, giá trị trước đó).

**Q40. Custom hook là gì?**
Hàm bắt đầu bằng `use`, gói logic dùng hook để **tái sử dụng** (vd `useFetch`, `useDebounce`). Tuân theo Rules of Hooks.

**Q41. Stale closure trong `useEffect`?**
Effect "bắt" giá trị **cũ** nếu thiếu dependency. Fix: thêm biến vào deps array, hoặc dùng functional update / ref.

**Q42. `React.memo` làm gì?**
HOC giúp component con **không re-render** nếu props không đổi (so sánh nông/shallow). Hợp với con render nặng.

---

## F. React Ecosystem (Q43–Q45) 🟡

**Q43. Vì sao dùng React Query thay vì `useEffect`+fetch?** *(câu chuyện EmoEase của anh)*
Quản **server state**: cache, dedupe request, retry, background refetch, tự có loading/error, invalidation. Giảm boilerplate, đồng bộ dữ liệu tốt hơn nhiều so với tự fetch.

**Q44. Context API vs Redux?**
Context: dữ liệu **ít đổi** (theme, auth) — đổi value làm **mọi consumer re-render**. Redux/Zustand: state phức tạp, nhiều update, cần devtools/middleware.

**Q45. Làm protected route với React Router?**
Bọc route bằng component kiểm tra auth; nếu chưa đăng nhập → `<Navigate to="/login" />`.

---

## G. Next.js (Q46–Q48) 🟡

**Q46. CSR vs SSR vs SSG vs ISR?**
CSR: render ở client (SPA). SSR: render **mỗi request** ở server (dữ liệu động, SEO tốt). SSG: render lúc **build** (tĩnh, nhanh nhất). ISR: SSG + **revalidate** định kỳ.

**Q47. Server Component vs Client Component?**
Server (mặc định App Router): render ở server, **không gửi JS xuống**, không dùng state/hook/sự kiện. Client (`"use client"`): tương tác được, dùng `useState`/`useEffect`.

**Q48. Next.js giúp SEO thế nào so với React SPA?**
SSR/SSG trả **HTML có nội dung sẵn** cho crawler; React SPA thuần trả trang rỗng + JS (crawler khó đọc). Next có Metadata API. *(liên quan trực tiếp việc GEO/SEO của anh ở CiteFlow)*

---

## H. Git (Q49–Q50) 🔴

**Q49. Merge vs rebase?**
`merge`: tạo commit gộp, **giữ** lịch sử nhánh. `rebase`: dời commit lên đầu nhánh khác → lịch sử **thẳng**. ⚠️ Không rebase nhánh đã push chung.

**Q50. Xử lý conflict thế nào?**
Khi 2 nhánh sửa **cùng dòng**, Git đánh dấu `<<<<<<< ======= >>>>>>>`. Mình chọn/ghép phần đúng, xoá dấu, rồi `git add` + `git commit`.

---

## Cách tự chấm
- Trả lời trôi chảy + cho được **ví dụ tự nghĩ** → ✅ nắm chắc.
- Nhớ ý nhưng không ví dụ được → 🟡 ôn thêm.
- Không nhớ → 🔴 quay lại module tương ứng trong `giao-trinh-on-phong-van.md`.

> Mẹo: làm 10 câu/ngày, hôm sau ôn lại 10 câu cũ trước khi học 10 câu mới (spaced repetition).
