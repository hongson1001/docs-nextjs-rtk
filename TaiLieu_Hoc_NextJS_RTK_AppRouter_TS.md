# TAI LIEU HOC NEXT.JS (APP ROUTER + TYPESCRIPT) VA REDUX TOOLKIT

> Muc tieu: Hoc de tu cat giao dien, viet logic, va lam duoc project thuc te voi Next.js + RTK.

---

## 0) Cach su dung tai lieu nay

- Moi giai doan co 4 phan:
  - **Ly thuyet cot loi**
  - **Thuc hanh**
  - **Checklist dat duoc**
  - **Ghi chu ca nhan**
- Ban hoc theo thu tu tu tren xuong duoi.
- Hoan thanh checklist moi chuyen sang phan tiep theo.
- Moi ngay nen hoc 60-120 phut, thuc hanh it nhat 60% thoi gian.

---

## 1) Tong quan lo trinh

### Giai doan 1: Nen tang React + TypeScript + Hooks
- Hieu component, props, state, event.
- Nam duoc TypeScript can ban trong React.
- Su dung thanh thao cac hooks quan trong.

### Giai doan 2: Next.js App Router (TypeScript)
- Hieu cau truc `app/`, `layout.tsx`, `page.tsx`.
- Phan biet Server Component va Client Component.
- Routing, dynamic route, loading/error/not-found.
- Data fetching dung cach trong App Router.

### Giai doan 3: Redux Toolkit (RTK) trong Next.js
- Tao store, slice, action, selector.
- Xu ly API voi `createAsyncThunk`.
- Tich hop RTK vao Next.js App Router dung client boundary.

### Giai doan 4: Project thuc chien
- Lam app CRUD (vi du: Quan ly cong viec).
- Co tim kiem, loc, phan trang, loading, error.
- To chuc code sach, de bao tri.

---

## 2) GIAI DOAN 1 - React + TypeScript + Hooks

### 2.1 Ly thuyet cot loi

#### A. React can ban
- Component la ham tra ve JSX.
- Props dung de truyen du lieu tu cha -> con.
- State dung de luu trang thai ben trong component.
- Event handling: `onClick`, `onChange`, `onSubmit`.

Vi du nhanh:

```tsx
type HelloProps = { name: string };

function Hello({ name }: HelloProps) {
  return <h1>Xin chao {name}</h1>;
}
```

- `Hello` la component.
- `name` la props truyen vao.

Vi du rieng cho Props (cha -> con):

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

- `Parent` (cha) truyen du lieu cho `UserCard` (con) qua props.
- Component con khong duoc sua truc tiep props.

Vi du rieng cho State (trang thai noi bo):

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

- `count` la state noi bo cua `Counter`.
- Moi lan `setCount`, component re-render.

#### B. TypeScript trong React
- Dinh nghia kieu cho props:
  - `type ButtonProps = { label: string; onClick: () => void }`
- Typing cho event:
  - `React.ChangeEvent<HTMLInputElement>`
- Union type:
  - `status: "idle" | "loading" | "success" | "error"`

Vi du nhanh:

```tsx
type LoginFormProps = {
  status: "idle" | "loading" | "success" | "error";
};

function LoginForm({ status }: LoginFormProps) {
  return <p>Trang thai: {status}</p>;
}
```

- Ban chi duoc truyen 1 trong 4 gia tri cho `status`.
- Neu truyen gia tri khac, TypeScript bao loi ngay.

#### C. React Hooks can hoc ky
- `useState`: quan ly state local.
- `useEffect`: side effects (goi API, lang nghe su kien, dong bo du lieu).
- `useMemo`: cache ket qua tinh toan nang.
- `useCallback`: giu tham chieu ham on dinh.
- `useRef`: truy cap DOM / luu gia tri khong gay re-render.
- `useContext`: chia se state nhe trong app.

Vi du nhanh `useState`:

```tsx
"use client";
import { useState } from "react";

export default function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount((v) => v + 1)}>Count: {count}</button>;
}
```

Vi du nhanh `useEffect`:

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

Vi du nhanh `useRef` (focus input):

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

Vi du nhanh `useMemo` (tranh tinh lai khong can thiet):

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

Vi du nhanh `useCallback` (giu ham on dinh):

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

Vi du nhanh `useContext` (chia se du lieu toan vung):

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

### 2.2 Thuc hanh de xuat
- Bai 1: Tao form them task (title, description).
- Bai 2: Viet bo loc task theo keyword va status.
- Bai 3: Tinh tong so task da hoan thanh (dung `useMemo`).
- Bai 4: Focus input tu dong khi mo modal (`useRef`).

### 2.3 Loi thuong gap
- Quen them dependency vao `useEffect`.
- Dung `useMemo/useCallback` qua som, khong can thiet.
- TypeScript bi `any` qua nhieu.

### 2.4 Checklist dat duoc
- [ ] Viet component co props typed day du.
- [ ] Dung `useState` va `useEffect` dung tinh huong.
- [ ] Hieu khi nao dung `useMemo`, `useCallback`, `useRef`.
- [ ] Khong dung `any` tuy tien.

### 2.5 Ghi chu ca nhan (tu dien)
- Kien thuc de nho:
  - 
- Van de gap phai:
  - 
- Cach toi sua:
  - 
- Vi du toi tu viet:
  - 

### 2.6 Lo trinh hoc Giai doan 1 theo tung buoi

#### Buoi 1 - Component, Props, State, Event
- Muc tieu:
  - Hieu function component la gi.
  - Biet truyen props co type.
  - Biet tao state va cap nhat state.
- Bai tap:
  - Tao `TaskItem` nhan props: `title`, `done`.
  - Tao `TaskForm` gom input + nut them task.
  - Render danh sach task o component cha.
- Ghi chu nhanh:
  - Props la du lieu "di xuong", khong sua truc tiep trong component con.
  - State la du lieu noi bo, dung ham setter de cap nhat.

