# Module 5 — React Core (Bài giảng chi tiết)

> Nhóm câu hỏi **hay gặp nhất** khi phỏng vấn Frontend. Dựa trực tiếp trên Module 1 (closure, immutability, `this`). Gõ lại từng ví dụ, dựng thử trên CodeSandbox/Vite.

Mục lục:
1. JSX, Component, Props vs State
2. `useState` & cập nhật bất đồng bộ
3. Render & Re-render — khi nào? (CỐT LÕI)
4. `useEffect` — deps, cleanup, 2 bug kinh điển
5. `useMemo` / `useCallback`
6. `useRef`
7. `useContext` & prop drilling
8. Lists & `key`
9. Controlled vs Uncontrolled (form)
10. Lifting state up & Composition
11. Custom Hooks
12. Virtual DOM & Reconciliation

---

## 1. JSX, Component, Props vs State

- **Component**: hàm trả về JSX (UI). Tên **viết hoa** chữ đầu.
- **Props**: dữ liệu **cha truyền xuống con**, **read-only** (con không được sửa).
- **State**: dữ liệu **nội bộ** component, đổi được qua hook, đổi → **re-render**.

```jsx
function Welcome({ name }) {        // name là prop
  const [count, setCount] = useState(0); // count là state
  return <h1>Hi {name}, count={count}</h1>;
}
// dùng: <Welcome name="Giang" />
```
> 🎯 **Phỏng vấn:** "Props vs State khác gì?" → Props từ ngoài vào, bất biến với con. State nội bộ, thay đổi được, gây re-render. **Không bao giờ sửa props trực tiếp.**

---

## 2. `useState` & cập nhật bất đồng bộ (rất hay bẫy)

```jsx
const [count, setCount] = useState(0);
```
`setCount` **không cập nhật ngay** — React gom (batch) rồi re-render. Trong cùng 1 lần xử lý, `count` vẫn là giá trị cũ:

```jsx
const handleClick = () => {
  setCount(count + 1);
  setCount(count + 1);
  setCount(count + 1);
};
// count chỉ thành 1 (cả 3 lần đều dùng count = 0 cũ)
```

**Fix — functional update** (dùng giá trị mới nhất):
```jsx
setCount(c => c + 1);
setCount(c => c + 1);
setCount(c => c + 1);
// count thành 3 ✅
```

**Update object/array — phải IMMUTABLE** (nối Module 1):
```jsx
// ❌ SAI — mutate trực tiếp, React không re-render
user.name = 'new'; setUser(user);

// ✅ ĐÚNG — tạo bản sao mới
setUser({ ...user, name: 'new' });
setList([...list, newItem]);
setList(list.filter(x => x.id !== id));
```
> 🎯 **Phỏng vấn:** "`setCount(count+1)` 3 lần ra mấy?" → 1, vì batching + stale value. Cách fix → functional update. Câu này lọc người hiểu sâu.

---

## 3. Render & Re-render — KHI NÀO? (câu hỏi cốt lõi)

Một component **re-render** khi:
1. **State của nó đổi** (`setX`).
2. **Component cha re-render** (con render lại theo, kể cả props không đổi — trừ khi `React.memo`).
3. **Context nó đang dùng đổi value**.

```jsx
function Parent() {
  const [n, setN] = useState(0);
  return (
    <div onClick={() => setN(n + 1)}>
      <Child />   {/* Child re-render mỗi lần Parent re-render, dù không nhận prop */}
    </div>
  );
}
```

**Cách tránh re-render thừa:**
- `React.memo(Child)` — chặn re-render nếu props không đổi.
- `useMemo` / `useCallback` — giữ ổn định giá trị/hàm truyền xuống con đã memo.
- Tách component nhỏ; đừng đặt state cao hơn mức cần.
- Đừng tạo object/array/function **mới mỗi render** truyền xuống con memo (sẽ phá memo).

> 🎯 **Phỏng vấn (đỉnh):** "Khi nào component re-render? Làm sao tối ưu?" → trả lời 3 nguyên nhân + công cụ tối ưu. Đây là câu phân loại junior/mid.

---

## 4. `useEffect` — deps, cleanup & 2 bug kinh điển

`useEffect` chạy **SAU khi render xong**. Dùng cho side effect: gọi API, subscribe, timer, thao tác DOM.

```jsx
useEffect(() => {
  // effect chạy ở đây
  const id = setInterval(() => console.log('tick'), 1000);
  return () => clearInterval(id);  // CLEANUP
}, [deps]);
```

