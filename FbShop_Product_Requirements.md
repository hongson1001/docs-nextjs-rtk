# FbShop POS - Tài liệu Yêu cầu Sản phẩm (PRD)

> **Lưu ý**: Đây là tài liệu yêu cầu sản phẩm (Product Requirements). Chưa bao gồm thiết kế kỹ thuật, database schema, hay API design. Mục đích: AI đọc tài liệu này để hiểu đầy đủ sản phẩm cần xây dựng trước khi lên kiến trúc.
>
> **Screenshot tham chiếu**: Thư mục `/screenshots/kiotviet/` sẽ được bổ sung để AI tham chiếu hình ảnh thực tế từ KiotViet.

---

## Context

FBShop (chuỗi cửa hàng cầu lông tại Việt Nam) đang dùng KiotViet để quản lý bán hàng. Mục tiêu: xây dựng hệ thống quản lý bán hàng riêng thay thế KiotViet, giữ lại toàn bộ tính năng quen thuộc nhưng:

- **Tự chủ dữ liệu** – không phụ thuộc bên thứ ba, toàn quyền kiểm soát
- **Tùy chỉnh linh hoạt** – khuyến mãi, thanh toán VietQR, báo cáo theo yêu cầu riêng
- **Đồng bộ đa kênh** – POS tại quầy, website admin, WooCommerce, mạng xã hội
- **Tiết kiệm chi phí** – không tốn phí thuê phần mềm hàng tháng

**Team**: 1–2 dev (senior + junior)
**Ưu tiên**: Chất lượng > tốc độ
**Hình thức bán**: Bán tại quầy (POS tablet), bán online (website/WooCommerce), bán qua mạng xã hội (chốt đơn thủ công)

---

## 1. Đối tượng người dùng (User Personas)

### 1.1 Admin – Chủ cửa hàng / Giám đốc
- Quản lý toàn bộ chuỗi cửa hàng, nhiều chi nhánh
- Cần xem báo cáo tổng hợp, phân tích kinh doanh, quản lý tài chính
- Truy cập từ máy tính desktop/laptop
- Có quyền cao nhất, nhìn thấy dữ liệu tất cả chi nhánh

### 1.2 Quản lý chi nhánh (Branch Manager)
- Quản lý một chi nhánh cụ thể
- Phân công ca, kiểm tra doanh thu chi nhánh, duyệt đơn nhập/chuyển hàng
- Truy cập từ máy tính hoặc tablet tại quầy
- Chỉ thấy dữ liệu chi nhánh mình

### 1.3 Nhân viên bán hàng / Thu ngân (Cashier)
- Bán hàng trực tiếp tại quầy, chốt đơn online từ mạng xã hội
- Dùng màn hình POS trên tablet
- Cần thao tác nhanh, ít bước nhất có thể
- Chỉ truy cập được tính năng bán hàng, sổ quỹ ca làm

### 1.4 Nhân viên kho (Warehouse Staff)
- Nhập hàng, kiểm kho, chuyển hàng giữa chi nhánh
- Truy cập từ máy tính hoặc tablet
- Cần thấy tồn kho, phiếu nhập, lịch sử xuất nhập

---

## 2. Luồng nghiệp vụ chính (Core Business Flows)

### 2.1 Luồng bán hàng tại quầy (POS Flow)
```
Nhân viên mở ca (khai báo tiền mặt đầu ca)
  → Tìm sản phẩm (quét mã vạch hoặc tìm tên)
  → Thêm vào giỏ hàng → Điều chỉnh số lượng / giảm giá
  → Chọn khách hàng (nếu có) → Áp mã khuyến mãi (nếu có)
  → Chọn phương thức thanh toán (tiền mặt / chuyển khoản / ví điện tử / kết hợp)
  → Xác nhận thanh toán → In hóa đơn nhiệt
  → Hệ thống tự động: trừ tồn kho + tạo phiếu thu sổ quỹ
  → Cuối ca: Đóng ca, kiểm tra tiền mặt thực tế vs. hệ thống
```

### 2.2 Luồng bán hàng giao hàng (Delivery Flow)
```
Nhân viên tạo đơn giao hàng (từ POS hoặc admin web)
  → Nhập thông tin người nhận (tên, SĐT, địa chỉ)
  → Chọn hình thức giao:
     [A] Giao qua đơn vị vận chuyển (GHTK, GHN...): tạo vận đơn → lấy mã tracking
     [B] Tự giao: ghi chú giao hàng nội bộ
  → Đơn ở trạng thái "Chờ giao hàng"
  → Cập nhật trạng thái → Đã giao / Không giao được / Trả lại
  → Khi giao xong: thu tiền → tạo phiếu thu
```

### 2.3 Luồng bán hàng online (Online / Social Flow)
```
Khách đặt hàng qua website (WooCommerce) hoặc nhắn tin mạng xã hội
  → Đơn WooCommerce tự đồng bộ vào hệ thống
  → Đơn mạng xã hội: nhân viên tạo đơn thủ công từ admin web
  → Nhân viên xác nhận đơn → chuẩn bị hàng → tạo vận đơn
  → Cập nhật trạng thái → Hoàn thành
```

### 2.4 Luồng nhập hàng
```
Tạo phiếu nhập hàng (chọn nhà cung cấp + danh sách sản phẩm + số lượng + giá nhập)
  → Lưu nháp hoặc hoàn thành
  → Hệ thống tự động: cộng tồn kho + ghi lịch sử nhập
  → Nếu chưa thanh toán NCC: ghi công nợ NCC
  → Khi thanh toán NCC: tạo phiếu chi sổ quỹ + trừ công nợ
```

### 2.5 Luồng thu chi tài chính (Sổ quỹ)
```
Mỗi giao dịch bán hàng → tự động tạo phiếu thu
Mỗi nhập hàng → tự động tạo phiếu chi (nếu thanh toán ngay)
Các khoản thu/chi khác → tạo phiếu thủ công
  → Sổ quỹ tổng hợp theo: tiền mặt / ngân hàng / ví điện tử / từng chi nhánh
  → Báo cáo thu chi cuối ngày / tuần / tháng
```

### 2.6 Luồng chuyển hàng giữa chi nhánh
```
Chi nhánh A tạo yêu cầu chuyển hàng → chọn sản phẩm + số lượng → chọn chi nhánh nhận
  → Quản lý duyệt yêu cầu
  → Chi nhánh A xuất hàng → tồn kho A giảm
  → Chi nhánh B nhận hàng → tồn kho B tăng
  → Lịch sử chuyển hàng được ghi lại
```

---

## 3. Chi tiết tính năng theo Module

---

### Module 1: Dashboard & Tổng quan

**Mục đích**: Trang chủ sau khi đăng nhập, cung cấp cái nhìn nhanh về tình hình kinh doanh.

---

#### 1.1 Bảng điều khiển chính
**Người dùng**: Admin, Quản lý chi nhánh
**Mô tả**: Hiển thị các chỉ số kinh doanh quan trọng theo thời gian thực. Admin thấy toàn chuỗi, quản lý chi nhánh chỉ thấy chi nhánh mình.

**User Story**:
> Với tư cách là quản lý, tôi muốn thấy tổng quan kinh doanh ngay khi đăng nhập để nắm tình hình mà không cần chạy báo cáo riêng.