#### Buoi 2 - useEffect va vong doi du lieu
- Muc tieu:
  - Hieu khi nao can `useEffect`.
  - Biet dependency array anh huong the nao.
- Bai tap:
  - Luu task vao `localStorage` moi khi danh sach thay doi.
  - Tai lai task tu `localStorage` khi vao trang.
- Ghi chu nhanh:
  - `useEffect` khong dung de tinh toan UI don gian.
  - Luon xem ky dependency de tranh bug.

#### Buoi 3 - useMemo, useCallback, useRef
- Muc tieu:
  - Biet toi uu dung luc, khong lam dung.
  - Biet dung ref de focus input.
- Bai tap:
  - Dung `useMemo` tinh so task done.
  - Dung `useRef` focus input sau khi them task.
- Ghi chu nhanh:
  - Chi toi uu khi co van de hieu nang that.
  - Uu tien code de doc truoc, toi uu sau.

### 2.7 Cach dung Function Component dung cach (rat quan trong)

#### A. Mau dung co ban
- Dat ten component viet hoa chu cai dau.
- Props phai co type ro rang.
- Tra ve JSX ro rang, ngan gon.

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

Giai thich nhanh:
- `TaskItem` viet hoa dung quy tac component.
- Props duoc type ro rang bang `TaskItemProps`.
- Khong sua truc tiep props, chi goi `onToggle` de component cha xu ly.

#### B. Quy tac quan trong
- Khong goi hooks ben trong `if`, `for`, `while`.
- Khong sua props truc tiep.
- Khong dat qua nhieu logic trong 1 component lon.
- Tach thanh component nho neu JSX dai, kho doc.

#### C. Khi nao them "use client" trong Next.js App Router
- Component co su dung hook (`useState`, `useEffect`, ...).
- Component co event browser (`onClick`, `onChange`, ...).
- Component dung API chi co tren browser (`window`, `localStorage`).

Vi du:

```tsx
"use client";
import { useState } from "react";

export default function SearchBox() {
  const [q, setQ] = useState("");
  return <input value={q} onChange={(e) => setQ(e.target.value)} />;
}
```

- Vi component co `useState` + `onChange`, bat buoc them `"use client"`.

#### D. Mau sai thuong gap

```tsx
// Sai: ten component viet thuong + hooks trong if
function taskItem() {
  if (true) {
    const [count, setCount] = useState(0);
  }
  return <div>Task</div>;
}
```

#### E. Mau dung thay the

```tsx
"use client";
import { useState } from "react";

export default function TaskItem() {
  const [count, setCount] = useState(0);

  return <button onClick={() => setCount((v) => v + 1)}>{count}</button>;
}
```

### 2.8 Khu vuc ghi chu buoi hoc (dien truc tiep vao day)

#### Mau ghi chu Buoi 1
- Toi hieu:
  - 
- Toi chua hieu:
  - 
- Loi toi da gap:
  - 
- Cach toi sua:
  - 
- Doan code toi viet tot nhat hom nay:
  - 

#### Mau ghi chu Buoi 2
- Toi hieu:
  - 
- Toi chua hieu:
  - 
- Loi toi da gap:
  - 
- Cach toi sua:
  - 
- Doan code toi viet tot nhat hom nay:
  - 

#### Mau ghi chu Buoi 3
- Toi hieu:
  - 
- Toi chua hieu:
  - 
- Loi toi da gap:
  - 
- Cach toi sua:
  - 
- Doan code toi viet tot nhat hom nay:
  - 

---

## 3) GIAI DOAN 2 - Next.js App Router + TypeScript

### 3.1 Ly thuyet cot loi

#### A. Cau truc thu muc App Router
- `app/layout.tsx`: layout goc.
- `app/page.tsx`: trang home.
- `app/about/page.tsx`: route `/about`.
- `app/products/[id]/page.tsx`: dynamic route.
- `app/loading.tsx`: UI loading.
- `app/error.tsx`: UI khi co loi runtime.
- `app/not-found.tsx`: UI khi khong tim thay trang.

Vi du cau truc:

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
- Mac dinh trong `app/` la **Server Component**.
- Neu can hooks/event browser -> them `"use client"` o dau file.
- Quy tac:
  - UI co click/typing state local: Client Component.
  - Lay data server va render lan dau: uu tien Server Component.

Vi du Server Component:

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

Vi du Client Component:

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
- `usePathname()`: lay current path.
- `useSearchParams()`: doc query string.
- `useParams()`: lay dynamic params.

Vi du:

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
- Goi API trong Server Component voi `fetch`.
- Caching:
  - mac dinh co cache (tuy theo request)
  - `cache: "no-store"` cho du lieu luon moi
  - `next: { revalidate: 60 }` de ISR moi 60s

Vi du `no-store`:

```tsx
const res = await fetch("https://dummyjson.com/products", {
  cache: "no-store",
});
```

Vi du `revalidate`:

```tsx
const res = await fetch("https://dummyjson.com/products", {
  next: { revalidate: 60 },
});
```

### 3.2 Thuc hanh de xuat
- Bai 1: Tao 3 trang: Home, Products, Product Detail.
- Bai 2: Tao dynamic route `/products/[id]`.
- Bai 3: Tao `loading.tsx` va `error.tsx` cho route products.
- Bai 4: Viet bo loc theo query string (`?q=...`) bang `useSearchParams`.

### 3.3 Loi thuong gap
- Dung hook React trong Server Component (gay loi).
- Quen `"use client"` o component can interactive.
- Khong hieu cache, dan den du lieu "khong update".

### 3.4 Checklist dat duoc
- [ ] Tu tao duoc route tinh va route dong.
- [ ] Hieu va ap dung dung Server/Client Component.
- [ ] Viet data fetching dung cach trong App Router.
- [ ] Xu ly loading/error/not-found ro rang.

