# Techstack — <Tên dự án>

| Phiên bản | v0.1 | Ngày | <yyyy-mm-dd> | Trạng thái | DRAFT |

> **Cách dùng:** copy ra `docs/sa/techstack.md` rồi điền. Khối `> 💡 Hướng dẫn` / `> 📝 Ví dụ` là chỉ dẫn —
> **xóa khi hoàn thiện**. Techstack phải **khớp kiểu kiến trúc đã chốt ở HLD (mục 1)**.

## Nguyên tắc lựa chọn
Ưu tiên: phù hợp NFR > thế mạnh đội ngũ > chi phí vận hành > độ trưởng thành.

> 💡 **Hướng dẫn — công nghệ phục vụ bài toán, KHÔNG mặc định framework.**
> - **Chọn theo NFR & ràng buộc dự án**, không vì "quen tay" hay vì một nền tảng có sẵn. Mỗi ô **Lựa chọn**
>   phải có **Lý do** bám NFR cụ thể (tải, độ trễ, chi phí, đội ngũ) **và** ít nhất **một phương án thay thế
>   đã cân nhắc** (nêu vì sao loại).
> - **Ánh xạ NFR:** cột "NFR liên quan" trỏ tới ID trong SRS (NFR-01…) để mỗi lựa chọn truy vết được.
> - **Khớp kiến trúc:** monolith/modular → có thể chung 1 CSDL; microservices → cân nhắc CSDL/hàng đợi riêng
>   theo service (khớp HLD).
> - Ô nào **không dùng** thì ghi "Không áp dụng" + lý do, đừng để trống; không đưa lựa chọn nào chưa có lý do.
> - **Version là BẮT BUỘC:** mỗi lựa chọn ghi **version cụ thể** (ưu tiên LTS; ghi rõ major.minor). Lý do chọn
>   version tính cả: vòng đời hỗ trợ (EOL), tương thích thư viện/chuẩn nội bộ, version đội đang vận hành.
>   **Toàn bộ cột Version phải được anh Thức XÁC NHẬN** (SA tư vấn → anh chốt) trước khi trình GATE-2;
>   đổi version sau GATE-2 = Change Request.

## Bảng công nghệ *(SA đề xuất → anh Thức xác nhận từng dòng)*
| Lớp | Lựa chọn | **Version** | Lý do (chọn & chọn version) | Phương án thay thế đã cân nhắc | NFR liên quan | Xác nhận |
|-----|----------|-------------|------------------------------|-------------------------------|---------------|:--------:|
| Ngôn ngữ/Backend | | | | | | ☐ |
| Framework API | | | | | | ☐ |
| CSDL vận hành | | | | | | ☐ |
| CSDL/kho phân tích | | | | | | ☐ |
| Hàng đợi/Streaming | | | | | | ☐ |
| Cache | | | | | | ☐ |
| Frontend | | | | | | ☐ |
| Mobile | | | | | | ☐ |
| Hạ tầng/Triển khai | Docker Compose (UAT) | | | | | ☐ |
| Quan sát (obs) | | | | | | ☐ |

> 📝 **Ví dụ điền sẵn (1–2 dòng):**
> | Lớp | Lựa chọn | Version | Lý do (chọn & chọn version) | Phương án thay thế | NFR liên quan | Xác nhận |
> |-----|----------|---------|------------------------------|--------------------|---------------|:--------:|
> | Ngôn ngữ/Backend | Java | 21 (LTS) | LTS tới 2029, virtual threads cho tải cao; đội đang chạy 21 | Java 17 (LTS cũ hơn, thiếu VT) | NFR-01 | ✅ |
> | Framework API | Spring Boot | 3.3.x | Chuẩn đội, jakarta.*, hỗ trợ Java 21; 3.3 còn dài hạn | Quarkus (đội chưa thạo) | NFR-01, NFR-02 | ✅ |
> | CSDL vận hành | PostgreSQL | 16.x | ACID, JSONB; 16 EOL 2028, bản đội DBA đang vận hành | MySQL 8 (yếu JSON), MongoDB (không cần) | NFR-01, NFR-05 | ✅ |

## Chuẩn dự án backend Java *(bắt buộc xác nhận cùng bảng trên — nếu backend là Java)*
| Hạng mục | Giá trị chốt | Xác nhận |
|----------|--------------|:--------:|
| Maven module | single-module / multi-module: <chọn + lý do> | ☐ |
| `groupId` / `artifactId` | `com.duy.<dự án>` / `<dự án>-<service>` | ☐ |
| Package gốc | `com.duy.<dự án>.<service>` — package-by-feature (convention `backend-dev-java`) | ☐ |

## Ràng buộc & Rủi ro công nghệ
- <giấy phép, khả năng cung ứng, vendor lock-in, tuân thủ PDPL>

---

## ✅ Checklist trước GATE-2
- [ ] **Mọi lựa chọn** có **Lý do** bám NFR + **≥1 phương án thay thế** đã cân nhắc (không mặc định framework).
- [ ] **Mọi dòng có VERSION cụ thể** (ưu tiên LTS, kèm lý do chọn version) và **đã được anh Thức xác nhận** (cột Xác nhận ✅ đủ).
- [ ] Backend Java: đã chốt & xác nhận **Maven** (module, groupId/artifactId) + **cấu trúc package** `com.duy.*`.
- [ ] Cột **NFR liên quan** trỏ đúng ID NFR ở SRS; mỗi lựa chọn truy vết được về yêu cầu.
- [ ] Techstack **khớp kiểu kiến trúc** đã chốt ở HLD (mục 1).
- [ ] Ô không dùng ghi rõ "Không áp dụng" + lý do; không để trống thiếu giải trình.
- [ ] Nêu **ràng buộc & rủi ro** (giấy phép, vendor lock-in, cung ứng, **PDPL**).
- [ ] Đã xóa hết khối 💡 Hướng dẫn / 📝 Ví dụ trước khi trình.
