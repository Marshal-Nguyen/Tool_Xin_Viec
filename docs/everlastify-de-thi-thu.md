# ĐỀ THI THỬ — Junior Full-stack Developer
## Thời gian: 55 phút · Tổng điểm: 100

QUY TẮC: viết tay ra giấy. Không mở máy tính, không tra Google, không dùng AI.
Bấm giờ 55 phút. Hết giờ là dừng, kể cả chưa xong.

Gợi ý phân bổ: Phần A 10ph · Phần B 18ph · Phần C 12ph · Phần D 10ph · Phần E 5ph

---

## PHẦN A — ĐOÁN OUTPUT (20 điểm, mỗi câu 4 điểm)

Viết ra màn hình in ra gì, theo đúng thứ tự.

**A1.**
```js
function test() {
  console.log(x);
  console.log(y);
  var x = 10;
  let y = 20;
}
test();
```

**A2.**
```js
const arr = [];
for (var i = 0; i < 3; i++) {
  arr.push(function () { return i; });
}
console.log(arr[0](), arr[1](), arr[2]());
```

**A3.**
```js
console.log('A');
setTimeout(() => console.log('B'), 0);
Promise.resolve().then(() => console.log('C')).then(() => console.log('D'));
queueMicrotask(() => console.log('E'));
console.log('F');
```

**A4.**
```js
const shop = {
  name: 'Everlastify',
  getName: function () { return this.name; },
  getNameArrow: () => this.name,
  getNameNested: function () {
    const inner = () => this.name;
    return inner();
  },
};
console.log(shop.getName());
console.log(shop.getNameArrow());
console.log(shop.getNameNested());
```

**A5.**
```js
const a = { list: [1, 2] };
const b = { ...a };
b.list.push(3);
b.name = 'B';
console.log(a.list);
console.log(a.name);
```

---

## PHẦN B — VIẾT HÀM (30 điểm)

**B1. (10 điểm)**
Cho mảng đơn hàng. Viết hàm `totalPaidRevenue(orders)` trả về TỔNG tiền của các đơn
có `status` là `'paid'`.

```js
const orders = [
  { id: 1, total: 250000, status: 'paid' },
  { id: 2, total: 180000, status: 'pending' },
  { id: 3, total: 320000, status: 'paid' },
];
// totalPaidRevenue(orders)  =>  570000
```

**B2. (10 điểm)**
Viết hàm `firstUniqueChar(str)` trả về **ký tự đầu tiên không lặp lại** trong chuỗi.
Nếu mọi ký tự đều lặp thì trả về `null`.

```js
// firstUniqueChar('everlastify')  =>  'v'
// firstUniqueChar('aabb')         =>  null
```

**B3. (10 điểm)**
Viết hàm `chunk(arr, size)` chia mảng thành các nhóm nhỏ có độ dài `size`.
Nhóm cuối có thể ngắn hơn.

```js
// chunk([1,2,3,4,5], 2)  =>  [[1,2],[3,4],[5]]
// chunk([1,2,3], 5)      =>  [[1,2,3]]
```

---

## PHẦN C — SQL (25 điểm)

Cho ba bảng:

```
products(id, name, price, category_id)
orders(id, customer_id, created_at, status)
order_items(id, order_id, product_id, quantity, price)
```

**C1. (8 điểm)**
Viết câu truy vấn lấy **5 sản phẩm bán chạy nhất** theo tổng số lượng đã bán.
Kết quả gồm: tên sản phẩm và tổng số lượng.

**C2. (8 điểm)**
Tính **doanh thu theo từng tháng của năm 2026**, chỉ tính các đơn có `status = 'paid'`.
Kết quả gồm: tháng và tổng doanh thu, sắp xếp theo tháng tăng dần.
(Doanh thu một dòng hàng = `quantity * price` trong bảng `order_items`.)

**C3. (9 điểm)**
Liệt kê **những sản phẩm CHƯA từng được đặt mua lần nào**.
Viết bằng hai cách khác nhau.

---

## PHẦN D — REACT (15 điểm)

**D1. (10 điểm)**
Viết một component `ProductList` gọi API `/api/products` khi component được mount,
và hiển thị:
- đang tải thì hiện chữ "Loading..."
- lỗi thì hiện "Something went wrong"
- thành công thì hiện danh sách tên sản phẩm

**D2. (5 điểm)**
Đoạn code dưới đây có lỗi gì? Giải thích và sửa lại.

```jsx
function Cart({ userId }) {
  const [items, setItems] = useState([]);

  useEffect(() => {
    fetch(`/api/cart/${userId}`)
      .then(r => r.json())
      .then(setItems);
  });

  return <div>{items.length} sản phẩm</div>;
}
```

---

## PHẦN E — LÝ THUYẾT NGẮN (10 điểm, mỗi câu 2 điểm)

Trả lời ngắn gọn, 1 đến 3 câu.

**E1.** Phân biệt mã lỗi HTTP **401** và **403**.

**E2.** `WHERE` khác `HAVING` chỗ nào? Cho ví dụ khi nào bắt buộc dùng `HAVING`.

**E3.** Index trong database dùng để làm gì, và đánh đổi điều gì?

**E4.** Node.js là đơn luồng hay đa luồng? Giải thích ngắn.

**E5.** Khi nào nên chọn NoSQL thay vì SQL?

---

HẾT ĐỀ

Làm xong thì gõ đáp án vào chat để chấm. Ghi rõ số câu, ví dụ "A1: ...", "B2: ...".
Câu nào bỏ trống thì ghi "bỏ trống" chứ đừng bỏ qua, để biết bạn hụt ở đâu.
