# Module 17 — Node.js Backend (Bài giảng chi tiết)

> Viết cho JD **Everlastify — Junior Full-stack (Node.js/Next.js)**. Đây là **nửa mà giáo trình cũ chưa có**.
> Cách học: mỗi đoạn code hãy **gõ lại bằng tay**, chạy `node file.js`, và **đoán output trước khi chạy**.
> Ký hiệu: 🔴 chắc chắn bị hỏi · 🟡 có thể hỏi · 🟢 hỏi thì cộng điểm

Mục lục:
1. Node.js chạy như thế nào — Event Loop 🔴
2. Bất đồng bộ: callback → Promise → async/await 🔴
3. CommonJS vs ES Modules 🟡
4. Express: middleware và vòng đời request 🔴
5. NestJS: kiến trúc và Dependency Injection 🔴
6. Thiết kế REST API 🔴
7. Xác thực & phân quyền 🔴
8. Bảo mật web cơ bản 🟡
9. Xử lý lỗi và logging 🟡
10. Hiệu năng & mở rộng 🟢

---

## 1. Node.js chạy như thế nào — Event Loop 🔴

### Câu hỏi kinh điển: "Node.js là single-thread hay multi-thread?"

Trả lời chuẩn:

> JavaScript của tôi chạy trên **một luồng duy nhất**. Nhưng Node không chỉ có luồng đó: bên dưới có thư viện **libuv** với một **thread pool** (mặc định 4 luồng) lo các việc nặng như đọc/ghi file, DNS, mã hoá. I/O mạng thì không dùng thread pool mà dùng cơ chế của hệ điều hành (epoll/kqueue).
> Nên chính xác là: **code JS đơn luồng, còn I/O thì bất đồng bộ và đa luồng ở tầng dưới**.

### Event Loop có 6 pha, chạy vòng lặp

```
   ┌───────────────────────────┐
┌─>│         timers            │  setTimeout, setInterval
│  ├───────────────────────────┤
│  │     pending callbacks     │  callback lỗi I/O bị hoãn
│  ├───────────────────────────┤
│  │       idle, prepare       │  nội bộ Node
│  ├───────────────────────────┤
│  │           poll            │  ← lấy I/O mới, chạy hầu hết callback
│  ├───────────────────────────┤
│  │           check           │  setImmediate
│  ├───────────────────────────┤
│  │      close callbacks      │  socket.on('close')
└──┴───────────────────────────┘
```

### Microtask chen ngang giữa mọi pha

Có **2 hàng đợi microtask**, chạy **hết sạch** sau mỗi callback:
1. `process.nextTick()` — ưu tiên cao nhất
2. Promise callbacks (`.then`, `await`)

**Bài tập đoán output** (rất hay bị hỏi):

```js
console.log('1: sync');

setTimeout(() => console.log('2: timeout 0'), 0);
setImmediate(() => console.log('3: immediate'));

process.nextTick(() => console.log('4: nextTick'));

Promise.resolve().then(() => console.log('5: promise'));

console.log('6: sync end');
```

Output:
```
1: sync
6: sync end
4: nextTick      ← microtask, ưu tiên cao nhất
5: promise       ← microtask
2: timeout 0     ← pha timers
3: immediate     ← pha check
```

> ⚠️ Riêng cặp `setTimeout(0)` vs `setImmediate` khi gọi ở **top-level** thì thứ tự **không xác định** (tuỳ máy chạy nhanh chậm). Nhưng nếu gọi **bên trong một I/O callback** thì `setImmediate` **luôn chạy trước**, vì lúc đó đang ở pha poll, pha check nằm ngay sau.

### "Blocking" nghĩa là gì và vì sao nguy hiểm

```js
// ❌ CHẶN toàn bộ server. Mọi request khác phải xếp hàng chờ.
const data = fs.readFileSync('big.json');
for (let i = 0; i < 1e10; i++) {}          // vòng lặp CPU nặng

// ✅ Không chặn
const data = await fs.promises.readFile('big.json');
```

**Câu trả lời ghi điểm:** "Vì JS đơn luồng, một vòng lặp CPU nặng sẽ chặn *tất cả* người dùng khác, không riêng người gửi request đó. Việc nặng thì tôi đẩy sang `worker_threads`, hoặc tách ra một service riêng, hoặc đưa vào hàng đợi (queue) để xử lý nền."

