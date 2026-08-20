# Luyện bài TEST GIẤY — Everlastify — 9:30 sáng thứ 6, 21/08/2026

> 55 phút, viết tay, không máy tính, không AI, không Google.
> Chấm tại chỗ trong 15 phút. Qua mới được phỏng vấn.
> Địa điểm: Helicopter Building, Nguyễn Văn Linh, Hoà Cường, Đà Nẵng. Tới trước 9:15.

## CÁCH DÙNG TÀI LIỆU NÀY

Đừng đọc. **Lấy giấy bút, che phần đáp án, tự viết ra**. Đọc hiểu và viết được là hai
việc khác nhau, và bài test đo cái thứ hai.

Mỗi ngày làm 1 lượt full, bấm giờ. Sai chỗ nào khoanh lại, hôm sau làm lại đúng chỗ đó.

---

# PHẦN 1 — ĐOÁN OUTPUT (gần như chắc chắn có)

Dạng này chiếm nhiều điểm nhất trong test giấy vì dễ chấm.

## 1.1 Hoisting và var/let

```js
console.log(a);   // ?
var a = 1;

console.log(b);   // ?
let b = 2;
```
Đáp án: `undefined` — rồi `ReferenceError: Cannot access 'b' before initialization`.
Vì `var` được hoist và gán `undefined`; `let` hoist nhưng nằm trong TDZ.

## 1.2 Closure trong vòng lặp — câu kinh điển nhất

```js
for (var i = 0; i < 3; i++) setTimeout(() => console.log(i), 0);
for (let j = 0; j < 3; j++) setTimeout(() => console.log(j), 0);
```
Đáp án: `3 3 3` rồi `0 1 2`.
`var` chỉ có một biến duy nhất ở function scope. `let` tạo binding mới mỗi vòng lặp.

## 1.3 Thứ tự bất đồng bộ

```js
console.log('1');
setTimeout(() => console.log('2'), 0);
Promise.resolve().then(() => console.log('3'));
console.log('4');
```
Đáp án: `1 4 3 2`.
Đồng bộ chạy trước, rồi microtask (Promise), cuối cùng macrotask (setTimeout).

## 1.4 this

```js
const obj = {
  name: 'A',
  regular() { return this.name; },
  arrow: () => this.name,
};
console.log(obj.regular());   // ?
console.log(obj.arrow());     // ?
```
Đáp án: `'A'` — rồi `undefined`.
Arrow function không có `this` riêng, nó lấy `this` của scope bao ngoài.

## 1.5 So sánh và ép kiểu

```js
console.log(1 == '1');        // true   (== ép kiểu)
console.log(1 === '1');       // false  (=== so cả kiểu)
console.log([] + []);         // ''     (chuỗi rỗng)
console.log(typeof null);     // 'object'  (bug lịch sử của JS)
console.log(0.1 + 0.2 === 0.3); // false  (số thực dấu phẩy động)
console.log([1,2,3] + [4]);   // '1,2,34'
```

## 1.6 Tham chiếu và bản sao

```js
const a = { x: 1 };
const b = a;
b.x = 2;
console.log(a.x);   // 2  — object gán bằng tham chiếu

const c = [1, 2];
const d = [...c];
d.push(3);
console.log(c.length);  // 2 — spread tạo bản sao NÔNG
```

---

# PHẦN 2 — VIẾT HÀM BẰNG TAY

Viết ra giấy, không nhìn đáp án. Đây là phần rèn tay.

## 2.1 Đảo ngược chuỗi (không dùng reverse)
```js
function reverse(str) {
  let out = '';
  for (let i = str.length - 1; i >= 0; i--) out += str[i];
  return out;
}
```

## 2.2 Kiểm tra palindrome
```js
function isPalindrome(s) {
  s = s.toLowerCase().replace(/[^a-z0-9]/g, '');
  let i = 0, j = s.length - 1;
  while (i < j) { if (s[i] !== s[j]) return false; i++; j--; }
  return true;
}
```

## 2.3 Loại phần tử trùng
```js
function unique(arr) { return [...new Set(arr)]; }
// bản không dùng Set:
function unique2(arr) {
  const out = [];
  for (const v of arr) if (!out.includes(v)) out.push(v);
  return out;
}
```

## 2.4 Đếm số lần xuất hiện
```js
function countWords(arr) {
  const map = {};
  for (const w of arr) map[w] = (map[w] || 0) + 1;
  return map;
}
```

## 2.5 Tìm phần tử lớn thứ hai
```js
function secondLargest(arr) {
  let m1 = -Infinity, m2 = -Infinity;
  for (const n of arr) {
    if (n > m1) { m2 = m1; m1 = n; }
    else if (n > m2 && n !== m1) m2 = n;
  }
  return m2 === -Infinity ? null : m2;
}
```

