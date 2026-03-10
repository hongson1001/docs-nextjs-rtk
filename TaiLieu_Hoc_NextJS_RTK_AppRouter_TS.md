# TÀI LIỆU HỌC NEXT.JS (APP ROUTER + TYPESCRIPT) VÀ REDUX TOOLKIT

> Mục tiêu: Học để tự cắt giao diện, viết logic, và làm được project thực tế với Next.js + RTK.

---

## 0) Cách sử dụng tài liệu này

- Mỗi giai đoạn có 4 phần:
  - **Lý thuyết cốt lõi**
  - **Thực hành**
  - **Checklist đạt được**
  - **Ghi chú cá nhân**
- Bạn học theo thứ tự từ trên xuống dưới.
- Hoàn thành checklist mới chuyển sang phần tiếp theo.
- Mỗi ngày nên học 60-120 phút, thực hành ít nhất 60% thời gian.

---

## 1) Tổng quan lộ trình

### Giai đoạn 1: Nền tảng React + TypeScript + Hooks
- Hiểu component, props, state, event.
- Nắm được TypeScript căn bản trong React.
- Sử dụng thành thạo các hooks quan trọng.

### Giai đoạn 2: Next.js App Router (TypeScript)
- Hiểu cấu trúc `app/`, `layout.tsx`, `page.tsx`.
- Phân biệt Server Component và Client Component.
- Routing, dynamic route, loading/error/not-found.
- Data fetching đúng cách trong App Router.

### Giai đoạn 3: Redux Toolkit (RTK) trong Next.js
- Tạo store, slice, action, selector.
- Xử lý API với `createAsyncThunk`.
- Tích hợp RTK vào Next.js App Router đúng client boundary.

### Giai đoạn 4: Project thực chiến
- Làm app CRUD (ví dụ: Quản lý công việc).
- Có tìm kiếm, lọc, phân trang, loading, error.
- Tổ chức code sạch, dễ bảo trì.

---

## 2) GIAI ĐOẠN 1 - React + TypeScript + Hooks

### 2.1 Lý thuyết cốt lõi

#### A. React căn bản
- Component là hàm trả về JSX.
- Props dùng để truyền dữ liệu từ cha -> con.
- State dùng để lưu trạng thái bên trong component.
- Event handling: `onClick`, `onChange`, `onSubmit`.

Ví dụ nhanh:

```tsx
type HelloProps = { name: string };

function Hello({ name }: HelloProps) {
  return <h1>Xin chao {name}</h1>;
}
```

- `Hello` là component.
- `name` là props truyền vào.

Ví dụ riêng cho Props (cha -> con):

```tsx
type UserCardProps = {
  name: string;
  age: number;
};

function UserCard({ name, age }: UserCardProps) {
  return <p>{name} - {age} tuoi</p>;
}

export default function Parent() {
  return <UserCard name="Son" age={25} />;
}
```

- `Parent` (cha) truyền dữ liệu cho `UserCard` (con) qua props.
- Component con không được sửa trực tiếp props.

Ví dụ riêng cho State (trạng thái nội bộ):

```tsx
"use client";
import { useState } from "react";

export default function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount((v) => v + 1)}>
      Count: {count}
    </button>
  );
}
```

- `count` là state nội bộ của `Counter`.
- Mỗi lần `setCount`, component re-render.

#### B. TypeScript trong React
- Định nghĩa kiểu cho props:
  - `type ButtonProps = { label: string; onClick: () => void }`
- Typing cho event:
  - `React.ChangeEvent<HTMLInputElement>`
- Union type:
  - `status: "idle" | "loading" | "success" | "error"`

Ví dụ nhanh:

```tsx
type LoginFormProps = {
  status: "idle" | "loading" | "success" | "error";
};

function LoginForm({ status }: LoginFormProps) {
  return <p>Trang thai: {status}</p>;
}
```

- Bạn chỉ được truyền 1 trong 4 giá trị cho `status`.
- Nếu truyền giá trị khác, TypeScript báo lỗi ngay.

#### C. React Hooks cần học kỹ
- `useState`: quản lý state local.
- `useEffect`: side effects (gọi API, lắng nghe sự kiện, đồng bộ dữ liệu).
- `useMemo`: cache kết quả tính toán nặng.
- `useCallback`: giữ tham chiếu hàm ổn định.
- `useRef`: truy cập DOM / lưu giá trị không gây re-render.
- `useContext`: chia sẻ state nhẹ trong app.

Ví dụ nhanh `useState`:

```tsx
"use client";
import { useState } from "react";

export default function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount((v) => v + 1)}>Count: {count}</button>;
}
```

Ví dụ nhanh `useEffect`:

```tsx
"use client";
import { useEffect, useState } from "react";

export default function Clock() {
  const [time, setTime] = useState<string>("");

  useEffect(() => {
    const id = setInterval(() => setTime(new Date().toLocaleTimeString()), 1000);
    return () => clearInterval(id);
  }, []);

  return <p>{time}</p>;
}
```

Ví dụ nhanh `useRef` (focus input):

```tsx
"use client";
import { useRef } from "react";

export default function FocusInput() {
  const inputRef = useRef<HTMLInputElement>(null);

  return (
    <div>
      <input ref={inputRef} />
      <button onClick={() => inputRef.current?.focus()}>Focus</button>
    </div>
  );
}
```

Ví dụ nhanh `useMemo` (tránh tính lại không cần thiết):

```tsx
"use client";
import { useMemo } from "react";

type Task = { id: number; done: boolean };

export default function DoneCount({ tasks }: { tasks: Task[] }) {
  const doneCount = useMemo(() => {
    return tasks.filter((t) => t.done).length;
  }, [tasks]);

  return <p>Da hoan thanh: {doneCount}</p>;
}
```

Ví dụ nhanh `useCallback` (giữ hàm ổn định):

```tsx
"use client";
import { useCallback, useState } from "react";

type Task = { id: number; title: string };

export default function TodoActions() {
  const [tasks, setTasks] = useState<Task[]>([]);

  const removeTask = useCallback((id: number) => {
    setTasks((prev) => prev.filter((t) => t.id !== id));
  }, []);

  return <button onClick={() => removeTask(1)}>Xoa task id=1</button>;
}
```

Ví dụ nhanh `useContext` (chia sẻ dữ liệu toàn vùng):

```tsx
"use client";
import { createContext, useContext } from "react";

const ThemeContext = createContext<"light" | "dark">("light");

function Header() {
  const theme = useContext(ThemeContext);
  return <p>Theme hien tai: {theme}</p>;
}

export default function AppTheme() {
  return (
    <ThemeContext.Provider value="dark">
      <Header />
    </ThemeContext.Provider>
  );
}
```

### 2.2 Thực hành đề xuất
- Bài 1: Tạo form thêm task (title, description).
- Bài 2: Viết bộ lọc task theo keyword và status.
- Bài 3: Tính tổng số task đã hoàn thành (dùng `useMemo`).
- Bài 4: Focus input tự động khi mở modal (`useRef`).

### 2.3 Lỗi thường gặp
- Quên thêm dependency vào `useEffect`.
- Dùng `useMemo/useCallback` quá sớm, không cần thiết.
- TypeScript bị `any` quá nhiều.

### 2.4 Checklist đạt được
- [ ] Viết component có props typed đầy đủ.
- [ ] Dùng `useState` và `useEffect` đúng tình huống.
- [ ] Hiểu khi nào dùng `useMemo`, `useCallback`, `useRef`.
- [ ] Không dùng `any` tuỳ tiện.

### 2.5 Ghi chú cá nhân (tự điền)
- Kiến thức dễ nhớ:
  - 
- Vấn đề gặp phải:
  - 
- Cách tôi sửa:
  - 
- Ví dụ tôi tự viết:
  - 

### 2.6 Lộ trình học Giai đoạn 1 theo từng buổi

#### Buổi 1 - Component, Props, State, Event
- Mục tiêu:
  - Hiểu function component là gì.
  - Biết truyền props có type.
  - Biết tạo state và cập nhật state.
- Bài tập:
  - Tạo `TaskItem` nhận props: `title`, `done`.
  - Tạo `TaskForm` gồm input + nút thêm task.
  - Render danh sách task ở component cha.
- Ghi chú nhanh:
  - Props là dữ liệu "đi xuống", không sửa trực tiếp trong component con.
  - State là dữ liệu nội bộ, dùng hàm setter để cập nhật.

#### Buổi 2 - useEffect và vòng đời dữ liệu
- Mục tiêu:
  - Hiểu khi nào cần `useEffect`.
  - Biết dependency array ảnh hưởng thế nào.
- Bài tập:
  - Lưu task vào `localStorage` mỗi khi danh sách thay đổi.
  - Tải lại task từ `localStorage` khi vào trang.
- Ghi chú nhanh:
  - `useEffect` không dùng để tính toán UI đơn giản.
  - Luôn xem kỹ dependency để tránh bug.

#### Buổi 3 - useMemo, useCallback, useRef
- Mục tiêu:
  - Biết tối ưu đúng lúc, không lạm dụng.
  - Biết dùng ref để focus input.
- Bài tập:
  - Dùng `useMemo` tính số task done.
  - Dùng `useRef` focus input sau khi thêm task.
- Ghi chú nhanh:
  - Chỉ tối ưu khi có vấn đề hiệu năng thật.
  - Ưu tiên code dễ đọc trước, tối ưu sau.

### 2.7 Cách dùng Function Component đúng cách (rất quan trọng)

#### A. Mẫu đúng cơ bản
- Đặt tên component viết hoa chữ cái đầu.
- Props phải có type rõ ràng.
- Trả về JSX rõ ràng, ngắn gọn.

```tsx
type TaskItemProps = {
  title: string;
  done: boolean;
  onToggle: () => void;
};

export default function TaskItem({ title, done, onToggle }: TaskItemProps) {
  return (
    <li>
      <button onClick={onToggle}>{done ? "Done" : "Todo"}</button>
      <span>{title}</span>
    </li>
  );
}
```

Giải thích nhanh:
- `TaskItem` viết hoa đúng quy tắc component.
- Props được type rõ ràng bằng `TaskItemProps`.
- Không sửa trực tiếp props, chỉ gọi `onToggle` để component cha xử lý.

#### B. Quy tắc quan trọng
- Không gọi hooks bên trong `if`, `for`, `while`.
- Không sửa props trực tiếp.
- Không đặt quá nhiều logic trong 1 component lớn.
- Tách thành component nhỏ nếu JSX dài, khó đọc.

#### C. Khi nào thêm "use client" trong Next.js App Router
- Component có sử dụng hook (`useState`, `useEffect`, ...).
- Component có event browser (`onClick`, `onChange`, ...).
- Component dùng API chỉ có trên browser (`window`, `localStorage`).

