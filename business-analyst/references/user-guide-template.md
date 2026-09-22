# Hướng dẫn sử dụng (HDSD) — <Tên phần mềm>

| Phiên bản | v0.1 | Ngày | <yyyy-mm-dd> | Trạng thái | DRAFT |
|-----------|------|------|--------------|------------|-------|

> **Cách dùng file này:** copy ra `docs/ba/user-guide.md` rồi điền. Đây là tài liệu cho **người dùng cuối** —
> viết đơn giản, **không thuật ngữ kỹ thuật**, bám **màn hình thật ở UAT** (chèn ảnh chụp thực tế). Các khối
> `> 💡 Hướng dẫn` / `> 📝 Ví dụ` là chỉ dẫn cho người soạn — **xóa khi hoàn thiện**. Định dạng làm việc là
> Markdown suốt dự án; chỉ chuyển Word khi bàn giao cuối dự án.

> 💡 **Viết cho ai — viết thế nào.** Người đọc là khách hàng / nhân viên nghiệp vụ, **không phải dev**.
> - Xưng hô nhất quán ("Quý khách" hoặc "Bạn"), câu ngắn, **mỗi bước một hành động**.
> - Gọi đúng tên nút/menu **như trên màn hình thật** (in đậm), tránh từ chuyên môn (API, endpoint, token…).
> - Mỗi chức năng **truy vết về FR ở SRS** để bảo đảm không sót và đúng nghiệp vụ đã duyệt.
> - Ảnh chụp từ môi trường **UAT**, **che/ẩn mọi dữ liệu thật** (số thuê bao, tên, số dư…) — PDPL; dùng dữ liệu mẫu.

