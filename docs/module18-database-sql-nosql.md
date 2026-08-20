# Module 18 — Database: SQL & NoSQL (Bài giảng chi tiết)

> JD Everlastify ghi rõ: *"Biết **phân tích và thiết kế database**: SQL (MySQL, Postgres), NoSQL (MongoDB, Redis)"*.
> Chữ **thiết kế** là mấu chốt — họ không hỏi cú pháp, họ hỏi anh **nghĩ** thế nào khi dựng một schema.
> Anh mạnh PostgreSQL nhưng **chưa dùng MongoDB/Redis** → phần 6 và 7 học để *trả lời được*, không cần thành thạo.

Mục lục:
1. Thiết kế schema & chuẩn hoá 🔴
2. Index — thứ hay bị hỏi nhất 🔴
3. JOIN và bài toán N+1 🔴
4. Transaction & mức cô lập 🔴
5. Đọc EXPLAIN 🟡
6. MongoDB — khi nào dùng 🟡
7. Redis — 4 công dụng cần biết 🟡
8. Bài tập thiết kế: giỏ hàng e-commerce 🔴

---

## 1. Thiết kế schema & chuẩn hoá 🔴

### Ba dạng chuẩn, giải thích bằng ví dụ thật

Bảng sai (chưa chuẩn hoá):

| order_id | customer_name | customer_email | products |
|---|---|---|---|
| 1 | Giang | g@x.com | "Áo, Quần, Mũ" |

Vấn đề:
- `products` nhồi nhiều giá trị trong 1 ô → không lọc, không đếm được
- Khách đổi email → phải sửa ở mọi đơn hàng (dữ liệu dễ lệch)

**1NF** — mỗi ô chỉ chứa **một giá trị**. Tách bảng `order_items`.
**2NF** — mọi cột phải phụ thuộc **toàn bộ** khoá chính, không phụ thuộc một phần.
**3NF** — không cột nào phụ thuộc vào cột **không phải khoá**. `customer_email` phụ thuộc `customer_id` chứ không phải `order_id` → tách bảng `customers`.

Sau khi chuẩn hoá:

```sql
CREATE TABLE customers (
  id         SERIAL PRIMARY KEY,
  email      VARCHAR(255) UNIQUE NOT NULL,
  name       VARCHAR(255) NOT NULL,
  created_at TIMESTAMPTZ NOT NULL DEFAULT now()
);

CREATE TABLE orders (
  id          SERIAL PRIMARY KEY,
  customer_id INT NOT NULL REFERENCES customers(id),
  status      VARCHAR(20) NOT NULL DEFAULT 'pending',
  total_cents BIGINT NOT NULL,            -- ⚠️ tiền lưu bằng SỐ NGUYÊN
  created_at  TIMESTAMPTZ NOT NULL DEFAULT now()
);

CREATE TABLE order_items (
  id         SERIAL PRIMARY KEY,
  order_id   INT NOT NULL REFERENCES orders(id) ON DELETE CASCADE,
  product_id INT NOT NULL REFERENCES products(id),
  quantity   INT NOT NULL CHECK (quantity > 0),
  price_cents BIGINT NOT NULL             -- ⚠️ chốt giá TẠI THỜI ĐIỂM MUA
);
```

### Ba quyết định thiết kế ghi điểm ngay

**1. Tiền không bao giờ dùng `FLOAT`.** Dùng `BIGINT` lưu theo đơn vị nhỏ nhất (xu/đồng), hoặc `DECIMAL(19,4)`.
Lý do: `0.1 + 0.2 !== 0.3` với số thực dấu phẩy động. Sai một xu trong hệ thống thanh toán là lỗi nghiêm trọng.

**2. `order_items.price_cents` phải lưu lại, không JOIN sang `products` để lấy giá.**
Vì mai shop đổi giá, hoá đơn cũ sẽ đổi theo → sai lịch sử. Đây là **denormalize có chủ đích**.

**3. Khi nào cố tình phá chuẩn hoá?** Khi đọc quá nhiều và JOIN quá tốn. Ví dụ lưu sẵn `orders.items_count`. Đánh đổi: nhanh hơn khi đọc, nhưng phải tự giữ cho số liệu đồng bộ.

> Câu trả lời hay: *"Em chuẩn hoá trước, chỉ phá chuẩn khi đo được là chậm thật, và phá thì phải biết mình đang đánh đổi tính nhất quán lấy tốc độ."*

---

## 2. Index — thứ hay bị hỏi nhất 🔴

### Index là gì