Ví dụ:

```tsx
"use client";
import { useState } from "react";

export default function SearchBox() {
  const [q, setQ] = useState("");
  return <input value={q} onChange={(e) => setQ(e.target.value)} />;
}
```

- Vì component có `useState` + `onChange`, bắt buộc thêm `"use client"`.

#### D. Mẫu sai thường gặp

```tsx
// Sai: ten component viet thuong + hooks trong if
function taskItem() {
  if (true) {
    const [count, setCount] = useState(0);
  }
  return <div>Task</div>;
}
```

#### E. Mẫu đúng thay thế

```tsx
"use client";
import { useState } from "react";

export default function TaskItem() {
  const [count, setCount] = useState(0);

  return <button onClick={() => setCount((v) => v + 1)}>{count}</button>;
}
```

### 2.8 Khu vực ghi chú buổi học (điền trực tiếp vào đây)

#### Mẫu ghi chú Buổi 1
- Tôi hiểu:
  - 
- Tôi chưa hiểu:
  - 
- Lỗi tôi đã gặp:
  - 
- Cách tôi sửa:
  - 
- Đoạn code tôi viết tốt nhất hôm nay:
  - 

#### Mẫu ghi chú Buổi 2
- Tôi hiểu:
  - 
- Tôi chưa hiểu:
  - 
- Lỗi tôi đã gặp:
  - 
- Cách tôi sửa:
  - 
- Đoạn code tôi viết tốt nhất hôm nay:
  - 

#### Mẫu ghi chú Buổi 3
- Tôi hiểu:
  - 
- Tôi chưa hiểu:
  - 
- Lỗi tôi đã gặp:
  - 
- Cách tôi sửa:
  - 
- Đoạn code tôi viết tốt nhất hôm nay:
  - 

---

## 3) GIAI ĐOẠN 2 - Next.js App Router + TypeScript

### 3.1 Lý thuyết cốt lõi

#### A. Cấu trúc thư mục App Router
- `app/layout.tsx`: layout gốc.
- `app/page.tsx`: trang home.
- `app/about/page.tsx`: route `/about`.
- `app/products/[id]/page.tsx`: dynamic route.
- `app/loading.tsx`: UI loading.
- `app/error.tsx`: UI khi có lỗi runtime.
- `app/not-found.tsx`: UI khi không tìm thấy trang.

Ví dụ cấu trúc:

```txt
app/
  layout.tsx
  page.tsx
  about/
    page.tsx
  products/
    page.tsx
    [id]/
      page.tsx
```

#### B. Server Component vs Client Component
- Mặc định trong `app/` là **Server Component**.
- Nếu cần hooks/event browser -> thêm `"use client"` ở đầu file.
- Quy tắc:
  - UI có click/typing state local: Client Component.
  - Lấy data server và render lần đầu: ưu tiên Server Component.

Ví dụ Server Component:

```tsx
// app/products/page.tsx
async function getProducts() {
  const res = await fetch("https://dummyjson.com/products");
  return res.json();
}

export default async function ProductsPage() {
  const data = await getProducts();
  return <pre>{JSON.stringify(data.products?.slice(0, 3), null, 2)}</pre>;
}
```

Ví dụ Client Component:

```tsx
"use client";
import { useState } from "react";

export default function CounterClient() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount((v) => v + 1)}>{count}</button>;
}
```

#### C. Navigation hooks trong Next.js
- `useRouter()`: push/replace/back.
- `usePathname()`: lấy current path.
- `useSearchParams()`: đọc query string.
- `useParams()`: lấy dynamic params.

Ví dụ:

```tsx
"use client";
import { useParams, usePathname, useRouter, useSearchParams } from "next/navigation";

export default function NavExample() {
  const router = useRouter();
  const pathname = usePathname();
  const searchParams = useSearchParams();
  const params = useParams<{ id: string }>();

  return (
    <div>
      <p>Path: {pathname}</p>
      <p>Query q: {searchParams.get("q")}</p>
      <p>Param id: {params?.id}</p>
      <button onClick={() => router.push("/products?q=phone")}>Di toi products</button>
    </div>
  );
}
```

#### D. Data Fetching trong App Router
- Gọi API trong Server Component với `fetch`.
- Caching:
  - mặc định có cache (tuỳ theo request)
  - `cache: "no-store"` cho dữ liệu luôn mới
  - `next: { revalidate: 60 }` để ISR mỗi 60s

Ví dụ `no-store`:

```tsx
const res = await fetch("https://dummyjson.com/products", {
  cache: "no-store",
});
```

Ví dụ `revalidate`:

```tsx
const res = await fetch("https://dummyjson.com/products", {
  next: { revalidate: 60 },
});
```

### 3.2 Thực hành đề xuất
- Bài 1: Tạo 3 trang: Home, Products, Product Detail.
- Bài 2: Tạo dynamic route `/products/[id]`.
- Bài 3: Tạo `loading.tsx` và `error.tsx` cho route products.
- Bài 4: Viết bộ lọc theo query string (`?q=...`) bằng `useSearchParams`.

### 3.3 Lỗi thường gặp
- Dùng hook React trong Server Component (gây lỗi).
- Quên `"use client"` ở component cần interactive.
- Không hiểu cache, dẫn đến dữ liệu "không update".

### 3.4 Checklist đạt được
- [ ] Tự tạo được route tĩnh và route động.
- [ ] Hiểu và áp dụng đúng Server/Client Component.
- [ ] Viết data fetching đúng cách trong App Router.
- [ ] Xử lý loading/error/not-found rõ ràng.

### 3.5 Ghi chú cá nhân (tự điền)
- Kiến thức dễ nhớ:
  - 
- Vấn đề gặp phải:
  - 
- Cách tôi sửa:
  - 
- Ví dụ tôi tự viết:
  - 

### 3.6 Lộ trình học Giai đoạn 2 theo từng buổi

#### Buổi 1 - Routing và layout
- Mục tiêu:
  - Tạo route tĩnh + route động.
  - Hiểu quan hệ giữa `layout.tsx` và `page.tsx`.
- Bài tập:
  - Tạo `/about`, `/products`, `/products/[id]`.
  - Tạo layout chung có header + main.

#### Buổi 2 - Server/Client Component + hooks Next
- Mục tiêu:
  - Phân biệt rõ component nào là server/client.
  - Dùng được `useRouter`, `usePathname`, `useSearchParams`, `useParams`.
- Bài tập:
  - Tạo ô tìm kiếm query `?q=...`.
  - Tạo nút chuyển trang bằng `router.push`.

#### Buổi 3 - Data fetching + loading/error
- Mục tiêu:
  - Gọi API đúng trong App Router.
  - Hiểu cache và revalidate cơ bản.
- Bài tập:
  - Tạo `loading.tsx` cho route products.
  - Tạo `error.tsx` để xử lý lỗi runtime.

### 3.7 Mẫu file quan trọng trong App Router

#### `app/products/loading.tsx`

```tsx
export default function Loading() {
  return <p>Dang tai danh sach san pham...</p>;
}
```

#### `app/products/error.tsx`

```tsx
"use client";

export default function Error({
  error,
  reset,
}: {
  error: Error;
  reset: () => void;
}) {
  return (
    <div>
      <p>Co loi: {error.message}</p>
      <button onClick={reset}>Thu lai</button>
    </div>
  );
}
```

### 3.8 Bài giảng chi tiết Giai đoạn 2 (học để tự làm được)

#### Mục tiêu sau khi học xong Giai đoạn 2
- Bạn tự tạo được project Next.js App Router có cấu trúc rõ ràng.
- Bạn biết route nào dùng Server Component, route nào dùng Client Component.
- Bạn fetch dữ liệu đúng cách, hiểu cache cơ bản.
- Bạn xử lý được loading, error, not-found trong thực tế.
- Bạn biết đọc query, params và điều hướng trang bằng hooks Next.

---

#### Phần 1 - Tư duy App Router (gốc rễ)

- App Router là cách tổ chức route dựa trên thư mục trong `app/`.
- Mỗi folder có `page.tsx` sẽ tạo ra 1 URL.
- `layout.tsx` là bộ khung dùng chung cho nhiều trang con.
- Component trong `app/` mặc định là Server Component (render phía server).

Ví dụ:

```txt
app/
  layout.tsx      -> bo khung toan app
  page.tsx        -> /
  blog/
    page.tsx      -> /blog
    [slug]/
      page.tsx    -> /blog/:slug
```

Tư duy nhanh:
- `page.tsx` = nội dung trang.
- `layout.tsx` = khung bao ngoài.
- `loading.tsx` = cho route đang tải.
- `error.tsx` = khi route bị lỗi.
- `not-found.tsx` = khi không có dữ liệu / route sai.

---

#### Phần 2 - Layout trong App Router (cực kỳ quan trọng)

`layout.tsx` gốc:

```tsx
// app/layout.tsx
export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="vi">
      <body>
        <header>My App</header>
        <main>{children}</main>
      </body>
    </html>
  );
}
```

Giải thích:
- `children` là nội dung của route con.
- Header ở layout gốc sẽ xuất hiện trên mọi trang.
- Bạn có thể tạo layout riêng cho từng nhóm route.

Ví dụ layout riêng cho products:

```tsx
// app/products/layout.tsx
export default function ProductsLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <section>
      <h2>Khu vuc san pham</h2>
      {children}
    </section>
  );
}
```

---

#### Phần 3 - Server Component vs Client Component (để tránh lỗi)

##### Server Component (mặc định)
- Dùng để lấy data và render ban đầu.
- Không dùng được `useState`, `useEffect`, event click.
- Có thể gọi `fetch` trực tiếp.

```tsx
// app/products/page.tsx
async function getProducts() {
  const res = await fetch("https://dummyjson.com/products");
  if (!res.ok) throw new Error("Khong tai duoc du lieu");
  return res.json();
}

export default async function ProductsPage() {
  const data = await getProducts();
  return (
    <ul>
      {data.products.slice(0, 5).map((p: { id: number; title: string }) => (
        <li key={p.id}>{p.title}</li>
      ))}
    </ul>
  );
}
```

##### Client Component
- Dùng khi cần state, hooks, event browser.
- Thêm `"use client"` ở dòng đầu file.

```tsx
"use client";
import { useState } from "react";

export default function QuantityPicker() {
  const [qty, setQty] = useState(1);
  return (
    <div>
      <button onClick={() => setQty((v) => Math.max(1, v - 1))}>-</button>
      <span>{qty}</span>
      <button onClick={() => setQty((v) => v + 1)}>+</button>
    </div>
  );
}
```

