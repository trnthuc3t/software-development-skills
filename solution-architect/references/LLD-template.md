# LLD — <Tên dự án>

| Phiên bản | v0.1 | Ngày | <yyyy-mm-dd> | Trạng thái | DRAFT |

> **Cách dùng:** copy ra `docs/sa/LLD.md` rồi điền. Khối `> 💡 Hướng dẫn` / `> 📝 Ví dụ` là chỉ dẫn — **xóa khi hoàn thiện**.
> LLD phải khớp HLD (kiểu kiến trúc & component) và SRS (FR/NFR). **Tên bảng/trường ở đây là nguồn sự thật**
> cho code và API spec — dùng nhất quán.

## 1. Data model

> 💡 **Hướng dẫn:** vẽ ERD (ASCII/Mermaid) + đặc tả từng bảng đủ để tạo schema. Ghi rõ khóa chính/ngoại,
> ràng buộc unique/check, chỉ mục phục vụ truy vấn nào, quy tắc sinh mã (nếu mã có ý nghĩa), và trường
> **PII cần mã hoá/masking** (PDPL). Nếu microservices: nêu **dữ liệu thuộc service nào** và cách nhất quán liên service.

| Bảng | Cột | Kiểu & độ dài | Null? | Khóa (PK/FK) | Chỉ mục | Mặc định | Nhạy cảm? | Mô tả/ràng buộc |
|------|-----|---------------|:-----:|--------------|---------|----------|:---------:|-----------------|
| | | | | | | | | |

> 📝 **Ví dụ — bảng `dang_ky_goi`:**
> | Bảng | Cột | Kiểu & độ dài | Null? | Khóa | Chỉ mục | Mặc định | Nhạy cảm? | Ràng buộc |
> |------|-----|---------------|:-----:|------|---------|----------|:---------:|-----------|
> | dang_ky_goi | id | BIGINT | N | PK | | auto | | |
> | dang_ky_goi | thue_bao_id | BIGINT | N | FK→thue_bao.id | idx_tb | | Có (PII) | |
> | dang_ky_goi | ma_goi | VARCHAR(6) | N | FK→goi_cuoc.ma | | | | ^PKG[0-9]{3}$ |
> | dang_ky_goi | trang_thai | VARCHAR(12) | N | | | 'HIEU_LUC' | | HIEU_LUC/HET_HAN/HUY |

## 2. Phân rã module/class
| Module/Class | Trách nhiệm | Giao diện (hàm/method) public | Phụ thuộc | FR phục vụ |
|--------------|-------------|-------------------------------|-----------|------------|

> 📝 **Ví dụ:** `GoiCuocService` — đăng ký/gia hạn/hủy gói — `dangKy(thueBaoId, maGoi)`, `huy(id)` — phụ thuộc `TaiKhoanService` — phục vụ FR-02.

## 3. Ánh xạ màn hình ↔ dữ liệu *(nếu có UI)*
Với mỗi màn hình/API-facing form: thành phần (control) → nguồn/đích dữ liệu (bảng.trường), input/output,
điều kiện load, công thức. Giúp dev không phải đoán mapping.

## 4. Sequence luồng chính
```
Client -> API: request
API -> Service: gọi
Service -> DB: truy vấn
Service -> API: kết quả
API -> Client: response
```
> 💡 Vẽ sequence cho các luồng quan trọng nhất; nêu điều kiện rẽ nhánh & lỗi.

## 5. Xử lý lỗi & Trạng thái
Bảng mã lỗi nội bộ, chiến lược retry/timeout, idempotency. Nếu đối tượng có vòng đời: bảng chuyển trạng thái.

> 📝 **Ví dụ mã lỗi:**
> | Mã | Ý nghĩa | HTTP | Thông báo |
> |----|---------|------|-----------|
> | ERR_BALANCE | Số dư không đủ | 402 | "Số dư không đủ để đăng ký gói này" |
> | ERR_PKG_NOT_FOUND | Gói không tồn tại | 404 | "Gói cước không tồn tại hoặc đã ngừng" |

## 6. Bảo mật ở mức thiết kế
Kiểm soát input, phân quyền theo endpoint/vai trò, mã hoá dữ liệu nhạy cảm, ghi log thao tác trọng yếu.

## 7. Truy vết
Module/bảng → FR/NFR tương ứng (mọi thành phần thiết kế phải phục vụ ít nhất một yêu cầu).