## Mục lục
1. [Giới thiệu](#1-giới-thiệu)
2. [Bắt đầu](#2-bắt-đầu)
3. [Hướng dẫn theo chức năng](#3-hướng-dẫn-theo-chức-năng)
4. [Câu hỏi thường gặp (FAQ)](#4-câu-hỏi-thường-gặp-faq)
5. [Xử lý sự cố thường gặp](#5-xử-lý-sự-cố-thường-gặp)
6. [Thông tin liên hệ hỗ trợ](#6-thông-tin-liên-hệ-hỗ-trợ)

## 1. Giới thiệu

| Mục | Nội dung |
|-----|----------|
| Đối tượng người đọc | <ai dùng tài liệu này: khách hàng / giao dịch viên / quản trị…> |
| Phạm vi | <hướng dẫn những chức năng nào; nêu rõ phần KHÔNG bao gồm> |
| Phiên bản phần mềm | <tên & số hiệu bản phần mềm mà HDSD này áp dụng> |
| Kênh sử dụng | <Web / App di động / Telegram… và trình duyệt/thiết bị khuyến nghị> |

<Đoạn giới thiệu ngắn: phần mềm này giúp người dùng làm được gì, giá trị chính.>

## 2. Bắt đầu

### 2.1 Đăng nhập
| # | Thao tác | Kết quả mong đợi |
|---|----------|------------------|
| 1 | <Mở địa chỉ / nhấn biểu tượng ứng dụng> | <màn hình đăng nhập hiển thị> |
| 2 | <Nhập tài khoản & mật khẩu, bấm **Đăng nhập**> | <vào màn hình chính> |

![Màn hình đăng nhập](<đường-dẫn-ảnh>)

> 💡 Nêu cả cách xử lý khi **quên mật khẩu** và yêu cầu mật khẩu (độ dài, ký tự) nếu có.

### 2.2 Tổng quan giao diện
Giới thiệu các khu vực chính của màn hình sau khi đăng nhập (menu, thanh công cụ, khu vực nội dung).

![Tổng quan giao diện chính](<đường-dẫn-ảnh>)

| Khu vực | Mục đích |
|---------|----------|
| <Menu bên trái> | <điều hướng giữa các chức năng> |
| <Thanh trên cùng> | <thông tin tài khoản, đăng xuất> |

## 3. Hướng dẫn theo chức năng

> 💡 **Mỗi chức năng một mục — theo đúng thứ tự người dùng thường thao tác.** Mỗi mục gồm: **Mục đích** →
> **Các bước** (đánh số, kèm ảnh nếu cần) → **Lưu ý/Mẹo** → **Truy vết FR**. Bám màn hình thật ở UAT: tên
> nút, nhãn trường, thông báo phải **trùng khít** với phần mềm. Nếu một bước có thể lỗi, chỉ người dùng cách
> nhận biết và xử lý ngay tại đó (hoặc dẫn xuống mục 5).

### 3.x <Tên chức năng>
- **Mục đích:** <người dùng đạt được gì khi dùng chức năng này>
- **Truy vết:** FR-<xx> (SRS mục 3.2) — <tên FR>
- **Các bước:**
  | # | Thao tác | Kết quả mong đợi |
  |---|----------|------------------|
  | 1 | | |
  | 2 | | |

  ![<Chú thích ảnh bước tương ứng>](<đường-dẫn-ảnh>)
- **Lưu ý / Mẹo:** <điều dễ nhầm, giới hạn, mẹo dùng nhanh>

*(Sao chép nguyên khối 3.x ở trên cho mỗi chức năng chính.)*

> 📝 **Ví dụ điền sẵn — Đăng ký gói cước**
> - **Mục đích:** Giúp Quý khách đăng ký một gói cước data ngay trên ứng dụng, không cần ra cửa hàng.
> - **Truy vết:** FR-02 (SRS mục 3.2) — Đăng ký gói cước data.
> - **Các bước:**
>   | # | Thao tác | Kết quả mong đợi |
>   |---|----------|------------------|
>   | 1 | Ở màn hình chính, chọn **Gói cước**, rồi chọn gói muốn đăng ký | Màn hình hiện chi tiết gói và giá |
>   | 2 | Bấm **Đăng ký**, kiểm tra thông tin rồi bấm **Xác nhận** | Hiện thông báo xác nhận, trừ cước |
>   | 3 | Chờ tin nhắn xác nhận đăng ký thành công | Nhận SMS "Đăng ký gói … thành công" |
>
>   ![Màn hình chọn và xác nhận gói cước](images/dang-ky-goi-cuoc.png)
> - **Lưu ý / Mẹo:** Cần đủ số dư trước khi đăng ký. Mỗi loại gói data chỉ giữ **một** gói còn hiệu lực;
>   muốn đổi gói hãy chờ gói cũ hết hạn hoặc chọn **Đổi gói**.

## 4. Câu hỏi thường gặp (FAQ)

> 💡 Gom các câu người dùng hay hỏi thật (từ CSKH/UAT). Trả lời ngắn, dẫn tới mục chức năng liên quan.

| # | Câu hỏi | Trả lời |
|---|---------|---------|
| 1 | <Câu hỏi thường gặp> | <trả lời ngắn gọn, dễ hiểu> |

> 📝 **Ví dụ:** *"Tôi đăng ký gói nhưng chưa dùng được?"* → Gói kích hoạt trong vài phút sau khi có SMS xác
> nhận; nếu quá 15 phút chưa dùng được, xem **mục 5. Xử lý sự cố**.

## 5. Xử lý sự cố thường gặp

> 💡 Bảng "hiện tượng → nguyên nhân có thể → cách xử lý". Viết theo điều người dùng **nhìn thấy**, không theo
> mã lỗi kỹ thuật.

| Hiện tượng | Nguyên nhân có thể | Cách xử lý |
|------------|--------------------|------------|
| <điều người dùng gặp> | <lý do thường gặp> | <các bước tự khắc phục; khi nào cần gọi hỗ trợ> |

> 📝 **Ví dụ:** *Đăng ký gói báo "Số dư không đủ".* → Tài khoản chưa đủ tiền → Nạp thêm rồi đăng ký lại; nếu
> vừa nạp mà vẫn báo lỗi, chờ 1–2 phút hoặc liên hệ hỗ trợ (mục 6).

## 6. Thông tin liên hệ hỗ trợ

| Kênh | Thông tin | Thời gian phục vụ |
|------|-----------|-------------------|
| Tổng đài | <số hotline> | <khung giờ> |
| Email | <email hỗ trợ> | |
| Điểm giao dịch | <địa chỉ / đường dẫn tra cứu> | |

---

## ✅ Checklist trước khi bàn giao HDSD
- [ ] Phủ **hết chức năng chính** người dùng cần (đối chiếu danh mục FR trong SRS).
- [ ] Mỗi chức năng có **mục đích + các bước + truy vết FR**; các bước khớp **màn hình thật ở UAT**.
- [ ] **Ngôn ngữ dễ hiểu**, không thuật ngữ kỹ thuật; tên nút/nhãn trùng khít phần mềm.
- [ ] Có **ảnh minh hoạ** cho các bước quan trọng; ảnh rõ, đúng bản phần mềm.
- [ ] Có **FAQ** và **mục xử lý sự cố** cho các tình huống hay gặp.
- [ ] **Không lộ PII thật** trong ảnh/ví dụ (che số thuê bao, tên, số dư…) — tuân thủ PDPL.
- [ ] Có thông tin **liên hệ hỗ trợ**; đã xoá hết khối 💡 Hướng dẫn / 📝 Ví dụ.
