# Module 6 — React Query / TanStack Query (Bài giảng chi tiết)

> ⭐ Đây là **điểm mạnh phỏng vấn của anh** — anh đã dùng React Query ở EmoEase (tích hợp 80+ API). Mục tiêu: không chỉ "biết dùng" mà **giải thích được VÌ SAO dùng** + cơ chế cache. Đó là chỗ anh nói sâu hơn ứng viên khác.
>
> Cú pháp dưới theo **TanStack Query v5** (mới nhất). Khác biệt v4 sẽ ghi chú riêng.

Mục lục:
1. Vấn đề React Query giải quyết (Server state vs Client state) — QUAN TRỌNG
2. `useQuery` cơ bản
3. Các trạng thái: loading/error/data, `isLoading` vs `isFetching`
4. Cache: `staleTime` vs `gcTime`
5. Refetch tự động (background, on-focus, interval)
6. `useMutation` (tạo/sửa/xoá)
7. Invalidation — giữ dữ liệu luôn mới
8. Dependent query & `enabled`
9. Optimistic update (mức nhận biết)
10. Vì sao React Query thay vì `useEffect`+fetch (CÂU TỦ)
11. Cách kể chuyện EmoEase khi phỏng vấn

---

## 1. Vấn đề nó giải quyết: Server State vs Client State

**Insight cốt lõi (nói câu này khi phỏng vấn là ăn điểm ngay):**

> "Dữ liệu từ server (server state) khác hẳn state nội bộ (client state): nó **bất đồng bộ**, **có thể cũ** (người khác sửa), **được nhiều component dùng chung**, và cần **cache/đồng bộ**. Tự quản bằng `useState`+`useEffect` phải viết tay rất nhiều: loading, error, cache, retry, refetch, chống race condition. React Query gói toàn bộ việc đó."

| | Client state | Server state |
|---|---|---|
| Ví dụ | mở/đóng modal, theme | danh sách user, bài viết |
| Đặc điểm | đồng bộ, mình sở hữu | bất đồng bộ, server sở hữu, có thể cũ |
| Công cụ | `useState`, Context, Redux | **React Query** |

---

## 2. `useQuery` cơ bản

```jsx
import { useQuery } from '@tanstack/react-query';

function Users() {
  const { data, isPending, isError, error } = useQuery({
    queryKey: ['users'],              // ID duy nhất của query (dùng cho cache)
    queryFn: () => fetch('/api/users').then(r => r.json()),
  });

  if (isPending) return <p>Đang tải...</p>;
  if (isError)   return <p>Lỗi: {error.message}</p>;
  return <ul>{data.map(u => <li key={u.id}>{u.name}</li>)}</ul>;
}
```

**2 thứ bắt buộc:**
- `queryKey`: mảng định danh duy nhất → React Query dùng làm **khoá cache**. Có tham số thì đưa vào key: `['user', userId]`.
- `queryFn`: hàm trả về Promise (gọi API).

> Phải bọc app trong `<QueryClientProvider client={queryClient}>` ở root.

---

## 3. Các trạng thái: `isLoading` vs `isFetching` (hay bẫy)

- `status`: `'pending' | 'error' | 'success'`.
- `isPending`: chưa có dữ liệu lần đầu (đang tải lần đầu).
- `isFetching`: **đang gọi API** (kể cả background refetch khi đã có data cũ).
- `isLoading` (v5) = `isPending && isFetching` (lần tải đầu).

```jsx
// data cũ vẫn hiển thị trong khi nền đang refetch
const { data, isFetching } = useQuery({ queryKey: ['users'], queryFn });
return (
  <>
    {isFetching && <Spinner small />}   {/* nền đang cập nhật */}
    <UserList data={data} />            {/* vẫn thấy data cũ, không trắng màn hình */}
  </>
);
```
> 🎯 **Phỏng vấn:** "`isLoading` vs `isFetching` khác gì?" → `isFetching` true mỗi lần gọi API (kể cả refetch nền); `isLoading`/`isPending` chỉ true ở lần tải **đầu tiên** chưa có data. Trả lời được = hiểu sâu.

