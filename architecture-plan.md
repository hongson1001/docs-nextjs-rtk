# FBSport POS - Technology & Architecture Plan

## Context

FBShop (chuỗi cửa hàng cầu lông) cần thay thế KiotViet bằng hệ thống tự phát triển để:
- Tự chủ dữ liệu, không phụ thuộc bên thứ 3
- Tùy chỉnh linh hoạt (khuyến mãi, thanh toán VietQR, báo cáo riêng)
- Đồng bộ giữa POS tại quầy, website admin, và WooCommerce
- Tiết kiệm chi phí dài hạn

**Team**: 1-2 dev, mixed levels (senior + junior)
**Ưu tiên**: Chất lượng > tốc độ
**POS**: Tablet/máy POS chuyên dụng, offline bắt buộc

---

## 1. Tech Stack

| Layer | Technology | Version |
|-------|-----------|---------|
| **Monorepo** | Turborepo + pnpm | latest |
| **Backend** | NestJS (TypeScript) | 10+ |
| **Database** | MongoDB + Mongoose | 7+ |
| **Cache/Queue** | Redis + BullMQ | 7+ |
| **Realtime** | Socket.IO (NestJS Gateway) | 4+ |
| **POS Frontend** | Next.js 14 (PWA, App Router) | 14+ |
| **Admin Frontend** | Next.js 14 (App Router) | 14+ |
| **UI Library** | Shadcn/UI + Tailwind CSS | latest |
| **State Management** | Zustand + React Query (TanStack) | latest |
| **Forms** | React Hook Form + Zod | latest |
| **Tables** | TanStack Table | latest |
| **Charts** | Recharts | latest |
| **Auth** | JWT (access 15min + refresh 7d) | - |
| **Offline (POS)** | Dexie.js (IndexedDB) + Workbox | latest |
| **Thermal Print** | WebUSB API (ESC/POS) | - |
| **QR Payment** | VietQR API + qrcode.react | - |
| **File Storage** | Local disk (Phase 1), S3 (later) | - |
| **Containerization** | Docker + Docker Compose | - |

---

## 2. Project Structure (Monorepo)

```
fbsport-pos/
├── apps/
│   ├── api/                    # NestJS Backend API
│   │   └── src/
│   │       ├── common/         # Guards, decorators, interceptors, filters, pipes
│   │       ├── modules/
│   │       │   ├── auth/       # JWT login, refresh, password reset
│   │       │   ├── users/      # Nhân viên CRUD + phân quyền
│   │       │   ├── branches/   # Chi nhánh CRUD
│   │       │   ├── products/   # Sản phẩm + biến thể
│   │       │   ├── categories/ # Danh mục (tree)
│   │       │   ├── brands/     # Thương hiệu
│   │       │   ├── inventory/  # Tồn kho per branch + variant
│   │       │   ├── orders/     # Đơn hàng (POS + online)
│   │       │   ├── customers/  # Khách hàng
│   │       │   ├── suppliers/  # Nhà cung cấp
│   │       │   ├── imports/    # Phiếu nhập hàng
│   │       │   ├── transfers/  # Chuyển hàng giữa chi nhánh
│   │       │   ├── returns/    # Trả hàng
│   │       │   ├── transactions/ # Sổ quỹ (phiếu thu/chi)
│   │       │   ├── shifts/     # Ca làm việc
│   │       │   ├── reports/    # Báo cáo tổng hợp
│   │       │   ├── woo-sync/   # Đồng bộ WooCommerce
│   │       │   ├── notifications/ # Thông báo realtime
│   │       │   ├── printing/   # Template in hóa đơn
│   │       │   ├── promotions/ # Khuyến mãi (Phase 2-3)
│   │       │   └── loyalty/    # Tích điểm (Phase 2-3)
│   │       ├── config/         # Database, JWT, Redis, Woo config
│   │       └── main.ts
│   ├── admin/                  # Next.js Admin Web (trụ sở)
│   └── pos/                    # Next.js POS Client (tại quầy, PWA)
├── packages/
│   ├── shared/                 # Shared types, enums, constants
│   ├── validators/             # Zod schemas (BE + FE validation)
│   ├── ui/                     # Shared UI components
│   └── db/                     # Mongoose models & schemas
├── docker-compose.yml          # MongoDB, Redis
├── turbo.json
├── package.json
└── .env.example
```

---

## 3. Database Schema (MongoDB)

### Core - Hệ thống