### 3.5 Ghi chu ca nhan (tu dien)
- Kien thuc de nho:
  - 
- Van de gap phai:
  - 
- Cach toi sua:
  - 
- Vi du toi tu viet:
  - 

### 3.6 Lo trinh hoc Giai doan 2 theo tung buoi

#### Buoi 1 - Routing va layout
- Muc tieu:
  - Tao route tinh + route dong.
  - Hieu quan he giua `layout.tsx` va `page.tsx`.
- Bai tap:
  - Tao `/about`, `/products`, `/products/[id]`.
  - Tao layout chung co header + main.

#### Buoi 2 - Server/Client Component + hooks Next
- Muc tieu:
  - Phan biet ro component nao la server/client.
  - Dung duoc `useRouter`, `usePathname`, `useSearchParams`, `useParams`.
- Bai tap:
  - Tao o tim kiem query `?q=...`.
  - Tao nut chuyen trang bang `router.push`.

#### Buoi 3 - Data fetching + loading/error
- Muc tieu:
  - Goi API dung trong App Router.
  - Hieu cache va revalidate co ban.
- Bai tap:
  - Tao `loading.tsx` cho route products.
  - Tao `error.tsx` de xu ly loi runtime.

### 3.7 Mau file quan trong trong App Router

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

### 3.8 Bai giang chi tiet Giai doan 2 (hoc de tu lam duoc)

#### Muc tieu sau khi hoc xong Giai doan 2
- Ban tu tao duoc project Next.js App Router co cau truc ro rang.
- Ban biet route nao dung Server Component, route nao dung Client Component.
- Ban fetch du lieu dung cach, hieu cache co ban.
- Ban xu ly duoc loading, error, not-found trong thuc te.
- Ban biet doc query, params va dieu huong trang bang hooks Next.

---

#### Phan 1 - Tu duy App Router (goc re)

- App Router la cach to chuc route dua tren thu muc trong `app/`.
- Moi folder co `page.tsx` se tao ra 1 URL.
- `layout.tsx` la bo khung dung chung cho nhieu trang con.
- Component trong `app/` mac dinh la Server Component (render phia server).

Vi du:

```txt
app/
  layout.tsx      -> bo khung toan app
  page.tsx        -> /
  blog/
    page.tsx      -> /blog
    [slug]/
      page.tsx    -> /blog/:slug
```

Tu duy nhanh:
- `page.tsx` = noi dung trang.
- `layout.tsx` = khung bao ngoai.
- `loading.tsx` = cho route dang tai.
- `error.tsx` = khi route bi loi.
- `not-found.tsx` = khi khong co du lieu / route sai.

---

#### Phan 2 - Layout trong App Router (cuc ky quan trong)

`layout.tsx` goc:

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

Giai thich:
- `children` la noi dung cua route con.
- Header o layout goc se xuat hien tren moi trang.
- Ban co the tao layout rieng cho tung nhom route.

Vi du layout rieng cho products:

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

#### Phan 3 - Server Component vs Client Component (de tranh loi)

##### Server Component (mac dinh)
- Dung de lay data va render ban dau.
- Khong dung duoc `useState`, `useEffect`, event click.
- Co the goi `fetch` truc tiep.

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
- Dung khi can state, hooks, event browser.
- Them `"use client"` o dong dau file.

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

Quy tac vang:
- Lay data: uu tien Server Component.
- Tuong tac UI: Client Component.
- Khong nhat thiet bien tat ca thanh Client.

---

#### Phan 4 - Dynamic Route va Params

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

Giai thich:
- `[id]` la dynamic segment.
- `params.id` chinh la gia tri URL.

---

#### Phan 5 - Query String va Search Params

Vi du URL: `/products?q=iphone&sort=price_asc`

Doc query trong Client Component:

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

Dieu huong co query:

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

#### Phan 6 - Data Fetching + Cache trong App Router

##### Cach 1: du lieu luon moi

```tsx
const res = await fetch("https://dummyjson.com/products", {
  cache: "no-store",
});
```

Dung khi:
- Du lieu thay doi lien tuc.
- Can dam bao moi request lay du lieu moi nhat.

##### Cach 2: revalidate theo chu ky

```tsx
const res = await fetch("https://dummyjson.com/products", {
  next: { revalidate: 60 },
});
```

Dung khi:
- Muon can bang giua toc do va do moi cua du lieu.
- Chap nhan du lieu cu toi da 60 giay.

Quy tac de nho:
- Chua chac -> bat dau voi `no-store`.
- Can toi uu -> doi sang `revalidate`.

---

#### Phan 7 - loading, error, not-found

##### `loading.tsx`
- Tu dong hien thi khi route dang cho data.

```tsx
// app/products/loading.tsx
export default function Loading() {
  return <p>Dang tai san pham...</p>;
}
```

##### `error.tsx`
- Bat loi runtime trong route segment.
- Bat buoc la Client Component.

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

Va trong `page.tsx` co the goi:

```tsx
import { notFound } from "next/navigation";

if (!product) notFound();
```

---

#### Phan 8 - Route Groups (to chuc du an de dep)

Vi du:

```txt
app/
  (auth)/
    login/page.tsx
    register/page.tsx
  (shop)/
    products/page.tsx
```

Giai thich:
- Ten trong ngoac `()` khong xuat hien tren URL.
- Chi de chia nhom route cho de quan ly.

---

#### Phan 9 - Checklist kiem tra nang luc Giai doan 2

- [ ] Tao duoc layout goc va layout rieng theo module.
- [ ] Tao route tinh (`/about`) va route dong (`/products/[id]`).
- [ ] Hieu ro Server vs Client Component, khong dat sai.
- [ ] Dung duoc `useRouter`, `useSearchParams`, `useParams`.
- [ ] Viet `loading.tsx`, `error.tsx`, `not-found.tsx`.
- [ ] Fetch du lieu va chon cache strategy phu hop.