---

## 4. Cache: `staleTime` vs `gcTime` (CÂU HAY HỎI NHẤT về React Query)

- **`staleTime`** (mặc định `0`): bao lâu data được coi là **"tươi" (fresh)**. Khi còn fresh, React Query **KHÔNG gọi lại API** (dùng cache ngay).
- **`gcTime`** (mặc định 5 phút; v4 tên là `cacheTime`): khi không còn component nào dùng query (inactive), data còn nằm trong cache bao lâu trước khi **bị dọn (garbage collect)**.

```jsx
useQuery({
  queryKey: ['users'],
  queryFn,
  staleTime: 60_000,   // 1 phút: trong 1 phút không refetch, dùng cache luôn
  gcTime: 5 * 60_000,  // sau khi không dùng, giữ cache 5 phút rồi xoá
});
```

**Hình dung:**
- `staleTime` = "data còn dùng được mà không cần hỏi lại server bao lâu".
- `gcTime` = "data bị bỏ rơi thì giữ trong bộ nhớ bao lâu nữa".

> 🎯 **Phỏng vấn:** "staleTime và cacheTime/gcTime khác nhau?" → staleTime quyết định **khi nào refetch**; gcTime quyết định **khi nào xoá cache của query không dùng**. Đây là câu test người dùng thật vs dùng hời hợt.

---

## 5. Refetch tự động — thứ khiến React Query "thần kỳ"

Mặc định React Query tự refetch khi:
- **Component mount lại** (nếu data đã stale).
- **Cửa sổ được focus lại** (`refetchOnWindowFocus` — quay lại tab → dữ liệu mới).
- **Mạng kết nối lại**.
- Có thể đặt `refetchInterval: 5000` để poll định kỳ.

```jsx
useQuery({ queryKey: ['notifications'], queryFn, refetchInterval: 10_000 }); // poll mỗi 10s
```
> 🎯 Đây là lý do dữ liệu "luôn mới" mà mình **không viết code đồng bộ thủ công**. Nói ý này khi phỏng vấn.

---

## 6. `useMutation` — tạo / sửa / xoá

`useQuery` để **đọc**; `useMutation` để **ghi** (POST/PUT/PATCH/DELETE).

```jsx
import { useMutation, useQueryClient } from '@tanstack/react-query';

function AddTodo() {
  const queryClient = useQueryClient();

  const mutation = useMutation({
    mutationFn: (newTodo) =>
      fetch('/api/todos', { method: 'POST', body: JSON.stringify(newTodo) }),
    onSuccess: () => {
      // làm danh sách todos "stale" → React Query tự refetch → UI cập nhật
      queryClient.invalidateQueries({ queryKey: ['todos'] });
    },
    onError: (err) => { /* toast lỗi */ },
  });

  return (
    <button
      disabled={mutation.isPending}
      onClick={() => mutation.mutate({ title: 'Học React Query' })}
    >
      {mutation.isPending ? 'Đang lưu...' : 'Thêm'}
    </button>
  );
}
```
> 🎯 **Phỏng vấn:** "Sau khi tạo mới, làm sao danh sách tự cập nhật?" → `invalidateQueries` trong `onSuccess` → query liên quan bị đánh dấu stale → tự refetch.

---

## 7. Invalidation — giữ dữ liệu luôn đồng bộ

```jsx
queryClient.invalidateQueries({ queryKey: ['todos'] });
```
"Đánh dấu mọi query khớp key này là cũ → refetch nếu đang hiển thị." Đây là cách phổ biến nhất để đồng bộ sau khi ghi dữ liệu.

---

## 8. Dependent query & `enabled`

Query chỉ chạy khi có điều kiện (vd phải có `userId` mới gọi):

```jsx
const { data: user } = useQuery({ queryKey: ['user', id], queryFn });
const { data: posts } = useQuery({
  queryKey: ['posts', user?.id],
  queryFn: () => fetchPosts(user.id),
  enabled: !!user?.id,   // chỉ chạy khi đã có user.id
});
```
> 🎯 "Làm sao để query B chờ query A xong?" → `enabled` dựa trên kết quả A.

