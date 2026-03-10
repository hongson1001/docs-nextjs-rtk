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

#### B. TypeScript trong React
- Dinh nghia kieu cho props:
  - `type ButtonProps = { label: string; onClick: () => void }`
- Typing cho event:
  - `React.ChangeEvent<HTMLInputElement>`
- Union type:
  - `status: "idle" | "loading" | "success" | "error"`

#### C. React Hooks can hoc ky
- `useState`: quan ly state local.
- `useEffect`: side effects (goi API, lang nghe su kien, dong bo du lieu).
- `useMemo`: cache ket qua tinh toan nang.
- `useCallback`: giu tham chieu ham on dinh.
- `useRef`: truy cap DOM / luu gia tri khong gay re-render.
- `useContext`: chia se state nhe trong app.

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

