# Test Cases — <Tên dự án>

| Phiên bản | v0.1 | Ngày | <yyyy-mm-dd> | Trạng thái | DRAFT |
|-----------|------|------|--------------|------------|-------|

> **Cách dùng file này:** copy ra `docs/qa/test-cases.md` rồi điền. Đây là bộ **test case** (khác `test-plan.md` — nơi ghi phạm vi/chiến lược). Các khối `> 💡 Hướng dẫn` và `> 📝 Ví dụ` là chỉ dẫn — **xóa khi hoàn thiện**.

## 1. Phạm vi & Nguồn suy test case
Chức năng/endpoint được phủ; nguồn suy ra: SRS (mỗi FR + **tiêu chí chấp nhận Given–When–Then**) và `api-spec.md` (mỗi endpoint).

## 2. Quy ước
- **TC-ID:** `TC-<FR>-<số>` (ví dụ `TC-FR02-01`).
- **Mức lỗi:** Blocker / Critical / Major / Minor.
- **Trạng thái:** Pass / Fail / Blocked / N/A.

## 3. Bảng test case

> 💡 **Hướng dẫn — suy test case tới mức chạy được:**
> - Mỗi **tiêu chí chấp nhận Given–When–Then** trong SRS → ít nhất 1 test case; mỗi **endpoint** trong `api-spec.md` → 1 case đúng schema + 1 case mã lỗi.
> - Phủ đủ 3 lớp: **happy path** (đúng luồng), **biên (boundary)** (giá trị nhỏ nhất/lớn nhất, rỗng, tối đa độ dài), **luồng lỗi** (validate fail, số dư/quyền không đủ, mã sai, timeout).
> - Mỗi case phải **tái lập được**: tiền điều kiện rõ, bước đánh số, dữ liệu cụ thể, đúng **một** kết quả kỳ vọng kiểm chứng được.
> - Cột **FR / Tiêu chí chấp nhận** ghi ID để **ánh xạ TC→FR** (đo độ phủ ở mục 4). Dữ liệu **ẩn danh** (PDPL) — không PII thật.

| TC-ID | FR / Tiêu chí chấp nhận | Tiền điều kiện | Bước thực hiện | Dữ liệu | Kết quả kỳ vọng | Kết quả thực tế | Trạng thái (Pass/Fail) | Mức lỗi |
|-------|-------------------------|----------------|----------------|---------|-----------------|-----------------|:----------------------:|---------|
| TC-FR01-01 | | | | | | | | |

> 📝 **Ví dụ điền sẵn — FR-02 Đăng ký gói cước data**
>
> | TC-ID | FR / Tiêu chí chấp nhận | Tiền điều kiện | Bước thực hiện | Dữ liệu | Kết quả kỳ vọng | Kết quả thực tế | Trạng thái | Mức lỗi |
> |-------|------|------|------|------|------|------|:--:|------|
> | TC-FR02-01 | FR-02 / AC "số dư đủ → kích hoạt" | Thuê bao hoạt động, số dư ≥ giá gói | 1) Chọn PKG010; 2) Bấm Đăng ký; 3) Xác nhận | Số dư 50.000; giá gói 30.000 | Trừ đúng 30.000; gói "Hiệu lực"; 1 SMS xác nhận | | | |
> | TC-FR02-02 | FR-02 / luồng lỗi "số dư không đủ" | Thuê bao hoạt động, số dư < giá gói | 1) Chọn PKG010; 2) Bấm Đăng ký | Số dư 10.000; giá gói 30.000 | Báo "Số dư không đủ để đăng ký gói này"; **không** trừ tiền; không tạo đăng ký | | | Major |
> | TC-FR02-03 | FR-02 / validate "mã gói sai" | Thuê bao đã đăng nhập | 1) Gọi đăng ký với mã gói không tồn tại | Mã gói `PKG999` | Báo "Gói cước không tồn tại hoặc đã ngừng"; HTTP 404/422 theo `api-spec.md` | | | Major |

## 4. Độ phủ TC → FR
| FR | Số TC | Có case biên? | Có case lỗi? | Ghi chú |
|----|:-----:|:-------------:|:------------:|---------|
| FR-01 | | | | |

## 5. Tổng hợp kết quả *(điền sau khi chạy)*
Tổng số TC / Pass / Fail / Blocked; danh sách TC Fail kèm mức lỗi → đầu vào cho `test-report.md`.

---

## ✅ Checklist
- [ ] Mỗi FR có test case; mỗi tiêu chí chấp nhận **Given–When–Then** được phủ ít nhất 1 case.
- [ ] Mỗi endpoint trong `api-spec.md` có case đúng schema + case mã lỗi.
- [ ] Có đủ **case biên (boundary)** và **case luồng lỗi**, không chỉ happy path.
- [ ] Mỗi TC **tái lập được**: tiền điều kiện, bước đánh số, dữ liệu cụ thể, một kết quả kỳ vọng kiểm chứng được.
- [ ] Ánh xạ **TC→FR** đầy đủ để đo độ phủ (mục 4).
- [ ] Dữ liệu **ẩn danh** (PDPL) — không PII thật.
- [ ] Đã xóa hết khối 💡 Hướng dẫn / 📝 Ví dụ trước khi báo PO.