**Tiêu chí chấp nhận**:
- [ ] Hiển thị doanh thu: hôm nay / 7 ngày / 30 ngày (có thể chuyển đổi)
- [ ] Hiển thị số đơn hàng theo trạng thái (chờ xử lý, đang giao, hoàn thành, hủy)
- [ ] Cảnh báo đỏ/vàng khi có sản phẩm tồn kho dưới mức tối thiểu
- [ ] Biểu đồ doanh thu theo ngày (7 ngày gần nhất)
- [ ] Top 5 sản phẩm bán chạy trong kỳ
- [ ] Danh sách 10 đơn hàng gần nhất
- [ ] Bộ lọc theo chi nhánh (admin thấy tất cả và từng chi nhánh)
- [ ] Dữ liệu tự làm mới mỗi 5 phút hoặc khi F5

**Ghi chú UI/UX**:
```
[Header] Chào buổi sáng, [Tên nhân viên] | Chi nhánh: [Tên chi nhánh] ▼
----------------------------------------------------------------------
[Widget 1]       [Widget 2]       [Widget 3]       [Widget 4]
Doanh thu hôm nay  Số đơn hôm nay   Khách mới        Hàng sắp hết
2,450,000đ ↑12%   18 đơn           3 khách          5 sản phẩm
----------------------------------------------------------------------
[Biểu đồ đường: Doanh thu 7 ngày qua]
----------------------------------------------------------------------
[Bảng: Top SP bán chạy] | [Bảng: Đơn hàng gần đây]
```

[TODO: Screenshot - Dashboard KiotViet tổng quan]

---

### Module 2: Quản lý hàng hóa

**Mục đích**: Quản lý toàn bộ danh mục sản phẩm, biến thể, giá bán, hình ảnh.

---

#### 2.1 Danh sách hàng hóa
**Người dùng**: Admin, Quản lý chi nhánh, Nhân viên kho
**Mô tả**: Xem, tìm kiếm, lọc toàn bộ sản phẩm. Mỗi sản phẩm có thể có nhiều biến thể (màu sắc, size, trọng lượng...).

**User Story**:
> Với tư cách là nhân viên kho, tôi muốn tìm nhanh sản phẩm theo tên/mã/barcode để kiểm tra tồn kho và giá.

**Tiêu chí chấp nhận**:
- [ ] Danh sách dạng bảng: ảnh, mã SP, tên, danh mục, giá bán, tồn kho tổng, trạng thái
- [ ] Tìm kiếm theo: tên sản phẩm, mã SKU, barcode
- [ ] Lọc theo: danh mục, thương hiệu, trạng thái (đang bán / ngừng bán), tồn kho thấp
- [ ] Phân trang hoặc cuộn vô tận (50 SP/trang)
- [ ] Nhấn vào SP để xem chi tiết + danh sách biến thể

**Ghi chú UI/UX**:
```
[Thanh tìm kiếm] [Lọc: Danh mục ▼] [Lọc: Thương hiệu ▼] [Lọc: Trạng thái ▼]
[Nút: + Thêm hàng hóa] [Nút: Import Excel] [Nút: Export]
------------------------------------------------------------------------
| Ảnh | Mã SP   | Tên sản phẩm          | Danh mục | Giá bán | Tồn  |
|-----|---------|----------------------|----------|---------|------|
| 🏸  | VT-001  | Vợt Yonex Astrox 88D  | Vợt cầu  | 2,800k  | 15   |
```

[TODO: Screenshot - Danh sách hàng hóa KiotViet]

---

#### 2.2 Thêm / Sửa hàng hóa
**Người dùng**: Admin, Quản lý chi nhánh
**Mô tả**: Form thêm mới hoặc chỉnh sửa thông tin sản phẩm, bao gồm thông tin cơ bản, hình ảnh, biến thể, giá.

**User Story**:
> Với tư cách là admin, tôi muốn thêm sản phẩm mới với đầy đủ biến thể và giá để nhân viên có thể bán ngay.

**Tiêu chí chấp nhận**:
- [ ] Form có các trường: Tên SP*, Mã SKU (tự sinh nếu để trống), Danh mục, Thương hiệu, Đơn vị tính, Mô tả, Tags
- [ ] Upload tối thiểu 1 ảnh, tối đa 10 ảnh, kéo thả thứ tự
- [ ] Thêm biến thể: chọn thuộc tính (màu, size...) → hệ thống tự sinh bảng biến thể
- [ ] Mỗi biến thể có: SKU riêng, barcode, giá vốn, giá bán, ảnh riêng (tuỳ chọn)
- [ ] Tồn kho nhập từ màn hình này hoặc từ phiếu nhập hàng
- [ ] Cài đặt: bán trực tuyến (sync WooCommerce), áp dụng cho chi nhánh nào
- [ ] Lưu nháp hoặc xuất bản ngay
- [ ] Xác nhận trước khi xóa sản phẩm

**Ghi chú UI/UX**:
```
[Tab: Thông tin chính] [Tab: Hình ảnh] [Tab: Biến thể] [Tab: Tồn kho]
------------------------------------------------------------------------
Thông tin chính:
  Tên sản phẩm*: [________________]
  Danh mục: [Chọn danh mục ▼]     Thương hiệu: [Chọn ▼]
  Đơn vị: [Cái ▼]                 Mã SP: [Auto hoặc tự nhập]
  Giá vốn: [______]               Giá bán*: [______]
  Mô tả: [Text area]

Tab Biến thể:
  + Thêm thuộc tính (Màu sắc / Size / Trọng lượng)
  Tự động tạo bảng tổ hợp biến thể với giá + SKU từng dòng
```

[TODO: Screenshot - Thêm/sửa hàng hóa KiotViet]

---

#### 2.3 Danh mục hàng hóa
**Người dùng**: Admin
**Mô tả**: Quản lý cây danh mục (có thể nhiều cấp). Ví dụ: Vợt cầu lông > Vợt tấn công / Vợt phòng thủ.

**User Story**:
> Với tư cách là admin, tôi muốn tổ chức sản phẩm theo danh mục có phân cấp để khách hàng và nhân viên dễ tìm kiếm.

**Tiêu chí chấp nhận**:
- [ ] Hiển thị dạng cây (tree view) hoặc bảng có cột "Danh mục cha"
- [ ] CRUD danh mục: thêm, sửa tên, xóa (chỉ xóa được danh mục rỗng), sắp xếp thứ tự
- [ ] Tối đa 3 cấp danh mục
- [ ] Đặt ảnh/icon cho danh mục (tuỳ chọn)

---

#### 2.4 Thương hiệu (Brands)
**Người dùng**: Admin
**Mô tả**: Quản lý danh sách thương hiệu để gán cho sản phẩm và lọc báo cáo. Ví dụ: Yonex, Victor, Lining.

**Tiêu chí chấp nhận**:
- [ ] CRUD thương hiệu: tên, logo, mô tả, trạng thái
- [ ] Sắp xếp thứ tự hiển thị

---

#### 2.5 Import / Export danh sách hàng hóa
**Người dùng**: Admin, Quản lý chi nhánh
**Mô tả**: Nhập hàng loạt sản phẩm từ file Excel, hoặc xuất danh sách sản phẩm ra Excel/CSV.

**User Story**:
> Với tư cách là admin, tôi muốn import 500 sản phẩm cùng lúc từ Excel thay vì nhập từng cái một.

**Tiêu chí chấp nhận**:
- [ ] Download file mẫu Excel với đúng cột format
- [ ] Upload file → hệ thống validate trước khi import (hiện báo lỗi dòng nào sai)
- [ ] Import: tạo mới hoặc cập nhật (theo mã SKU)
- [ ] Xem kết quả: X thành công, Y lỗi (kèm chi tiết lỗi)
- [ ] Export: lọc theo danh mục/thương hiệu rồi export

---

### Module 3: Quản lý tồn kho

**Mục đích**: Theo dõi và điều chỉnh tồn kho từng sản phẩm/biến thể tại từng chi nhánh.

