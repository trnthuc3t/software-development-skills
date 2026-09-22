# Báo cáo thẩm định & khôi phục tài liệu (hệ thống cũ) — <Tên hệ thống>

| Phiên bản | v0.1 | Ngày | <yyyy-mm-dd> | Trạng thái | DRAFT |
|-----------|------|------|--------------|------------|-------|

> **Cách dùng file này:** copy ra `docs/audit/audit-report.md` rồi điền. **BA chủ trì** (phần nghiệp vụ),
> **SA đóng góp phần kỹ thuật** (schema/API/kiến trúc/data model). Chỉ dùng khi làm việc với **hệ thống CŨ /
> yêu cầu nâng cấp**. Các khối `> 💡 Hướng dẫn` và `> 📝 Ví dụ` là chỉ dẫn — **xóa khi hoàn thiện**. Định dạng
> Markdown suốt dự án; chỉ chuyển Word khi kết thúc. **Ràng buộc: chỉ khi GATE-0 kết luận "Đạt" (mục 6) mới
> được đánh giá & thiết kế yêu cầu nâng cấp.**

> 💡 **Nguyên tắc thẩm định.** Với hệ thống cũ, **hệ thống đang chạy là nguồn sự thật** — tài liệu cũ có thể
> lỗi thời hoặc không khớp code. Reverse-engineer từ hành vi thực tế, code, schema CSDL, log, cấu hình để dựng
> lại baseline. Phạm vi khôi phục **tối thiểu đủ cho vùng bị ảnh hưởng** bởi yêu cầu nâng cấp — không cần phủ
> toàn hệ thống. Mọi bằng chứng/dữ liệu minh hoạ phải **ẩn danh, tuân thủ PDPL — không PII thật, không secret/khoá**.

## 1. Thông tin hệ thống cũ & phạm vi thẩm định

| Mục | Nội dung |
|-----|----------|
| Hệ thống / dịch vụ | <tên> |
| Phiên bản đang chạy | <vd v3.2 · build/commit · ngày phát hành> |
| Môi trường tham chiếu | <PROD/UAT — nơi lấy bằng chứng> |
| Yêu cầu nâng cấp | <mô tả ngắn thay đổi cần đánh giá> |
| Vùng liên quan (in-scope) | <module / luồng / bảng / API bị ảnh hưởng> |
| Ngoài phạm vi | <phần không thẩm định lần này> |
| Người thẩm định | BA: <tên> · SA: <tên> |
| Nguồn đối chiếu | <repo code · schema CSDL · log · cấu hình · người vận hành…> |

## 2. Đối chiếu tài liệu chuẩn vs hiện có

> 💡 Liệt kê bộ tài liệu chuẩn SDLC và đánh giá cái đang có. **Tình trạng** chọn một trong: **Đủ / Lỗi thời /
> Không khớp code / Thiếu** ("Không khớp code" = tài liệu tồn tại nhưng mô tả sai so với hệ thống thực). Chủ
> trì: BA cho BRD/SRS; SA cho HLD/LLD/CSDL/api-spec.

| Tài liệu | Có? | Tình trạng | Ghi chú |
|----------|:---:|-----------|---------|
| BRD | | | |
| SRS | | | |
| HLD | | | |
| LLD | | | |
| CSDL (schema/ERD) | | | |
| api-spec (OpenAPI) | | | |
| design (UI/UX) | | | |
| test (test case/kịch bản) | | | |

## 3. Thiếu sót tài liệu

> 💡 Từ mục 2, gom các tài liệu **Thiếu / Lỗi thời / Không khớp code** cần dựng lại. Ưu tiên vùng in-scope.

- **Thiếu hẳn:** <vd LLD, api-spec chưa từng có>
- **Lỗi thời (cần cập nhật):** <vd SRS bản cũ chưa có luồng đã thêm sau này>
- **Không khớp code (cần khôi phục lại):** <vd HLD mô tả 3 service nhưng thực tế đã gộp còn 2>

## 4. Thiếu sót & mâu thuẫn logic

> 💡 Phần cốt lõi: những chỗ **nghiệp vụ không rõ, luồng thiếu, data model lệch code, API không tài liệu, quy
> tắc mâu thuẫn**. Mỗi dòng phải có **bằng chứng từ hệ thống thực** (đường dẫn code / truy vấn schema / log /
> hành vi quan sát được), không suy đoán. **Mức độ:** Cao / Trung bình / Thấp theo ảnh hưởng tới yêu cầu nâng cấp.

| # | Hạng mục | Mô tả thiếu / mâu thuẫn | Bằng chứng từ hệ thống thực | Mức độ | Tài liệu cần khôi phục |
|---|----------|-------------------------|-----------------------------|:------:|------------------------|
| 1 | | | | | |

## 5. Kế hoạch khôi phục baseline

> 💡 Ưu tiên theo **phạm vi nâng cấp** (vùng in-scope trước). Phân công rõ: **BA** khôi phục phần nghiệp vụ
> (BRD/SRS), **SA** khôi phục phần kỹ thuật (HLD/LLD/CSDL/api-spec). Chỉ khôi phục **tối thiểu đủ** để đánh giá
> và thiết kế thay đổi an toàn.