Là **cây B-tree** sắp xếp sẵn theo cột. Không có index, DB phải **quét toàn bảng** (`Seq Scan`). Có index thì tìm như tra từ điển.

```sql
CREATE INDEX idx_orders_customer ON orders(customer_id);
```

### Đánh đổi — phải nói được

| Được | Mất |
|---|---|
| `SELECT ... WHERE` nhanh hơn nhiều | `INSERT/UPDATE/DELETE` **chậm hơn** (phải cập nhật cả index) |
| `ORDER BY` khỏi phải sắp lại | Tốn thêm dung lượng đĩa |

→ **Không đánh index bừa.** Chỉ đánh vào cột hay nằm trong `WHERE`, `JOIN`, `ORDER BY`.

### Composite index và quy tắc tiền tố trái

```sql
CREATE INDEX idx_orders ON orders(customer_id, status, created_at);
```

Index này dùng được cho:
- `WHERE customer_id = 1` ✅
- `WHERE customer_id = 1 AND status = 'paid'` ✅
- `WHERE customer_id = 1 AND status = 'paid' ORDER BY created_at` ✅

**Không** dùng được cho:
- `WHERE status = 'paid'` ❌ — vì bỏ qua cột đầu tiên

> Ví von: như danh bạ sắp theo (Họ, Tên). Tìm theo Họ thì nhanh; tìm theo mỗi Tên thì vẫn phải lật hết.

### Khi nào index bị vô hiệu — câu hỏi bẫy kinh điển

```sql
-- ❌ Bọc hàm quanh cột → index chết
WHERE LOWER(email) = 'a@b.com'
-- ✅ Tạo index biểu thức
CREATE INDEX idx_email_lower ON users(LOWER(email));

-- ❌ LIKE mở đầu bằng %
WHERE name LIKE '%giang'
-- ✅ khớp tiền tố thì vẫn dùng được index
WHERE name LIKE 'giang%'

-- ❌ Ép kiểu ngầm (cột int so với chuỗi)
WHERE user_id = '123'
```

### Các loại index khác nên biết tên

- **UNIQUE** — vừa tăng tốc vừa ràng buộc không trùng
- **Partial** (Postgres): `CREATE INDEX ... WHERE status = 'active'` — nhỏ hơn, nhanh hơn
- **Covering**: index chứa luôn cột cần lấy → không cần đọc bảng (`Index Only Scan`)
- **GIN**: cho tìm kiếm toàn văn và cột `JSONB`

---

## 3. JOIN và bài toán N+1 🔴

### Bốn kiểu JOIN

```sql
INNER JOIN   -- chỉ lấy dòng khớp cả hai bên
LEFT  JOIN   -- lấy hết bảng trái, bên phải không khớp thì NULL
RIGHT JOIN   -- ngược lại (hiếm dùng, thường viết lại thành LEFT)
FULL  JOIN   -- lấy hết cả hai
```

Câu hỏi thường gặp: *"Đếm số đơn của mỗi khách, kể cả khách chưa mua gì."*

```sql
SELECT c.id, c.name, COUNT(o.id) AS order_count
FROM customers c
LEFT JOIN orders o ON o.customer_id = c.id     -- LEFT để giữ khách chưa mua
GROUP BY c.id, c.name;
```

⚠️ Phải dùng `COUNT(o.id)` chứ không phải `COUNT(*)`. `COUNT(*)` đếm cả dòng NULL → khách chưa mua ra 1 thay vì 0.

### N+1 — lỗi hiệu năng phổ biến nhất

```js
// ❌ 1 query lấy đơn + N query lấy khách = 101 lần gọi DB
const orders = await db.order.findMany({ take: 100 });
for (const o of orders) {
  o.customer = await db.customer.findUnique({ where: { id: o.customerId } });
}

// ✅ Cách 1: JOIN / include — 1 query
const orders = await db.order.findMany({ take: 100, include: { customer: true } });

// ✅ Cách 2: gom id rồi lấy một lần — 2 query
const ids = [...new Set(orders.map(o => o.customerId))];
const customers = await db.customer.findMany({ where: { id: { in: ids } } });
```

**Cách phát hiện:** bật log query, thấy cùng một câu lặp lại nhiều lần là dính.

> 💡 Kể được chuyện anh từng gặp và sửa N+1 ở CiteFlow sẽ mạnh hơn nhiều so với đọc định nghĩa.

---

## 4. Transaction & mức cô lập 🔴

### ACID

- **A**tomicity — toàn bộ thành công, hoặc không gì cả
- **C**onsistency — dữ liệu luôn thoả ràng buộc
- **I**solation — các giao dịch chạy song song không giẫm chân nhau
- **D**urability — đã commit thì mất điện cũng còn