## 2.6 FizzBuzz
```js
for (let i = 1; i <= 100; i++) {
  let s = '';
  if (i % 3 === 0) s += 'Fizz';
  if (i % 5 === 0) s += 'Buzz';
  console.log(s || i);
}
```

## 2.7 Làm phẳng mảng lồng
```js
function flatten(arr) {
  return arr.reduce((acc, v) => acc.concat(Array.isArray(v) ? flatten(v) : v), []);
}
```

## 2.8 Tự viết map / filter / reduce (hay bị hỏi để kiểm tra hiểu bản chất)
```js
function myMap(arr, fn) {
  const out = [];
  for (let i = 0; i < arr.length; i++) out.push(fn(arr[i], i, arr));
  return out;
}
function myFilter(arr, fn) {
  const out = [];
  for (let i = 0; i < arr.length; i++) if (fn(arr[i], i, arr)) out.push(arr[i]);
  return out;
}
function myReduce(arr, fn, init) {
  let acc = init, i = 0;
  if (acc === undefined) { acc = arr[0]; i = 1; }
  for (; i < arr.length; i++) acc = fn(acc, arr[i], i, arr);
  return acc;
}
```

## 2.9 Debounce (hay hỏi vì liên quan frontend thật)
```js
function debounce(fn, delay) {
  let timer;
  return function (...args) {
    clearTimeout(timer);
    timer = setTimeout(() => fn.apply(this, args), delay);
  };
}
```

## 2.10 Nhóm mảng object theo thuộc tính
```js
function groupBy(arr, key) {
  const out = {};
  for (const item of arr) {
    const k = item[key];
    if (!out[k]) out[k] = [];
    out[k].push(item);
  }
  return out;
}
```

---

# PHẦN 3 — SQL VIẾT TAY (JD ghi rõ "phân tích và thiết kế database")

Giả sử có bảng:
`customers(id, name, email, city)`
`orders(id, customer_id, total, status, created_at)`

## 3.1 Lấy tất cả đơn kèm tên khách
```sql
SELECT o.id, c.name, o.total
FROM orders o
JOIN customers c ON c.id = o.customer_id;
```

## 3.2 Đếm số đơn mỗi khách, KỂ CẢ khách chưa mua
```sql
SELECT c.id, c.name, COUNT(o.id) AS order_count
FROM customers c
LEFT JOIN orders o ON o.customer_id = c.id
GROUP BY c.id, c.name;
```
BẪY: phải dùng `COUNT(o.id)` chứ không phải `COUNT(*)`.
`COUNT(*)` đếm cả dòng NULL nên khách chưa mua sẽ ra 1 thay vì 0.

## 3.3 Khách có tổng chi tiêu trên 10 triệu
```sql
SELECT c.name, SUM(o.total) AS total_spent
FROM customers c
JOIN orders o ON o.customer_id = c.id
GROUP BY c.id, c.name
HAVING SUM(o.total) > 10000000
ORDER BY total_spent DESC;
```
BẪY: lọc trên hàm tổng hợp phải dùng `HAVING`, không dùng `WHERE`.

## 3.4 Top 5 đơn giá trị lớn nhất
```sql
SELECT * FROM orders ORDER BY total DESC LIMIT 5;
```

## 3.5 Đơn trong 30 ngày gần nhất
```sql
SELECT * FROM orders
WHERE created_at >= NOW() - INTERVAL '30 days';   -- PostgreSQL
-- MySQL: WHERE created_at >= DATE_SUB(NOW(), INTERVAL 30 DAY)
```

## 3.6 Khách CHƯA từng mua
```sql
SELECT c.* FROM customers c
LEFT JOIN orders o ON o.customer_id = c.id
WHERE o.id IS NULL;
```

## 3.7 Tìm email trùng
```sql
SELECT email, COUNT(*) FROM customers
GROUP BY email HAVING COUNT(*) > 1;
```

## 3.8 Thứ tự thực thi của câu SQL (hay hỏi lý thuyết)
`FROM` → `JOIN` → `WHERE` → `GROUP BY` → `HAVING` → `SELECT` → `ORDER BY` → `LIMIT`
Đây là lý do không dùng được alias của SELECT trong WHERE.

---

# PHẦN 4 — REACT VIẾT TAY

## 4.1 Component đếm số
```jsx
import { useState } from 'react';
function Counter() {
  const [count, setCount] = useState(0);
  return (
    <div>
      <p>{count}</p>
      <button onClick={() => setCount(c => c + 1)}>+</button>
    </div>
  );
}
```
Nhớ dùng `setCount(c => c + 1)` chứ không phải `setCount(count + 1)` khi cập nhật liên tiếp.