| # | Việc khôi phục | Tài liệu đích | Người làm (BA/SA) | Ưu tiên | Ước lượng | Trạng thái |
|---|----------------|---------------|:-----------------:|:-------:|-----------|:----------:|
| 1 | | | | Cao | | Chưa làm |

## 6. Kết luận GATE-0

> 💡 GATE-0 trả lời: **đã đủ cơ sở để đánh giá yêu cầu nâng cấp chưa?** **Đạt** khi baseline vùng in-scope đã
> đủ và không còn mâu thuẫn mức **Cao** chưa xử lý. Nếu **Chưa đạt**, liệt kê việc còn thiếu — **không được**
> chuyển sang đánh giá/thiết kế nâng cấp.

- **Kết luận:** ☐ Đạt ☐ Chưa đạt
- **Cơ sở kết luận:** <baseline in-scope đã đủ chưa? còn mâu thuẫn mức Cao nào?>
- **Việc còn thiếu (nếu Chưa đạt):** <liệt kê, gắn # ở mục 4/5>
- **Người xác nhận:** BA <tên> · SA <tên> · Ngày <yyyy-mm-dd>

> **Ràng buộc:** chỉ khi GATE-0 **Đạt**, BA/SA mới được đánh giá & thiết kế yêu cầu nâng cấp (sang B1/B2/B3).

> 📝 **Ví dụ điền sẵn — hệ thống Nạp tiền & Gói cước** *(yêu cầu nâng cấp: thêm kênh nạp tiền qua ví điện tử)*
>
> **Phạm vi (mục 1):** in-scope = luồng nạp tiền, bảng `TaiKhoan`, `GiaoDichNap`, API `POST /topup`; ngoài
> phạm vi = phân hệ CSKH, báo cáo.
>
> **Đối chiếu tài liệu (mục 2):**
> | Tài liệu | Có? | Tình trạng | Ghi chú |
> |----------|:---:|-----------|---------|
> | SRS | Có | Lỗi thời | Bản cũ, chưa có kênh nạp qua ngân hàng đã thêm sau này |
> | api-spec | Không | Thiếu | API `/topup` chỉ tồn tại trong code, chưa có OpenAPI |
> | CSDL | Có | Không khớp code | ERD thiếu cột `vi_dien_tu_ref` đã có trong bảng thực |
>
> **Thiếu sót & mâu thuẫn logic (mục 4):**
> | # | Hạng mục | Mô tả thiếu / mâu thuẫn | Bằng chứng từ hệ thống thực | Mức độ | Tài liệu cần khôi phục |
> |---|----------|-------------------------|-----------------------------|:------:|------------------------|
> | 1 | Quy tắc nghiệp vụ | Hạn mức nạp/ngày không có trong tài liệu | Code chặn > 5.000.000 MMK/ngày (`TopupService:88`) | Cao | SRS (BR nạp tiền) |
> | 2 | Data model lệch code | `GiaoDichNap.trang_thai` có 5 giá trị, tài liệu ghi 3 | Enum trong code + `SHOW COLUMNS` | Cao | CSDL + SRS (bảng trạng thái) |
> | 3 | API không tài liệu | `POST /topup/callback` (webhook nhà cung cấp) không mô tả | Route trong code + log gọi thực tế | Trung bình | api-spec |
>
> **GATE-0 (mục 6):** **Chưa đạt** — phải khôi phục BR hạn mức nạp, bảng trạng thái `GiaoDichNap` và api-spec
> vùng nạp tiền trước khi đánh giá tích hợp ví điện tử.

---

## ✅ Checklist thẩm định (chạy trước khi chốt GATE-0)
- [ ] Đã xác định rõ **phạm vi in-scope** theo yêu cầu nâng cấp (mục 1).
- [ ] Đối chiếu đủ bộ tài liệu chuẩn; mỗi dòng có **Tình trạng** rõ (mục 2).
- [ ] Đã gom tài liệu thiếu / lỗi thời / không khớp code cần dựng lại (mục 3).
- [ ] Mỗi thiếu sót logic có **bằng chứng từ hệ thống thực** + mức độ + tài liệu cần khôi phục (mục 4).
- [ ] Kế hoạch khôi phục **phân công BA/SA** rõ, ưu tiên vùng in-scope (mục 5).
- [ ] Baseline khôi phục **tối thiểu đủ** cho vùng bị ảnh hưởng (không phình toàn hệ thống).
- [ ] Mọi bằng chứng **ẩn danh, PDPL** — không PII thật, không secret/khoá.
- [ ] Kết luận GATE-0 (Đạt / Chưa đạt) có cơ sở; nếu Chưa đạt, liệt kê việc còn thiếu (mục 6).
- [ ] Đã xóa hết khối 💡 Hướng dẫn / 📝 Ví dụ trước khi bàn giao.
