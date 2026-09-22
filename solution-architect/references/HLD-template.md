# HLD — <Tên dự án>

| Phiên bản | v0.1 | Ngày | <yyyy-mm-dd> | Trạng thái | DRAFT |

> **Cách dùng:** copy ra `docs/sa/HLD.md` rồi điền. Khối `> 💡 Hướng dẫn` / `> 📝 Ví dụ` là chỉ dẫn — **xóa khi hoàn thiện**.

## 1. Quyết định kiểu kiến trúc *(làm TRƯỚC khi vẽ component)*

> 💡 **Hướng dẫn — kiến trúc theo bài toán, KHÔNG theo framework.** Chốt kiểu kiến trúc dựa trên **quy mô
> người dùng** và **độ phức tạp nghiệp vụ (BRD/SRS)**, không mặc định một nền tảng có sẵn.
> Thu thập trước khi quyết:
> - **Quy mô người dùng:** tổng user, đồng thời (peak), tốc độ tăng trưởng.
> - **Độ phức tạp nghiệp vụ:** số miền/phân hệ, mức độc lập giữa các miền (bounded context), tần suất thay đổi.
> - **Nhu cầu triển khai/mở rộng độc lập; số đội song song; năng lực CI/CD, giám sát, hạ tầng phân tán.**
>
> **Heuristic:**
> - **Mặc định bắt đầu bằng monolith / modular monolith** — đơn giản, rẻ, nhanh ra mắt; tách service sau khi ranh giới đã rõ.
> - **Chỉ chọn microservices khi CÓ ĐỦ CẢ HAI:** quy mô đủ lớn **và** nghiệp vụ đủ phức tạp/độc lập (nhiều miền, nhiều đội, cần deploy/scale riêng).
> - Tránh 2 thái cực: microservices cho hệ nhỏ = over-engineering; gom 1 khối cho hệ đã lớn/đa miền = nghẽn phát triển.

| Câu hỏi quyết định | Trả lời (trích BRD/SRS) | Nghiêng Monolith / Modular | Nghiêng Microservices |
|--------------------|-------------------------|----------------------------|-----------------------|
| Quy mô người dùng (tổng, đồng thời/peak, tăng trưởng) | | Nhỏ–vừa, tải ổn định | Rất lớn, tải biến động |
| Số miền nghiệp vụ & mức độc lập giữa các miền | | Ít miền, gắn kết chặt | Nhiều miền tách bạch rõ |
| Nhu cầu triển khai/mở rộng độc lập từng phần | | Trọn gói là đủ | Cần deploy/scale riêng |
| Số đội song song & tần suất release | | 1 đội, thay đổi vừa | Nhiều đội, release độc lập |
| Năng lực vận hành (CI/CD, giám sát, hạ tầng phân tán) | | Hạn chế | Đủ trưởng thành |

**Kết luận:** Kiểu kiến trúc = **<Monolith phân lớp / Modular monolith / Microservices / Kết hợp>** — vì <lý do bám quy mô & độ phức tạp>.

> 📝 **Ví dụ:** ~30.000 thuê bao, peak ~200 req/s, 3 miền (tài khoản, gói cước, CSKH) gắn kết chặt, 1 đội 5
> người → **Modular monolith**: một khối, tách module theo miền, chung 1 DB; tách microservices sau nếu tải/đội tăng.

## 2. Tổng quan kiến trúc
Sơ đồ component đúng kiểu kiến trúc đã chọn (ASCII/Mermaid). Ví dụ:
```
[Client web/mobile] -> [API Gateway] -> [Service A] -> [DB]
                                      -> [Service B] -> [Queue]
```
> 💡 Monolith: vẽ phân lớp + ranh giới module. Microservices: bản đồ dịch vụ theo miền (bounded context), API gateway, DB riêng theo service, kiểu giao tiếp đồng bộ/bất đồng bộ.

## 3. Thành phần chính
| Component/Service | Trách nhiệm | Công nghệ | Dữ liệu sở hữu | Phụ thuộc |
|-------------------|-------------|-----------|----------------|-----------|

## 4. Luồng dữ liệu chính
Mô tả 2–3 luồng nghiệp vụ quan trọng nhất (request → xử lý → lưu trữ → phản hồi).

## 5. Tích hợp ngoài
Hệ thống/bên thứ ba, giao thức (REST/gRPC), xác thực.

## 6. Hạ tầng & Môi trường
Dev / UAT (Docker Compose) / Prod (định hướng). Sơ đồ triển khai.

## 7. Xuyên suốt (cross-cutting)
Authn/authz, logging, tracing, cấu hình, xử lý lỗi, phân loại & bảo vệ dữ liệu (PDPL).

## 8. Quyết định kiến trúc (ADR ngắn)
- ADR-01: kiểu kiến trúc ở mục 1 → <bối cảnh: quy mô & độ phức tạp> → <quyết định> → <hệ quả về vận hành/chi phí/đội>.
- ADR-02: <quyết định khác> → <hệ quả>.
