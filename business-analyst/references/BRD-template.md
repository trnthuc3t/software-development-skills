# BRD — <Tên dự án>

| Phiên bản | v0.1 | Ngày | <yyyy-mm-dd> | Người viết | BA | Trạng thái | DRAFT |
|-----------|------|------|--------------|------------|----|-----------|-------|

> **Cách dùng file này:** copy ra `docs/ba/BRD.md` rồi điền. Các khối `> 💡 Hướng dẫn` và `> 📝 Ví dụ` là
> chỉ dẫn — **xóa khi hoàn thiện**. Markdown suốt dự án; chỉ chuyển Word khi kết thúc.
>
> **Tiền đề:** đã **brainstorm & được anh Thức chốt hướng** (xem Bước khởi đầu trong SKILL BA) — BRD là bản
> ghi lại có cấu trúc của hướng đã thống nhất, không phải nơi bắt đầu suy nghĩ.
>
> **Vai trò trong chuỗi:** BRD trả lời **VÌ SAO & CÁI GÌ ở mức kinh doanh**. Chi tiết yêu cầu phần mềm để ở
> SRS (B2) — BRD **không** đặc tả chức năng, không validate trường, không chọn công nghệ.

## 1. Bối cảnh & Vấn đề

> 💡 Vài đoạn ngắn: hiện trạng, nỗi đau (kèm con số nếu có), vì sao phải làm bây giờ. Người ngoài dự án đọc
> xong phải hiểu được "chuyện gì đang xảy ra".

> 📝 *Thuê bao muốn đăng ký gói cước data hiện phải soạn cú pháp SMS hoặc ra cửa hàng; 38% giao dịch lỗi cú
> pháp, tải lên call center ~12k cuộc/tháng. Cần kênh tự phục vụ trên app.*

## 2. Mục tiêu kinh doanh (đo được)

> 💡 Mỗi mục tiêu có mã `BG-xx` + chỉ số **baseline → target + thời hạn**. SRS sẽ truy vết FR về đây —
> mục tiêu không đo được thì sau này không nghiệm thu được.

| ID | Mục tiêu | Chỉ số (baseline → target) | Thời hạn |
|----|----------|-----------------------------|----------|
| BG-01 | | | |

> 📝 *BG-01: Tăng tỉ lệ đăng ký gói tự phục vụ — 0% → 40% tổng lượt đăng ký — sau 6 tháng go-live.*
> *BG-02: Giảm cuộc gọi hỗ trợ đăng ký gói — 12k/tháng → dưới 6k/tháng.*

## 3. Phạm vi

> 💡 "Ngoài phạm vi" quan trọng ngang "trong phạm vi" — đây là hàng rào chống phình yêu cầu. Điểm mập mờ
> nào đã tranh luận lúc brainstorm thì ghi rõ vào một trong hai cột.

**Trong phạm vi:** <...>
**Ngoài phạm vi:** <...>

> 📝 *Trong: đăng ký/hủy/gia hạn gói data trên app (Android/iOS), thanh toán trừ tài khoản chính. — Ngoài:
> thanh toán thẻ ngân hàng, gói thoại/SMS, kênh web (giai đoạn 2).*

## 4. Stakeholders

| Vai trò | Người/Bộ phận | Quan tâm chính / Quyền quyết |
|---------|---------------|-------------------------------|
| Chủ đầu tư (duyệt) | anh Thức | |

## 5. Yêu cầu mức nghiệp vụ

> 💡 Mức **năng lực nghiệp vụ** (hệ thống phải *cho phép làm gì*), mã `BR-xx`, mỗi dòng nối về một `BG-xx`.
> **Không** viết chi tiết luồng/validate — đó là việc của SRS.

| ID | Yêu cầu nghiệp vụ | Ưu tiên | Phục vụ mục tiêu |
|----|-------------------|---------|------------------|
| BR-01 | | Cao | BG-01 |

> 📝 *BR-01: Thuê bao tự xem danh sách gói & đăng ký trên app — Cao — BG-01. BR-02: Hệ thống tự nhắc trước
> khi gói hết hạn — TB — BG-02.*

## 6. Ràng buộc & Giả định

- **Ràng buộc:** <pháp lý (PDPL), tích hợp hệ thống tính cước hiện có, ngân sách, deadline, hạ tầng>
- **Giả định:** <điều tin là đúng nhưng chưa xác nhận — ghi rõ để còn kiểm>

## 7. Tiêu chí thành công / Nghiệm thu

> 💡 Điều kiện đo được để tuyên bố dự án thành công — thường ánh xạ 1:1 từ BG-xx + điều kiện chất lượng
> (ví dụ: không sự cố Sev-1 trong 30 ngày đầu).

- SC-01: <điều kiện>

## 8. Rủi ro & Câu hỏi mở

| Rủi ro / Câu hỏi | Ảnh hưởng | Giảm thiểu / Ai quyết | Hạn chót |
|-------------------|-----------|------------------------|----------|
| | | anh Thức | |

---

## ✅ Checklist BRD (trước khi báo PO trình GATE-1)
- [ ] Đã brainstorm và được anh Thức **chốt hướng** trước khi viết.
- [ ] Mọi mục tiêu `BG-xx` **đo được** (baseline → target + thời hạn).
- [ ] Có cả "trong" lẫn "**ngoài** phạm vi"; điểm tranh luận đã chốt rõ thuộc bên nào.
- [ ] Mọi `BR-xx` nối về một `BG-xx`; **không** lấn sang chi tiết SRS (luồng, validate, công nghệ).
- [ ] Ràng buộc PDPL ghi nhận; không PII thật trong ví dụ.
- [ ] Câu hỏi mở còn lại đã liệt kê kèm người quyết + hạn chót.
- [ ] Đã xóa hết khối 💡 Hướng dẫn / 📝 Ví dụ.