---

#### Phan 10 - Bai tap thuc chien (lam xong se len tay rat nhanh)

De bai: Tao mini module "Products"

Yeu cau:
- Trang danh sach `/products`: fetch 20 san pham.
- O tim kiem client side (`q` tren URL).
- Trang chi tiet `/products/[id]`.
- Co `loading.tsx` va `error.tsx` cho module products.
- Neu id khong hop le thi hien `not-found`.

Huong dan lam:
1. Tao cau truc route va layout.
2. Lam danh sach truoc.
3. Lam detail sau.
4. Them loading/error/not-found.
5. Them query search.

Tieu chi dat:
- Code chay dung.
- Folder ro rang.
- TypeScript khong `any`.
- Khong dung hook trong Server Component.

---

#### Phan 11 - Loi thuong gap va cach sua nhanh

- Loi: "You're importing a component that needs useState..."
  - Nguyen nhan: quen `"use client"`.
  - Cach sua: them `"use client"` vao dau file component can hook/event.

- Loi: Du lieu cu, khong thay doi sau khi refresh
  - Nguyen nhan: dinh cache mac dinh.
  - Cach sua: dung `cache: "no-store"` hoac `revalidate`.

- Loi: `params` undefined
  - Nguyen nhan: sai ten folder dynamic.
  - Cach sua: dam bao folder la `[id]` va truy cap qua `params.id`.

- Loi: `error.tsx` khong chay dung
  - Nguyen nhan: chua dat `"use client"`.
  - Cach sua: them `"use client"` cho `error.tsx`.

---

#### Phan 12 - Mau ghi chu hoc Giai doan 2 (ban dien vao)

- Hom nay toi hoc:
  - 
- Dieu toi hieu ro:
  - 
- Dieu toi chua ro:
  - 
- Loi toi da gap:
  - 
- Cach toi fix:
  - 
- 1 dieu toi se ap dung vao project that:
  - 

---

## 4) GIAI DOAN 3 - Redux Toolkit (RTK) trong Next.js

### 4.1 Ly thuyet cot loi

#### A. Cac khai niem RTK
- `configureStore`: tao store.
- `createSlice`: tao reducer + action.
- `useSelector`: doc state.
- `useDispatch`: goi action.
- `createAsyncThunk`: xu ly bat dong bo (goi API).

#### B. Cau truc folder goi y
- `src/store/store.ts`
- `src/store/hooks.ts`
- `src/store/features/todo/todoSlice.ts`
- `src/providers/StoreProvider.tsx`

#### C. Tich hop voi Next.js App Router
- Dat `StoreProvider` trong `app/layout.tsx` (thong qua client component trung gian).
- Thanh phan dung `useSelector/useDispatch` phai la Client Component.

#### D. Khi nao dung RTK?
- Dung RTK khi:
  - State dung chung nhieu man hinh.
  - Logic cap nhat state phuc tap.
  - Can theo doi loading/error/global state.
- Khong can RTK khi:
  - State nho, chi trong 1-2 component.
  - Du lieu co the lay truc tiep o Server Component.

### 4.2 Thuc hanh de xuat
- Bai 1: Tao `todoSlice` (add/remove/toggle).
- Bai 2: Viet `createAsyncThunk` de fetch todo list.
- Bai 3: Hien thi loading, error, data tren giao dien.
- Bai 4: Tach selector va custom typed hooks.

### 4.3 Loi thuong gap
- Dat reducer sai key trong store.
- Quen typed hooks (`useAppDispatch`, `useAppSelector`).
- Dispatch thunk nhung khong xu ly state loading/error.

### 4.4 Checklist dat duoc
- [ ] Tao store + slice dung TypeScript.
- [ ] Viet va dung `createAsyncThunk`.
- [ ] Hieu luong du lieu: UI -> action -> reducer -> UI.
- [ ] Tich hop RTK vao Next App Router dung cach.

### 4.5 Ghi chu ca nhan (tu dien)
- Kien thuc de nho:
  - 
- Van de gap phai:
  - 
- Cach toi sua:
  - 
- Vi du toi tu viet:
  - 

### 4.6 Bai giang chi tiet Giai doan 3 (RTK trong Next.js App Router)

#### Muc tieu sau khi hoc xong Giai doan 3
- Ban tu tao duoc store voi `configureStore`.
- Ban viet duoc slice co action + reducer ro rang.
- Ban viet duoc `createAsyncThunk` de goi API.
- Ban ket noi RTK vao Next.js App Router dung vi tri.
- Ban hieu khi nao dung RTK, khi nao khong can.

---

#### Phan 1 - Tu duy RTK trong du an Next.js

- RTK dung de quan ly global state (state dung chung nhieu noi).
- Khong phai cai gi cung dua vao RTK.
- Trong App Router:
  - Du lieu render lan dau co the fetch o Server Component.
  - State tuong tac nguoi dung (gio hang, bo loc, UI state,...) hop voi RTK.

Quy tac de nho:
- Local state nho -> `useState`.
- State chia se phuc tap -> RTK.

---

#### Phan 2 - Cau truc folder goi y (de de quan ly)

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

Y nghia:
- `store.ts`: tao store tong.
- `hooks.ts`: typed hooks de dung an toan voi TS.
- `todoSlice.ts`: noi viet state + reducer + action cho module todo.
- `StoreProvider.tsx`: bo Redux Provider vao app.

---

#### Phan 3 - Tao store dung chuan TypeScript

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

Giai thich:
- `todo` la key state trong store, sau nay doc bang `state.todo`.
- `RootState`, `AppDispatch` la type can thiet cho typed hooks.

---

#### Phan 4 - Typed hooks (rat nen dung)

`src/store/hooks.ts`

```ts
import { TypedUseSelectorHook, useDispatch, useSelector } from "react-redux";
import type { AppDispatch, RootState } from "./store";

export const useAppDispatch: () => AppDispatch = useDispatch;
export const useAppSelector: TypedUseSelectorHook<RootState> = useSelector;
```