---

#### 3.1 Tồn kho theo chi nhánh
**Người dùng**: Admin, Quản lý chi nhánh, Nhân viên kho
**Mô tả**: Xem tồn kho hiện tại của từng sản phẩm tại từng chi nhánh. Cảnh báo khi tồn dưới mức tối thiểu.

**User Story**:
> Với tư cách là quản lý chi nhánh, tôi muốn thấy tồn kho thực tế hiện tại của tất cả sản phẩm tại chi nhánh mình để biết cần đặt thêm hàng gì.

**Tiêu chí chấp nhận**:
- [ ] Bảng tồn kho: sản phẩm, biến thể, chi nhánh, số lượng hiện tại, tồn tối thiểu, trạng thái (đủ/thấp/hết)
- [ ] Lọc theo chi nhánh, danh mục, trạng thái tồn kho
- [ ] Cảnh báo màu đỏ khi tồn = 0, màu vàng khi tồn < tồn tối thiểu
- [ ] Điều chỉnh tồn kho thủ công (có lý do: nhập nhầm, hư hỏng, kiểm kê...)

---

#### 3.2 Phiếu nhập hàng
**Người dùng**: Admin, Quản lý chi nhánh, Nhân viên kho
**Mô tả**: Tạo phiếu nhập hàng từ nhà cung cấp. Sau khi hoàn thành, tồn kho tự động tăng.

**User Story**:
> Với tư cách là nhân viên kho, tôi muốn tạo phiếu nhập hàng khi nhận hàng từ nhà cung cấp để hệ thống tự cộng tồn kho và ghi nợ.

**Tiêu chí chấp nhận**:
- [ ] Chọn nhà cung cấp, chi nhánh nhập, ngày nhập
- [ ] Thêm từng sản phẩm/biến thể: tên, số lượng, giá nhập, thành tiền
- [ ] Tổng tiền phiếu nhập, trạng thái thanh toán (đã thanh toán / còn nợ)
- [ ] Lưu nháp → Hoàn thành (tồn kho tăng) / Hủy
- [ ] Khi hoàn thành: tự tạo phiếu chi nếu thanh toán ngay, hoặc ghi công nợ NCC
- [ ] In phiếu nhập

---

#### 3.3 Chuyển hàng giữa chi nhánh
**Người dùng**: Quản lý chi nhánh, Admin
**Mô tả**: Tạo phiếu chuyển hàng từ chi nhánh này sang chi nhánh khác. Cần được duyệt.

**User Story**:
> Với tư cách là quản lý chi nhánh A, tôi muốn yêu cầu chuyển hàng từ chi nhánh B khi hàng chi nhánh A hết để phục vụ khách kịp thời.

**Tiêu chí chấp nhận**:
- [ ] Tạo yêu cầu chuyển: chọn chi nhánh xuất, chi nhánh nhận, danh sách SP+SL
- [ ] Trạng thái: Chờ duyệt → Đã duyệt → Đang vận chuyển → Hoàn thành / Từ chối
- [ ] Khi hoàn thành: tồn kho chi nhánh xuất giảm, chi nhánh nhận tăng
- [ ] Ghi chú lý do chuyển

---

#### 3.4 Kiểm kho
**Người dùng**: Nhân viên kho, Quản lý chi nhánh
**Mô tả**: So sánh tồn kho thực tế (đếm tay) với tồn kho hệ thống để phát hiện chênh lệch và điều chỉnh.

**Tiêu chí chấp nhận**:
- [ ] Tạo phiếu kiểm kho: chọn chi nhánh, chọn danh mục hoặc toàn bộ
- [ ] Nhập số lượng thực tế từng sản phẩm (cột riêng cạnh số liệu hệ thống)
- [ ] Hệ thống tính chênh lệch (thừa/thiếu)
- [ ] Xác nhận → cập nhật tồn kho theo thực tế + ghi lịch sử
- [ ] Trạng thái: Nháp → Hoàn thành → Đã duyệt

---

#### 3.5 Lịch sử xuất nhập tồn kho
**Người dùng**: Admin, Quản lý chi nhánh, Nhân viên kho
**Mô tả**: Xem toàn bộ lịch sử thay đổi tồn kho của từng sản phẩm/biến thể: bán hàng, nhập hàng, chuyển kho, điều chỉnh...

**Tiêu chí chấp nhận**:
- [ ] Lọc theo: sản phẩm, chi nhánh, loại thao tác, thời gian
- [ ] Hiện: ngày giờ, loại thao tác, số lượng thay đổi (âm/dương), tồn trước, tồn sau, người thực hiện, tham chiếu (đơn hàng/phiếu nhập...)

---

### Module 4: Quản lý đơn hàng

**Mục đích**: Xem, xử lý và theo dõi tất cả đơn hàng từ mọi kênh bán.

---

#### 4.1 Danh sách đơn hàng
**Người dùng**: Admin, Quản lý chi nhánh, Nhân viên bán hàng
**Mô tả**: Toàn bộ đơn hàng từ POS, WooCommerce, và đơn thủ công. Mỗi đơn có mã, trạng thái, kênh bán, giá trị.

**Tiêu chí chấp nhận**:
- [ ] Bảng đơn hàng: mã đơn, ngày tạo, khách hàng, tổng tiền, phương thức thanh toán, trạng thái, kênh bán, chi nhánh
- [ ] Lọc theo: trạng thái, kênh bán, chi nhánh, thời gian, nhân viên tạo
- [ ] Tìm kiếm theo mã đơn, tên khách, SĐT khách
- [ ] Click xem chi tiết đơn
- [ ] Export danh sách ra Excel

---

#### 4.2 Chi tiết đơn hàng
**Người dùng**: Admin, Quản lý chi nhánh, Nhân viên bán hàng
**Mô tả**: Xem đầy đủ thông tin một đơn hàng, cập nhật trạng thái, in hóa đơn.

**Tiêu chí chấp nhận**:
- [ ] Hiển thị: mã đơn, ngày tạo, kênh bán, chi nhánh, nhân viên tạo
- [ ] Danh sách sản phẩm: tên, SKU, số lượng, đơn giá, chiết khấu, thành tiền
- [ ] Tóm tắt: tổng tiền hàng, giảm giá, phí ship, tổng thanh toán
- [ ] Thông tin thanh toán: phương thức, số tiền nhận, tiền thừa
- [ ] Thông tin giao hàng (nếu có): tên, SĐT, địa chỉ, đơn vị vận chuyển, mã vận đơn
- [ ] Lịch sử trạng thái đơn (timeline)
- [ ] Nút: Cập nhật trạng thái / In hóa đơn / Tạo đơn trả hàng

---

#### 4.3 Cập nhật trạng thái đơn hàng
**Người dùng**: Admin, Quản lý chi nhánh, Nhân viên bán hàng
**Mô tả**: Chuyển trạng thái đơn theo luồng xử lý. Mỗi thay đổi trạng thái được ghi log kèm người thực hiện.

**Tiêu chí chấp nhận**:
- [ ] Luồng trạng thái: Nháp → Xác nhận → Đang đóng gói → Đang giao → Hoàn thành / Hủy / Trả hàng
- [ ] Cho phép ghi chú khi đổi trạng thái
- [ ] Khi hủy đơn: hỏi lý do, tự động hoàn tồn kho
- [ ] Khi hoàn thành: xác nhận đã thu tiền (nếu chưa)

---

#### 4.4 Đơn trả hàng / Hoàn tiền
**Người dùng**: Admin, Quản lý chi nhánh
**Mô tả**: Xử lý khi khách trả hàng. Tồn kho tăng lại, tạo phiếu chi hoàn tiền.