**Dependency array quyết định khi nào chạy:**
| Deps | Khi nào chạy |
|------|--------------|
| `[]` | 1 lần khi **mount** |
| `[a, b]` | khi a hoặc b **đổi** |
| (không có) | **mỗi lần render** |

**Cleanup** chạy: trước lần effect kế tiếp + khi component **unmount** (huỷ timer, huỷ subscribe, tránh memory leak).

### Bug 1 — Infinite loop
```jsx
// ❌ set state trong effect không có deps → render → effect → set → render...
useEffect(() => {
  setCount(count + 1);
}); // thiếu deps array
```
Fix: thêm deps đúng, hoặc đừng setState vô điều kiện trong effect.

### Bug 2 — Stale closure (nối thẳng closure ở Module 1)
```jsx
useEffect(() => {
  const id = setInterval(() => {
    console.log(count); // luôn in 0! (closure "bắt" count lúc mount)
  }, 1000);
  return () => clearInterval(id);
}, []); // deps rỗng → closure giữ count cũ mãi
```
Fix: thêm `count` vào deps, **hoặc** dùng functional update / `useRef`.
```jsx
setCount(c => c + 1); // không phụ thuộc count cũ
```
> 🎯 **Phỏng vấn:** "useEffect chạy khi nào? cleanup để làm gì? cho ví dụ bug deps." → trả lời được cả stale closure = ăn điểm lớn (vì liên hệ closure).

---

## 5. `useMemo` / `useCallback`

- `useMemo(fn, deps)` → cache **giá trị** trả về (tính toán nặng).
- `useCallback(fn, deps)` → cache **hàm** (truyền xuống con đã `memo`).

```jsx
// Tính toán nặng — chỉ chạy lại khi 'items' đổi
const sorted = useMemo(() => items.slice().sort(expensiveCmp), [items]);

// Hàm ổn định để con memo không re-render
const handleClick = useCallback(() => doSomething(id), [id]);
```

**Khi nào DÙNG:** tính toán thật sự nặng, hoặc truyền hàm/giá trị xuống con đã `React.memo`.
**Khi nào ĐỪNG:** mọi thứ lặt vặt — bản thân memo cũng tốn bộ nhớ + so sánh. **Lạm dụng = chậm hơn**.

> 🎯 Mẹo trả lời: "`useMemo` cache value, `useCallback` cache function. Chỉ dùng khi có vấn đề hiệu năng đo được, không dùng mặc định."

---

## 6. `useRef`

2 công dụng:
1. **Tham chiếu DOM** (focus, đo kích thước, play video).
2. **Giữ giá trị mutable KHÔNG gây re-render** khi đổi (timer id, giá trị trước đó).

```jsx
// 1) DOM ref
const inputRef = useRef(null);
useEffect(() => inputRef.current.focus(), []);
return <input ref={inputRef} />;

// 2) Giá trị giữa các render mà không re-render
const renderCount = useRef(0);
renderCount.current++; // đổi .current KHÔNG làm re-render
```
> 🎯 **Phỏng vấn:** "`useRef` khác `useState` chỗ nào?" → đổi `ref.current` **không** re-render; đổi state **có**. Ref hợp để lưu thứ không liên quan UI.

---

## 7. `useContext` & prop drilling

**Prop drilling**: truyền props qua nhiều tầng trung gian không cần. **Context** giải quyết:

```jsx
const ThemeContext = createContext('light');

function App() {
  return (
    <ThemeContext.Provider value="dark">
      <Toolbar />
    </ThemeContext.Provider>
  );
}
function DeepButton() {
  const theme = useContext(ThemeContext); // lấy trực tiếp, không cần truyền qua Toolbar
  return <button className={theme}>OK</button>;
}
```
⚠️ Lưu ý: khi Context **value đổi**, **mọi consumer re-render**. Đừng nhét state đổi liên tục vào 1 context lớn.
> 🎯 "Khi nào Context, khi nào Redux?" → Context cho dữ liệu ít đổi (theme, user, locale). State phức tạp/đổi nhiều → Redux/Zustand.

---

## 8. Lists & `key`

```jsx
{users.map(u => <li key={u.id}>{u.name}</li>)}
```
`key` giúp React biết item nào **thêm/xoá/đổi vị trí** giữa 2 lần render.