Vi sao can:
- Tranh phai ep kieu thu cong moi lan dung.
- TypeScript goi y dung, bat loi som.

---

#### Phan 5 - Tao slice (state + reducer + action)

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

Giai thich:
- Reducer trong RTK cho phep "viet giong nhu mutate" nhung ben trong da immutable.
- `PayloadAction<T>` giup action payload co type ro rang.

---

#### Phan 6 - Async voi createAsyncThunk

`src/store/features/todo/todoSlice.ts` (ban nang cao)

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

#### Phan 7 - Tich hop Redux Provider vao Next.js App Router

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

Luu y:
- `Provider` phai dat trong Client Component (`StoreProvider.tsx`).
- `layout.tsx` co the la Server Component, nhung van wrap duoc client provider.

---

#### Phan 8 - Su dung state/action trong component

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

#### Phan 9 - Selector de tranh lap code

Trong `todoSlice.ts` hoac file selectors rieng:

```ts
import type { RootState } from "@/src/store/store";

export const selectTodos = (state: RootState) => state.todo.items;
export const selectTodoLoading = (state: RootState) => state.todo.loading;
export const selectTodoError = (state: RootState) => state.todo.error;
export const selectDoneCount = (state: RootState) =>
  state.todo.items.filter((t) => t.done).length;
```

Loi ich:
- De test.
- De tai su dung.
- Component gon hon.

---

#### Phan 10 - Khi nao dung RTK, khi nao khong?

Dung RTK khi:
- Du lieu dung chung nhieu route/component.
- Co nhieu thao tac cap nhat state phuc tap.
- Can quan ly loading/error nhat quan.

Khong can RTK khi:
- Form nho trong 1 component.
- State UI nho (modal open/close).
- Data co the fetch truc tiep o Server Component ma khong can global.

---

#### Phan 11 - Loi thuong gap va cach sua

- Loi: `could not find react-redux context value`
  - Nguyen nhan: chua wrap Provider.
  - Cach sua: them `StoreProvider` vao `app/layout.tsx`.

- Loi: `Property 'todo' does not exist on type RootState`
  - Nguyen nhan: key reducer khong trung.
  - Cach sua: kiem tra `reducer: { todo: todoReducer }`.

- Loi: Dispatch bi sai type
  - Nguyen nhan: dung `useDispatch` thuong.
  - Cach sua: dung `useAppDispatch`.

- Loi: UI khong cap nhat
  - Nguyen nhan: sua state sai cach ngoai reducer RTK.
  - Cach sua: chi cap nhat state thong qua action/reducer.

---

#### Phan 12 - Checklist dat chuan Giai doan 3

- [ ] Tao store + hooks typed dung TypeScript.
- [ ] Tao slice voi `createSlice`.
- [ ] Tao async logic voi `createAsyncThunk`.
- [ ] Hien thi loading/error/data day du.
- [ ] Tich hop Redux Provider dung vi tri trong App Router.
- [ ] Co selector cho cac du lieu dung nhieu lan.

---

#### Phan 13 - Bai tap thuc chien Giai doan 3

De bai: Lam module "Cart + Wishlist" voi RTK

Yeu cau:
- `cartSlice`: add/remove/change quantity.
- `wishlistSlice`: add/remove item yeu thich.
- Hien thi tong so luong cart o header (global state).
- Luu cart vao `localStorage` (co the dung `useEffect` trong client component de dong bo).

Nang cao:
- Tao `createAsyncThunk` fetch danh sach san pham.
- Khi add cart thi kiem tra da ton tai -> tang quantity.

Tieu chi dat:
- Khong dung `any`.
- Action/reducer dat ten ro rang.
- TypeScript bat loi som.

---

#### Phan 14 - Mau ghi chu hoc Giai doan 3 (ban dien vao)

- Hom nay toi hoc:
  - 
- Dieu toi hieu ro:
  - 
- Dieu toi chua ro:
  - 
- Loi toi da gap:
  - 
- Cach toi fix:
  - 
- 1 dieu toi se ap dung vao project that:
  - 

### 4.7 RTK nang cao - Middleware + RTK Query (chi tiet de di project that)

#### A. Middleware la gi? dung de lam gi?

- Middleware la lop nam giua `dispatch(action)` va `reducer`.
- Dung de:
  - log action/state,
  - xu ly async flow,
  - them header/token,
  - xu ly loi chung.

So do:

```txt
UI -> dispatch(action) -> middleware -> reducer -> store update -> UI re-render
```

---

#### B. Cach them middleware vao store

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

Luu y:
- Luon bat dau tu `getDefaultMiddleware()`.
- Sau do moi `concat(...)` middleware custom.
- Khong nen bo middleware mac dinh neu khong co ly do dac biet.

---

#### C. Vi du middleware auth token (tu duy)

Muc tieu:
- Moi request API deu co token neu da dang nhap.

Co 2 huong:
- Huong 1: middleware can thiep action async.
- Huong 2 (de hon, de bao tri hon): xu ly token ngay trong `baseQuery` cua RTK Query.

Thuc te du an thuong dung huong 2.

---

#### D. RTK Query la gi?

- RTK Query la cong cu trong Redux Toolkit de fetch/caching data.
- Giam rat nhieu code thu cong so voi `createAsyncThunk`.
- Tu dong co:
  - loading/error/data state
  - caching
  - refetch
  - invalidation bang tags

Khi nao nen dung:
- App co nhieu man hinh goi API.
- Can cache va dong bo du lieu giua nhieu component.

---

#### E. Cac thanh phan cot loi cua RTK Query

- `createApi`: tao API service.
- `baseQuery`: cau hinh request chung.
- `endpoints`: dinh nghia query/mutation.
- `tagTypes` + `providesTags` + `invalidatesTags`: cache invalidation.