Quy tắc vàng:
- Lấy data: ưu tiên Server Component.
- Tương tác UI: Client Component.
- Không nhất thiết biến tất cả thành Client.

---

#### Phần 4 - Dynamic Route và Params

Folder:

```txt
app/products/[id]/page.tsx
```

Code:

```tsx
// app/products/[id]/page.tsx
export default async function ProductDetail({
  params,
}: {
  params: { id: string };
}) {
  const res = await fetch(`https://dummyjson.com/products/${params.id}`);
  if (!res.ok) throw new Error("Khong tim thay san pham");
  const product = await res.json();

  return (
    <div>
      <h1>{product.title}</h1>
      <p>Gia: {product.price}</p>
    </div>
  );
}
```

Giải thích:
- `[id]` là dynamic segment.
- `params.id` chính là giá trị URL.

---

#### Phần 5 - Query String và Search Params

Ví dụ URL: `/products?q=iphone&sort=price_asc`

Đọc query trong Client Component:

```tsx
"use client";
import { useSearchParams } from "next/navigation";

export default function SearchInfo() {
  const searchParams = useSearchParams();
  const q = searchParams.get("q");
  const sort = searchParams.get("sort");

  return (
    <p>
      Tu khoa: {q} - Sap xep: {sort}
    </p>
  );
}
```

Điều hướng có query:

```tsx
"use client";
import { useRouter } from "next/navigation";

export default function SearchButton() {
  const router = useRouter();
  return (
    <button onClick={() => router.push("/products?q=phone&sort=price_desc")}>
      Tim kiem
    </button>
  );
}
```

---

#### Phần 6 - Data Fetching + Cache trong App Router

##### Cách 1: dữ liệu luôn mới

```tsx
const res = await fetch("https://dummyjson.com/products", {
  cache: "no-store",
});
```

Dùng khi:
- Dữ liệu thay đổi liên tục.
- Cần đảm bảo mỗi request lấy dữ liệu mới nhất.

##### Cách 2: revalidate theo chu kỳ

```tsx
const res = await fetch("https://dummyjson.com/products", {
  next: { revalidate: 60 },
});
```

Dùng khi:
- Muốn cân bằng giữa tốc độ và độ mới của dữ liệu.
- Chấp nhận dữ liệu cũ tối đa 60 giây.

Quy tắc dễ nhớ:
- Chưa chắc -> bắt đầu với `no-store`.
- Cần tối ưu -> đổi sang `revalidate`.

---

#### Phần 7 - loading, error, not-found

##### `loading.tsx`
- Tự động hiển thị khi route đang chờ data.

```tsx
// app/products/loading.tsx
export default function Loading() {
  return <p>Dang tai san pham...</p>;
}
```

##### `error.tsx`
- Bắt lỗi runtime trong route segment.
- Bắt buộc là Client Component.

```tsx
"use client";

export default function Error({
  error,
  reset,
}: {
  error: Error;
  reset: () => void;
}) {
  return (
    <div>
      <p>Da xay ra loi: {error.message}</p>
      <button onClick={reset}>Thu lai</button>
    </div>
  );
}
```

##### `not-found.tsx`

```tsx
// app/products/not-found.tsx
export default function NotFound() {
  return <p>Khong tim thay san pham.</p>;
}
```

Và trong `page.tsx` có thể gọi:

```tsx
import { notFound } from "next/navigation";

if (!product) notFound();
```

---

#### Phần 8 - Route Groups (tổ chức dự án dễ đẹp)

Ví dụ:

```txt
app/
  (auth)/
    login/page.tsx
    register/page.tsx
  (shop)/
    products/page.tsx
```

Giải thích:
- Tên trong ngoặc `()` không xuất hiện trên URL.
- Chỉ để chia nhóm route cho dễ quản lý.

---

#### Phần 9 - Checklist kiểm tra năng lực Giai đoạn 2

- [ ] Tạo được layout gốc và layout riêng theo module.
- [ ] Tạo route tĩnh (`/about`) và route động (`/products/[id]`).
- [ ] Hiểu rõ Server vs Client Component, không đặt sai.
- [ ] Dùng được `useRouter`, `useSearchParams`, `useParams`.
- [ ] Viết `loading.tsx`, `error.tsx`, `not-found.tsx`.
- [ ] Fetch dữ liệu và chọn cache strategy phù hợp.

---

#### Phần 10 - Bài tập thực chiến (làm xong sẽ lên tay rất nhanh)

Đề bài: Tạo mini module "Products"

Yêu cầu:
- Trang danh sách `/products`: fetch 20 sản phẩm.
- Ô tìm kiếm client side (`q` trên URL).
- Trang chi tiết `/products/[id]`.
- Có `loading.tsx` và `error.tsx` cho module products.
- Nếu id không hợp lệ thì hiện `not-found`.

Hướng dẫn làm:
1. Tạo cấu trúc route và layout.
2. Làm danh sách trước.
3. Làm detail sau.
4. Thêm loading/error/not-found.
5. Thêm query search.

Tiêu chí đạt:
- Code chạy đúng.
- Folder rõ ràng.
- TypeScript không `any`.
- Không dùng hook trong Server Component.

---

#### Phần 11 - Lỗi thường gặp và cách sửa nhanh

- Lỗi: "You're importing a component that needs useState..."
  - Nguyên nhân: quên `"use client"`.
  - Cách sửa: thêm `"use client"` vào đầu file component cần hook/event.

- Lỗi: Dữ liệu cũ, không thay đổi sau khi refresh
  - Nguyên nhân: dính cache mặc định.
  - Cách sửa: dùng `cache: "no-store"` hoặc `revalidate`.

- Lỗi: `params` undefined
  - Nguyên nhân: sai tên folder dynamic.
  - Cách sửa: đảm bảo folder là `[id]` và truy cập qua `params.id`.

- Lỗi: `error.tsx` không chạy đúng
  - Nguyên nhân: chưa đặt `"use client"`.
  - Cách sửa: thêm `"use client"` cho `error.tsx`.

---

#### Phần 12 - Mẫu ghi chú học Giai đoạn 2 (bạn điền vào)

- Hôm nay tôi học:
  - 
- Điều tôi hiểu rõ:
  - 
- Điều tôi chưa rõ:
  - 
- Lỗi tôi đã gặp:
  - 
- Cách tôi fix:
  - 
- 1 điều tôi sẽ áp dụng vào project thật:
  - 

---

## 4) GIAI ĐOẠN 3 - Redux Toolkit (RTK) trong Next.js

### 4.1 Lý thuyết cốt lõi

#### A. Các khái niệm RTK
- `configureStore`: tạo store.
- `createSlice`: tạo reducer + action.
- `useSelector`: đọc state.
- `useDispatch`: gọi action.
- `createAsyncThunk`: xử lý bất đồng bộ (gọi API).

#### B. Cấu trúc folder gợi ý
- `src/store/store.ts`
- `src/store/hooks.ts`
- `src/store/features/todo/todoSlice.ts`
- `src/providers/StoreProvider.tsx`

#### C. Tích hợp với Next.js App Router
- Đặt `StoreProvider` trong `app/layout.tsx` (thông qua client component trung gian).
- Thành phần dùng `useSelector/useDispatch` phải là Client Component.

#### D. Khi nào dùng RTK?
- Dùng RTK khi:
  - State dùng chung nhiều màn hình.
  - Logic cập nhật state phức tạp.
  - Cần theo dõi loading/error/global state.
- Không cần RTK khi:
  - State nhỏ, chỉ trong 1-2 component.
  - Dữ liệu có thể lấy trực tiếp ở Server Component.

### 4.2 Thực hành đề xuất
- Bài 1: Tạo `todoSlice` (add/remove/toggle).
- Bài 2: Viết `createAsyncThunk` để fetch todo list.
- Bài 3: Hiển thị loading, error, data trên giao diện.
- Bài 4: Tách selector và custom typed hooks.

### 4.3 Lỗi thường gặp
- Đặt reducer sai key trong store.
- Quên typed hooks (`useAppDispatch`, `useAppSelector`).
- Dispatch thunk nhưng không xử lý state loading/error.

### 4.4 Checklist đạt được
- [ ] Tạo store + slice đúng TypeScript.
- [ ] Viết và dùng `createAsyncThunk`.
- [ ] Hiểu luồng dữ liệu: UI -> action -> reducer -> UI.
- [ ] Tích hợp RTK vào Next App Router đúng cách.

### 4.5 Ghi chú cá nhân (tự điền)
- Kiến thức dễ nhớ:
  - 
- Vấn đề gặp phải:
  - 
- Cách tôi sửa:
  - 
- Ví dụ tôi tự viết:
  - 

### 4.6 Bài giảng chi tiết Giai đoạn 3 (RTK trong Next.js App Router)

#### Mục tiêu sau khi học xong Giai đoạn 3
- Bạn tự tạo được store với `configureStore`.
- Bạn viết được slice có action + reducer rõ ràng.
- Bạn viết được `createAsyncThunk` để gọi API.
- Bạn kết nối RTK vào Next.js App Router đúng vị trí.
- Bạn hiểu khi nào dùng RTK, khi nào không cần.

---

#### Phần 1 - Tư duy RTK trong dự án Next.js

- RTK dùng để quản lý global state (state dùng chung nhiều nơi).
- Không phải cái gì cũng đưa vào RTK.
- Trong App Router:
  - Dữ liệu render lần đầu có thể fetch ở Server Component.
  - State tương tác người dùng (giỏ hàng, bộ lọc, UI state,...) hợp với RTK.

Quy tắc dễ nhớ:
- Local state nhỏ -> `useState`.
- State chia sẻ phức tạp -> RTK.

---

#### Phần 2 - Cấu trúc folder gợi ý (để dễ quản lý)

```txt
src/
  store/
    store.ts
    hooks.ts
    features/
      todo/
        todoSlice.ts
  providers/
    StoreProvider.tsx
app/
  layout.tsx
```

Ý nghĩa:
- `store.ts`: tạo store tổng.
- `hooks.ts`: typed hooks để dùng an toàn với TS.
- `todoSlice.ts`: nơi viết state + reducer + action cho module todo.
- `StoreProvider.tsx`: bọc Redux Provider vào app.

---

#### Phần 3 - Tạo store đúng chuẩn TypeScript

`src/store/store.ts`

```ts
import { configureStore } from "@reduxjs/toolkit";
import todoReducer from "./features/todo/todoSlice";

export const store = configureStore({
  reducer: {
    todo: todoReducer,
  },
});

