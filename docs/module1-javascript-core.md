# Module 1 — JavaScript Core (Bài giảng chi tiết)

> Đây là phần **quan trọng nhất** cho phỏng vấn Frontend. Đọc kỹ, **gõ lại từng ví dụ bằng tay** (mở trình duyệt → Console, hoặc Node), **dự đoán output trước khi chạy**. Quên chỗ nào quay lại chỗ đó.

Mục lục:
1. Scope, `var/let/const`, Hoisting, TDZ
2. Kiểu dữ liệu: Primitive vs Reference
3. Ép kiểu, `==` vs `===`, truthy/falsy
4. Closure
5. `this` & `call/apply/bind`
6. Prototype & kế thừa
7. Array methods (map/filter/reduce…)
8. Object: destructuring, spread/rest, `?.`, `??`
9. Bất đồng bộ: Callback → Promise → async/await
10. Event Loop (microtask vs macrotask)
11. Xử lý lỗi async
12. Pure function & Immutability

---

## 1. Scope, `var/let/const`, Hoisting, TDZ

**Scope** = phạm vi biến tồn tại.
- `var`: **function-scoped** (chỉ giới hạn trong function, KHÔNG theo block `{}`).
- `let`/`const`: **block-scoped** (giới hạn trong `{}` gần nhất).

**Hoisting** = JS "nâng" khai báo lên đầu scope khi biên dịch.
- `var` được nâng và gán `undefined`.
- `let`/`const` được nâng nhưng nằm trong **TDZ (Temporal Dead Zone)** → truy cập trước khi khai báo sẽ lỗi.
- `function` declaration được nâng **cả thân hàm**.

```js
console.log(a); // undefined  (var được hoist = undefined)
var a = 5;

console.log(b); // ❌ ReferenceError (b trong TDZ)
let b = 10;

foo();          // ✅ chạy được (function declaration hoisted đầy đủ)
function foo() { console.log('hi'); }

bar();          // ❌ TypeError: bar is not a function
var bar = function () {}; // chỉ 'bar' được hoist = undefined
```

**Ví dụ kinh điển (rất hay hỏi) — vòng lặp + setTimeout:**
```js
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 0);
}
// In ra: 3, 3, 3   → vì var dùng CHUNG 1 biến i, lúc timeout chạy i đã = 3

for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 0);
}
// In ra: 0, 1, 2   → let tạo biến i MỚI mỗi vòng lặp (block scope)
```
> 🎯 **Phỏng vấn:** giải thích được tại sao `var` ra 3,3,3 còn `let` ra 0,1,2 = hiểu scope + closure. Câu này cực hay gặp.

---

## 2. Kiểu dữ liệu: Primitive vs Reference

**Primitive** (7 loại): `string, number, boolean, null, undefined, symbol, bigint` → **copy theo GIÁ TRỊ**.
**Reference**: `object, array, function` → **copy theo THAM CHIẾU** (cùng trỏ tới 1 vùng nhớ).

```js
let a = 10;
let b = a;     // copy giá trị
b = 20;
console.log(a); // 10  (a không đổi)

let obj1 = { x: 1 };
let obj2 = obj1; // copy tham chiếu (cùng 1 object)
obj2.x = 99;
console.log(obj1.x); // 99  (đổi obj2 = đổi obj1!)
```

> 🎯 Đây là gốc rễ của bug "tự nhiên state bị thay đổi" trong React. Hiểu cái này → hiểu vì sao phải **copy bất biến** khi update state (`{...obj}`, `[...arr]`).

---

## 3. Ép kiểu, `==` vs `===`, truthy/falsy

- `===`: so sánh **strict**, KHÔNG ép kiểu. Khác kiểu → luôn `false`.
- `==`: ép kiểu (coercion) rồi mới so → dễ gây bug.

```js
1 === '1'   // false (khác kiểu)
1 == '1'    // true  (ép '1' → 1)
null == undefined // true
null === undefined // false
NaN === NaN // false  (NaN không bằng chính nó! dùng Number.isNaN())
```

**Falsy values** (8 cái, ép sang boolean = false): `false, 0, -0, '', null, undefined, NaN, 0n`.
Mọi thứ khác là **truthy** (kể cả `'0'`, `[]`, `{}`).

```js
if ('')  {} // không chạy (falsy)
if ([])  {} // CHẠY (mảng rỗng là truthy!)
if ({})  {} // CHẠY
```
> 🎯 **Quy tắc thực chiến:** luôn dùng `===`. Chỉ dùng `==` khi cố ý check `== null` (bắt cả null & undefined).

---

## 4. Closure (CỰC KỲ hay hỏi)

**Closure** = một hàm + "nhớ" được scope nơi nó **được tạo ra**, truy cập biến ngoài kể cả sau khi hàm bao đã return.

```js
function makeCounter() {
  let count = 0;            // biến riêng tư
  return function () {
    count++;
    return count;
  };
}
const counter = makeCounter();
counter(); // 1
counter(); // 2
counter(); // 3   → 'count' vẫn sống dù makeCounter() đã return
```