**branches**
```
_id, name, address, phone, email,
bank_account: { bank_name, account_number, account_holder, bin_code },
is_active, settings: { tax_rate, receipt_template_id },
created_at, updated_at
```

**users**
```
_id, username (unique), password_hash, full_name, email, phone,
role: 'admin' | 'branch_manager' | 'cashier' | 'sales',
branch_id (ref branches, nullable for admin),
permissions: [String],
is_active, avatar_url,
created_at, updated_at
```

**settings**
```
_id, key (unique), value (Mixed), description,
scope: 'global' | 'branch', branch_id (nullable)
```

### Sản phẩm & Tồn kho

**categories**
```
_id, name, slug, parent_id (self-ref, nullable),
sort_order, is_active, created_at, updated_at
```

**brands**
```
_id, name, slug, logo_url, sort_order, is_active
```

**products**
```
_id, sku (unique), name, slug, description,
category_id (ref), brand_id (ref),
unit: 'cái' | 'đôi' | 'cuộn' | 'hộp',
cost_price, selling_price,
images: [{ url, is_primary }],
woo_product_id (nullable),
branches: [ObjectId],  // chi nhánh áp dụng
tags: [String],
is_active, is_sell_online,
created_at, updated_at
```

**product_variants**
```
_id, product_id (ref), variant_sku (unique), barcode (unique sparse),
attributes: { color, weight, size, material },
cost_price, selling_price,
image_url (nullable), woo_variation_id (nullable),
is_active, created_at, updated_at
```
Indexes: `{ product_id: 1 }`, `{ variant_sku: 1 }` unique, `{ barcode: 1 }` unique sparse

**inventory**
```
_id, variant_id (ref), branch_id (ref),
quantity, min_stock (default 0), updated_at
```
UNIQUE compound index: `{ variant_id: 1, branch_id: 1 }`

**inventory_logs**
```
_id, variant_id, branch_id,
type: 'import' | 'sale' | 'return_in' | 'return_out' | 'transfer_in' | 'transfer_out' | 'adjustment' | 'damage',
quantity_change, quantity_before, quantity_after,
reference_type: 'order' | 'import_receipt' | 'transfer' | 'stock_check' | 'manual',
reference_id, user_id (ref), note, created_at
```
Index: `{ variant_id: 1, branch_id: 1, created_at: -1 }`

### Đơn hàng

**orders** (items embedded)
```
_id, order_code (unique, auto: 'ORD-YYYYMMDD-XXXX'),
source: 'pos' | 'woo' | 'manual',
branch_id (ref), customer_id (ref, nullable), user_id (ref),

items: [{
  variant_id, product_name, variant_sku, variant_info,
  quantity, unit_price, discount_amount, line_total
}],

subtotal, discount_amount, shipping_fee, total_amount,
payment_method: 'cash' | 'bank_transfer' | 'card' | 'momo' | 'vnpay' | 'mixed',
payment_status: 'pending' | 'paid' | 'partial' | 'refunded',
cash_received, cash_change,

status: 'draft' | 'confirmed' | 'packing' | 'shipping' | 'completed' | 'cancelled' | 'returned',

shipping: { recipient_name, phone, address, carrier, tracking_number, shipping_status },

woo_order_id, promotion_code, note,
is_offline (default false), synced_at (nullable),
created_at, updated_at
```
Indexes: `{ branch_id: 1, created_at: -1 }`, `{ customer_id: 1 }`, `{ order_code: 1 }` unique, `{ status: 1, source: 1 }`

### Khách hàng

**customers**
```
_id, customer_code (unique, auto: 'KH-XXXX'),
name, phone (unique), email, address, date_of_birth,
type: 'regular' | 'vip' | 'dormant' | 'bad_debt',
total_spent (default 0), order_count (default 0), last_purchase_at,
debt (default 0), loyalty_points (default 0),
tags: [String], notes,
branch_id (nullable),
created_at, updated_at
```
Index: `{ phone: 1 }` unique, `{ type: 1 }`, `{ debt: -1 }`

### Nhà cung cấp & Nhập hàng

**suppliers**
```
_id, name, phone, email, address, contact_person,
debt (default 0), notes, is_active, created_at, updated_at
```

**import_receipts** (items embedded)
```
_id, receipt_code (unique, auto: 'NK-YYYYMMDD-XXXX'),
supplier_id (ref), branch_id (ref), user_id (ref),
items: [{ variant_id, product_name, variant_sku, quantity, cost_price, line_total }],
total_cost, payment_status, paid_amount, status: 'draft' | 'completed' | 'cancelled',
note, created_at, updated_at
```