export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;
```

Giải thích:
- `todo` là key state trong store, sau này đọc bằng `state.todo`.
- `RootState`, `AppDispatch` là type cần thiết cho typed hooks.

---

#### Phần 4 - Typed hooks (rất nên dùng)

`src/store/hooks.ts`

```ts
import { TypedUseSelectorHook, useDispatch, useSelector } from "react-redux";
import type { AppDispatch, RootState } from "./store";

export const useAppDispatch: () => AppDispatch = useDispatch;
export const useAppSelector: TypedUseSelectorHook<RootState> = useSelector;
```

Vì sao cần:
- Tránh phải ép kiểu thủ công mỗi lần dùng.
- TypeScript gợi ý đúng, bắt lỗi sớm.

---

#### Phần 5 - Tạo slice (state + reducer + action)

`src/store/features/todo/todoSlice.ts`

```ts
import { createSlice, PayloadAction } from "@reduxjs/toolkit";

type Todo = {
  id: number;
  title: string;
  done: boolean;
};

type TodoState = {
  items: Todo[];
};

const initialState: TodoState = {
  items: [],
};

const todoSlice = createSlice({
  name: "todo",
  initialState,
  reducers: {
    addTodo: (state, action: PayloadAction<string>) => {
      state.items.push({
        id: Date.now(),
        title: action.payload,
        done: false,
      });
    },
    toggleTodo: (state, action: PayloadAction<number>) => {
      const todo = state.items.find((t) => t.id === action.payload);
      if (todo) todo.done = !todo.done;
    },
    removeTodo: (state, action: PayloadAction<number>) => {
      state.items = state.items.filter((t) => t.id !== action.payload);
    },
  },
});

export const { addTodo, toggleTodo, removeTodo } = todoSlice.actions;
export default todoSlice.reducer;
```

Giải thích:
- Reducer trong RTK cho phép "viết giống như mutate" nhưng bên trong đã immutable.
- `PayloadAction<T>` giúp action payload có type rõ ràng.

---

#### Phần 6 - Async với createAsyncThunk

`src/store/features/todo/todoSlice.ts` (bản nâng cao)

```ts
import { createAsyncThunk, createSlice } from "@reduxjs/toolkit";

type Todo = { id: number; title: string; done?: boolean; completed?: boolean };
type TodoState = {
  items: Todo[];
  loading: boolean;
  error: string | null;
};

export const fetchTodos = createAsyncThunk("todo/fetchTodos", async () => {
  const res = await fetch("https://jsonplaceholder.typicode.com/todos?_limit=10");
  if (!res.ok) throw new Error("Khong tai duoc todos");
  return (await res.json()) as Todo[];
});

const initialState: TodoState = {
  items: [],
  loading: false,
  error: null,
};

const todoSlice = createSlice({
  name: "todo",
  initialState,
  reducers: {},
  extraReducers: (builder) => {
    builder
      .addCase(fetchTodos.pending, (state) => {
        state.loading = true;
        state.error = null;
      })
      .addCase(fetchTodos.fulfilled, (state, action) => {
        state.loading = false;
        state.items = action.payload.map((t) => ({
          id: t.id,
          title: t.title,
          done: t.done ?? t.completed ?? false,
        }));
      })
      .addCase(fetchTodos.rejected, (state, action) => {
        state.loading = false;
        state.error = action.error.message ?? "Loi khong xac dinh";
      });
  },
});

export default todoSlice.reducer;
```

---

#### Phần 7 - Tích hợp Redux Provider vào Next.js App Router

`src/providers/StoreProvider.tsx`

```tsx
"use client";

import { Provider } from "react-redux";
import { store } from "@/src/store/store";

export default function StoreProvider({
  children,
}: {
  children: React.ReactNode;
}) {
  return <Provider store={store}>{children}</Provider>;
}
```

`app/layout.tsx`

```tsx
import StoreProvider from "@/src/providers/StoreProvider";

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="vi">
      <body>
        <StoreProvider>{children}</StoreProvider>
      </body>
    </html>
  );
}
```

Lưu ý:
- `Provider` phải đặt trong Client Component (`StoreProvider.tsx`).
- `layout.tsx` có thể là Server Component, nhưng vẫn wrap được client provider.

---

#### Phần 8 - Sử dụng state/action trong component

```tsx
"use client";

import { useEffect, useState } from "react";
import { addTodo, fetchTodos, removeTodo, toggleTodo } from "@/src/store/features/todo/todoSlice";
import { useAppDispatch, useAppSelector } from "@/src/store/hooks";

