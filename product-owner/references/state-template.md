# PROJECT_STATE — <TÊN DỰ ÁN>

> File điều phối trung tâm. PO và mọi role cập nhật file này. Không xoá lịch sử; chỉ thêm/đổi trạng thái.

- **Dự án:** <tên>
- **Ngày khởi tạo:** <yyyy-mm-dd>
- **Loại dự án:** <Mới | Nâng cấp (hệ thống cũ)>
- **Giai đoạn hiện tại:** B1
- **Cổng đang chờ:** GATE-1
- **GATE-3 (đóng băng thiết kế):** BẬT   <!-- BẬT/TẮT theo ý anh Thức -->
- **GATE-4 (nghiệm thu chức năng):** BẬT

## Trạng thái artifact
Trạng thái: `NOT_STARTED` → `IN_PROGRESS` → `DONE` → `APPROVED` (hoặc `NEEDS_REVISION`)

| Artifact          | Role        | Trạng thái   | Cập nhật (yyyy-mm-dd) |
|-------------------|-------------|--------------|------------------------|
| audit-report.md   | BA + SA     | NOT_STARTED  |     (chỉ hệ thống cũ)  |
| BRD.md            | BA          | NOT_STARTED  |                        |
| SRS.md            | BA          | NOT_STARTED  |                        |
| techstack.md      | SA          | NOT_STARTED  |                        |
| HLD.md            | SA          | NOT_STARTED  |                        |
| LLD.md            | SA          | NOT_STARTED  |                        |
| api-spec.md     | SA          | NOT_STARTED  |                        |
| design.md         | Designer    | NOT_STARTED  |                        |
| scaffold-backend  | Backend     | NOT_STARTED  |                        |
| scaffold-frontend | Frontend    | NOT_STARTED  |                        |
| scaffold-mobile   | Mobile      | NOT_STARTED  |                        |
| src (code)        | 3 dev       | NOT_STARTED  |                        |
| test-plan.md      | Tester      | NOT_STARTED  |                        |
| test-cases.md     | Tester      | NOT_STARTED  |                        |
| test-report.md    | Tester      | NOT_STARTED  |                        |
| pentest-report.md | Security    | NOT_STARTED  |                        |
| user-guide.md     | BA          | NOT_STARTED  |                        |
| docker-compose.yml| DevOps      | NOT_STARTED  |                        |

## Log phê duyệt (chỉ con người)
| Cổng   | Kết quả  | Người duyệt | Ngày | Ghi chú |
|--------|----------|-------------|------|---------|
| GATE-0 | —        |             |      | chỉ hệ thống cũ |
| GATE-1 | —        |             |      |         |
| GATE-2 | —        |             |      |         |
| GATE-3 | —        |             |      |         |
| GATE-4 | —        |             |      |         |
| GATE-5 | —        |             |      |         |
| GATE-6 | —        |             |      |         |

## Thông số môi trường
- **Dải port UAT (anh Thức cấp):** <chưa có — hỏi trước khi deploy>

## Nhật ký (append-only)
- <yyyy-mm-dd> — <sự kiện>