> 💡 **Nối vào dự án của anh:** đây chính là lý do anh tách **GEO scan engine** ra khỏi web app CiteFlow — quét một website mất nhiều giây, nếu chạy chung sẽ chặn các request khác. Câu này *rất* nên nói ra trong phỏng vấn.

---

## 2. Bất đồng bộ: callback → Promise → async/await 🔴

### Callback hell và cách thoát

```js
// ❌ callback hell
getUser(id, (err, user) => {
  getOrders(user.id, (err, orders) => {
    getItems(orders[0].id, (err, items) => { /* ... */ });
  });
});

// ✅ async/await
const user   = await getUser(id);
const orders = await getOrders(user.id);
const items  = await getItems(orders[0].id);
```

### Tuần tự vs song song — lỗi junior hay mắc nhất

```js
// ❌ CHẬM: 3 giây (chạy lần lượt)
const a = await fetchA();   // 1s
const b = await fetchB();   // 1s
const c = await fetchC();   // 1s

// ✅ NHANH: 1 giây (chạy song song)
const [a, b, c] = await Promise.all([fetchA(), fetchB(), fetchC()]);
```

Chỉ dùng tuần tự khi việc sau **cần kết quả** của việc trước.

### Phân biệt 4 hàm tổ hợp Promise

| Hàm | Khi nào xong | Một cái lỗi thì sao |
|---|---|---|
| `Promise.all` | tất cả thành công | **hỏng ngay**, mất hết kết quả khác |
| `Promise.allSettled` | tất cả xong (kể cả lỗi) | vẫn trả về đủ, mỗi cái có `status` |
| `Promise.race` | **cái đầu tiên** xong | lỗi đầu tiên cũng tính là xong |
| `Promise.any` | cái **thành công** đầu tiên | bỏ qua lỗi, chỉ hỏng khi tất cả lỗi |

**Dùng `allSettled` khi:** gọi nhiều API bên thứ ba mà một cái chết không nên làm hỏng cả trang.

### Bắt lỗi trong async

```js
// ❌ Lỗi này KHÔNG bắt được, vì hàm async trả Promise, try/catch đã thoát trước
try {
  doAsync();          // thiếu await
} catch (e) {}

// ✅
try {
  await doAsync();
} catch (e) { /* xử lý */ }
```

### Giới hạn số việc chạy cùng lúc

Gọi 1000 API cùng lúc sẽ làm sập cả bên mình lẫn bên kia. Cách gọn nhất:

```js
async function mapLimit(items, limit, fn) {
  const results = [];
  const running = new Set();
  for (const item of items) {
    const p = fn(item).then(r => { running.delete(p); return r; });
    running.add(p);
    results.push(p);
    if (running.size >= limit) await Promise.race(running);
  }
  return Promise.all(results);
}
```

> 💡 **Nối vào dự án:** khi quét nhiều URL trong GEO engine, anh phải giới hạn số request đồng thời để không bị chặn. Kể chuyện này thay vì nói lý thuyết suông.

---

## 3. CommonJS vs ES Modules 🟡

| | CommonJS | ES Modules |
|---|---|---|
| Cú pháp | `require` / `module.exports` | `import` / `export` |
| Nạp lúc nào | **runtime**, đồng bộ | **phân tích lúc biên dịch** |
| Import động | được (require ở bất kỳ đâu) | phải dùng `await import()` |
| Bật thế nào | mặc định | `"type": "module"` hoặc đuôi `.mjs` |
| Tree-shaking | khó | **được** (vì tĩnh) |
| `__dirname` | có sẵn | không có, phải tự dựng từ `import.meta.url` |

Câu hỏi hay gặp: *"Tại sao ESM tree-shake được mà CJS thì không?"*
→ Vì `import` là tĩnh, bundler biết chắc module nào export gì ngay khi build. `require()` có thể nằm trong `if`, biến động, nên không suy ra được.

---

## 4. Express: middleware và vòng đời request 🔴

### Middleware là gì

Một hàm nhận `(req, res, next)`, chạy **theo đúng thứ tự khai báo**, giống dây chuyền.