**Tiêu chí chấp nhận**:
- [ ] Tạo từ đơn gốc: chọn sản phẩm và số lượng cần trả
- [ ] Chọn lý do trả: lỗi hàng, giao sai, khách đổi ý...
- [ ] Tự động tạo phiếu chi (hoàn tiền cho khách) theo phương thức thanh toán ban đầu
- [ ] Tồn kho tự động tăng lại theo số lượng trả

---

### Module 5: Quản lý khách hàng (CRM)

**Mục đích**: Lưu trữ thông tin khách hàng, theo dõi lịch sử mua hàng, quản lý công nợ và tích điểm.

---

#### 5.1 Danh sách khách hàng
**Người dùng**: Admin, Quản lý chi nhánh, Nhân viên bán hàng
**Mô tả**: Toàn bộ khách hàng đã mua hoặc được tạo trong hệ thống.

**Tiêu chí chấp nhận**:
- [ ] Bảng: mã KH, tên, SĐT, loại KH (thường/VIP/xấu nợ), tổng chi tiêu, công nợ, lần mua cuối
- [ ] Tìm kiếm theo tên, SĐT, mã KH
- [ ] Lọc theo loại KH, chi nhánh, khoảng thời gian mua hàng
- [ ] Export danh sách

---

#### 5.2 Thêm / Sửa khách hàng
**Người dùng**: Admin, Nhân viên bán hàng
**Mô tả**: Tạo mới hoặc cập nhật thông tin khách hàng.

**Tiêu chí chấp nhận**:
- [ ] Trường thông tin: tên*, SĐT* (unique), email, địa chỉ, ngày sinh, giới tính, tags, ghi chú
- [ ] Mã khách hàng tự sinh (KH-XXXX)
- [ ] Kiểm tra trùng SĐT khi tạo mới
- [ ] Gán nhóm khách hàng / tags

---

#### 5.3 Chi tiết khách hàng
**Người dùng**: Admin, Quản lý chi nhánh
**Mô tả**: Hồ sơ đầy đủ của một khách hàng: thông tin cá nhân, lịch sử mua hàng, công nợ, điểm tích lũy.

**Tiêu chí chấp nhận**:
- [ ] Thông tin cơ bản + thống kê: tổng đơn, tổng chi tiêu, đơn gần nhất, điểm tích lũy, công nợ
- [ ] Tab lịch sử đơn hàng: xem tất cả đơn đã mua
- [ ] Tab công nợ: danh sách khoản nợ, lịch sử thanh toán nợ
- [ ] Tab điểm tích lũy: số điểm hiện có, lịch sử tích/tiêu điểm
- [ ] Chức năng: Thu nợ (tạo phiếu thu), Đổi điểm

---

#### 5.4 Phân loại khách hàng
**Người dùng**: Admin
**Mô tả**: Hệ thống tự động xếp loại khách dựa trên hành vi mua hàng. Admin cũng có thể set thủ công.

**Tiêu chí chấp nhận**:
- [ ] Loại KH: Khách lẻ, Khách thường xuyên (VIP), Khách tiềm năng, Khách nợ xấu
- [ ] Điều kiện tự động phân loại (config được): VIP nếu chi tiêu > X triệu hoặc > Y đơn/tháng
- [ ] Admin có thể override loại thủ công
- [ ] Hiển thị badge loại KH ở danh sách và chi tiết

---

#### 5.5 Tích điểm khách hàng (Loyalty)
**Người dùng**: Admin (cấu hình), Nhân viên bán hàng (áp dụng khi bán)
**Mô tả**: Khách tích điểm khi mua hàng, dùng điểm để giảm giá đơn hàng.

**Tiêu chí chấp nhận**:
- [ ] Cấu hình tỉ lệ tích điểm: X đồng = 1 điểm
- [ ] Cấu hình đổi điểm: Y điểm = Z đồng giảm giá
- [ ] Khi bán hàng: nhân viên xem điểm khách, hỏi có muốn dùng điểm không
- [ ] Điểm tự động cộng sau khi đơn hoàn thành
- [ ] Lịch sử tích/tiêu điểm trong hồ sơ KH

---

#### 5.6 Công nợ khách hàng
**Người dùng**: Admin, Quản lý chi nhánh
**Mô tả**: Khách mua hàng chưa trả tiền ngay (mua nợ). Theo dõi và thu hồi công nợ.

**Tiêu chí chấp nhận**:
- [ ] Khi tạo đơn có thể chọn "Mua nợ" → ghi vào công nợ KH
- [ ] Danh sách công nợ: KH nào, số tiền, từ đơn nào, ngày tạo nợ, hạn thanh toán
- [ ] Thu nợ: nhập số tiền thu → tạo phiếu thu → trừ công nợ
- [ ] Cảnh báo KH có công nợ khi tạo đơn mới

---

#### 5.7 Đặt hàng / Đơn đặt chuyển chi nhánh
**Người dùng**: Nhân viên bán hàng
**Mô tả**: Khách muốn sản phẩm không có ở chi nhánh hiện tại → tạo đơn đặt, hệ thống kiểm tra tồn kho chi nhánh khác.

**Tiêu chí chấp nhận**:
- [ ] Khi tạo đơn, nếu SP hết tại chi nhánh hiện tại, hệ thống gợi ý chi nhánh có hàng
- [ ] Tạo đơn đặt hàng chờ chuyển kho hoặc khách đến chi nhánh khác
- [ ] Trạng thái đơn đặt: Chờ hàng → Có hàng → Đã giao

---

### Module 6: Quản lý nhà cung cấp

**Mục đích**: Quản lý danh sách nhà cung cấp, lịch sử nhập hàng và công nợ phải trả.

---

#### 6.1 Danh sách nhà cung cấp
**Người dùng**: Admin, Quản lý chi nhánh
**Mô tả**: Danh sách tất cả nhà cung cấp hàng hóa của cửa hàng.

**Tiêu chí chấp nhận**:
- [ ] Bảng: tên NCC, SĐT, địa chỉ, người liên hệ, tổng nợ hiện tại, trạng thái
- [ ] Tìm kiếm theo tên, SĐT
- [ ] Lọc theo trạng thái, có/không công nợ

---

#### 6.2 Thêm / Sửa nhà cung cấp
**Tiêu chí chấp nhận**:
- [ ] Trường: tên*, SĐT, email, địa chỉ, người liên hệ, ghi chú, trạng thái hoạt động
- [ ] Mã NCC tự sinh

---

#### 6.3 Chi tiết nhà cung cấp
**Mô tả**: Xem lịch sử nhập hàng từ NCC, công nợ phải trả.

**Tiêu chí chấp nhận**:
- [ ] Thống kê: tổng đã nhập, tổng đã trả, còn nợ
- [ ] Tab lịch sử phiếu nhập
- [ ] Tab công nợ + lịch sử thanh toán
- [ ] Thanh toán nợ: nhập số tiền → tạo phiếu chi

---

### Module 7: Quản lý nhân viên

**Mục đích**: Quản lý thông tin nhân viên, phân ca, tính lương và hoa hồng.

---

#### 7.1 Danh sách nhân viên
**Người dùng**: Admin, Quản lý chi nhánh
**Mô tả**: Danh sách tất cả nhân viên, chi nhánh làm việc, vai trò, trạng thái.

**Tiêu chí chấp nhận**:
- [ ] Bảng: ảnh đại diện, tên, mã NV, SĐT, chi nhánh, vai trò, trạng thái (đang làm/nghỉ)
- [ ] Tìm kiếm theo tên, mã, SĐT
- [ ] Lọc theo chi nhánh, vai trò, trạng thái

---