### Ví dụ bắt buộc dùng transaction

```sql
BEGIN;
  UPDATE inventory SET stock = stock - 1 WHERE product_id = 10 AND stock > 0;
  INSERT INTO orders (customer_id, total_cents) VALUES (1, 250000);
COMMIT;   -- lỗi giữa chừng → ROLLBACK, không bị trừ kho mà mất đơn
```

### Bốn mức cô lập và lỗi tương ứng

| Mức | Dirty read | Non-repeatable read | Phantom read |
|---|---|---|---|
| READ UNCOMMITTED | ✅ xảy ra | ✅ | ✅ |
| READ COMMITTED *(mặc định Postgres)* | ❌ | ✅ | ✅ |
| REPEATABLE READ *(mặc định MySQL)* | ❌ | ❌ | ✅ |
| SERIALIZABLE | ❌ | ❌ | ❌ |

- **Dirty read**: đọc phải dữ liệu người khác chưa commit
- **Non-repeatable read**: đọc 2 lần cùng 1 dòng ra 2 kết quả
- **Phantom read**: đọc 2 lần cùng điều kiện, lần sau xuất hiện dòng mới

Càng chặt càng an toàn nhưng càng chậm và dễ deadlock.

### Race condition kinh điển của e-commerce

Hai người cùng mua sản phẩm cuối cùng:

```sql
-- ❌ Sai: đọc rồi mới ghi, hai người đọc cùng lúc đều thấy stock = 1
SELECT stock FROM inventory WHERE product_id = 10;   -- cả hai thấy 1
UPDATE inventory SET stock = 0 WHERE product_id = 10; -- bán 2 lần!

-- ✅ Cách 1: cập nhật nguyên tử, đẩy điều kiện vào WHERE
UPDATE inventory SET stock = stock - 1
WHERE product_id = 10 AND stock > 0;
-- kiểm tra số dòng bị ảnh hưởng, = 0 nghĩa là hết hàng

-- ✅ Cách 2: khoá bi quan
SELECT stock FROM inventory WHERE product_id = 10 FOR UPDATE;
```

> Đây là câu **rất hay được hỏi ở công ty e-commerce như Everlastify.** Học thuộc.

---

## 5. Đọc EXPLAIN 🟡

```sql
EXPLAIN ANALYZE
SELECT * FROM orders WHERE customer_id = 42;
```

Cần nhận ra 3 thứ:

| Thấy gì | Nghĩa là |
|---|---|
| `Seq Scan` trên bảng lớn | **thiếu index** → cần thêm |
| `Index Scan` / `Index Only Scan` | tốt |
| `rows` ước lượng lệch xa thực tế | thống kê cũ → chạy `ANALYZE` |

---

## 6. MongoDB — khi nào dùng 🟡

> Anh chưa dùng thật. Mục tiêu: **trả lời được câu "khi nào chọn MongoDB thay vì Postgres"**.

### Mô hình dữ liệu

Không có bảng và dòng; có **collection** chứa **document** (giống JSON). Không cần schema cố định.

```js
{
  _id: ObjectId("..."),
  customer: { name: "Giang", email: "g@x.com" },   // lồng nhau được
  items: [                                          // mảng ngay trong document
    { productId: 10, qty: 2, price: 250000 }
  ],
  createdAt: ISODate("2026-08-17")
}
```

### Nhúng (embed) hay tham chiếu (reference)

- **Nhúng** khi: dữ liệu luôn đọc cùng nhau, không lớn vô hạn, ít thay đổi độc lập. Ví dụ `items` trong đơn hàng.
- **Tham chiếu** khi: dùng lại ở nhiều nơi, hoặc mảng có thể phình to vô hạn. Ví dụ `customerId`.

⚠️ Giới hạn 1 document = **16 MB**. Đừng nhúng thứ tăng không giới hạn (như comment).

### Khi nào chọn Mongo, khi nào chọn Postgres

| Chọn MongoDB | Chọn PostgreSQL |
|---|---|
| Cấu trúc thay đổi liên tục, mỗi bản ghi mỗi khác | Quan hệ rõ ràng, cần JOIN nhiều |
| Đọc nguyên khối theo document | Cần **transaction nhiều bảng** chặt chẽ |
| Log, sự kiện, catalog sản phẩm nhiều thuộc tính lạ | Đơn hàng, thanh toán, kho — chỗ tiền bạc |