export default function TodoPage() {
  const dispatch = useAppDispatch();
  const { items, loading, error } = useAppSelector((state) => state.todo);
  const [title, setTitle] = useState("");

  useEffect(() => {
    dispatch(fetchTodos());
  }, [dispatch]);

  return (
    <div>
      <h1>Todo voi RTK</h1>

      <input value={title} onChange={(e) => setTitle(e.target.value)} />
      <button
        onClick={() => {
          if (!title.trim()) return;
          dispatch(addTodo(title.trim()));
          setTitle("");
        }}
      >
        Them
      </button>

      {loading && <p>Dang tai du lieu...</p>}
      {error && <p>Loi: {error}</p>}

      <ul>
        {items.map((t) => (
          <li key={t.id}>
            <span>{t.done ? "✅" : "⬜"} {t.title}</span>
            <button onClick={() => dispatch(toggleTodo(t.id))}>Toggle</button>
            <button onClick={() => dispatch(removeTodo(t.id))}>Xoa</button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

---

#### Phần 9 - Selector để tránh lặp code

Trong `todoSlice.ts` hoặc file selectors riêng:

```ts
import type { RootState } from "@/src/store/store";

export const selectTodos = (state: RootState) => state.todo.items;
export const selectTodoLoading = (state: RootState) => state.todo.loading;
export const selectTodoError = (state: RootState) => state.todo.error;
export const selectDoneCount = (state: RootState) =>
  state.todo.items.filter((t) => t.done).length;
```

Lợi ích:
- Dễ test.
- Dễ tái sử dụng.
- Component gọn hơn.

---

#### Phần 10 - Khi nào dùng RTK, khi nào không?

Dùng RTK khi:
- Dữ liệu dùng chung nhiều route/component.
- Có nhiều thao tác cập nhật state phức tạp.
- Cần quản lý loading/error nhất quán.

Không cần RTK khi:
- Form nhỏ trong 1 component.
- State UI nhỏ (modal open/close).
- Data có thể fetch trực tiếp ở Server Component mà không cần global.

---

#### Phần 11 - Lỗi thường gặp và cách sửa

- Lỗi: `could not find react-redux context value`
  - Nguyên nhân: chưa wrap Provider.
  - Cách sửa: thêm `StoreProvider` vào `app/layout.tsx`.

- Lỗi: `Property 'todo' does not exist on type RootState`
  - Nguyên nhân: key reducer không trùng.
  - Cách sửa: kiểm tra `reducer: { todo: todoReducer }`.

- Lỗi: Dispatch bị sai type
  - Nguyên nhân: dùng `useDispatch` thường.
  - Cách sửa: dùng `useAppDispatch`.

- Lỗi: UI không cập nhật
  - Nguyên nhân: sửa state sai cách ngoài reducer RTK.
  - Cách sửa: chỉ cập nhật state thông qua action/reducer.

---

#### Phần 12 - Checklist đạt chuẩn Giai đoạn 3

- [ ] Tạo store + hooks typed đúng TypeScript.
- [ ] Tạo slice với `createSlice`.
- [ ] Tạo async logic với `createAsyncThunk`.
- [ ] Hiển thị loading/error/data đầy đủ.
- [ ] Tích hợp Redux Provider đúng vị trí trong App Router.
- [ ] Có selector cho các dữ liệu dùng nhiều lần.

---

#### Phần 13 - Bài tập thực chiến Giai đoạn 3

Đề bài: Làm module "Cart + Wishlist" với RTK

Yêu cầu:
- `cartSlice`: add/remove/change quantity.
- `wishlistSlice`: add/remove item yêu thích.
- Hiển thị tổng số lượng cart ở header (global state).
- Lưu cart vào `localStorage` (có thể dùng `useEffect` trong client component để đồng bộ).

Nâng cao:
- Tạo `createAsyncThunk` fetch danh sách sản phẩm.
- Khi add cart thì kiểm tra đã tồn tại -> tăng quantity.

Tiêu chí đạt:
- Không dùng `any`.
- Action/reducer đặt tên rõ ràng.
- TypeScript bắt lỗi sớm.

---

#### Phần 14 - Mẫu ghi chú học Giai đoạn 3 (bạn điền vào)

- Hôm nay tôi học:
  - 
- Điều tôi hiểu rõ:
  - 
- Điều tôi chưa rõ:
  - 
- Lỗi tôi đã gặp:
  - 
- Cách tôi fix:
  - 
- 1 điều tôi sẽ áp dụng vào project thật:
  - 

### 4.7 RTK nâng cao - Middleware + RTK Query (chi tiết để đi project thật)

#### A. Middleware là gì? dùng để làm gì?

- Middleware là lớp nằm giữa `dispatch(action)` và `reducer`.
- Dùng để:
  - log action/state,
  - xử lý async flow,
  - thêm header/token,
  - xử lý lỗi chung.

Sơ đồ:

```txt
UI -> dispatch(action) -> middleware -> reducer -> store update -> UI re-render
```

---

#### B. Cách thêm middleware vào store

`src/store/store.ts`

```ts
import { configureStore, Middleware } from "@reduxjs/toolkit";
import todoReducer from "./features/todo/todoSlice";

const loggerMiddleware: Middleware = (api) => (next) => (action) => {
  // Log don gian de debug
  console.log("Dispatch:", action.type);
  const result = next(action);
  console.log("State sau dispatch:", api.getState());
  return result;
};

export const store = configureStore({
  reducer: {
    todo: todoReducer,
  },
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware().concat(loggerMiddleware),
});

export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;
```

Lưu ý:
- Luôn bắt đầu từ `getDefaultMiddleware()`.
- Sau đó mới `concat(...)` middleware custom.
- Không nên bỏ middleware mặc định nếu không có lý do đặc biệt.

---

#### C. Ví dụ middleware auth token (tư duy)

Mục tiêu:
- Mỗi request API đều có token nếu đã đăng nhập.

Có 2 hướng:
- Hướng 1: middleware can thiệp action async.
- Hướng 2 (dễ hơn, dễ bảo trì hơn): xử lý token ngay trong `baseQuery` của RTK Query.

Thực tế dự án thường dùng hướng 2.

---

#### D. RTK Query là gì?

- RTK Query là công cụ trong Redux Toolkit để fetch/caching data.
- Giảm rất nhiều code thủ công so với `createAsyncThunk`.
- Tự động có:
  - loading/error/data state
  - caching
  - refetch
  - invalidation bằng tags

Khi nào nên dùng:
- App có nhiều màn hình gọi API.
- Cần cache và đồng bộ dữ liệu giữa nhiều component.

---

#### E. Các thành phần cốt lõi của RTK Query

- `createApi`: tạo API service.
- `baseQuery`: cấu hình request chung.
- `endpoints`: định nghĩa query/mutation.
- `tagTypes` + `providesTags` + `invalidatesTags`: cache invalidation.

---

#### F. Tạo API service với `createApi`

`src/store/services/productApi.ts`

```ts
import { createApi, fetchBaseQuery } from "@reduxjs/toolkit/query/react";

type Product = {
  id: number;
  title: string;
  price: number;
};

type ProductListResponse = {
  products: Product[];
};

export const productApi = createApi({
  reducerPath: "productApi",
  baseQuery: fetchBaseQuery({
    baseUrl: "https://dummyjson.com",
    prepareHeaders: (headers) => {
      // Vi du: gan token neu co
      // const token = localStorage.getItem("token"); // chi dung client
      // if (token) headers.set("Authorization", `Bearer ${token}`);
      headers.set("Content-Type", "application/json");
      return headers;
    },
  }),
  tagTypes: ["Product"],
  endpoints: (builder) => ({
    getProducts: builder.query<ProductListResponse, string | void>({
      query: (q) => (q ? `/products/search?q=${q}` : "/products"),
      providesTags: ["Product"],
    }),
    getProductById: builder.query<Product, number>({
      query: (id) => `/products/${id}`,
      providesTags: (_result, _error, id) => [{ type: "Product", id }],
    }),
    createProduct: builder.mutation<Product, Partial<Product>>({
      query: (body) => ({
        url: "/products/add",
        method: "POST",
        body,
      }),
      invalidatesTags: ["Product"],
    }),
  }),
});

export const {
  useGetProductsQuery,
  useGetProductByIdQuery,
  useCreateProductMutation,
} = productApi;
```

---

#### G. Kết nối RTK Query vào store

`src/store/store.ts`

```ts
import { configureStore } from "@reduxjs/toolkit";
import todoReducer from "./features/todo/todoSlice";
import { productApi } from "./services/productApi";

export const store = configureStore({
  reducer: {
    todo: todoReducer,
    [productApi.reducerPath]: productApi.reducer,
  },
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware().concat(productApi.middleware),
});

export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;
```

Lưu ý quan trọng:
- RTK Query bắt buộc thêm cả:
  - `productApi.reducer`
  - `productApi.middleware`

---

#### H. Sử dụng hooks của RTK Query trong component

```tsx
"use client";

import { useState } from "react";
import { useCreateProductMutation, useGetProductsQuery } from "@/src/store/services/productApi";

export default function ProductList() {
  const [q, setQ] = useState("");
  const { data, isLoading, isError, error, refetch } = useGetProductsQuery(q || undefined);
  const [createProduct, { isLoading: isCreating }] = useCreateProductMutation();

  if (isLoading) return <p>Dang tai san pham...</p>;
  if (isError) return <p>Loi: {JSON.stringify(error)}</p>;

  return (
    <div>
      <input value={q} onChange={(e) => setQ(e.target.value)} placeholder="Tim san pham..." />
      <button onClick={() => refetch()}>Refetch</button>

      <button
        disabled={isCreating}
        onClick={() =>
          createProduct({
            title: "San pham moi",
            price: 100,
          })
        }
      >
        Them san pham
      </button>

      <ul>
        {data?.products?.slice(0, 10).map((p) => (
          <li key={p.id}>
            {p.title} - ${p.price}
          </li>
        ))}
      </ul>
    </div>
  );
}
```

---

#### I. So sánh nhanh: `createAsyncThunk` vs `RTK Query`

- `createAsyncThunk`
  - Linh hoạt cao.
  - Phải tự viết loading/error/data reducers.
  - Hợp với logic async phức tạp, không chỉ fetch CRUD.

- `RTK Query`
  - Cực nhanh cho fetch API CRUD.
  - Tự có hooks + cache + invalidation.
  - Giảm nhiều code lặp lại.

Gợi ý:
- App hiện đại, nhiều API: ưu tiên RTK Query cho server state.
- Client state thường trực (theme, ui, wizard step): để ở slice thường.

---

#### J. Checklist đạt chuẩn RTK nâng cao

- [ ] Biết middleware nằm ở đâu trong data flow Redux.
- [ ] Thêm middleware custom đúng cách với `getDefaultMiddleware`.
- [ ] Tạo được 1 `createApi` service.
- [ ] Kết nối được `api.reducer` + `api.middleware`.
- [ ] Dùng được query hook + mutation hook.
- [ ] Hiểu `providesTags/invalidatesTags` để refetch đúng lúc.

---

#### K. Bài tập thực chiến (làm để nhớ rất lâu)

Đề bài:
- Tạo `postApi` với các endpoint:
  - `getPosts`
  - `getPostById`
  - `createPost`
  - `updatePost`
  - `deletePost`
- Có tag invalidation để sau khi tạo/sửa/xoá thì danh sách tự refresh.
- Tạo 1 trang hiển thị danh sách + form tạo post.

Mục tiêu:
- Vừa hiểu RTK Query,
- vừa biết cách tổ chức store sạch trong Next.js.

---

## 5) GIAI ĐOẠN 4 - Project thực chiến (CRUD)

### 5.1 Mục tiêu Giai đoạn 4

Sau khi học xong giai đoạn này, bạn có thể:
- Tự tạo 1 project Next.js App Router hoàn chỉnh từ đầu.
- Biết tổ chức folder, tách component, viết logic sạch.
- Kết hợp Server Component + Client Component + RTK đúng chỗ.
- Tự tin làm được project tương tự trong thực tế.

---

### 5.2 Đề bài: App Quản lý công việc (Task Manager)

Chức năng:
- Xem danh sách task.
- Thêm task mới.
- Sửa task.
- Xoá task.
- Lọc task theo trạng thái: Tất cả / Đang làm / Đã xong.
- Tìm kiếm task theo tên.
- Hiển thị loading khi đang tải dữ liệu.
- Hiển thị lỗi khi có vấn đề.

---

### 5.3 Cấu trúc folder gợi ý (để dễ bảo trì)

```txt
app/
  layout.tsx
  page.tsx
  tasks/
    page.tsx
    [id]/
      page.tsx
    loading.tsx
    error.tsx
    not-found.tsx
src/
  components/
    TaskForm.tsx
    TaskItem.tsx
    TaskList.tsx
    TaskFilter.tsx
    SearchBox.tsx
  store/
    store.ts
    hooks.ts
    features/
      task/
        taskSlice.ts
    services/
      taskApi.ts
  providers/
    StoreProvider.tsx
  types/
    task.ts
```

Giải thích:
- `app/` chứa route và layout.
- `src/components/` chứa các component tái sử dụng.
- `src/store/` chứa toàn bộ logic RTK.
- `src/types/` chứa type chung.

---

### 5.4 Bước 1 - Định nghĩa type chung

`src/types/task.ts`

```ts
export type Task = {
  id: number;
  title: string;
  description: string;
  status: "todo" | "in_progress" | "done";
  createdAt: string;
};
```

---

### 5.5 Bước 2 - Tạo RTK Query API service

`src/store/services/taskApi.ts`

```ts
import { createApi, fetchBaseQuery } from "@reduxjs/toolkit/query/react";
import type { Task } from "@/src/types/task";

export const taskApi = createApi({
  reducerPath: "taskApi",
  baseQuery: fetchBaseQuery({ baseUrl: "https://jsonplaceholder.typicode.com" }),
  tagTypes: ["Task"],
  endpoints: (builder) => ({
    getTasks: builder.query<Task[], void>({
      query: () => "/todos?_limit=20",
      transformResponse: (res: { id: number; title: string; completed: boolean }[]) =>
        res.map((t) => ({
          id: t.id,
          title: t.title,
          description: "",
          status: t.completed ? ("done" as const) : ("todo" as const),
          createdAt: new Date().toISOString(),
        })),
      providesTags: ["Task"],
    }),
    createTask: builder.mutation<Task, Pick<Task, "title" | "description">>({
      query: (body) => ({
        url: "/todos",
        method: "POST",
        body: { ...body, completed: false },
      }),
      invalidatesTags: ["Task"],
    }),
    updateTask: builder.mutation<Task, Partial<Task> & Pick<Task, "id">>({
      query: ({ id, ...patch }) => ({
        url: `/todos/${id}`,
        method: "PATCH",
        body: patch,
      }),
      invalidatesTags: ["Task"],
    }),
    deleteTask: builder.mutation<void, number>({
      query: (id) => ({
        url: `/todos/${id}`,
        method: "DELETE",
      }),
      invalidatesTags: ["Task"],
    }),
  }),
});

export const {
  useGetTasksQuery,
  useCreateTaskMutation,
  useUpdateTaskMutation,
  useDeleteTaskMutation,
} = taskApi;
```

---

### 5.6 Bước 3 - Tạo UI state slice (lọc, tìm kiếm)

`src/store/features/task/taskSlice.ts`

```ts
import { createSlice, PayloadAction } from "@reduxjs/toolkit";

type TaskFilter = {
  keyword: string;
  status: "all" | "todo" | "in_progress" | "done";
};

const initialState: TaskFilter = {
  keyword: "",
  status: "all",
};

const taskFilterSlice = createSlice({
  name: "taskFilter",
  initialState,
  reducers: {
    setKeyword: (state, action: PayloadAction<string>) => {
      state.keyword = action.payload;
    },
    setStatusFilter: (state, action: PayloadAction<TaskFilter["status"]>) => {
      state.status = action.payload;
    },
    resetFilter: () => initialState,
  },
});

export const { setKeyword, setStatusFilter, resetFilter } = taskFilterSlice.actions;
export default taskFilterSlice.reducer;
```

---

### 5.7 Bước 4 - Cấu hình store

`src/store/store.ts`

```ts
import { configureStore } from "@reduxjs/toolkit";
import taskFilterReducer from "./features/task/taskSlice";
import { taskApi } from "./services/taskApi";

export const store = configureStore({
  reducer: {
    taskFilter: taskFilterReducer,
    [taskApi.reducerPath]: taskApi.reducer,
  },
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware().concat(taskApi.middleware),
});

export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;
```

`src/store/hooks.ts`

```ts
import { TypedUseSelectorHook, useDispatch, useSelector } from "react-redux";
import type { AppDispatch, RootState } from "./store";

export const useAppDispatch: () => AppDispatch = useDispatch;
export const useAppSelector: TypedUseSelectorHook<RootState> = useSelector;
```

---

### 5.8 Bước 5 - StoreProvider

`src/providers/StoreProvider.tsx`

```tsx
"use client";

import { Provider } from "react-redux";
import { store } from "@/src/store/store";

export default function StoreProvider({ children }: { children: React.ReactNode }) {
  return <Provider store={store}>{children}</Provider>;
}
```

---

### 5.9 Bước 6 - Layout gốc

`app/layout.tsx`

```tsx
import StoreProvider from "@/src/providers/StoreProvider";

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="vi">
      <body>
        <StoreProvider>
          <header style={{ padding: 16, borderBottom: "1px solid #ddd" }}>
            <h1>Task Manager</h1>
          </header>
          <main style={{ padding: 16 }}>{children}</main>
        </StoreProvider>
      </body>
    </html>
  );
}
```

---

### 5.10 Bước 7 - Các component

#### `src/components/SearchBox.tsx`

```tsx
"use client";

import { setKeyword } from "@/src/store/features/task/taskSlice";
import { useAppDispatch, useAppSelector } from "@/src/store/hooks";

export default function SearchBox() {
  const dispatch = useAppDispatch();
  const keyword = useAppSelector((s) => s.taskFilter.keyword);

  return (
    <input
      placeholder="Tim kiem task..."
      value={keyword}
      onChange={(e) => dispatch(setKeyword(e.target.value))}
    />
  );
}
```

#### `src/components/TaskFilter.tsx`

```tsx
"use client";

import { setStatusFilter } from "@/src/store/features/task/taskSlice";
import { useAppDispatch, useAppSelector } from "@/src/store/hooks";

const options = [
  { value: "all", label: "Tat ca" },
  { value: "todo", label: "Chua lam" },
  { value: "in_progress", label: "Dang lam" },
  { value: "done", label: "Da xong" },
] as const;

export default function TaskFilter() {
  const dispatch = useAppDispatch();
  const current = useAppSelector((s) => s.taskFilter.status);

  return (
    <div>
      {options.map((o) => (
        <button
          key={o.value}
          onClick={() => dispatch(setStatusFilter(o.value))}
          style={{ fontWeight: current === o.value ? "bold" : "normal", marginRight: 8 }}
        >
          {o.label}
        </button>
      ))}
    </div>
  );
}
```

#### `src/components/TaskForm.tsx`

```tsx
"use client";

import { useState } from "react";
import { useCreateTaskMutation } from "@/src/store/services/taskApi";

export default function TaskForm() {
  const [title, setTitle] = useState("");
  const [desc, setDesc] = useState("");
  const [createTask, { isLoading }] = useCreateTaskMutation();

  const handleSubmit = async (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    if (!title.trim()) return;
    await createTask({ title: title.trim(), description: desc.trim() });
    setTitle("");
    setDesc("");
  };

  return (
    <form onSubmit={handleSubmit} style={{ marginBottom: 16 }}>
      <input
        placeholder="Ten task..."
        value={title}
        onChange={(e) => setTitle(e.target.value)}
        required
      />
      <input
        placeholder="Mo ta..."
        value={desc}
        onChange={(e) => setDesc(e.target.value)}
      />
      <button disabled={isLoading}>{isLoading ? "Dang them..." : "Them task"}</button>
    </form>
  );
}
```

#### `src/components/TaskItem.tsx`

```tsx
"use client";

import type { Task } from "@/src/types/task";
import { useDeleteTaskMutation, useUpdateTaskMutation } from "@/src/store/services/taskApi";

type TaskItemProps = {
  task: Task;
};

export default function TaskItem({ task }: TaskItemProps) {
  const [deleteTask] = useDeleteTaskMutation();
  const [updateTask] = useUpdateTaskMutation();

  const toggleDone = () => {
    updateTask({
      id: task.id,
      status: task.status === "done" ? "todo" : "done",
    });
  };

  return (
    <li style={{ display: "flex", gap: 8, alignItems: "center", marginBottom: 8 }}>
      <button onClick={toggleDone}>{task.status === "done" ? "Done" : "Todo"}</button>
      <span style={{ textDecoration: task.status === "done" ? "line-through" : "none" }}>
        {task.title}
      </span>
      <button onClick={() => deleteTask(task.id)}>Xoa</button>
    </li>
  );
}
```

#### `src/components/TaskList.tsx`

```tsx
"use client";

import { useGetTasksQuery } from "@/src/store/services/taskApi";
import { useAppSelector } from "@/src/store/hooks";
import TaskItem from "./TaskItem";

export default function TaskList() {
  const { data: tasks, isLoading, isError } = useGetTasksQuery();
  const { keyword, status } = useAppSelector((s) => s.taskFilter);

  if (isLoading) return <p>Dang tai danh sach...</p>;
  if (isError) return <p>Loi khi tai du lieu!</p>;
  if (!tasks) return null;

  const filtered = tasks.filter((t) => {
    const matchKeyword = t.title.toLowerCase().includes(keyword.toLowerCase());
    const matchStatus = status === "all" || t.status === status;
    return matchKeyword && matchStatus;
  });

  return (
    <ul style={{ listStyle: "none", padding: 0 }}>
      {filtered.length === 0 && <p>Khong co task nao.</p>}
      {filtered.map((t) => (
        <TaskItem key={t.id} task={t} />
      ))}
    </ul>
  );
}
```

---

### 5.11 Bước 8 - Trang tasks

`app/tasks/page.tsx`

```tsx
import SearchBox from "@/src/components/SearchBox";
import TaskFilter from "@/src/components/TaskFilter";
import TaskForm from "@/src/components/TaskForm";
import TaskList from "@/src/components/TaskList";

export default function TasksPage() {
  return (
    <div>
      <h2>Quan ly cong viec</h2>
      <TaskForm />
      <SearchBox />
      <TaskFilter />
      <TaskList />
    </div>
  );
}
```

`app/tasks/loading.tsx`

```tsx
export default function Loading() {
  return <p>Dang tai trang tasks...</p>;
}
```

`app/tasks/error.tsx`

```tsx
"use client";

export default function Error({ error, reset }: { error: Error; reset: () => void }) {
  return (
    <div>
      <p>Loi: {error.message}</p>
      <button onClick={reset}>Thu lai</button>
    </div>
  );
}
```

---

### 5.12 Sơ đồ luồng dữ liệu toàn app

```txt
User click nut "Them task"
  -> TaskForm dispatch createTask mutation
    -> RTK Query goi API POST /todos
      -> API tra ve ket qua
        -> invalidatesTags: ["Task"]
          -> RTK Query tu dong refetch getTasks
            -> TaskList nhan data moi
              -> UI tu cap nhat
```

```txt
User go o tim kiem
  -> SearchBox dispatch setKeyword
    -> taskFilter state cap nhat
      -> TaskList doc filter tu useAppSelector
        -> Loc lai danh sach hien thi
```

---

### 5.13 Tư duy thiết kế quan trọng

#### Khi nào dùng Server Component?
- Trang tasks/page.tsx có thể là Server Component (không có hook, chỉ render component con).

#### Khi nào dùng Client Component?
- TaskForm, TaskList, TaskItem, SearchBox, TaskFilter
  (vì có hook, event, state).

#### Khi nào dùng RTK?
- Filter (keyword, status) dùng chung nhiều component -> RTK slice.
- Data tasks từ API -> RTK Query (cache + refetch).

#### Khi nào KHÔNG cần RTK?
- Input tạm trong form (title, desc) -> `useState` local là đủ.

---

### 5.14 Lỗi thường gặp trong project thật

- Lỗi: Component client import trong server component -> warning hydration
  - Cách sửa: đảm bảo component client có `"use client"`.

- Lỗi: Mutation không refetch list
  - Cách sửa: kiểm tra `invalidatesTags` trùng với `providesTags`.

- Lỗi: Filter không hoạt động
  - Cách sửa: kiểm tra selector đọc đúng key (`state.taskFilter`).

- Lỗi: TypeScript báo lỗi `any` nhiều
  - Cách sửa: định nghĩa type rõ ràng ở `src/types/`.

---

### 5.15 Checklist hoàn thành project

- [ ] Cấu trúc folder sạch, dễ hiểu.
- [ ] Có type riêng trong `src/types/`.
- [ ] RTK Query CRUD đầy đủ.
- [ ] Filter (keyword + status) hoạt động.
- [ ] loading.tsx + error.tsx có cho route tasks.
- [ ] Không dùng `any`.
- [ ] Component tách nhỏ, tái sử dụng được.
- [ ] Code chạy ổn định, không lỗi console.

---

### 5.16 Nâng cao (làm thêm nếu muốn giỏi hơn)

- Thêm phân trang (pagination).
- Thêm confirm dialog trước khi xoá.
- Thêm toast thông báo thành công/lỗi.
- Thêm dark mode bằng `useContext` hoặc CSS variable.
- Deploy lên Vercel.

---

### 5.17 Ghi chú cá nhân (tự điền)

- Chức năng tôi đã xong:
  - 
- Chức năng còn thiếu:
  - 
- Bug đã gặp:
  - 
- Cách tôi fix:
  - 
- Điều tôi học được nhiều nhất từ project này:
  - 

---

## 5B) BỔ SUNG - Cắt giao diện, Hooks nâng cao, Next.js tiện ích

### 5B.1 Tư duy cắt giao diện từ design (Figma -> Code)

#### Bước 1: Nhìn tổng thể -> chia vùng lớn

Ví dụ nhìn 1 trang web:

```txt
+------------------------------------------+
| Header (logo, menu, avatar)              |
+------------------------------------------+
| Sidebar        | Main Content            |
|  - Nav item 1  |  +--Card--+ +--Card--+  |
|  - Nav item 2  |  |        | |        |  |
|  - Nav item 3  |  +--------+ +--------+  |
+------------------------------------------+
| Footer                                   |
+------------------------------------------+
```

Chia thành component:
- `Header`
- `Sidebar` (chứa `NavItem`)
- `MainContent` (chứa `Card`)
- `Footer`

#### Bước 2: Mỗi vùng lớn -> tách component con

Ví dụ `Header`:

```txt
Header
  -> Logo
  -> NavMenu (chua NavLink)
  -> UserAvatar
```

#### Bước 3: Xác định props cho mỗi component

```tsx
type CardProps = {
  title: string;
  image: string;
  price: number;
  onAddToCart: () => void;
};
```

#### Bước 4: Style rồi ghép lại

Quy tắc vàng:
- Component nào lặp lại -> tách riêng.
- Component nào có state riêng -> Client Component.
- Component nào chỉ hiển thị -> có thể là Server Component.

---

### 5B.2 CSS trong Next.js (3 cách chính)

#### Cách 1: CSS Modules (mặc định, không cần cài thêm)

Tạo file: `TaskItem.module.css`

```css
.item {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 8px;
  border-bottom: 1px solid #eee;
}

.done {
  text-decoration: line-through;
  color: #999;
}
```

Dùng trong component:

```tsx
import styles from "./TaskItem.module.css";

export default function TaskItem({ title, isDone }: { title: string; isDone: boolean }) {
  return (
    <li className={styles.item}>
      <span className={isDone ? styles.done : ""}>{title}</span>
    </li>
  );
}
```

Lợi ích:
- Class tự động không trùng tên (scoped).
- Không ảnh hưởng component khác.

#### Cách 2: Tailwind CSS (phổ biến nhất hiện nay)

Cài đặt: khi tạo project Next.js chọn "Yes" cho Tailwind.

Ví dụ:

```tsx
export default function TaskItem({ title, isDone }: { title: string; isDone: boolean }) {
  return (
    <li className="flex items-center gap-2 p-2 border-b border-gray-200">
      <span className={isDone ? "line-through text-gray-400" : ""}>{title}</span>
    </li>
  );
}
```

Lợi ích:
- Viết style nhanh, không tạo file CSS riêng.
- Responsive dễ: `md:flex-row`, `lg:text-xl`.

Ví dụ responsive:

```tsx
<div className="flex flex-col md:flex-row gap-4">
  <aside className="w-full md:w-64">Sidebar</aside>
  <main className="flex-1">Content</main>
</div>
```

#### Cách 3: Inline style (chỉ dùng khi rất đơn giản)

```tsx
<div style={{ padding: 16, backgroundColor: "#f5f5f5" }}>
  Noi dung
</div>
```

#### Nên dùng cách nào?

- Dự án mới, team thích utility -> Tailwind.
- Dự án cần CSS truyền thống, scoped -> CSS Modules.
- Tránh inline style cho layout phức tạp.

---

### 5B.3 `next/image` - Hiển thị ảnh tối ưu

#### Vì sao không dùng thẻ `<img>` thường?
- `next/image` tự động:
  - Lazy load (chỉ tải khi cuộn tới).
  - Resize theo màn hình.
  - Tối ưu dung lượng ảnh.

#### Ví dụ cơ bản

```tsx
import Image from "next/image";

export default function ProductCard() {
  return (
    <Image
      src="/images/product.jpg"
      alt="San pham"
      width={300}
      height={200}
    />
  );
}
```

#### Ví dụ ảnh từ URL ngoài

Bước 1: cấu hình `next.config.ts` (hoặc `next.config.js`):

```ts
const nextConfig = {
  images: {
    remotePatterns: [
      { protocol: "https", hostname: "cdn.dummyjson.com" },
    ],
  },
};
export default nextConfig;
```

Bước 2: dùng trong component:

```tsx
<Image
  src="https://cdn.dummyjson.com/products/images/1/thumbnail.jpg"
  alt="Anh san pham"
  width={200}
  height={200}
/>
```

#### Ví dụ ảnh full width (responsive)

```tsx
<Image
  src="/images/banner.jpg"
  alt="Banner"
  fill
  style={{ objectFit: "cover" }}
/>
```

Lưu ý: container cha phải có `position: relative` và kích thước rõ ràng.

---

### 5B.4 `next/link` - Điều hướng nhanh không reload trang

#### Ví dụ cơ bản

```tsx
import Link from "next/link";

export default function Nav() {
  return (
    <nav>
      <Link href="/">Trang chu</Link>
      <Link href="/tasks">Cong viec</Link>
      <Link href="/products/5">San pham #5</Link>
    </nav>
  );
}
```

#### So với thẻ `<a>`?
- `<a>` reload toàn trang.
- `<Link>` chỉ đổi phần nội dung, giữ nguyên layout -> nhanh hơn nhiều.

#### Ví dụ với query string

```tsx
<Link href={{ pathname: "/tasks", query: { status: "done" } }}>
  Xem task da xong
</Link>
```

---

### 5B.5 `next/font` - Font chữ tối ưu

#### Ví dụ Google Font

```tsx
// app/layout.tsx
import { Inter } from "next/font/google";

const inter = Inter({ subsets: ["latin", "vietnamese"] });

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="vi" className={inter.className}>
      <body>{children}</body>
    </html>
  );
}
```

Lợi ích:
- Font được tải về lúc build, không phụ thuộc CDN.
- Không bị nhảy layout (FOUT/FOIT).

---

### 5B.6 Metadata và SEO

#### Static metadata (khai báo trực tiếp)

```tsx
// app/page.tsx
import type { Metadata } from "next";

export const metadata: Metadata = {
  title: "Task Manager - Trang chu",
  description: "Ung dung quan ly cong viec voi Next.js",
};

export default function HomePage() {
  return <h1>Trang chu</h1>;
}
```

#### Dynamic metadata (theo dữ liệu)

```tsx
// app/tasks/[id]/page.tsx
import type { Metadata } from "next";

type Props = { params: { id: string } };

export async function generateMetadata({ params }: Props): Promise<Metadata> {
  return {
    title: `Task #${params.id}`,
    description: `Chi tiet cong viec so ${params.id}`,
  };
}

export default function TaskDetailPage({ params }: Props) {
  return <h1>Task #{params.id}</h1>;
}
```

Lưu ý:
- `metadata` và `generateMetadata` chỉ dùng trong Server Component.
- Mỗi page nên có title + description riêng.

---

### 5B.7 Hook `useReducer` (quản lý state phức tạp)

#### Khi nào dùng?
- State có nhiều field liên quan nhau.
- Có nhiều loại action khác nhau.
- Logic cập nhật phức tạp hơn `useState`.

#### Ví dụ: Form nhiều bước (wizard)

```tsx
"use client";
import { useReducer } from "react";

type State = {
  step: number;
  name: string;
  email: string;
};

type Action =
  | { type: "NEXT_STEP" }
  | { type: "PREV_STEP" }
  | { type: "SET_NAME"; payload: string }
  | { type: "SET_EMAIL"; payload: string }
  | { type: "RESET" };

const initialState: State = { step: 1, name: "", email: "" };

function reducer(state: State, action: Action): State {
  switch (action.type) {
    case "NEXT_STEP":
      return { ...state, step: state.step + 1 };
    case "PREV_STEP":
      return { ...state, step: Math.max(1, state.step - 1) };
    case "SET_NAME":
      return { ...state, name: action.payload };
    case "SET_EMAIL":
      return { ...state, email: action.payload };
    case "RESET":
      return initialState;
    default:
      return state;
  }
}

export default function WizardForm() {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <div>
      <p>Buoc: {state.step}</p>

      {state.step === 1 && (
        <input
          placeholder="Ten"
          value={state.name}
          onChange={(e) => dispatch({ type: "SET_NAME", payload: e.target.value })}
        />
      )}

      {state.step === 2 && (
        <input
          placeholder="Email"
          value={state.email}
          onChange={(e) => dispatch({ type: "SET_EMAIL", payload: e.target.value })}
        />
      )}

      {state.step === 3 && (
        <p>Xac nhan: {state.name} - {state.email}</p>
      )}

      <button onClick={() => dispatch({ type: "PREV_STEP" })}>Quay lai</button>
      <button onClick={() => dispatch({ type: "NEXT_STEP" })}>Tiep theo</button>
      <button onClick={() => dispatch({ type: "RESET" })}>Reset</button>
    </div>
  );
}
```

#### So sánh `useState` vs `useReducer`

- `useState`: state đơn giản, 1-2 giá trị.
- `useReducer`: state có nhiều field, nhiều loại cập nhật, logic phức tạp.

---

### 5B.8 Custom Hooks (tái sử dụng logic)

#### Custom hook là gì?
- Là hàm bắt đầu bằng `use...`.
- Bên trong dùng các hook khác.
- Mục đích: gom logic lại, tái sử dụng ở nhiều component.

#### Ví dụ 1: `useLocalStorage`

```ts
"use client";
import { useEffect, useState } from "react";

export function useLocalStorage<T>(key: string, defaultValue: T) {
  const [value, setValue] = useState<T>(defaultValue);

  useEffect(() => {
    const stored = localStorage.getItem(key);
    if (stored) setValue(JSON.parse(stored));
  }, [key]);

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value));
  }, [key, value]);

  return [value, setValue] as const;
}
```

Dùng:

```tsx
const [tasks, setTasks] = useLocalStorage<string[]>("tasks", []);
```

#### Ví dụ 2: `useDebounce`

```ts
"use client";
import { useEffect, useState } from "react";