#### 7.2 Thêm / Sửa nhân viên
**Tiêu chí chấp nhận**:
- [ ] Thông tin: họ tên*, SĐT*, email, CCCD, địa chỉ, ngày sinh, ngày vào làm, chi nhánh, vai trò
- [ ] Tài khoản đăng nhập: username, mật khẩu (có thể tự động gửi qua email/SĐT)
- [ ] Ảnh đại diện
- [ ] Cấu hình lương: lương cố định, % hoa hồng trên doanh số

---

#### 7.3 Phân ca làm việc
**Người dùng**: Quản lý chi nhánh
**Mô tả**: Lên lịch ca làm việc cho nhân viên theo tuần/tháng.

**Tiêu chí chấp nhận**:
- [ ] Xem lịch ca dạng grid (ngày × nhân viên)
- [ ] Thêm ca: chọn nhân viên, ngày, giờ vào/ra
- [ ] Nhân viên thấy lịch ca của mình
- [ ] Cảnh báo khi có nhân viên trùng ca hoặc thiếu ca

---

#### 7.4 Bảng lương & Hoa hồng
**Người dùng**: Admin, Quản lý chi nhánh
**Mô tả**: Tính lương tháng cho nhân viên dựa trên lương cố định + hoa hồng doanh số.

**User Story**:
> Với tư cách là admin, tôi muốn hệ thống tự tính lương nhân viên cuối tháng để tiết kiệm thời gian và tránh nhầm lẫn.

**Tiêu chí chấp nhận**:
- [ ] Tính lương theo tháng: lương cơ bản + (tổng doanh số NV × % hoa hồng)
- [ ] Hiển thị bảng lương từng nhân viên: doanh số, hoa hồng, lương cố định, tổng lương
- [ ] Duyệt bảng lương → tạo phiếu chi hàng loạt
- [ ] Export bảng lương ra Excel

---

#### 7.5 KPI & Hiệu suất bán hàng
**Người dùng**: Admin, Quản lý chi nhánh
**Mô tả**: Xem doanh số, số đơn của từng nhân viên trong kỳ.

**Tiêu chí chấp nhận**:
- [ ] Bảng xếp hạng nhân viên theo doanh số trong kỳ
- [ ] Chi tiết: số đơn, tổng doanh thu, trung bình đơn hàng
- [ ] Lọc theo chi nhánh, khoảng thời gian

---

### Module 8: Phân quyền & Vai trò

**Mục đích**: Kiểm soát ai được làm gì trong hệ thống.

---

#### 8.1 Vai trò hệ thống
**Người dùng**: Admin
**Mô tả**: Hệ thống có các vai trò mặc định, mỗi vai trò có bộ quyền riêng.

**Các vai trò mặc định**:
- **Admin**: Toàn quyền, thấy tất cả chi nhánh
- **Quản lý chi nhánh** (Branch Manager): Quản lý chi nhánh mình, không thấy tài chính toàn hệ thống
- **Nhân viên bán hàng** (Cashier): Chỉ dùng POS, xem đơn hàng, tạo KH
- **Nhân viên kho** (Warehouse): Nhập hàng, kiểm kho, chuyển hàng, không thấy tài chính

**Tiêu chí chấp nhận**:
- [ ] Hiển thị danh sách vai trò + bộ quyền tương ứng
- [ ] Admin có thể tạo vai trò tùy chỉnh
- [ ] Gán vai trò cho nhân viên khi thêm/sửa

---

#### 8.2 Phân quyền chi tiết
**Người dùng**: Admin
**Mô tả**: Cấu hình chi tiết từng quyền cho từng vai trò (xem / tạo / sửa / xóa theo từng module).

**Tiêu chí chấp nhận**:
- [ ] Matrix quyền: [Vai trò] × [Module] × [Hành động: view/create/edit/delete]
- [ ] Thay đổi quyền có hiệu lực ngay
- [ ] Không thể xóa hoặc thu hẹp quyền của vai trò Admin

---

### Module 9: Sổ quỹ (Cash Management)

**Mục đích**: Theo dõi toàn bộ luồng tiền vào/ra của cửa hàng theo từng hình thức thanh toán và từng chi nhánh.

---

#### 9.1 Phiếu thu
**Người dùng**: Admin, Quản lý chi nhánh, Nhân viên bán hàng
**Mô tả**: Ghi nhận tiền vào quỹ. Tự động tạo từ bán hàng, cũng có thể tạo thủ công (thu nợ KH, thu khác...).

**User Story**:
> Với tư cách là nhân viên bán hàng, tôi muốn khi chốt đơn xong hệ thống tự tạo phiếu thu để tôi không cần nhập lại, tránh sót.

**Tiêu chí chấp nhận**:
- [ ] Phiếu thu tự động tạo khi: hoàn thành đơn hàng, khách trả nợ
- [ ] Phiếu thu thủ công: chọn loại thu (bán hàng/thu nợ/thu khác), số tiền, phương thức, ghi chú
- [ ] Loại phương thức: tiền mặt, chuyển khoản ngân hàng, ví điện tử (MoMo/ZaloPay...)
- [ ] Mỗi phiếu có mã tự sinh, ngày giờ, người tạo, chi nhánh
- [ ] In phiếu thu

---

#### 9.2 Phiếu chi
**Người dùng**: Admin, Quản lý chi nhánh
**Mô tả**: Ghi nhận tiền ra khỏi quỹ. Tự động tạo khi thanh toán NCC, trả lương, hoàn tiền KH. Cũng tạo thủ công.

**Tiêu chí chấp nhận**:
- [ ] Phiếu chi tự động: thanh toán nhập hàng, hoàn tiền đơn trả, chi lương
- [ ] Phiếu chi thủ công: chọn loại chi (nhập hàng/lương/thuê mặt bằng/khác), số tiền, phương thức, ghi chú
- [ ] Mỗi phiếu có mã, ngày giờ, người tạo, chi nhánh
- [ ] In phiếu chi

---

#### 9.3 Sổ quỹ tổng hợp
**Người dùng**: Admin, Quản lý chi nhánh
**Mô tả**: Xem toàn bộ thu chi trong kỳ, số dư hiện tại theo từng hình thức (tiền mặt / ngân hàng / ví điện tử).

**Tiêu chí chấp nhận**:
- [ ] Tab: Tiền mặt / Ngân hàng / Ví điện tử (hoặc xem tổng hợp tất cả)
- [ ] Số dư đầu kỳ + Tổng thu trong kỳ - Tổng chi trong kỳ = Số dư cuối kỳ
- [ ] Danh sách giao dịch theo thời gian (mới nhất lên trên)
- [ ] Lọc theo: chi nhánh, khoảng ngày, loại giao dịch, phương thức
- [ ] Export ra Excel

---

#### 9.4 Tài khoản ngân hàng theo chi nhánh
**Người dùng**: Admin
**Mô tả**: Mỗi chi nhánh có thể có một hoặc nhiều tài khoản ngân hàng riêng. Tiền chuyển khoản vào đúng tài khoản chi nhánh đó.

**Tiêu chí chấp nhận**:
- [ ] Thêm/sửa tài khoản: tên ngân hàng, số TK, chủ TK, mã BIN (cho VietQR)
- [ ] Gán tài khoản cho chi nhánh (một chi nhánh nhiều TK, chọn TK mặc định)
- [ ] Khi thanh toán chuyển khoản tại POS: hiện QR code đúng TK của chi nhánh đó
- [ ] Sổ quỹ ngân hàng tách biệt theo từng TK ngân hàng

---

