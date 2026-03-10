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

#### B. Server Component vs Client Component
- Mac dinh trong `app/` la **Server Component**.
- Neu can hooks/event browser -> them `"use client"` o dau file.
- Quy tac:
  - UI co click/typing state local: Client Component.
  - Lay data server va render lan dau: uu tien Server Component.

#### C. Navigation hooks trong Next.js
- `useRouter()`: push/replace/back.
- `usePathname()`: lay current path.
- `useSearchParams()`: doc query string.
- `useParams()`: lay dynamic params.

#### D. Data Fetching trong App Router
- Goi API trong Server Component voi `fetch`.
- Caching:
  - mac dinh co cache (tuy theo request)
  - `cache: "no-store"` cho du lieu luon moi
  - `next: { revalidate: 60 }` de ISR moi 60s

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

---

## 5) GIAI DOAN 4 - Project thuc chien (CRUD)

### 5.1 De bai goi y
- Xay dung app Quan ly cong viec:
  - Dang task
  - Sua task
  - Xoa task
  - Loc theo trang thai
  - Tim kiem theo ten

### 5.2 Yeu cau bat buoc
- Su dung Next.js App Router + TypeScript.
- Co it nhat 1 module dung RTK.
- Co loading + error UI ro rang.
- Co tach component tai su dung.

### 5.3 Tieu chi danh gia
- Code de doc, ten bien ro rang.
- Khong lam dung global state.
- Data flow de hieu.
- UI chay on dinh, khong vo layout.

### 5.4 Ghi chu ca nhan (tu dien)
- Chuc nang toi da xong:
  - 
- Chuc nang con thieu:
  - 
- Bug da gap:
  - 
- Cach toi fix:
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