**Ứng dụng thực tế:**
- Biến private (đóng gói dữ liệu).
- `debounce`, `throttle`, các hàm "nhớ trạng thái".
- Mọi custom hook trong React đều dựa trên closure.

**Bug thường gặp:** stale closure — hàm "bắt" giá trị cũ. (sẽ gặp lại ở `useEffect`).

> 🎯 **Phỏng vấn:** "Closure là gì? Cho ví dụ." → kể `makeCounter` + nói ứng dụng (private var, debounce). Tự viết được = đậu câu này.

---

## 5. `this` & `call/apply/bind`

`this` phụ thuộc **CÁCH GỌI hàm**, không phải nơi định nghĩa:

```js
const obj = {
  name: 'Giang',
  regular() { return this.name; },     // this = obj
  arrow:  () => this.name,             // this = scope ngoài (KHÔNG phải obj)
};
obj.regular(); // 'Giang'
obj.arrow();   // undefined

const fn = obj.regular;
fn();          // undefined (gọi standalone → this = undefined/global)
```

**4 quy tắc xác định `this`:**
1. Gọi như **method** (`obj.fn()`) → `this` = obj.
2. Gọi **standalone** (`fn()`) → `this` = `undefined` (strict) / global.
3. **Arrow function** → không có `this` riêng, lấy `this` của scope bao quanh (lexical).
4. `call/apply/bind` → set `this` thủ công.

```js
function greet() { return `Hi ${this.name}`; }
const user = { name: 'An' };
greet.call(user);          // 'Hi An'  (call: tham số rời)
greet.apply(user);         // 'Hi An'  (apply: tham số dạng mảng)
const bound = greet.bind(user);
bound();                   // 'Hi An'  (bind: trả hàm mới, gọi sau)
```
> 🎯 **Phỏng vấn:** "arrow function khác function thường ở `this` thế nào?" → arrow không có this riêng (lexical), nên hay dùng trong callback để giữ this. `call` vs `apply` chỉ khác cách truyền tham số.

---

## 6. Prototype & kế thừa (mức hiểu)

Mỗi object có liên kết ẩn `[[Prototype]]` tới object cha. Tìm property không thấy → JS đi lên **prototype chain**.

```js
const animal = { eat() { return 'eating'; } };
const dog = Object.create(animal);
dog.bark = () => 'woof';
dog.bark(); // 'woof' (của chính dog)
dog.eat();  // 'eating' (kế thừa từ animal qua prototype chain)
```
`class` trong JS là "đường cú pháp" (syntactic sugar) trên prototype.
> 🎯 Junior chỉ cần hiểu: "JS kế thừa qua prototype chain; tìm property đi từ object lên cha tới khi gặp hoặc `null`."

---

## 7. Array methods (dùng hằng ngày + hay hỏi)

```js
const nums = [1, 2, 3, 4];

nums.map(n => n * 2);        // [2,4,6,8]   → trả MẢNG MỚI đã biến đổi
nums.filter(n => n % 2 === 0); // [2,4]     → lọc theo điều kiện
nums.reduce((acc, n) => acc + n, 0); // 10  → gộp về 1 giá trị
nums.find(n => n > 2);       // 3           → phần tử đầu thoả
nums.some(n => n > 3);       // true        → có ít nhất 1
nums.every(n => n > 0);      // true        → tất cả thoả
nums.forEach(n => console.log(n)); // undefined → chỉ lặp, không trả
```

**`slice` vs `splice` (hay nhầm):**
- `slice(start, end)`: trả mảng con, **KHÔNG đổi** mảng gốc.
- `splice(start, count, ...items)`: **ĐỔI** mảng gốc (xoá/chèn).

**`reduce` mạnh — đếm tần suất:**
```js
const fruits = ['a','b','a','c','a'];
const count = fruits.reduce((acc, f) => {
  acc[f] = (acc[f] || 0) + 1;
  return acc;
}, {});
// { a: 3, b: 1, c: 1 }
```
> 🎯 **Phỏng vấn:** `map` vs `forEach` (map trả mảng mới, forEach không). Và biết dùng `reduce` để đếm/nhóm.

---

## 8. Object: destructuring, spread/rest, `?.`, `??`

```js
const user = { name: 'Giang', age: 24, address: { city: 'Đà Nẵng' } };

// Destructuring
const { name, age } = user;
const { address: { city } } = user; // city = 'Đà Nẵng'

// Spread (copy NÔNG + ghi đè)
const updated = { ...user, age: 25 }; // copy + đổi age

// Rest
const { name: n, ...rest } = user; // rest = { age, address }

// Optional chaining ?. — tránh lỗi khi truy cập sâu
user?.address?.city;   // 'Đà Nẵng'
user?.job?.title;      // undefined (không lỗi)

// Nullish coalescing ?? — chỉ fallback khi null/undefined (KHÁC ||)
const x = 0;
x || 10;  // 10  (vì 0 falsy)
x ?? 10;  // 0   (vì 0 không phải null/undefined) ← thường đúng ý hơn
```
> 🎯 **Phỏng vấn:** `??` khác `||` thế nào? → `||` fallback với mọi falsy (0, '', false); `??` chỉ fallback với null/undefined. Rất hay hỏi.