```js
const express = require('express');
const app = express();

app.use(express.json());                    // 1. parse JSON body

app.use((req, res, next) => {               // 2. log
  console.log(`${req.method} ${req.url}`);
  next();                                   // ⚠️ quên next() → request treo mãi mãi
});

function auth(req, res, next) {             // 3. middleware riêng cho 1 route
  if (!req.headers.authorization) return res.status(401).json({ error: 'Unauthorized' });
  next();
}

app.get('/orders', auth, async (req, res, next) => {
  try {
    const orders = await db.getOrders(req.user.id);
    res.json(orders);
  } catch (err) {
    next(err);                              // ⚠️ lỗi async PHẢI đẩy qua next()
  }
});

// 4. Error middleware: BẮT BUỘC 4 tham số, và đặt CUỐI CÙNG
app.use((err, req, res, next) => {
  console.error(err);
  res.status(err.status || 500).json({ error: 'Internal Server Error' });
});
```

### Ba lỗi Express hay bị hỏi bẫy

1. **Quên `next()`** → request treo tới khi timeout.
2. **Error middleware phải có đúng 4 tham số.** Viết 3 tham số thì Express coi là middleware thường, không bao giờ nhận được lỗi.
3. **Lỗi trong hàm async không tự chảy vào error middleware** ở Express 4. Phải `try/catch` rồi `next(err)`, hoặc bọc bằng helper:

```js
const wrap = fn => (req, res, next) => Promise.resolve(fn(req, res, next)).catch(next);
app.get('/orders', wrap(async (req, res) => { /* ... */ }));
```

### Thứ tự đăng ký quan trọng

```js
app.use('/api', auth);        // đặt TRƯỚC các route /api → mọi route /api đều được bảo vệ
app.get('/api/public', ...);  // ❌ route này cũng bị chặn, dù muốn public
```

---

## 5. NestJS: kiến trúc và Dependency Injection 🔴

> ⚠️ **Anh chưa dùng NestJS.** Đừng nói dối. Nhưng phải **hiểu nó là gì và giải quyết vấn đề gì** để trả lời được khi bị hỏi. Mục tiêu: nói được 3 phút mạch lạc.

### NestJS giải quyết vấn đề gì

Express rất tự do — mỗi người tổ chức code một kiểu, dự án lớn thì loạn. NestJS áp một **kiến trúc cố định** (lấy cảm hứng từ Angular/Spring): chia theo **module**, dùng **decorator**, và **tiêm phụ thuộc (DI)** tự động.

### Ba khối cơ bản

```ts
// Controller: nhận HTTP request, KHÔNG chứa logic nghiệp vụ
@Controller('orders')
export class OrdersController {
  constructor(private readonly ordersService: OrdersService) {}   // ← DI ở đây

  @Get(':id')
  findOne(@Param('id') id: string) {
    return this.ordersService.findOne(id);
  }

  @Post()
  create(@Body() dto: CreateOrderDto) {
    return this.ordersService.create(dto);
  }
}

// Provider / Service: chứa logic nghiệp vụ
@Injectable()
export class OrdersService {
  constructor(private readonly db: DatabaseService) {}
  findOne(id: string) { return this.db.order.findUnique({ where: { id } }); }
}

// Module: gom nhóm và khai báo phụ thuộc
@Module({
  imports: [DatabaseModule],
  controllers: [OrdersController],
  providers: [OrdersService],
  exports: [OrdersService],
})
export class OrdersModule {}
```

### Dependency Injection — giải thích cho người phỏng vấn

> "Thay vì trong service tôi tự `new DatabaseService()`, tôi chỉ khai báo nó ở constructor. Nest nhìn kiểu dữ liệu rồi tự tạo và đưa vào. Lợi ích lớn nhất là **test**: lúc viết test tôi thay bằng một object giả, không cần đụng database thật."

### 4 loại "chốt chặn" của Nest — biết tên và thứ tự là đủ

| Tên | Nhiệm vụ | Ví dụ |
|---|---|---|
| **Middleware** | như Express | log, cors |
| **Guard** | quyết định *cho đi tiếp hay không* | kiểm tra JWT, kiểm tra role |
| **Interceptor** | bọc trước/sau handler | đo thời gian, đổi format response |
| **Pipe** | biến đổi & validate dữ liệu vào | `ValidationPipe` + class-validator |

Thứ tự: `Middleware → Guard → Interceptor (trước) → Pipe → Handler → Interceptor (sau) → Filter (nếu lỗi)`

