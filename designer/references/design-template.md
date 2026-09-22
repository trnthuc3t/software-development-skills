# design.md — <Tên dự án>

| Phiên bản | v0.1 | Ngày | <yyyy-mm-dd> | Trạng thái | DRAFT |
|-----------|------|------|--------------|------------|-------|

> **Cách dùng file này:** copy ra `docs/design/design.md` rồi điền. Các khối `> 💡 Hướng dẫn` và `> 📝 Ví dụ` là chỉ dẫn — **xóa khi hoàn thiện**.

## 1. Nguyên tắc thiết kế
Giọng điệu thương hiệu, đối tượng, thiết bị chính (web/mobile), ngôn ngữ.

> 💡 **Hướng dẫn:** nêu ngắn brand voice, nhóm người dùng chính, thiết bị & ngôn ngữ ưu tiên. Nếu có nhận diện thương hiệu (ví dụ: cam–trắng) thì chốt ở đây để token mục 2 bám theo.
> 📝 **Ví dụ:** App thuê bao, ưu tiên mobile (Android), tiếng Myanmar + Anh; brand cam–trắng, giao diện gọn, thao tác một tay.

## 2. Design token
| Nhóm | Token | Giá trị |
|------|-------|---------|
| Màu | primary | #<...> |
| Màu | surface/background | #<...> |
| Typography | font / cỡ / cân nặng | |
| Spacing | đơn vị cơ sở | 4/8px |
| Bo góc / bóng | | |

> 💡 **Hướng dẫn:** đặt token dùng chung để FE/mobile không tự chế mỗi nơi một kiểu; ghi **giá trị cụ thể** (mã màu, px).
> 📝 **Ví dụ:** `primary = #FFB600`; `background = #FFFFFF`; font `Padauk / 14–20 / 400–700`; spacing cơ sở `8px`; bo góc `8px`.

## 3. User flow (mỗi use case chính)
**UC-01 <tên>**
```
[Màn A] --hành động--> [Màn B] --xác nhận--> [Màn C: thành công]
                                  \--lỗi--> [Màn B: thông báo lỗi]
```

> 💡 **Hướng dẫn:** mỗi use case chính trong SRS → 1 flow; vẽ cả **nhánh lỗi** (không chỉ happy path). Tên màn hình khớp mục 4 và mục 7.
> 📝 **Ví dụ — UC "Đăng ký gói cước":**
> ```
> [Danh sách gói] --chọn gói--> [Xác nhận đăng ký] --xác nhận--> [Thành công: gói Hiệu lực]
>                                       \--số dư không đủ--> [Xác nhận: báo "Số dư không đủ"]
> ```

## 4. Wireframe (mô tả bố cục)
**Màn A**
```
+------------------------------+
| Header / logo                |
+------------------------------+
| [Nội dung chính]             |
| [Ô nhập] [Nút hành động]     |
+------------------------------+
```

> 💡 **Hướng dẫn:** mô tả bố cục dạng ASCII đủ để dev hiểu thứ tự thành phần; chưa cần pixel-perfect. Nêu rõ nút chính và vùng thông báo lỗi.
> 📝 **Ví dụ — Màn "Xác nhận đăng ký":**
> ```
> +------------------------------+
> | < Xác nhận đăng ký           |
> +------------------------------+
> | Gói: Data 30 ngày            |
> | Giá: 30.000                  |
> | Số dư: 50.000                |
> | [Huỷ]            [Xác nhận]  |
> +------------------------------+
> ```

## 5. Component & Trạng thái
| Component | Trạng thái (default/hover/disabled/error) | Ghi chú |
|-----------|-------------------------------------------|---------|

> 💡 **Hướng dẫn:** liệt kê component tái sử dụng và **mọi trạng thái** (default/hover/disabled/error) để dev không bỏ sót trạng thái lỗi.
> 📝 **Ví dụ:** `Nút chính` — default cam / hover cam đậm / disabled xám / error viền đỏ; dùng cho mọi hành động xác nhận.

## 6. Accessibility
Tương phản >= 4.5:1, vùng chạm >= 44px, có nhãn cho input, thông báo lỗi rõ.

> 💡 **Hướng dẫn:** kiểm tối thiểu tương phản, kích thước chạm, nhãn input, và thông báo lỗi bằng **chữ** (không chỉ đổi màu).
> 📝 **Ví dụ:** Nút "Xác nhận" cao 48px; chữ trắng trên nền cam đạt tương phản 4.6:1; lỗi hiển thị "Số dư không đủ" kèm icon, không chỉ đổi màu viền.

## 7. Truy vết màn hình → FR
| Màn hình | FR phục vụ |
|----------|-----------|

> 💡 **Hướng dẫn:** mỗi màn hình phải phục vụ ít nhất một FR; không vẽ màn "thừa". Dùng bảng này để truy vết ngược về SRS.
> 📝 **Ví dụ:**
> | Màn hình | FR phục vụ |
> |----------|-----------|
> | Danh sách gói | FR-01 |
> | Xác nhận đăng ký | FR-02 |

---

## ✅ Checklist
- [ ] Có nguyên tắc thiết kế + design token với **giá trị cụ thể** (mã màu, px).
- [ ] Mỗi use case chính trong SRS có **user flow**, vẽ cả nhánh lỗi.
- [ ] Wireframe mô tả đủ bố cục & nút chính cho các màn quan trọng.
- [ ] Component liệt kê **đủ trạng thái** (default/hover/disabled/error).
- [ ] Đạt accessibility cơ bản (tương phản ≥ 4.5:1, vùng chạm ≥ 44px, nhãn input, lỗi bằng chữ).
- [ ] **Mỗi màn hình truy vết được về ít nhất một FR**; không có màn "thừa".
- [ ] Đã xóa hết khối 💡 Hướng dẫn / 📝 Ví dụ.
