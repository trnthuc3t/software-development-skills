# SRS — <Tên dự án>

| Phiên bản | v0.1 | Ngày | <yyyy-mm-dd> | Trạng thái | DRAFT |
|-----------|------|------|--------------|------------|-------|

> **Cách dùng file này:** copy ra `docs/ba/SRS.md` rồi điền. Các khối `> 💡 Hướng dẫn` và `> 📝 Ví dụ`
> là chỉ dẫn — **xóa khi hoàn thiện**. Định dạng làm việc là Markdown suốt dự án; chỉ chuyển Word khi kết thúc.

## 1. Giới thiệu
Mục đích, phạm vi (link BRD), thuật ngữ.

## 2. Mô tả tổng quan
Bối cảnh hệ thống, nhóm người dùng & quyền, ràng buộc, giả định/phụ thuộc.

## 3. Yêu cầu chức năng (FR)

### 3.1 Danh mục FR
| ID | Tên | Mô tả | Ưu tiên | Truy vết BRD |
|----|-----|-------|---------|--------------|
| FR-01 | | | Cao | BG-01 |

### 3.2 Đặc tả chi tiết từng FR *(bắt buộc cho FR quan trọng)*

> 💡 **Hướng dẫn — viết đến mức test được.** Mỗi FR phải rõ tới mức **dev code được, tester viết được
> test case mà không hỏi lại**. Trả lời đủ 3 câu:
> 1. **Đầu vào nào?** từng trường: bắt buộc/không, kiểu, định dạng, độ dài, miền giá trị, mặc định, nguồn.
> 2. **Hệ thống ứng xử ra sao?** mọi nhánh: thành công, thất bại, ngoại lệ; công thức; điều kiện rẽ; thông báo.
> 3. **Kết quả & dữ liệu đổi gì?** C/R/U/D trên thực thể nào; trạng thái sau khi chạy.
> Tránh từ mơ hồ ("xử lý phù hợp", "nhanh", "một số", "v.v.") → thay bằng con số và quy tắc cụ thể.
> Nhánh ngoại lệ bị bỏ sót là **nguyên nhân lỗi phổ biến nhất** — liệt kê tới khi không còn nhánh nào chưa xử lý.

#### FR-01 — <Tên chức năng>
- **Actor / Tiền điều kiện / Hậu điều kiện:** <actor> / <trạng thái trước khi chạy> / <hậu điều kiện: nêu cả **thành công** và **thất bại**>
- **Luồng chính:**
  | # | Hành động người dùng | Phản ứng hệ thống | Dữ liệu (C/R/U/D) |
  |---|----------------------|-------------------|-------------------|
  | 1 | | | |
- **Luồng thay thế / ngoại lệ:** *(đánh số gắn bước của luồng chính)*
  - 2a. <điều kiện> → <ứng xử + mã lỗi>
- **Validate từng trường:** *(viết thông báo lỗi thành câu cụ thể)*
  | Trường | Bắt buộc | Kiểu | Định dạng | Độ dài | Miền giá trị / ràng buộc | Thông báo lỗi |
  |--------|:-------:|------|-----------|:------:|--------------------------|---------------|
  | | | | | | | |
- **Quy tắc nghiệp vụ / công thức:** BR-xx: <mô tả> — công thức viết tường minh (biến, đơn vị, làm tròn).
- **Bảng trạng thái** *(nếu đối tượng có vòng đời)*: `Trạng thái nguồn | Sự kiện | Điều kiện | Trạng thái đích`.
- **Tiêu chí chấp nhận (Given–When–Then):**
  - Given <bối cảnh> — When <hành động> — Then <kết quả kiểm chứng được>