---

#### F. Tao API service voi `createApi`

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

#### G. Ket noi RTK Query vao store

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

Luu y quan trong:
- RTK Query bat buoc them ca:
  - `productApi.reducer`
  - `productApi.middleware`

---

#### H. Su dung hooks cua RTK Query trong component

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

#### I. So sanh nhanh: `createAsyncThunk` vs `RTK Query`

- `createAsyncThunk`
  - Linh hoat cao.
  - Phai tu viet loading/error/data reducers.
  - Hop voi logic async phuc tap, khong chi fetch CRUD.

- `RTK Query`
  - Cuc nhanh cho fetch API CRUD.
  - Tu co hooks + cache + invalidation.
  - Giam nhieu code lap lai.

Goi y:
- App hien dai, nhieu API: uu tien RTK Query cho server state.
- Client state thuong truc (theme, ui, wizard step): de o slice thuong.

---

#### J. Checklist dat chuan RTK nang cao

- [ ] Biet middleware nam o dau trong data flow Redux.
- [ ] Them middleware custom dung cach voi `getDefaultMiddleware`.
- [ ] Tao duoc 1 `createApi` service.
- [ ] Ket noi duoc `api.reducer` + `api.middleware`.
- [ ] Dung duoc query hook + mutation hook.
- [ ] Hieu `providesTags/invalidatesTags` de refetch dung luc.

---

#### K. Bai tap thuc chien (lam de nho rat lau)

De bai:
- Tao `postApi` voi cac endpoint:
  - `getPosts`
  - `getPostById`
  - `createPost`
  - `updatePost`
  - `deletePost`
- Co tag invalidation de sau khi tao/sua/xoa thi danh sach tu refresh.
- Tao 1 trang hien thi danh sach + form tao post.

Muc tieu:
- Vua hieu RTK Query,
- vua biet cach to chuc store sach trong Next.js.

---

## 5) GIAI DOAN 4 - Project thuc chien (CRUD)

### 5.1 Muc tieu Giai doan 4

Sau khi hoc xong giai doan nay, ban co the:
- Tu tao 1 project Next.js App Router hoan chinh tu dau.
- Biet to chuc folder, tach component, viet logic sach.
- Ket hop Server Component + Client Component + RTK dung cho.
- Tu tin lam duoc project tuong tu trong thuc te.

---

### 5.2 De bai: App Quan ly cong viec (Task Manager)

Chuc nang:
- Xem danh sach task.
- Them task moi.
- Sua task.
- Xoa task.
- Loc task theo trang thai: Tat ca / Dang lam / Da xong.
- Tim kiem task theo ten.
- Hien thi loading khi dang tai du lieu.
- Hien thi loi khi co van de.

---

### 5.3 Cau truc folder goi y (de de bao tri)

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

Giai thich:
- `app/` chua route va layout.
- `src/components/` chua cac component tai su dung.
- `src/store/` chua toan bo logic RTK.
- `src/types/` chua type chung.

---

### 5.4 Buoc 1 - Dinh nghia type chung

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

### 5.5 Buoc 2 - Tao RTK Query API service

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

### 5.6 Buoc 3 - Tao UI state slice (loc, tim kiem)

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

### 5.7 Buoc 4 - Cau hinh store

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

### 5.8 Buoc 5 - StoreProvider

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

### 5.9 Buoc 6 - Layout goc

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

### 5.10 Buoc 7 - Cac component

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

### 5.11 Buoc 8 - Trang tasks

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

### 5.12 So do luong du lieu toan app

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

### 5.13 Tu duy thiet ke quan trong

#### Khi nao dung Server Component?
- Trang tasks/page.tsx co the la Server Component (khong co hook, chi render component con).

#### Khi nao dung Client Component?
- TaskForm, TaskList, TaskItem, SearchBox, TaskFilter
  (vi co hook, event, state).

#### Khi nao dung RTK?
- Filter (keyword, status) dung chung nhieu component -> RTK slice.
- Data tasks tu API -> RTK Query (cache + refetch).

#### Khi nao KHONG can RTK?
- Input tam trong form (title, desc) -> `useState` local la du.

---

### 5.14 Loi thuong gap trong project that

- Loi: Component client import trong server component -> warning hydration
  - Cach sua: dam bao component client co `"use client"`.

- Loi: Mutation khong refetch list
  - Cach sua: kiem tra `invalidatesTags` trung voi `providesTags`.

- Loi: Filter khong hoat dong
  - Cach sua: kiem tra selector doc dung key (`state.taskFilter`).

- Loi: TypeScript bao loi `any` nhieu
  - Cach sua: dinh nghia type ro rang o `src/types/`.

---

### 5.15 Checklist hoan thanh project

- [ ] Cau truc folder sach, de hieu.
- [ ] Co type rieng trong `src/types/`.
- [ ] RTK Query CRUD day du.
- [ ] Filter (keyword + status) hoat dong.
- [ ] loading.tsx + error.tsx co cho route tasks.
- [ ] Khong dung `any`.
- [ ] Component tach nho, tai su dung duoc.
- [ ] Code chay on dinh, khong loi console.

---

### 5.16 Nang cao (lam them neu muon gioi hon)

- Them phan trang (pagination).
- Them confirm dialog truoc khi xoa.
- Them toast thong bao thanh cong/loi.
- Them dark mode bang `useContext` hoac CSS variable.
- Deploy len Vercel.

---

### 5.17 Ghi chu ca nhan (tu dien)

- Chuc nang toi da xong:
  - 
- Chuc nang con thieu:
  - 
- Bug da gap:
  - 
- Cach toi fix:
  - 
- Dieu toi hoc duoc nhieu nhat tu project nay:
  - 

---

## 5B) BO SUNG - Cat giao dien, Hooks nang cao, Next.js tien ich

### 5B.1 Tu duy cat giao dien tu design (Figma -> Code)