---

## 9. Optimistic update (mức nhận biết — cộng điểm)

Cập nhật UI **ngay** trước khi server trả lời (cảm giác nhanh), rồi rollback nếu lỗi. Dùng `onMutate` để set cache tạm + `onError` để hoàn tác. Junior chỉ cần **biết khái niệm** và khi nào dùng (like, toggle).

---

## 10. ⭐ CÂU TỦ: Vì sao React Query thay vì `useEffect` + fetch?

**Tự viết `useEffect` + fetch, anh phải tự lo:** loading/error state, cache, dedupe (nhiều component gọi cùng API → gọi nhiều lần), retry khi lỗi, refetch khi quay lại tab, chống **race condition** (response cũ về sau ghi đè response mới), đồng bộ sau khi mutate.

**React Query cho sẵn tất cả:**
- ✅ Tự quản loading / error / success
- ✅ **Cache + dedupe** request (nhiều component dùng chung 1 cache)
- ✅ **Background refetch** (data luôn mới) + on-focus / on-reconnect
- ✅ **Retry** tự động khi lỗi
- ✅ **Invalidation** để đồng bộ sau ghi
- ✅ Giảm cực nhiều boilerplate

> **Câu trả lời mẫu (học thuộc ý):**
> "Khi tự dùng `useEffect`+fetch, tôi phải viết tay loading, error, cache, retry, và xử lý race condition — rất dễ sót. React Query quản **server state**: nó cache và dedupe request, tự refetch nền để data luôn mới, retry khi lỗi, và cho `invalidateQueries` để đồng bộ sau khi ghi. Ở EmoEase với 80+ endpoint, nó giúp tôi giảm rất nhiều code lặp và tránh bug dữ liệu cũ/không đồng bộ."

---

## 11. Cách kể chuyện EmoEase (chuẩn bị trước)

Khi họ hỏi về dự án, chủ động kéo về React Query:
- **Bối cảnh:** EmoEase tích hợp 80+ REST API (chat AI, đánh giá, lịch hẹn...).
- **Vấn đề:** nếu tự fetch, quản loading/cache/đồng bộ cho 80+ endpoint là ác mộng + dễ bug data cũ.
- **Giải pháp:** dùng React Query — `queryKey` rõ ràng, cache + dedupe, `invalidateQueries` sau mutate để UI luôn đồng bộ, retry tự động.
- **Kết quả:** code gọn, ít bug đồng bộ, app phục vụ 1,000+ users.
- **Nếu được hỏi sâu:** giải thích staleTime/gcTime, isFetching vs isLoading (đã học ở trên).

> ⚠️ **Trung thực:** trước phỏng vấn, mở lại code EmoEase xem anh đã set `staleTime`, `invalidateQueries` ở đâu — để nói **đúng cái mình đã làm**, không chế.

---

## ✅ Bài tập (code tay / đọc lại code cũ)
1. Viết 1 `useQuery` lấy danh sách + 1 `useMutation` thêm item, `invalidateQueries` sau khi thêm.
2. Thêm `staleTime` và quan sát: trong khoảng staleTime, chuyển trang rồi quay lại — KHÔNG gọi API lại.
3. Mở code EmoEase: tìm 1 chỗ dùng React Query, viết ra giấy "vì sao chỗ này dùng nó".

## ✅ Nắm chắc khi:
- Giải thích được **server state vs client state** và vì sao React Query.
- Phân biệt `isLoading`/`isFetching`, `staleTime`/`gcTime`.
- Nói được luồng: mutate → `invalidateQueries` → refetch → UI đồng bộ.
- Kể được câu chuyện EmoEase mạch lạc trong 2-3 phút.

> Tiếp theo nên ôn **Module 13 — Đào sâu dự án**: ghép React Query + kiến trúc EmoEase/CiteFlow thành câu chuyện hoàn chỉnh.