### Chuyển kho & Kiểm kho

**transfers** (items embedded)
```
_id, transfer_code (unique, auto: 'CK-YYYYMMDD-XXXX'),
from_branch_id (ref), to_branch_id (ref),
requested_by (ref), approved_by (ref, nullable),
items: [{ variant_id, product_name, variant_sku, quantity }],
status: 'pending' | 'approved' | 'in_transit' | 'completed' | 'rejected',
reason, note, created_at, updated_at
```

**stock_checks** (items embedded)
```
_id, check_code (unique, auto: 'KK-YYYYMMDD-XXXX'),
branch_id (ref), user_id (ref),
items: [{ variant_id, product_name, variant_sku, system_quantity, actual_quantity, difference, reason }],
status: 'draft' | 'completed' | 'approved',
approved_by (nullable), note, created_at, updated_at
```

### Sổ quỹ & Ca làm

**transactions**
```
_id, transaction_code (unique, auto: 'PT/PC-YYYYMMDD-XXXX'),
type: 'income' | 'expense',
category: 'sale' | 'customer_payment' | 'refund' | 'supplier_payment' | 'salary' | 'rent' | 'import' | 'other',
amount, payment_method: 'cash' | 'bank_transfer' | 'ewallet',
branch_id (ref), user_id (ref),
order_id (ref, nullable), supplier_id (ref, nullable), customer_id (ref, nullable),
description, note, is_auto (default false), created_at
```
Index: `{ branch_id: 1, created_at: -1 }`, `{ type: 1, category: 1 }`

**shifts**
```
_id, branch_id (ref), user_id (ref),
opened_at, closed_at (nullable),
opening_cash,
summary: {
  total_orders, total_revenue, total_income, total_expense,
  expected_cash, actual_cash, discrepancy, discrepancy_reason
},
status: 'open' | 'closed', created_at
```
Index: `{ branch_id: 1, user_id: 1, status: 1 }`

### Hỗ trợ

**notifications**
```
_id, title, content,
type: 'system' | 'low_stock' | 'new_order' | 'transfer' | 'promotion',
priority: 'low' | 'normal' | 'high',
target_type: 'all' | 'branch' | 'user', target_ids: [ObjectId],
read_by: [{ user_id, read_at }],
sender_id (ref, nullable), link (nullable), created_at
```

**receipt_templates**
```
_id, name, template_type: 'sale' | 'return' | 'shift_report' | 'stock_check',
content (HTML/ESC-POS template),
branch_id (nullable), is_default, created_at, updated_at
```

**bank_accounts**
```
_id, bank_name, bank_code (bin), account_number, account_holder,
branch_id (ref, nullable), is_vat_account (default false),
qr_template, is_active, created_at, updated_at
```

---

## 4. Architecture

### Communication
```
POS Client (PWA) ──┐
                    ├── REST API + Socket.IO ──► NestJS API Server
Admin Web ─────────┘                               │
                                                    ├── MongoDB
WooCommerce ── Webhook + REST ── Woo Sync Module    ├── Redis (cache)
                                                    └── BullMQ (queue)
```

### Auth Flow
```
Login → JWT (access 15min + refresh 7d)
Each request carries: user_id, role, branch_id
Guard chain: JwtAuthGuard → RolesGuard → BranchGuard
- cashier/branch_manager: chỉ data của branch mình
- admin: tất cả, filter by branch_id
```

### Offline Architecture (POS)
```
Service Worker (Workbox) → Cache API resources + Background Sync
IndexedDB (Dexie.js):
  - products (synced on login)
  - inventory (synced, read-only local)
  - pendingOrders (created offline → sync when online)
  - pendingTransactions (phiếu thu/chi offline)

Online → API trực tiếp
Offline → IndexedDB + queue → sync khi reconnect
Conflict resolution: server wins, alert admin nếu tồn kho < 0
```

---

## 5. Frontend Architecture

### POS Client (Touch-optimized, PWA)
- Left panel: Scan barcode + tìm SP + actions
- Right panel: Giỏ hàng + tổng tiền + thanh toán
- Navigation: Sidebar/bottom tabs (Bán hàng, Đơn hàng, Sổ quỹ, Tồn kho, Chuyển hàng, Kiểm kho, Khách hàng, Kết thúc ca)