> 📝 **Ví dụ điền sẵn — FR-02 Đăng ký gói cước data**
> - **Actor / Tiền điều kiện / Hậu điều kiện:** Thuê bao đã đăng nhập / thuê bao đang hoạt động / *Thành công:* trừ tiền, kích hoạt gói, gửi SMS xác nhận; *Thất bại:* không trừ tiền, giữ nguyên trạng thái.
> - **Luồng chính:**
>   | # | Hành động người dùng | Phản ứng hệ thống | Dữ liệu (C/R/U/D) |
>   |---|----------------------|-------------------|-------------------|
>   | 1 | Chọn gói cước, bấm "Đăng ký" | Kiểm tra số dư, hiển thị xác nhận | R: GoiCuoc, TaiKhoan |
>   | 2 | Bấm "Xác nhận" | Trừ tiền, kích hoạt gói, gửi SMS | U: TaiKhoan; C: DangKyGoi |
> - **Luồng thay thế / ngoại lệ:**
>   - 1a. Số dư không đủ → báo "Số dư không đủ để đăng ký gói này", dừng.
>   - 1b. Đã có gói cùng loại còn hiệu lực → hỏi gia hạn/đổi gói (BR-02).
> - **Validate từng trường:**
>   | Trường | Bắt buộc | Kiểu | Định dạng | Độ dài | Miền giá trị / ràng buộc | Thông báo lỗi |
>   |--------|:-------:|------|-----------|:------:|--------------------------|---------------|
>   | Mã gói | Có | Chuỗi | ^PKG[0-9]{3}$ | 6 | Gói đang mở bán | "Gói cước không tồn tại hoặc đã ngừng" |
> - **Quy tắc nghiệp vụ:** BR-02: mỗi thuê bao chỉ 1 gói data cùng loại còn hiệu lực tại một thời điểm.
> - **Tiêu chí chấp nhận:** Given thuê bao có số dư ≥ giá gói — When xác nhận đăng ký PKG010 — Then trừ đúng giá gói, gói ở trạng thái "Hiệu lực", có 1 SMS xác nhận.

#### FR-02 — <Tên chức năng>
*(Sao chép nguyên khối FR-01 ở trên cho mỗi chức năng.)*

## 4. Yêu cầu phi chức năng (NFR)

> 💡 NFR phải **đo được** — có con số & điều kiện, không nói chung chung.

| ID | Loại | Yêu cầu đo được |
|----|------|-----------------|
| NFR-01 | Hiệu năng | P95 < 300ms @ 1.000 req/s |
| NFR-02 | Bảo mật | Mọi API xác thực; PII mã hoá khi lưu |
| NFR-03 | Tuân thủ | Xử lý dữ liệu theo PDPL |

## 5. Ràng buộc dữ liệu
Thực thể dữ liệu chính, phân loại nhạy cảm (PII/nội bộ/công khai), lưu trú & vòng đời.
Nếu đối tượng có vòng đời trạng thái, lập bảng: `Trạng thái nguồn | Sự kiện | Điều kiện | Trạng thái đích`.

## 6. Giao diện ngoài
Hệ thống tích hợp, API bên thứ ba, ràng buộc kênh (web/Telegram/Zalo…).

## 7. Truy vết
Ma trận FR/NFR → mục tiêu BRD (mọi yêu cầu phải nối được về ít nhất một mục tiêu BRD).

---

## ✅ Checklist đặc tả (chạy cho từng FR trước khi báo PO)
- [ ] Có ID, actor, tiền/hậu điều kiện (cả thành công & thất bại).
- [ ] Luồng chính đánh số, có cột C/R/U/D.
- [ ] **Mọi** nhánh lỗi/ngoại lệ có luồng thay thế đánh số gắn bước.
- [ ] Có **bảng validate field-level** với thông báo lỗi cụ thể cho mọi trường nhập.
- [ ] Công thức/quy tắc nghiệp vụ và (nếu có) bảng trạng thái đã viết.
- [ ] Có tiêu chí chấp nhận Given–When–Then kiểm chứng được.
- [ ] NFR đo được; mọi FR/NFR truy vết ngược về BRD; không PII thật (PDPL).
- [ ] Không còn từ mơ hồ, đã xóa hết khối 💡 Hướng dẫn / 📝 Ví dụ.