---

## 9. Bất đồng bộ: Callback → Promise → async/await

**Promise** có 3 trạng thái: pending → fulfilled / rejected.

```js
// Promise
fetch('/api/user')
  .then(res => res.json())
  .then(data => console.log(data))
  .catch(err => console.error(err));

// async/await (dễ đọc hơn, cùng bản chất)
async function getUser() {
  try {
    const res = await fetch('/api/user');
    const data = await res.json();
    return data;
  } catch (err) {
    console.error(err);
  }
}
```

**Chạy song song nhiều request:**
```js
const [a, b] = await Promise.all([fetch('/a'), fetch('/b')]); // chờ cả 2
```

| Hàm | Hành vi |
|-----|--------|
| `Promise.all` | Chờ tất cả; **fail 1 → reject ngay** |
| `Promise.allSettled` | Chờ tất cả; trả status từng cái (không reject) |
| `Promise.race` | Cái nào xong/lỗi **trước** thì lấy |
| `Promise.any` | Cái **thành công** đầu tiên |

> 🎯 **Phỏng vấn:** khi cần gọi 3 API độc lập cùng lúc → dùng `Promise.all` (nhanh hơn await tuần tự). Biết `allSettled` khi không muốn 1 cái lỗi làm hỏng tất cả.

---

## 10. Event Loop (microtask vs macrotask) — câu "tủ"

JS **đơn luồng**. Cơ chế:
1. Code đồng bộ chạy trên **call stack**.
2. Stack rỗng → chạy **HẾT microtask queue** (Promise.then, queueMicrotask).
3. Rồi lấy **1 macrotask** (setTimeout, setInterval, I/O) → lặp lại.

```js
console.log('1');                          // sync
setTimeout(() => console.log('2'), 0);     // macrotask
Promise.resolve().then(() => console.log('3')); // microtask
console.log('4');                          // sync

// Output: 1, 4, 3, 2
// Giải thích: sync trước (1,4) → microtask (3) → macrotask (2)
```
> 🎯 **Phỏng vấn:** "Vì sao Promise chạy trước setTimeout(0)?" → microtask được ưu tiên xử lý hết trước mỗi macrotask. Đây là câu test hiểu sâu.

---

## 11. Xử lý lỗi async

```js
// async/await: dùng try/catch
async function load() {
  try {
    const data = await fetchData();
  } catch (err) {
    // xử lý lỗi (toast, log, fallback)
  } finally {
    // luôn chạy (tắt loading...)
  }
}

// Promise: dùng .catch()
fetchData().then(handle).catch(handleError);
```
**Lỗi hay mắc:** quên `await` → biến nhận Promise thay vì dữ liệu. Quên `try/catch` → unhandled rejection.

---

## 12. Pure Function & Immutability (nền tảng tư duy React)

**Pure function:** cùng input → cùng output, **không side effect** (không sửa biến ngoài, không gọi API/DOM).
```js
// Pure ✅
const add = (a, b) => a + b;

// Impure ❌ (sửa biến ngoài)
let total = 0;
const addToTotal = (n) => { total += n; };
```

**Immutability:** không sửa trực tiếp, mà tạo bản sao mới.
```js
// ❌ Mutate (sai trong React)
state.list.push(item);

// ✅ Immutable (đúng)
const newList = [...state.list, item];
const newObj  = { ...state.obj, name: 'new' };
```
> 🎯 **Vì sao React cần cái này:** React so sánh tham chiếu để biết có re-render không. Mutate trực tiếp → tham chiếu không đổi → React "tưởng" không có gì thay đổi → UI không cập nhật. Hiểu Mục 2 + 12 = hiểu 80% bug state của junior.

---

## ✅ Bài tập tổng hợp (code TAY, không AI)
1. Viết `makeCounter()` có thêm `decrement()` và `reset()`.
2. Dự đoán output rồi chạy kiểm tra:
   ```js
   console.log('A');
   setTimeout(() => console.log('B'), 0);
   Promise.resolve().then(() => console.log('C'));
   console.log('D');
   ```
3. Viết `deepClone(obj)` xử lý object lồng + array (đệ quy).
4. Dùng `reduce` nhóm mảng user theo `city`: `[{name,city}] → { city: [names] }`.
5. Giải thích bằng lời (thu âm): closure, `this` của arrow, vì sao `var` trong vòng lặp ra 3,3,3.

## ✅ Bạn nắm chắc Module này khi:
- Giải thích **closure, `this`, event loop, primitive vs reference** bằng lời + ví dụ tự nghĩ.
- Tự viết `makeCounter`, `deepClone`, dùng `reduce` để đếm/nhóm — không nhìn tài liệu.
- Hiểu vì sao immutability quan trọng với React (nối sang Module 5).

> Sau khi xong Module này, sang **Module 5 (React Core)** — nó dựa trực tiếp trên closure, `this`, và immutability anh vừa học.