#### 9.5 Mở ca / Đóng ca (Ca làm việc)
**Người dùng**: Nhân viên bán hàng, Quản lý chi nhánh
**Mô tả**: Nhân viên mở ca khi bắt đầu làm, khai báo tiền mặt đầu ca. Khi kết thúc ca, đếm tiền thực tế và đối chiếu với hệ thống.

**User Story**:
> Với tư cách là nhân viên, tôi muốn mở và đóng ca để hệ thống biết tôi đang làm ca nào, tiền mặt trong tay là bao nhiêu, tránh tranh chấp.

**Tiêu chí chấp nhận**:
- [ ] Mở ca: nhập số tiền mặt đầu ca → ghi nhận giờ bắt đầu
- [ ] Trong ca: tất cả giao dịch tiền mặt được ghi vào ca
- [ ] Đóng ca: nhập số tiền mặt thực tế → hệ thống tính chênh lệch (thừa/thiếu)
- [ ] Báo cáo ca: tổng thu, tổng chi, tiền mặt kỳ vọng, thực tế, chênh lệch
- [ ] Nhân viên ký xác nhận (ghi chú lý do nếu có chênh lệch)
- [ ] In báo cáo ca

---

### Module 10: Màn hình bán hàng (POS)

**Mục đích**: Giao diện bán hàng tại quầy, được tối ưu cho tablet/cảm ứng, hỗ trợ bán nhanh, bán thường và bán giao hàng.

---

#### 10.1 Giao diện POS chung
**Người dùng**: Nhân viên bán hàng
**Mô tả**: Màn hình chính khi vào chế độ bán hàng. Layout 2 cột: trái tìm/chọn sản phẩm, phải là giỏ hàng.

**Tiêu chí chấp nhận**:
- [ ] Layout cố định 2 cột (không scroll ngang)
- [ ] Cột trái: ô tìm kiếm (ưu tiên tìm barcode/scan) + lưới sản phẩm dạng thẻ có ảnh
- [ ] Cột phải: giỏ hàng + tổng tiền + nút thanh toán
- [ ] Trên cùng: tên nhân viên, tên chi nhánh, giờ hiện tại, nút chuyển tab
- [ ] Hỗ trợ quét mã vạch qua camera tablet hoặc máy quét USB
- [ ] Font size đủ lớn để đọc từ xa

**Ghi chú UI/UX**:
```
+--[Ca làm: Nguyễn A] [Chi nhánh: Q1] [08:32] [Tab 1 ✕][Tab 2 +]--+
|                                                                     |
| [🔍 Tìm SP hoặc quét barcode_____] [Loại: Tất cả ▼]              |  [Giỏ hàng]
|                                                                     |
| [Vợt Yonex]  [Dây căng]  [Bao vợt]  [Giày cầu]                   |  SP 1: Yonex 88D x1  2,800k
| [Cầu lông]   [Quần áo]   [Phụ kiện]                               |  SP 2: Dây BG65  x2    160k
|                                                                     |  ──────────────────────────
|                                                                     |  Tổng:             2,960k
|                                                                     |  [Chọn KH] [Giảm giá]
|                                                                     |  [THANH TOÁN]
+--------------------------------------------------------------------+
```

[TODO: Screenshot - Màn hình POS KiotViet]

---

#### 10.2 Bán hàng nhanh (Quick Sale)
**Người dùng**: Nhân viên bán hàng
**Mô tả**: Bán tại quầy không cần thông tin giao hàng. Phù hợp với đa số giao dịch thông thường.

**Tiêu chí chấp nhận**:
- [ ] Thêm sản phẩm vào giỏ (scan mã hoặc click thẻ)
- [ ] Điều chỉnh số lượng trực tiếp trong giỏ
- [ ] Giảm giá: theo số tiền hoặc phần trăm, trên từng dòng hoặc tổng đơn
- [ ] Chọn khách hàng (tuỳ chọn): tìm theo SĐT, tự động hiện tên + điểm + nợ
- [ ] Thanh toán: chọn phương thức (tiền mặt / chuyển khoản / kết hợp)
  - Tiền mặt: nhập tiền nhận → tính tiền thừa
  - Chuyển khoản: hiện QR code VietQR của chi nhánh
- [ ] Xác nhận thanh toán → in hóa đơn nhiệt / gửi SMS (tuỳ chọn)
- [ ] Tự động tạo phiếu thu + trừ tồn kho

---

#### 10.3 Bán hàng thường (Regular Sale)
**Người dùng**: Nhân viên bán hàng
**Mô tả**: Tương tự bán nhanh nhưng có thêm thông tin ghi chú, yêu cầu đặc biệt, lưu đơn nháp.

**Tiêu chí chấp nhận**:
- [ ] Tất cả tính năng bán nhanh +
- [ ] Lưu đơn nháp (để đó phục vụ KH khác, quay lại sau)
- [ ] Thêm ghi chú cho đơn và cho từng dòng sản phẩm
- [ ] Chọn kênh bán (tại quầy / Facebook / Zalo / khác)
- [ ] Thanh toán một phần (đặt cọc) + ghi nợ phần còn lại

---

#### 10.4 Bán giao hàng – Đơn vị vận chuyển
**Người dùng**: Nhân viên bán hàng
**Mô tả**: Tạo đơn cần giao hàng qua đơn vị vận chuyển (GHTK, GHN...). Hệ thống tạo vận đơn và lấy tracking code.

**Tiêu chí chấp nhận**:
- [ ] Form thông tin giao hàng: tên người nhận, SĐT, địa chỉ (tỉnh/huyện/xã), ghi chú giao hàng
- [ ] Chọn đơn vị vận chuyển (dropdown các đơn vị đã tích hợp)
- [ ] Chọn hình thức thanh toán ship: người gửi trả / người nhận trả (COD)
- [ ] Nhập phí ship hoặc tự tính theo API đơn vị vận chuyển
- [ ] Tạo đơn → lấy mã vận đơn tự động
- [ ] In phiếu giao hàng

---

#### 10.5 Bán giao hàng – Tự giao
**Người dùng**: Nhân viên bán hàng
**Mô tả**: Giao hàng bằng nhân viên nội bộ (không qua đơn vị vận chuyển bên ngoài).

**Tiêu chí chấp nhận**:
- [ ] Form thông tin giao: tên, SĐT, địa chỉ, ghi chú
- [ ] Gán nhân viên giao hàng
- [ ] Nhập phí ship (thu của khách hoặc miễn phí)
- [ ] Trạng thái giao: Chờ lấy hàng → Đang giao → Giao thành công / Thất bại
- [ ] Khi giao thành công: thu tiền (COD) → tạo phiếu thu

---

#### 10.6 Quản lý đa tab
**Người dùng**: Nhân viên bán hàng
**Mô tả**: Cho phép mở nhiều tab đơn hàng song song (phục vụ nhiều khách cùng lúc).

**Tiêu chí chấp nhận**:
- [ ] Mở tối đa 5 tab đơn hàng cùng lúc
- [ ] Chuyển tab không mất dữ liệu giỏ hàng tab khác
- [ ] Đặt tên tab hoặc tab hiển thị tên KH (nếu đã chọn KH)
- [ ] Đóng tab xóa giỏ hàng (có xác nhận)

---

#### 10.7 In hóa đơn nhiệt
**Người dùng**: Nhân viên bán hàng
**Mô tả**: In hóa đơn ra máy in nhiệt (thermal printer) sau khi thanh toán thành công.

**Tiêu chí chấp nhận**:
- [ ] Kết nối máy in nhiệt qua USB (WebUSB) hoặc Bluetooth
- [ ] Mẫu hóa đơn: logo cửa hàng, địa chỉ chi nhánh, ngày giờ, danh sách SP, tổng tiền, phương thức TT, nhân viên
- [ ] In tự động ngay sau thanh toán (có thể tắt)
- [ ] In lại hóa đơn từ màn hình chi tiết đơn