#### Buoc 1: Nhin tong the -> chia vung lon

Vi du nhin 1 trang web:

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

Chia thanh component:
- `Header`
- `Sidebar` (chua `NavItem`)
- `MainContent` (chua `Card`)
- `Footer`

#### Buoc 2: Moi vung lon -> tach component con

Vi du `Header`:

```txt
Header
  -> Logo
  -> NavMenu (chua NavLink)
  -> UserAvatar
```

#### Buoc 3: Xac dinh props cho moi component

```tsx
type CardProps = {
  title: string;
  image: string;
  price: number;
  onAddToCart: () => void;
};
```

#### Buoc 4: Style roi ghep lai

Quy tac vang:
- Component nao lap lai -> tach rieng.
- Component nao co state rieng -> Client Component.
- Component nao chi hien thi -> co the la Server Component.

---

### 5B.2 CSS trong Next.js (3 cach chinh)

#### Cach 1: CSS Modules (mac dinh, khong can cai them)

Tao file: `TaskItem.module.css`

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

Dung trong component:

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

Loi ich:
- Class tu dong khong trung ten (scoped).
- Khong anh huong component khac.

#### Cach 2: Tailwind CSS (pho bien nhat hien nay)

Cai dat: khi tao project Next.js chon "Yes" cho Tailwind.

Vi du:

```tsx
export default function TaskItem({ title, isDone }: { title: string; isDone: boolean }) {
  return (
    <li className="flex items-center gap-2 p-2 border-b border-gray-200">
      <span className={isDone ? "line-through text-gray-400" : ""}>{title}</span>
    </li>
  );
}
```

Loi ich:
- Viet style nhanh, khong tao file CSS rieng.
- Responsive de: `md:flex-row`, `lg:text-xl`.

Vi du responsive:

```tsx
<div className="flex flex-col md:flex-row gap-4">
  <aside className="w-full md:w-64">Sidebar</aside>
  <main className="flex-1">Content</main>
</div>
```

#### Cach 3: Inline style (chi dung khi rat don gian)

```tsx
<div style={{ padding: 16, backgroundColor: "#f5f5f5" }}>
  Noi dung
</div>
```

#### Nen dung cach nao?

- Du an moi, team thich utility -> Tailwind.
- Du an can CSS truyen thong, scoped -> CSS Modules.
- Tranh inline style cho layout phuc tap.

---

### 5B.3 `next/image` - Hien thi anh toi uu

#### Vi sao khong dung the `<img>` thuong?
- `next/image` tu dong:
  - Lazy load (chi tai khi cuon toi).
  - Resize theo man hinh.
  - Toi uu dung luong anh.

#### Vi du co ban

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

#### Vi du anh tu URL ngoai

Buoc 1: cau hinh `next.config.ts` (hoac `next.config.js`):

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

Buoc 2: dung trong component:

```tsx
<Image
  src="https://cdn.dummyjson.com/products/images/1/thumbnail.jpg"
  alt="Anh san pham"
  width={200}
  height={200}
/>
```

#### Vi du anh full width (responsive)

```tsx
<Image
  src="/images/banner.jpg"
  alt="Banner"
  fill
  style={{ objectFit: "cover" }}
/>
```

Luu y: container cha phai co `position: relative` va kich thuoc ro rang.

---

### 5B.4 `next/link` - Dieu huong nhanh khong reload trang

#### Vi du co ban

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

#### So voi the `<a>`?
- `<a>` reload toan trang.
- `<Link>` chi doi phan noi dung, giu nguyen layout -> nhanh hon nhieu.

#### Vi du voi query string

```tsx
<Link href={{ pathname: "/tasks", query: { status: "done" } }}>
  Xem task da xong
</Link>
```

---

### 5B.5 `next/font` - Font chu toi uu

#### Vi du Google Font

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

Loi ich:
- Font duoc tai ve luc build, khong phu thuoc CDN.
- Khong bi nhay layout (FOUT/FOIT).

---

### 5B.6 Metadata va SEO

#### Static metadata (khai bao truc tiep)

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

#### Dynamic metadata (theo du lieu)

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

Luu y:
- `metadata` va `generateMetadata` chi dung trong Server Component.
- Moi page nen co title + description rieng.

---

### 5B.7 Hook `useReducer` (quan ly state phuc tap)

#### Khi nao dung?
- State co nhieu field lien quan nhau.
- Co nhieu loai action khac nhau.
- Logic cap nhat phuc tap hon `useState`.

#### Vi du: Form nhieu buoc (wizard)

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

#### So sanh `useState` vs `useReducer`

- `useState`: state don gian, 1-2 gia tri.
- `useReducer`: state co nhieu field, nhieu loai cap nhat, logic phuc tap.

---

### 5B.8 Custom Hooks (tai su dung logic)

#### Custom hook la gi?
- La ham bat dau bang `use...`.
- Ben trong dung cac hook khac.
- Muc dich: gom logic lai, tai su dung o nhieu component.

#### Vi du 1: `useLocalStorage`

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

Dung:

```tsx
const [tasks, setTasks] = useLocalStorage<string[]>("tasks", []);
```

#### Vi du 2: `useDebounce`

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

Dung:

```tsx
const [q, setQ] = useState("");
const debouncedQ = useDebounce(q, 300);
// dung debouncedQ de goi API, tranh goi qua nhieu lan
```

#### Vi du 3: `useToggle`

```ts
"use client";
import { useCallback, useState } from "react";

export function useToggle(initial = false) {
  const [value, setValue] = useState(initial);
  const toggle = useCallback(() => setValue((v) => !v), []);
  return [value, toggle] as const;
}
```

Dung:

```tsx
const [isOpen, toggleOpen] = useToggle();
// ...
<button onClick={toggleOpen}>{isOpen ? "Dong" : "Mo"}</button>
```