### Cách nói khi bị hỏi "em có biết NestJS không?"

> "Em chưa dùng NestJS trong dự án thật. Em làm backend qua **Node.js trong Next.js API routes**, và có đọc tìm hiểu Nest. Em hiểu Nest chuẩn hoá cấu trúc bằng module/controller/service và dùng DI để dễ test — về mặt tư duy thì giống cách em tách tầng ở CiteFlow: route chỉ nhận request và validate, còn logic em để ở tầng service riêng, cho nên em nghĩ mình bắt nhịp nhanh. Em cũng đã tự học Drizzle, Stripe, Shopify app và Playwright trong năm nay theo cách tương tự."

**Ôn tối thiểu trước phỏng vấn:** dựng thử 1 project Nest bằng `nest new`, tạo 1 module CRUD. Mất 2 tiếng, nhưng đổi được từ "chưa biết" sang "em có làm thử".

---

## 6. Thiết kế REST API 🔴

### Quy tắc đặt endpoint

```
GET    /orders            lấy danh sách
GET    /orders/:id        lấy 1 cái
POST   /orders            tạo mới
PUT    /orders/:id        thay toàn bộ
PATCH  /orders/:id        sửa một phần
DELETE /orders/:id        xoá

GET    /orders/:id/items  tài nguyên con
```

Dùng **danh từ số nhiều**, không dùng động từ (`/getOrders` ❌).

### Mã trạng thái cần thuộc

| Mã | Khi nào |
|---|---|
| 200 | OK |
| 201 | Tạo thành công (kèm header `Location`) |
| 204 | Thành công, không có nội dung trả về (thường cho DELETE) |
| 400 | Dữ liệu client gửi sai |
| 401 | **Chưa đăng nhập** |
| 403 | Đã đăng nhập nhưng **không có quyền** |
| 404 | Không tìm thấy |
| 409 | Xung đột (trùng email, sửa đồng thời) |
| 422 | Đúng định dạng nhưng sai nghiệp vụ |
| 429 | Quá nhiều request |
| 500 | Lỗi phía server |

> 401 vs 403 là câu hỏi bẫy phổ biến. **401 = anh là ai? 403 = biết anh rồi, nhưng không cho.**

### Idempotency — hay bị hỏi ở e-commerce

Gọi lại nhiều lần cho ra cùng kết quả: `GET`, `PUT`, `DELETE` là idempotent; `POST` thì **không**.

Vấn đề thực tế: khách bấm "Thanh toán" 2 lần → tạo 2 đơn. Cách xử lý: client gửi kèm `Idempotency-Key` (một UUID), server lưu key đó, gặp lại thì trả kết quả cũ thay vì tạo mới. **Stripe làm đúng như vậy — anh đã tích hợp Stripe nên nói được chuyện này.**

### Phân trang

```
# Offset — đơn giản, nhưng chậm khi trang sâu và bị lệch khi có bản ghi mới
GET /orders?page=2&limit=20

# Cursor — nhanh và ổn định, nên dùng cho danh sách lớn
GET /orders?cursor=eyJpZCI6MTIzfQ&limit=20
```

Nêu được sự khác biệt này là điểm cộng rõ rệt ở vòng backend.

---

## 7. Xác thực & phân quyền 🔴

### Session vs JWT

| | Session (cookie) | JWT |
|---|---|---|
| Trạng thái lưu ở | **server** | **trong chính token** |
| Thu hồi ngay | dễ (xoá session) | **khó** — đây là nhược điểm lớn nhất |
| Mở rộng nhiều server | cần store chung (Redis) | không cần |
| Kích thước | nhỏ | lớn hơn, gửi kèm mỗi request |

### Mẫu access + refresh token

- **Access token**: sống ngắn (15 phút), gửi kèm mỗi request.
- **Refresh token**: sống dài (7 ngày), lưu trong **cookie `httpOnly`**, chỉ dùng để xin access token mới.

Vì sao tách? Access token bị lộ thì chỉ hại 15 phút. Refresh token nằm trong cookie httpOnly nên **JavaScript không đọc được** → chống XSS ăn cắp token.

### Cờ cookie phải thuộc

```js
res.cookie('refresh_token', token, {
  httpOnly: true,     // JS không đọc được → chống XSS
  secure: true,       // chỉ gửi qua HTTPS
  sameSite: 'lax',    // chống CSRF
  maxAge: 7*24*60*60*1000,
});
```