**Vì sao ĐỪNG dùng index làm key:** khi list đổi thứ tự / chèn / xoá, index không ổn định → React map sai state vào sai item → bug (input nhảy giá trị, checkbox sai). Dùng **id ổn định, duy nhất**.

> 🎯 Câu hay hỏi: "Vì sao không dùng index làm key?" → trả lời rõ tình huống reorder/insert gây bug.

---

## 9. Controlled vs Uncontrolled (form)

```jsx
// Controlled — React state là "nguồn sự thật"
const [email, setEmail] = useState('');
<input value={email} onChange={e => setEmail(e.target.value)} />

// Uncontrolled — DOM tự quản, đọc qua ref khi cần
const ref = useRef();
<input ref={ref} />;  // ref.current.value
```
**Controlled** dễ validate/biến đổi realtime, là cách phổ biến. **Uncontrolled** đơn giản, ít re-render (form lớn / file input).
> 🎯 "Controlled vs uncontrolled?" → controlled: value do state quản (value+onChange); uncontrolled: DOM quản, đọc qua ref.

---

## 10. Lifting state up & Composition

- **Lifting state up**: khi 2 component anh em cần chia sẻ state → đưa state lên **cha chung**, truyền xuống qua props.
- **Composition** (thay vì kế thừa): truyền JSX qua `children` để tái sử dụng.

```jsx
function Card({ title, children }) {
  return <div className="card"><h3>{title}</h3>{children}</div>;
}
<Card title="Hồ sơ"><Profile /></Card>
```
> 🎯 React khuyến khích **composition over inheritance** — câu này thỉnh thoảng hỏi.

---

## 11. Custom Hooks (anh đã dùng ở EmoEase — ôn cho chắc)

Hàm bắt đầu bằng `use`, gói logic dùng hook để **tái sử dụng**.

```jsx
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    let active = true;            // tránh setState sau unmount (cleanup)
    setLoading(true);
    fetch(url)
      .then(r => r.json())
      .then(d => { if (active) { setData(d); setLoading(false); } })
      .catch(e => { if (active) { setError(e); setLoading(false); } });
    return () => { active = false; };
  }, [url]);

  return { data, loading, error };
}
// dùng: const { data, loading, error } = useFetch('/api/user');
```
> 🎯 **Phỏng vấn:** "Custom hook là gì? Khi nào tạo?" → khi logic dùng hook bị lặp ở nhiều component (fetch, debounce, form). Kể `useFetch`/`useDebounce` anh từng viết.

---

## 12. Virtual DOM & Reconciliation

- **Virtual DOM**: bản sao nhẹ của DOM trong bộ nhớ (object JS).
- Khi state đổi, React tạo VDOM mới, **diff** với VDOM cũ (reconciliation), rồi cập nhật **DOM thật tối thiểu** cần thiết → nhanh hơn sửa DOM trực tiếp nhiều lần.
- `key` giúp thuật toán diff list hiệu quả.

> 🎯 "Virtual DOM là gì, vì sao React dùng?" → diff trong bộ nhớ rồi cập nhật DOM thật tối thiểu; thao tác DOM thật đắt, gom lại 1 lần sẽ nhanh.

---

## ✅ Bài tập code TAY (không AI)
1. **Todo app**: thêm/xoá/toggle/lọc (all/active/done), input controlled, update **immutable**.
2. **`useFetch(url)`** từ đầu (có loading/error + cleanup chống setState sau unmount).
3. **`useDebounce(value, delay)`** + ô search gọi API giả.
4. Demo `React.memo` + `useCallback`: chứng minh con KHÔNG re-render khi cha đổi state không liên quan.
5. Sửa bug stale closure: 1 `setInterval` in count — làm cho nó in đúng số tăng dần (2 cách: deps & functional update).

## ✅ Nắm chắc Module này khi:
- Tự code todo app + 1 custom hook **không nhìn tài liệu**.
- Trả lời trôi chảy: khi nào re-render, `useEffect` chạy/cleanup khi nào, stale closure, `useMemo` vs `useCallback`, vì sao không dùng index làm key.
- Giải thích được **vòng đời 1 lần cập nhật**: setState → re-render → VDOM diff → cập nhật DOM → useEffect chạy.

> Tiếp theo nên ôn: **Module 6 (React Query — câu chuyện EmoEase của anh)** và **Module 13 (đào sâu dự án)** — kết hợp cả hai là phần anh ghi điểm mạnh nhất.