export function useDebounce<T>(value: T, delay: number): T {
  const [debounced, setDebounced] = useState(value);

  useEffect(() => {
    const id = setTimeout(() => setDebounced(value), delay);
    return () => clearTimeout(id);
  }, [value, delay]);

  return debounced;
}
```

Dùng:

```tsx
const [q, setQ] = useState("");
const debouncedQ = useDebounce(q, 300);
// dung debouncedQ de goi API, tranh goi qua nhieu lan
```

#### Ví dụ 3: `useToggle`

```ts
"use client";
import { useCallback, useState } from "react";

export function useToggle(initial = false) {
  const [value, setValue] = useState(initial);
  const toggle = useCallback(() => setValue((v) => !v), []);
  return [value, toggle] as const;
}
```

Dùng:

```tsx
const [isOpen, toggleOpen] = useToggle();
// ...
<button onClick={toggleOpen}>{isOpen ? "Dong" : "Mo"}</button>
```

#### Quy tắc viết custom hook
- Tên bắt đầu bằng `use`.
- Chỉ gọi hook khác bên trong (không gọi trong if/for).
- Trả về giá trị + hàm cập nhật.
- Đặt trong folder `src/hooks/`.

---

### 5B.9 Checklist bổ sung (bạn kiểm tra)

- [ ] Biết cắt giao diện từ design thành component.
- [ ] Dùng được CSS Modules hoặc Tailwind.
- [ ] Dùng được `next/image`, `next/link`, `next/font`.
- [ ] Thêm metadata/SEO cho từng trang.
- [ ] Dùng được `useReducer` cho state phức tạp.
- [ ] Tự viết được custom hook.

---

### 5B.10 SEO chuẩn trong Next.js App Router (đầy đủ để lên production)

#### A. Tổng quan: Website chuẩn SEO cần những gì?

```txt
1. Title + Description moi trang (metadata)
2. Open Graph (anh + mo ta khi share Facebook/Zalo)
3. Semantic HTML (h1, h2, h3 dung thu tu, alt anh)
4. sitemap.xml (ban do trang cho Google bot)
5. robots.txt (chi dan bot nen/khong nen crawl gi)
6. Canonical URL (tranh trung noi dung)
7. generateStaticParams (tao trang tinh cho SEO tot nhat)
8. Structured data / JSON-LD (giup Google hieu noi dung)
9. Performance (Core Web Vitals: LCP, CLS, INP)
```

---

#### B. Open Graph + Twitter Card (chia sẻ mạng xã hội)

```tsx
// app/page.tsx
import type { Metadata } from "next";