### Mật khẩu

**Không bao giờ lưu mật khẩu dạng thô.** Dùng **bcrypt** hoặc **argon2** (có salt sẵn, cố ý chạy chậm để chống dò).
`md5`/`sha256` là **sai** — chúng nhanh, nên dò càng dễ.

```js
const hash = await bcrypt.hash(password, 10);      // 10 = cost factor
const ok   = await bcrypt.compare(password, hash);
```

---

## 8. Bảo mật web cơ bản 🟡

| Lỗ hổng | Là gì | Chống bằng |
|---|---|---|
| **SQL Injection** | nhét SQL vào input | **truy vấn tham số hoá**, ORM. Không nối chuỗi |
| **XSS** | nhét `<script>` vào nội dung | escape output, `httpOnly` cookie, CSP |
| **CSRF** | web khác gửi request thay người dùng | `sameSite`, CSRF token |
| **IDOR** | đổi `/orders/1` thành `/orders/2` để xem đơn người khác | **kiểm tra quyền sở hữu ở server**, không tin client |
| **Mass assignment** | gửi thêm `{ role: "admin" }` vào body | whitelist trường, dùng DTO |

```js
// ❌ SQL Injection
db.query(`SELECT * FROM users WHERE email = '${email}'`);
// email = "' OR '1'='1"  → lộ toàn bộ bảng

// ✅ Tham số hoá
db.query('SELECT * FROM users WHERE email = $1', [email]);
```

> 💡 **IDOR nối thẳng vào dự án của anh:** CiteFlow là multi-tenant, anh dùng **row-level security của PostgreSQL** để một tenant không đọc được dữ liệu tenant khác. Đây là câu trả lời rất mạnh, ít junior nói được.

---

## 9. Xử lý lỗi và logging 🟡

### Phân biệt lỗi vận hành và lỗi lập trình

- **Lỗi vận hành** (mất mạng, DB timeout, input sai): dự đoán được → xử lý, retry, trả lỗi đẹp cho client.
- **Lỗi lập trình** (đọc thuộc tính của `undefined`): là bug → nên để nó nổ, log lại, sửa code. Đừng nuốt lỗi.

### Không bao giờ để lộ chi tiết lỗi ra ngoài

```js
// ❌ lộ cấu trúc DB, đường dẫn file cho kẻ tấn công
res.status(500).json({ error: err.stack });

// ✅
logger.error({ err, requestId: req.id });
res.status(500).json({ error: 'Internal Server Error', requestId: req.id });
```

Trả `requestId` để khách báo lỗi thì mình tra log ra đúng request đó.

---

## 10. Hiệu năng & mở rộng 🟢

- **Connection pool**: đừng mở kết nối DB mới cho mỗi request. Pool giữ sẵn N kết nối dùng lại.
- **Cache**: Redis cho dữ liệu đọc nhiều ghi ít. Luôn đặt TTL.
- **Queue** (BullMQ, RabbitMQ): việc chậm như gửi mail, xuất PDF, quét website thì đẩy vào hàng đợi, trả `202 Accepted` ngay cho người dùng.
- **Mở rộng ngang**: chạy nhiều tiến trình Node (PM2 cluster, hoặc nhiều container). Điều kiện là server phải **stateless** — không giữ trạng thái trong RAM, đẩy vào Redis/DB.
- **N+1 query**: xem Module 18.

---

## Checklist tự kiểm tra trước phỏng vấn

Nói được trôi chảy, không cần nhìn tài liệu:

- [ ] Event loop có mấy pha, microtask chạy lúc nào
- [ ] Vì sao code CPU nặng lại chặn cả server, và cách xử lý
- [ ] `Promise.all` khác `allSettled` chỗ nào, khi nào dùng cái nào
- [ ] Middleware Express chạy theo thứ tự nào, error middleware khác gì
- [ ] NestJS là gì, DI giải quyết vấn đề gì
- [ ] 401 vs 403
- [ ] Idempotency và vì sao quan trọng với thanh toán
- [ ] Session vs JWT, vì sao cần refresh token
- [ ] 5 lỗ hổng bảo mật và cách chống
- [ ] Vì sao phải hash mật khẩu bằng bcrypt chứ không phải sha256
