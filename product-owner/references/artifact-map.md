# ARTIFACT MAP — Ai đọc gì / ghi ra đâu (RACI rút gọn)

Mỗi role **đọc tài liệu tiền đề đã duyệt** rồi **ghi output của mình**. Không role nào sửa output của
role khác; nếu cần đổi → báo PO, PO điều phối role sở hữu sửa.

| Role       | Đọc (đầu vào)                                             | Ghi (đầu ra)                              |
|------------|-----------------------------------------------------------|-------------------------------------------|
| BA         | Yêu cầu anh Thức; (cũ) hệ thống đang chạy; (B3) SRS+design; (B6) màn hình UAT | `docs/ba/BRD.md`, `docs/ba/SRS.md`, `docs/ba/function-map.html` (B2), `docs/ba/FSD.md` (B3), `docs/ba/user-guide.md`, `docs/audit/audit-report.md` (chủ trì; SA đóng góp) |
| SA         | BRD, SRS (đã duyệt)                                        | `docs/sa/techstack.md`, `HLD.md`, `architecture.html` (B2), `LLD.md`, `api-spec.md` |
| Designer   | BRD, SRS                                                   | `docs/design/design.md`                   |
| Backend    | SRS, techstack, HLD, LLD, api-spec, design                | `src/backend/` + unit test                |
| Frontend   | SRS, **FSD**, HLD, LLD, api-spec, design                  | `src/frontend/` + unit test               |
| Mobile     | SRS, **FSD**, HLD, LLD, api-spec, design                  | `src/mobile/` + unit test                 |
| Tester     | SRS, **FSD**, api-spec, LLD                               | `docs/qa/test-plan.md`, `test-cases.md`, `test-report.md` |
| Security   | HLD, LLD, api-spec, mã nguồn, cấu hình                    | `docs/security/pentest-report.md`         |
| DevOps     | techstack, HLD, mã nguồn                                  | `deploy/docker-compose.yml`, `deploy-notes.md` |

> **Kiểu B (microservices, nhiều module):** tài liệu tách sang **repo tài liệu độc lập**. Tầng tổng thể là 4 file ở gốc `docs/` (`system_overview.md`, `brd_overview.md`, `workflow.md`, `system_architecture.md`); tài liệu từng role cho mỗi service ở `docs/<service-name>/` — **cùng tên** với repo code. Các đường dẫn `docs/<role>/…` ở bảng trên là ví dụ cho **kiểu A**; kiểu B ánh xạ tương ứng vào gốc `docs/` hoặc `docs/<service-name>/`.

## Cây thư mục chuẩn

Chọn theo **quy mô dự án** (PO quyết định ở Bước 0).

### A. Monolith / ít module (mặc định) — tài liệu đi chung repo code
```
<repo>/
├── PROJECT_STATE.md
├── docs/
│   ├── ba/{BRD.md, SRS.md, function-map.html, FSD.md, user-guide.md}
│   ├── audit/audit-report.md          # chỉ hệ thống cũ (B0)
│   ├── sa/{techstack.md, HLD.md, architecture.html, LLD.md, api-spec.md}
│   ├── design/design.md
│   ├── qa/{test-plan.md, test-cases.md, test-report.md}
│   └── security/pentest-report.md
├── src/{backend, frontend, mobile}/
└── deploy/{docker-compose.yml, deploy-notes.md}
```

### B. Microservices, nhiều module — REPO TÀI LIỆU ĐỘC LẬP (tách khỏi repo code)
**Repo tài liệu** `<project>-docs`:
```
<project>-docs/
├── PROJECT_STATE.md                    # PO điều phối toàn dự án
├── README.md                           # bản đồ tài liệu + quy ước
├── docs/
│   ├── system_overview.md              # TỔNG QUAN HỆ THỐNG: bối cảnh, người dùng, phạm vi, bản đồ service
│   ├── brd_overview.md                 # BRD TỔNG THỂ: mục tiêu kinh doanh (BG-xx) toàn hệ
│   ├── function-map.html               # BẢN ĐỒ CHỨC NĂNG (BA): cây chức năng theo nghiệp vụ + actor + trạng thái rõ/chưa
│   ├── workflow.md                     # LUỒNG NGHIỆP VỤ LIÊN SERVICE (end-to-end, sequence giữa các service)
│   ├── system_architecture.md          # KIẾN TRÚC TỔNG THỂ: sơ đồ service & giao tiếp, hạ tầng, techstack,
│   │                                   #   chuẩn dùng chung (envelope/error-code/auth), ADR
│   ├── system_architecture.html        # SƠ ĐỒ KIẾN TRÚC KHỐI (SA): bản "dễ nhìn" đi kèm .md ở trên
│   ├── audit/audit-report.md           # hệ thống cũ (B0)
│   └── <service-name>/                 # TÀI LIỆU TỪNG ROLE cho mỗi service (cùng tên repo code)
│       ├── README.md                   #   URL repo code + owner + Jira component
│       ├── SRS.md                      #   BA — yêu cầu của service
│       ├── FSD.md                      #   BA — đặc tả màn hình (nếu service có UI)
│       ├── HLD.md                      #   SA — thiết kế mức service
│       ├── LLD.md                      #   SA — thiết kế chi tiết + data model
│       ├── api-spec.md                 #   SA — hợp đồng API (contract-first, Markdown — không YAML tay)
│       ├── test-cases.md               #   Tester
│       └── security.md                 #   Security — finding riêng service
└── releases/                           # release-notes, deploy-notes, lịch sử version
```
**Repo code** (tách riêng — monorepo hoặc mỗi service một repo), cùng tên `<service-name>`:
```
<project>(-<service>)/
├── src/<service-A>/                    # backend/frontend/mobile tuỳ loại service
├── src/<service-B>/
└── deploy/{docker-compose.yml, deploy-notes.md}
```
**Liên kết & chống lệch:** `docs/<service-name>/README.md` ghi URL repo code + owner + Jira; `api-spec.md` (contract) ở repo tài liệu, **CI code đối chiếu**; **tag `v<version>`** repo tài liệu mỗi lần lên production.