---

#### 10.8 Offline mode (POS hoạt động khi mất mạng)
**Người dùng**: Nhân viên bán hàng
**Mô tả**: POS vẫn hoạt động bình thường khi mất kết nối internet. Dữ liệu đồng bộ lại khi có mạng.

**Tiêu chí chấp nhận**:
- [ ] Khi mất mạng: hiện thông báo "Đang offline – dữ liệu lưu tạm"
- [ ] Vẫn bán được: tìm SP từ cache local, tạo đơn, thanh toán
- [ ] Khi có mạng trở lại: tự động sync đơn hàng offline lên server
- [ ] Cảnh báo nếu sync thất bại (cần xử lý thủ công)
- [ ] Danh sách SP được cache lúc mở ca / đăng nhập

---

### Module 11: Quản lý chi nhánh

**Mục đích**: Quản lý thông tin và cài đặt từng chi nhánh trong chuỗi cửa hàng.

---

#### 11.1 Danh sách chi nhánh
**Người dùng**: Admin
**Mô tả**: Xem toàn bộ chi nhánh trong hệ thống.

**Tiêu chí chấp nhận**:
- [ ] Bảng: tên, địa chỉ, SĐT, số nhân viên, trạng thái (hoạt động/tạm đóng)
- [ ] Click vào chi nhánh để xem/chỉnh sửa chi tiết

---

#### 11.2 Thêm / Sửa chi nhánh
**Tiêu chí chấp nhận**:
- [ ] Thông tin: tên chi nhánh*, địa chỉ*, SĐT, email, giờ mở/đóng cửa
- [ ] Cài đặt chi nhánh: thuế suất mặc định, mẫu hóa đơn mặc định
- [ ] Tài khoản ngân hàng riêng (thêm/sửa/xóa nhiều TK)
- [ ] Trạng thái hoạt động

---

#### 11.3 Phân bổ hàng hóa theo chi nhánh
**Mô tả**: Mỗi chi nhánh có thể bán một tập hàng hóa khác nhau.

**Tiêu chí chấp nhận**:
- [ ] Khi thêm sản phẩm, chọn chi nhánh nào được bán
- [ ] Quản lý chi nhánh chỉ thấy SP được phân bổ cho chi nhánh mình

---

### Module 12: Báo cáo & Phân tích

**Mục đích**: Cung cấp số liệu kinh doanh để ra quyết định.

---

#### 12.1 Báo cáo doanh thu
**Người dùng**: Admin, Quản lý chi nhánh
**Mô tả**: Doanh thu tổng hợp theo ngày/tuần/tháng/năm, so sánh kỳ trước.

**Tiêu chí chấp nhận**:
- [ ] Biểu đồ doanh thu theo thời gian (đường, cột)
- [ ] Bảng tóm tắt: tổng doanh thu, số đơn, giá trị đơn trung bình, tỉ lệ hoàn hàng
- [ ] So sánh với kỳ trước (tăng/giảm %)
- [ ] Lọc theo chi nhánh, kênh bán, nhân viên, khoảng thời gian tùy chỉnh

---

#### 12.2 Báo cáo hàng hóa
**Mô tả**: Sản phẩm bán chạy nhất, ít bán, doanh thu theo danh mục.

**Tiêu chí chấp nhận**:
- [ ] Top N sản phẩm bán chạy (số lượng + doanh thu)
- [ ] Sản phẩm không bán được trong kỳ
- [ ] Doanh thu theo danh mục / thương hiệu
- [ ] Lãi gộp theo sản phẩm (giá bán - giá vốn)

---

#### 12.3 Báo cáo tồn kho
**Mô tả**: Giá trị tồn kho hiện tại, hàng sắp hết, hàng tồn lâu.

**Tiêu chí chấp nhận**:
- [ ] Tổng giá trị tồn kho (theo giá vốn) theo chi nhánh
- [ ] Danh sách hàng dưới mức tối thiểu
- [ ] Hàng tồn lâu (chưa bán trong X ngày)
- [ ] Lịch sử nhập xuất tổng hợp

---

#### 12.4 Báo cáo khách hàng
**Mô tả**: Phân tích tệp khách hàng, KH mua nhiều nhất, KH có nợ.

**Tiêu chí chấp nhận**:
- [ ] Top KH chi tiêu cao nhất
- [ ] KH mới trong kỳ vs. KH cũ quay lại
- [ ] Danh sách KH có công nợ tổng hợp
- [ ] Phân bố KH theo loại (thường/VIP...)

---

#### 12.5 Báo cáo nhân viên
**Mô tả**: Doanh số từng nhân viên, số đơn, hoa hồng.

**Tiêu chí chấp nhận**:
- [ ] Bảng xếp hạng nhân viên theo doanh số trong kỳ
- [ ] Chi tiết từng NV: số đơn, doanh thu, hoa hồng dự kiến
- [ ] Lọc theo chi nhánh, khoảng thời gian

---

#### 12.6 Báo cáo sổ quỹ (Thu/Chi)
**Mô tả**: Tổng hợp thu chi theo kỳ, phân loại theo danh mục và phương thức.

**Tiêu chí chấp nhận**:
- [ ] Tổng thu / Tổng chi / Số dư cuối kỳ
- [ ] Phân tích theo loại thu/chi (bán hàng / nhập hàng / lương / khác)
- [ ] Theo phương thức (tiền mặt / ngân hàng / ví điện tử)
- [ ] Lọc theo chi nhánh, khoảng ngày

---

#### 12.7 Export báo cáo
**Tiêu chí chấp nhận**:
- [ ] Tất cả báo cáo có nút "Export Excel" và "Export PDF"
- [ ] PDF có header logo cửa hàng, ngày xuất, người xuất

---

### Module 13: Kênh bán hàng & Tích hợp

**Mục đích**: Kết nối hệ thống với các kênh bán hàng online và đối tác vận chuyển.

---

#### 13.1 Đồng bộ WooCommerce
**Người dùng**: Admin
**Mô tả**: Đồng bộ 2 chiều sản phẩm và đơn hàng giữa FbShop và website WooCommerce.

**User Story**:
> Với tư cách là admin, tôi muốn khi thêm sản phẩm mới hoặc cập nhật giá trong FbShop, website WooCommerce tự cập nhật theo mà không cần làm thủ công.

**Tiêu chí chấp nhận**:
- [ ] Cấu hình kết nối: nhập WooCommerce URL + API Key
- [ ] Đồng bộ sản phẩm từ FbShop lên Woo (tên, giá, ảnh, tồn kho)
- [ ] Đơn hàng từ Woo tự động tải về FbShop qua webhook
- [ ] Khi bán tại quầy, tồn kho Woo tự giảm theo
- [ ] Xem trạng thái đồng bộ: lần sync cuối, lỗi nếu có
- [ ] Sync thủ công khi cần

---

#### 13.2 Đơn hàng mạng xã hội (Thủ công)
**Người dùng**: Nhân viên bán hàng
**Mô tả**: Khách nhắn tin qua Facebook/Zalo/Tiktok → nhân viên tạo đơn thủ công trong hệ thống.

**Tiêu chí chấp nhận**:
- [ ] Tạo đơn từ Admin web hoặc POS với kênh = "Facebook" / "Zalo" / "Khác"
- [ ] Nhập thông tin KH, SP, địa chỉ giao (nếu giao hàng)
- [ ] Xử lý như đơn bình thường (trạng thái, giao hàng, thanh toán)

---