**Câu trả lời an toàn và trung thực:**
> "Em chưa dùng MongoDB trong dự án thật, ở CiteFlow em dùng PostgreSQL với Drizzle ORM. Em hiểu Mongo mạnh khi dữ liệu không cố định cấu trúc và đọc theo cả document. Nhưng với đơn hàng và thanh toán em vẫn nghiêng về quan hệ vì cần transaction và ràng buộc khoá ngoại. Postgres giờ cũng có kiểu `JSONB` nên phần linh hoạt em xử lý được ngay trong Postgres."

Câu này cho thấy anh **hiểu đánh đổi**, không phải học vẹt.

---

## 7. Redis — 4 công dụng cần biết 🟡

Redis là kho **key-value nằm trong RAM** → cực nhanh, nhưng mất điện có thể mất dữ liệu (trừ khi bật persistence). Không dùng làm nguồn dữ liệu chính.

**1. Cache**
```js
const key = `product:${id}`;
let data = await redis.get(key);
if (!data) {
  data = await db.product.findUnique({ where: { id } });
  await redis.setex(key, 300, JSON.stringify(data));   // TTL 5 phút
}
```
Mẫu này gọi là **cache-aside**. Luôn đặt TTL.

**2. Session store** — nhiều server Node dùng chung một nơi lưu session.

**3. Rate limiting**
```js
const n = await redis.incr(`rate:${ip}`);
if (n === 1) await redis.expire(`rate:${ip}`, 60);
if (n > 100) throw new Error('429 Too Many Requests');
```

**4. Hàng đợi / pub-sub** — nền của BullMQ để chạy việc nặng ở background.

### Vô hiệu hoá cache — câu hỏi khó

*"Sửa sản phẩm rồi mà web vẫn hiện giá cũ, xử lý sao?"*
- **TTL**: đơn giản nhất, chấp nhận lệch trong khoảng TTL
- **Xoá chủ động**: sau khi `UPDATE` thì `redis.del('product:10')`
- **Write-through**: ghi DB xong ghi luôn cache

---

## 8. Bài tập thiết kế: giỏ hàng e-commerce 🔴

> Everlastify là công ty **thương mại điện tử**. Khả năng rất cao họ đưa một đề thiết kế kiểu này.

**Đề:** Thiết kế database cho chức năng giỏ hàng + đặt hàng.

**Cách trả lời có cấu trúc (nói to trong lúc vẽ):**

1. **Hỏi lại trước khi vẽ.** Khách chưa đăng nhập có giỏ hàng không? Có nhiều biến thể sản phẩm (size, màu) không? Có mã giảm giá không?
2. **Liệt kê thực thể**: `customers`, `products`, `product_variants`, `carts`, `cart_items`, `orders`, `order_items`, `payments`, `inventory`.
3. **Vẽ quan hệ**: 1 khách ↔ nhiều đơn; 1 đơn ↔ nhiều dòng hàng; 1 sản phẩm ↔ nhiều biến thể.
4. **Nêu quyết định quan trọng** — đây là phần ăn điểm:
   - `order_items` **chốt giá tại thời điểm mua**, không JOIN lấy giá hiện tại
   - Tiền lưu `BIGINT` theo đơn vị xu
   - Trừ kho bằng `UPDATE ... WHERE stock > 0` để chống bán vượt
   - Giỏ hàng của khách vãng lai lưu theo `session_id`, gộp vào tài khoản khi họ đăng nhập
   - Index: `orders(customer_id, created_at)`, `cart_items(cart_id)`
5. **Nói cả cái mình chưa làm**: "Nếu lượng đơn lớn em sẽ tách bảng theo tháng hoặc archive đơn cũ, nhưng ở quy mô ban đầu thì chưa cần."

Bước 1 và bước 5 là thứ phân biệt ứng viên tốt với ứng viên trung bình. **Đừng vẽ ngay khi chưa hỏi.**

---

## Checklist tự kiểm tra

- [ ] Giải thích 1NF/2NF/3NF bằng ví dụ đơn hàng, không đọc định nghĩa
- [ ] Vì sao tiền không lưu FLOAT
- [ ] Vì sao `order_items` phải lưu lại giá
- [ ] Index đánh đổi cái gì, khi nào bị vô hiệu
- [ ] Quy tắc tiền tố trái của composite index
- [ ] N+1 là gì, phát hiện và sửa thế nào
- [ ] Viết được câu SQL chống bán vượt kho
- [ ] 4 mức cô lập và lỗi tương ứng
- [ ] Khi nào Mongo, khi nào Postgres — nói được đánh đổi
- [ ] 4 công dụng của Redis
- [ ] Thiết kế xong schema giỏ hàng trong 15 phút, có hỏi lại đề trước