## 4.2 Fetch dữ liệu trong useEffect
```jsx
function Users() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  useEffect(() => {
    let ignore = false;
    fetch('/api/users')
      .then(r => r.json())
      .then(d => { if (!ignore) { setUsers(d); setLoading(false); } });
    return () => { ignore = true; };   // cleanup, tránh set state sau unmount
  }, []);                              // [] = chỉ chạy 1 lần khi mount
  if (loading) return <p>Loading...</p>;
  return <ul>{users.map(u => <li key={u.id}>{u.name}</li>)}</ul>;
}
```

## 4.3 Controlled input
```jsx
const [value, setValue] = useState('');
<input value={value} onChange={e => setValue(e.target.value)} />
```

## 4.4 Câu lý thuyết hay hỏi
- **key trong list để làm gì?** Giúp React nhận diện phần tử nào thay đổi khi so sánh
  cây ảo. Không nên dùng index làm key nếu danh sách có thể sắp xếp lại hoặc xoá giữa.
- **useEffect với [] khác không có mảng phụ thuộc chỗ nào?** `[]` chạy một lần sau khi
  mount. Không truyền mảng thì chạy sau MỌI lần render.
- **State cập nhật đồng bộ hay bất đồng bộ?** Bất đồng bộ, React gom nhiều lần setState
  trong cùng một sự kiện lại thành một lần render.

---

# PHẦN 5 — LÝ THUYẾT NGẮN (thường là câu hỏi tự luận cuối bài)

- **REST là gì?** Kiến trúc dùng HTTP method đúng ngữ nghĩa trên tài nguyên: GET đọc,
  POST tạo, PUT thay toàn bộ, PATCH sửa một phần, DELETE xoá.
- **Mã HTTP:** 200 OK, 201 Created, 400 sai dữ liệu client, 401 chưa đăng nhập,
  403 không có quyền, 404 không tìm thấy, 500 lỗi server.
- **GET khác POST:** GET không đổi dữ liệu, tham số nằm trên URL, cache được.
  POST đổi dữ liệu, tham số trong body.
- **SQL khác NoSQL:** SQL có schema cố định, quan hệ, transaction chặt. NoSQL linh hoạt
  schema, mở rộng ngang dễ, hợp dữ liệu không cố định cấu trúc.
- **Index để làm gì?** Tăng tốc tìm kiếm, đổi lại ghi chậm hơn và tốn dung lượng.
- **JWT là gì?** Chuỗi token tự chứa thông tin, server không cần lưu phiên,
  dùng để xác thực giữa các request.
- **Node.js đơn luồng hay đa luồng?** JS chạy một luồng, I/O bất đồng bộ ở tầng dưới.

---

# CHIẾN THUẬT LÀM BÀI GIẤY — đọc lại sáng thứ 6

1. **Phút đầu tiên: lướt hết đề.** Biết có bao nhiêu câu, câu nào dễ. Làm dễ trước.
2. **Viết nháp ở lề trước khi viết code.** Nghĩ bằng tiếng Việt: "duyệt mảng, gặp số lớn
   hơn thì thay". Rồi mới dịch sang code.
3. **Bí thì viết cách làm bằng lời.** Người chấm cho điểm tư duy. Bỏ trống là 0 điểm,
   viết được hướng đi vẫn có điểm.
4. **Sai thì gạch một đường, viết lại bên cạnh.** Đừng tẩy xoá lem nhem.
5. **Đừng dừng lại vì quên cú pháp nhỏ.** Quên tên method thì viết `// hàm lấy độ dài`
   rồi đi tiếp. Không ai đánh rớt vì thiếu dấu chấm phẩy.
6. **Chia giờ:** 55 phút. Nếu 5 câu thì mỗi câu ~10 phút, chừa 5 phút soát lại.
   Quá 12 phút một câu thì bỏ qua, quay lại sau.
7. **Viết chữ dễ đọc.** Bài chấm trong 15 phút, người chấm không có thời gian đoán chữ.

# KẾ HOẠCH 2 NGÀY RƯỠI

**Tối 18/08:** Điền form Lark xác nhận NGAY. Rồi làm Phần 1 và Phần 2 bằng giấy.
**19/08:** Phần 3 (SQL) và Phần 4 (React). Làm lại Phần 2 không nhìn đáp án.
**20/08:** Làm full 1 lượt bấm giờ 55 phút. Chiều tối đọc lại Google Doc phần phỏng vấn
(vì nếu qua test thì 20 phút sau là phỏng vấn luôn).
**Sáng 21/08:** Dậy sớm, ăn sáng, tới nơi lúc 9:15. Mang CV in, bút bi 2 cây, CMND.