### Admin Web (Desktop-first)
- Sidebar: Dashboard, Hàng hóa, Đơn hàng, Khách hàng, Nhân viên, Sổ quỹ, Báo cáo, Bán online, Cấu hình
- Dashboard: Widgets (doanh thu, đơn, tồn thấp) + Charts + Tables

### Shared Tech
| Concern | POS | Admin |
|---------|-----|-------|
| Framework | Next.js 14 PWA | Next.js 14 |
| UI | Shadcn/UI + Tailwind | Shadcn/UI + Tailwind |
| State | Zustand | Zustand + React Query |
| Forms | React Hook Form + Zod | React Hook Form + Zod |
| Tables | TanStack Table | TanStack Table |
| Offline | Dexie.js + Workbox | No |
| Print | WebUSB (ESC/POS) | Export PDF (jsPDF) |

---

## 6. Phase Plan

### Phase 1 - MVP (~18 tuần cho 1-2 dev)

**Sprint 0 (2 tuần): Foundation**
- Monorepo setup (Turborepo + pnpm)
- NestJS boilerplate (auth, guards, swagger, exception filters)
- Next.js boilerplate POS + Admin (layout, auth, shared UI)
- Docker Compose (MongoDB, Redis)
- Shared packages (validators, types, constants)
- CI/CD pipeline

**Sprint 1-2 (4 tuần): Hàng hóa & Tồn kho**
- API: CRUD products, variants, categories, brands, inventory
- API: Import Excel/CSV sản phẩm
- Admin: Quản lý sản phẩm, tồn kho, danh mục, thương hiệu

**Sprint 3-4 (4 tuần): POS Bán hàng**
- API: Orders, Shifts
- POS: Login + load data chi nhánh
- POS: Bán hàng (scan, tìm SP, giỏ hàng, thanh toán, in bill)
- POS: Offline mode (IndexedDB + sync queue)

**Sprint 5 (2 tuần): Sổ quỹ & Khách hàng**
- API: Transactions, Customers
- POS: Sổ quỹ chi nhánh + kết thúc ca + khách hàng
- Admin: Sổ quỹ tổng hợp

**Sprint 6 (2 tuần): Nhập hàng & NCC**
- API: Suppliers, Import receipts
- Admin: Quản lý NCC + phiếu nhập

**Sprint 7 (2 tuần): WooCommerce Sync**
- API: Woo sync module + webhook receiver + BullMQ jobs
- Admin: Cấu hình Woo + manual sync + đơn online

**Sprint 8 (2 tuần): Báo cáo & Polish**
- API: Reports (doanh thu, tồn kho, top SP)
- Admin: Dashboard + báo cáo
- Bug fixes, performance, UX polish

### Phase 2 - Mở rộng
- Chuyển hàng giữa chi nhánh
- Kiểm kho, xuất hủy, trả hàng
- Khách hàng nâng cao (phân loại, công nợ)
- Nhân viên (phân quyền chi tiết, chấm công)
- Đối tác giao hàng + vận đơn
- Thanh toán VNPAY/MoMo callback
- Realtime WebSocket
- Báo cáo nâng cao

### Phase 3 - CRM & Nâng cao
- Phân khúc khách (RFM), loyalty, khuyến mãi
- Chiến dịch marketing (SMS/Zalo/Email)
- Dashboard chart nâng cao
- Báo cáo tài chính, lương, hoa hồng

### Phase 4 - Mobile
- React Native app
- Push notifications
- Dự báo tồn kho

---

## 7. Verification

### Phase 1 MVP testing checklist:
1. **Auth**: Login các role (admin, cashier), token refresh, permission check
2. **Products**: CRUD + variants + import Excel → verify data trong MongoDB
3. **POS bán hàng**: Scan barcode → tạo đơn → thanh toán → in bill → verify tồn kho giảm
4. **Offline**: Tắt mạng → tạo đơn → bật mạng → verify sync + tồn kho đúng
5. **Sổ quỹ**: Bán hàng → auto tạo phiếu thu → đóng ca → verify số liệu
6. **WooCommerce**: Tạo SP → sync Woo → đặt hàng Woo → webhook → verify đơn + tồn kho
7. **Reports**: Verify doanh thu, tồn kho, top SP match với data thực tế
8. **Multi-branch**: Login 2 chi nhánh → verify data isolation + admin thấy tất cả

### Dev environment:
```bash
docker-compose up -d          # MongoDB + Redis
pnpm install                  # Install all packages
pnpm dev --filter=api         # Start backend
pnpm dev --filter=admin       # Start admin web
pnpm dev --filter=pos         # Start POS client
```