#### 13.3 Đơn vị vận chuyển
**Người dùng**: Admin (cấu hình), Nhân viên bán hàng (sử dụng)
**Mô tả**: Tích hợp với các đơn vị vận chuyển để tạo vận đơn và tra cứu trạng thái giao hàng.

**Tiêu chí chấp nhận**:
- [ ] Cấu hình API key cho từng đơn vị (GHN, GHTK, J&T...)
- [ ] Khi tạo đơn giao hàng: chọn đơn vị, nhập địa chỉ → tạo vận đơn → lấy tracking code
- [ ] Xem trạng thái vận đơn từ trong hệ thống (không cần vào website ĐVVC)
- [ ] Tính phí ship tự động dựa trên địa chỉ (nếu API hỗ trợ)

---

#### 13.4 Thanh toán VietQR
**Người dùng**: Nhân viên bán hàng, Khách hàng
**Mô tả**: Tạo mã QR động (VietQR) cho từng đơn hàng để khách quét và chuyển khoản chính xác.

**Tiêu chí chấp nhận**:
- [ ] Khi thanh toán chọn "Chuyển khoản": hiện QR code với số tiền và nội dung đúng đơn hàng
- [ ] QR code theo tài khoản ngân hàng của chi nhánh hiện tại
- [ ] Có thể xác nhận thủ công khi nhận được tiền (Phase 1)
- [ ] Tự động xác nhận qua webhook ngân hàng (Phase 2+)

---

### Module 14: Cài đặt hệ thống

**Mục đích**: Cấu hình toàn bộ hoạt động của hệ thống theo nhu cầu riêng.

---

#### 14.1 Thông tin cửa hàng / Công ty
**Người dùng**: Admin
**Tiêu chí chấp nhận**:
- [ ] Tên công ty, logo, địa chỉ, SĐT, email, website, mã số thuế
- [ ] Thông tin xuất hiện trên hóa đơn in

---

#### 14.2 Mẫu hóa đơn in
**Người dùng**: Admin
**Mô tả**: Tùy chỉnh mẫu hóa đơn nhiệt (80mm) và hóa đơn A4.

**Tiêu chí chấp nhận**:
- [ ] Chọn các thành phần hiển thị: logo, địa chỉ, barcode đơn, QR code, slogan, ghi chú cuối...
- [ ] Preview mẫu trước khi lưu
- [ ] Tạo nhiều mẫu cho các mục đích khác nhau (bán hàng, trả hàng, kiểm kho...)

---

#### 14.3 Cài đặt thuế & Phí
**Người dùng**: Admin
**Tiêu chí chấp nhận**:
- [ ] Thuế VAT mặc định (0% / 8% / 10%)
- [ ] Có thể set thuế khác nhau cho từng chi nhánh

---

#### 14.4 Cài đặt điểm tích lũy
**Người dùng**: Admin
**Tiêu chí chấp nhận**:
- [ ] Bật/tắt chương trình tích điểm
- [ ] Tỉ lệ tích: X đồng = 1 điểm
- [ ] Tỉ lệ đổi: Y điểm = Z đồng
- [ ] Điểm tối thiểu để đổi

---

#### 14.5 Nhật ký hoạt động (Audit Log)
**Người dùng**: Admin
**Mô tả**: Ghi lại mọi thao tác quan trọng trong hệ thống.

**Tiêu chí chấp nhận**:
- [ ] Ghi log: ai làm gì, lúc nào, với đối tượng nào, giá trị trước/sau
- [ ] Tìm kiếm log theo người dùng, thời gian, loại hành động
- [ ] Log không thể xóa (chỉ đọc)

---

#### 14.6 Cài đặt thông báo
**Người dùng**: Admin
**Mô tả**: Cấu hình khi nào hệ thống gửi thông báo (trong app, SMS, email).

**Tiêu chí chấp nhận**:
- [ ] Cài đặt loại thông báo: tồn kho thấp, đơn hàng mới, chuyển khoản nhận được
- [ ] Chọn kênh: in-app / email / SMS (nếu tích hợp)
- [ ] Cài đặt người nhận thông báo theo loại

---

## 4. Yêu cầu phi chức năng (Non-functional Requirements)

### 4.1 Hiệu năng
- Trang admin load dưới 3 giây trên kết nối 10Mbps
- POS xử lý thanh toán dưới 2 giây
- Hỗ trợ tối thiểu 3 chi nhánh hoạt động đồng thời

### 4.2 Bảo mật
- JWT authentication (access token ngắn + refresh token dài)
- Mỗi API request kiểm tra quyền truy cập theo vai trò + chi nhánh
- Mật khẩu mã hóa bcrypt
- Không lưu thông tin thẻ ngân hàng
- HTTPS bắt buộc

### 4.3 Khả dụng (Availability)
- Uptime tối thiểu 99% trong giờ hành chính
- POS offline mode: bán được ít nhất 4 giờ khi mất mạng

### 4.4 Khả năng mở rộng
- Có thể thêm chi nhánh mới mà không cần thay đổi code
- Database thiết kế để scale theo chiều ngang

### 4.5 Giao diện
- Tiếng Việt toàn bộ (không hybrid Anh-Việt)
- Responsive: admin web desktop-first, POS tablet-first
- Hỗ trợ màn hình từ 1024px trở lên (admin), 768px trở lên (POS)
- Dark mode (tuỳ chọn, Phase 2+)

---

## 5. Roadmap / Phân chia Phase

### Phase 1 – MVP (ưu tiên bán hàng được ngay)
**Mục tiêu**: Nhân viên có thể bán hàng tại quầy thay thế KiotViet ngay.

| Module | Tính năng đưa vào Phase 1 |
|--------|--------------------------|
| Hàng hóa | CRUD SP + biến thể, import Excel, danh mục |
| Tồn kho | Xem tồn, nhập hàng, cảnh báo tồn thấp |
| Đơn hàng | Danh sách, chi tiết, cập nhật trạng thái |
| POS | Bán nhanh, bán thường, in hóa đơn, VietQR |
| Sổ quỹ | Phiếu thu/chi, sổ quỹ, mở/đóng ca |
| Khách hàng | CRUD cơ bản, tìm khi bán hàng |
| Nhân viên | CRUD + đăng nhập, phân quyền cơ bản |
| Chi nhánh | CRUD, tài khoản ngân hàng |
| WooCommerce | Đồng bộ SP + đơn hàng cơ bản |
| Dashboard | Widgets + biểu đồ cơ bản |

### Phase 2 – Vận hành đầy đủ
- Chuyển hàng giữa chi nhánh
- Kiểm kho, xuất hủy
- Bán giao hàng (tích hợp ĐVVC)
- Khách hàng nâng cao: CRM, công nợ, phân loại
- Nhân viên: ca làm việc, bảng lương
- Báo cáo đầy đủ
- Đơn trả hàng / hoàn tiền
- Audit log

### Phase 3 – Mở rộng & CRM
- Tích điểm khách hàng
- Phân khúc khách (VIP, RFM)
- Khuyến mãi (giảm giá theo điều kiện)
- KPI nhân viên
- Báo cáo lãi lỗ, báo cáo tài chính
- Thông báo realtime (WebSocket)
- Thanh toán VNPay / MoMo callback tự động

### Phase 4 – Mobile & AI
- Ứng dụng mobile (React Native)
- Dự báo tồn kho thông minh
- Push notifications
- Dashboard nâng cao với AI insights

---

*Tài liệu này cần được bổ sung screenshot KiotViet tham chiếu trong thư mục `/screenshots/kiotviet/`*
*Phiên bản: 1.0 | Ngày tạo: 12/03/2026 | Người tạo: FbShop Team*