#### Quy tac viet custom hook
- Ten bat dau bang `use`.
- Chi goi hook khac ben trong (khong goi trong if/for).
- Tra ve gia tri + ham cap nhat.
- Dat trong folder `src/hooks/`.

---

### 5B.9 Checklist bo sung (ban kiem tra)

- [ ] Biet cat giao dien tu design thanh component.
- [ ] Dung duoc CSS Modules hoac Tailwind.
- [ ] Dung duoc `next/image`, `next/link`, `next/font`.
- [ ] Them metadata/SEO cho tung trang.
- [ ] Dung duoc `useReducer` cho state phuc tap.
- [ ] Tu viet duoc custom hook.

---

### 5B.10 SEO chuan trong Next.js App Router (day du de len production)

#### A. Tong quan: Website chuan SEO can nhung gi?

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

#### B. Open Graph + Twitter Card (chia se mang xa hoi)

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

Khi share link len Facebook/Zalo se hien anh + mo ta dep.

---

#### C. Dynamic Open Graph (theo du lieu)

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

#### D. Semantic HTML (rat quan trong cho SEO)

Quy tac:
- Moi trang chi co 1 the `<h1>`.
- Thu tu: `h1 > h2 > h3`, khong nhay cap.
- Anh luon co `alt` mo ta.
- Dung `<nav>`, `<main>`, `<article>`, `<section>`, `<footer>` thay vi `<div>` khi co the.

Vi du dung:

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

Vi du sai:

```tsx
<div>
  <h3>Ten san pham</h3>  <!-- sai: khong co h1, nhay tu h3 -->
  <div>Mo ta</div>
  <img src="anh.jpg" />   <!-- sai: thieu alt -->
</div>
```

---

#### E. sitemap.xml (ban do web cho Google)

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

Next.js tu dong tao file `/sitemap.xml` khi deploy.

#### Sitemap dong (nhieu trang tu database)

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

#### F. robots.txt (chi dan cho bot)

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

Next.js tu dong tao `/robots.txt`.

---

#### G. Canonical URL (tranh trung noi dung)

```tsx
export const metadata: Metadata = {
  alternates: {
    canonical: "https://example.com/tasks",
  },
};
```

Khi nao can:
- 1 noi dung xuat hien tren nhieu URL khac nhau.
- Vi du: `/tasks` va `/tasks?page=1` cung noi dung -> can canonical.

---

#### H. generateStaticParams (tao trang tinh, SEO tot nhat)

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

Loi ich:
- Next.js tao san HTML luc build.
- Google bot crawl nhanh hon, SEO tot hon.
- Trang tai cuc nhanh.

---

#### I. Structured Data / JSON-LD (giup Google hieu noi dung)

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

Khi co JSON-LD, Google co the hien thi rich snippet (gia, sao, hinh anh) ngay tren trang tim kiem.

---

#### J. Performance / Core Web Vitals

3 chi so Google danh gia:
- **LCP** (Largest Contentful Paint): noi dung chinh hien thi nhanh.
  - Dung `next/image` de toi uu anh.
  - Dung `next/font` de tranh nhay layout.
  - Uu tien Server Component cho trang dau.

- **CLS** (Cumulative Layout Shift): trang khong bi nhay khi tai.
  - Luon dat `width`/`height` cho anh.
  - Khong insert noi dung dong lam dich layout.

- **INP** (Interaction to Next Paint): tuong tac phan hoi nhanh.
  - Tranh logic nang trong event handler.
  - Dung `useTransition` neu can cap nhat state lon.

Vi du `useTransition`:

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

#### K. Checklist SEO chuan cho moi trang

- [ ] Co `<h1>` duy nhat, heading dung thu tu.
- [ ] Co title + description (metadata).
- [ ] Co Open Graph (anh + mo ta).
- [ ] Anh co `alt`, dung `next/image`.
- [ ] Co `sitemap.xml` va `robots.txt`.
- [ ] Trang quan trong co `generateStaticParams`.
- [ ] Co canonical URL neu can.
- [ ] HTML semantic (`main`, `nav`, `article`...).
- [ ] Core Web Vitals dat muc xanh.

---

### 5B.11 Ghi chu hoc phan bo sung (ban dien vao)

- Phan toi hieu ro nhat:
  - 
- Phan toi can on lai:
  - 
- Custom hook toi da tu viet:
  - 
- Loi thuong gap khi cat giao dien:
  - 
- Dieu toi hoc duoc ve SEO:
  - 

---

## 6) Mau nhat ky hoc tap hang ngay

### Ngay: ____ / ____ / ______
- Thoi gian hoc: 
- Muc tieu hom nay: 
- Phan da hoc: 
- Dieu hieu ro nhat: 
- Dieu con mo ho: 
- Cac cau hoi can hoi them:
  - 
- Bai tap da lam:
  - 
- Tu danh gia (1-10): 

---

## 7) Tai nguyen hoc tap goi y

- Next.js docs: https://nextjs.org/docs
- React docs: https://react.dev
- Redux Toolkit docs: https://redux-toolkit.js.org
- TypeScript docs: https://www.typescriptlang.org/docs

---

## 8) Huong dan hoc voi minh (neu ban muon hoc co mentor)

- Cach hoc:
  1. Ban hoc 1 muc trong tai lieu.
  2. Ban gui code bai tap cho minh review.
  3. Minh sua logic, toi uu, va giai thich lai.
  4. Ban cap nhat ghi chu vao dung phan trong tai lieu.

- Quy tac:
  - Hoc den dau lam bai den do.
  - Khong hoc vo toi va.
  - Luon viet ghi chu theo mau de nho lau.

---

## 9) Muc tieu cuoi cung

Sau khi hoc xong tai lieu nay, ban co the:
- Tu cat duoc giao dien theo Figma co ban.
- Tu to chuc duoc project Next.js App Router + TypeScript.
- Dung RTK dung noi dung luc.
- Viet logic ro rang, de bao tri.