export const metadata: Metadata = {
  title: "Task Manager",
  description: "Ung dung quan ly cong viec hieu qua",
  openGraph: {
    title: "Task Manager",
    description: "Ung dung quan ly cong viec hieu qua",
    url: "https://example.com",
    siteName: "Task Manager",
    images: [
      {
        url: "https://example.com/og-image.jpg",
        width: 1200,
        height: 630,
        alt: "Task Manager preview",
      },
    ],
    type: "website",
  },
  twitter: {
    card: "summary_large_image",
    title: "Task Manager",
    description: "Ung dung quan ly cong viec hieu qua",
    images: ["https://example.com/og-image.jpg"],
  },
};
```

Khi share link lên Facebook/Zalo sẽ hiện ảnh + mô tả đẹp.

---

#### C. Dynamic Open Graph (theo dữ liệu)

```tsx
// app/tasks/[id]/page.tsx
import type { Metadata } from "next";

type Props = { params: { id: string } };

export async function generateMetadata({ params }: Props): Promise<Metadata> {
  const res = await fetch(`https://jsonplaceholder.typicode.com/todos/${params.id}`);
  const task = await res.json();

  return {
    title: task.title,
    description: `Chi tiet cong viec: ${task.title}`,
    openGraph: {
      title: task.title,
      description: `Chi tiet cong viec: ${task.title}`,
      images: [`https://example.com/api/og?title=${encodeURIComponent(task.title)}`],
    },
  };
}
```

---

#### D. Semantic HTML (rất quan trọng cho SEO)

Quy tắc:
- Mỗi trang chỉ có 1 thẻ `<h1>`.
- Thứ tự: `h1 > h2 > h3`, không nhảy cấp.
- Ảnh luôn có `alt` mô tả.
- Dùng `<nav>`, `<main>`, `<article>`, `<section>`, `<footer>` thay vì `<div>` khi có thể.

Ví dụ đúng:

```tsx
export default function ProductPage() {
  return (
    <main>
      <article>
        <h1>Ten san pham</h1>
        <section>
          <h2>Mo ta</h2>
          <p>Noi dung mo ta san pham...</p>
        </section>
        <section>
          <h2>Danh gia</h2>
          <p>5 sao</p>
        </section>
      </article>
    </main>
  );
}
```

Ví dụ sai:

```tsx
<div>
  <h3>Ten san pham</h3>  <!-- sai: khong co h1, nhay tu h3 -->
  <div>Mo ta</div>
  <img src="anh.jpg" />   <!-- sai: thieu alt -->
</div>
```

---

#### E. sitemap.xml (bản đồ web cho Google)

`app/sitemap.ts`

```ts
import type { MetadataRoute } from "next";

export default function sitemap(): MetadataRoute.Sitemap {
  return [
    {
      url: "https://example.com",
      lastModified: new Date(),
      changeFrequency: "daily",
      priority: 1,
    },
    {
      url: "https://example.com/tasks",
      lastModified: new Date(),
      changeFrequency: "daily",
      priority: 0.8,
    },
    {
      url: "https://example.com/about",
      lastModified: new Date(),
      changeFrequency: "monthly",
      priority: 0.5,
    },
  ];
}
```

Next.js tự động tạo file `/sitemap.xml` khi deploy.

#### Sitemap động (nhiều trang từ database)

```ts
import type { MetadataRoute } from "next";

export default async function sitemap(): Promise<MetadataRoute.Sitemap> {
  const res = await fetch("https://jsonplaceholder.typicode.com/todos?_limit=50");
  const tasks: { id: number }[] = await res.json();

  const taskUrls = tasks.map((t) => ({
    url: `https://example.com/tasks/${t.id}`,
    lastModified: new Date(),
    changeFrequency: "weekly" as const,
    priority: 0.6,
  }));

  return [
    { url: "https://example.com", lastModified: new Date(), priority: 1 },
    ...taskUrls,
  ];
}
```

---

#### F. robots.txt (chỉ dẫn cho bot)

`app/robots.ts`

```ts
import type { MetadataRoute } from "next";

export default function robots(): MetadataRoute.Robots {
  return {
    rules: {
      userAgent: "*",
      allow: "/",
      disallow: ["/api/", "/admin/"],
    },
    sitemap: "https://example.com/sitemap.xml",
  };
}
```

Next.js tự động tạo `/robots.txt`.

---

#### G. Canonical URL (tránh trùng nội dung)

```tsx
export const metadata: Metadata = {
  alternates: {
    canonical: "https://example.com/tasks",
  },
};
```

Khi nào cần:
- 1 nội dung xuất hiện trên nhiều URL khác nhau.
- Ví dụ: `/tasks` và `/tasks?page=1` cùng nội dung -> cần canonical.

---

#### H. generateStaticParams (tạo trang tĩnh, SEO tốt nhất)

```tsx
// app/tasks/[id]/page.tsx
export async function generateStaticParams() {
  const res = await fetch("https://jsonplaceholder.typicode.com/todos?_limit=20");
  const tasks: { id: number }[] = await res.json();

  return tasks.map((t) => ({ id: String(t.id) }));
}

export default async function TaskDetail({ params }: { params: { id: string } }) {
  const res = await fetch(`https://jsonplaceholder.typicode.com/todos/${params.id}`);
  const task = await res.json();
  return <h1>{task.title}</h1>;
}
```

Lợi ích:
- Next.js tạo sẵn HTML lúc build.
- Google bot crawl nhanh hơn, SEO tốt hơn.
- Trang tải cực nhanh.

---

#### I. Structured Data / JSON-LD (giúp Google hiểu nội dung)

```tsx
export default function ProductPage() {
  const jsonLd = {
    "@context": "https://schema.org",
    "@type": "Product",
    name: "Ao thun nam",
    description: "Ao thun cotton 100%",
    image: "https://example.com/ao-thun.jpg",
    offers: {
      "@type": "Offer",
      price: "199000",
      priceCurrency: "VND",
      availability: "https://schema.org/InStock",
    },
  };

  return (
    <main>
      <script
        type="application/ld+json"
        dangerouslySetInnerHTML={{ __html: JSON.stringify(jsonLd) }}
      />
      <h1>Ao thun nam</h1>
      <p>Gia: 199,000 VND</p>
    </main>
  );
}
```

Khi có JSON-LD, Google có thể hiển thị rich snippet (giá, sao, hình ảnh) ngay trên trang tìm kiếm.

---

#### J. Performance / Core Web Vitals

3 chỉ số Google đánh giá:
- **LCP** (Largest Contentful Paint): nội dung chính hiển thị nhanh.
  - Dùng `next/image` để tối ưu ảnh.
  - Dùng `next/font` để tránh nhảy layout.
  - Ưu tiên Server Component cho trang đầu.

- **CLS** (Cumulative Layout Shift): trang không bị nhảy khi tải.
  - Luôn đặt `width`/`height` cho ảnh.
  - Không insert nội dung động làm dịch layout.

- **INP** (Interaction to Next Paint): tương tác phản hồi nhanh.
  - Tránh logic nặng trong event handler.
  - Dùng `useTransition` nếu cần cập nhật state lớn.

Ví dụ `useTransition`:

```tsx
"use client";
import { useState, useTransition } from "react";

export default function HeavyFilter({ items }: { items: string[] }) {
  const [q, setQ] = useState("");
  const [filtered, setFiltered] = useState(items);
  const [isPending, startTransition] = useTransition();

  const onChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    setQ(e.target.value);
    startTransition(() => {
      setFiltered(items.filter((i) => i.includes(e.target.value)));
    });
  };

  return (
    <div>
      <input value={q} onChange={onChange} />
      {isPending && <p>Dang loc...</p>}
      <ul>
        {filtered.map((i, idx) => (
          <li key={idx}>{i}</li>
        ))}
      </ul>
    </div>
  );
}
```

---

#### K. Checklist SEO chuẩn cho mỗi trang

- [ ] Có `<h1>` duy nhất, heading đúng thứ tự.
- [ ] Có title + description (metadata).
- [ ] Có Open Graph (ảnh + mô tả).
- [ ] Ảnh có `alt`, dùng `next/image`.
- [ ] Có `sitemap.xml` và `robots.txt`.
- [ ] Trang quan trọng có `generateStaticParams`.
- [ ] Có canonical URL nếu cần.
- [ ] HTML semantic (`main`, `nav`, `article`...).
- [ ] Core Web Vitals đạt mức xanh.

---

### 5B.11 Ghi chú học phần bổ sung (bạn điền vào)

- Phần tôi hiểu rõ nhất:
  - 
- Phần tôi cần ôn lại:
  - 
- Custom hook tôi đã tự viết:
  - 
- Lỗi thường gặp khi cắt giao diện:
  - 
- Điều tôi học được về SEO:
  - 

---

## 6) Mẫu nhật ký học tập hàng ngày

### Ngày: ____ / ____ / ______
- Thời gian học: 
- Mục tiêu hôm nay: 
- Phần đã học: 
- Điều hiểu rõ nhất: 
- Điều còn mơ hồ: 
- Các câu hỏi cần hỏi thêm:
  - 
- Bài tập đã làm:
  - 
- Tự đánh giá (1-10): 

---

## 7) Tài nguyên học tập gợi ý

- Next.js docs: https://nextjs.org/docs
- React docs: https://react.dev
- Redux Toolkit docs: https://redux-toolkit.js.org
- TypeScript docs: https://www.typescriptlang.org/docs

---

## 8) Hướng dẫn học với mình (nếu bạn muốn học có mentor)

- Cách học:
  1. Bạn học 1 mục trong tài liệu.
  2. Bạn gửi code bài tập cho mình review.
  3. Mình sửa logic, tối ưu, và giải thích lại.
  4. Bạn cập nhật ghi chú vào đúng phần trong tài liệu.

- Quy tắc:
  - Học đến đâu làm bài đến đó.
  - Không học vô tội vạ.
  - Luôn viết ghi chú theo mẫu để nhớ lâu.

---

## 9) Mục tiêu cuối cùng

Sau khi học xong tài liệu này, bạn có thể:
- Tự cắt được giao diện theo Figma cơ bản.
- Tự tổ chức được project Next.js App Router + TypeScript.
- Dùng RTK đúng nơi đúng lúc.
- Viết logic rõ ràng, dễ bảo trì.
