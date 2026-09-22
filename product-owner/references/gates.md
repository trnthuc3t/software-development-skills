# GATES — Checklist Definition of Done cho từng cổng

PO dùng checklist này để quyết định một cổng có đủ điều kiện trình anh Thức chưa. **Đủ điều kiện ≠ được
duyệt** — vẫn phải con người ký duyệt.

## 🚦 GATE-0 — Duyệt baseline tài liệu (sau B0 — chỉ hệ thống cũ/nâng cấp)
- [ ] `audit-report.md` liệt kê **đủ** tài liệu thiếu + điểm thiếu/mâu thuẫn logic ở vùng liên quan yêu cầu.
- [ ] Baseline khôi phục (SRS + HLD + LLD/CSDL tối thiểu) **phản ánh đúng hệ thống đang chạy**.
- [ ] Kết luận: đủ cơ sở đánh giá tác động của yêu cầu nâng cấp; không PII thật.

> Không đạt GATE-0 → **không** được đánh giá/hứa yêu cầu nâng cấp trên hệ thống cũ.

## 🚦 GATE-1 — Duyệt BRD (sau B1)
- [ ] `BRD.md` có: bối cảnh, vấn đề, mục tiêu kinh doanh đo được, phạm vi (in/out), stakeholders.
- [ ] Có tiêu chí thành công rõ ràng.
- [ ] Không chứa PII thật.

## 🚦 GATE-2 — Duyệt SRS + Techstack + HLD (sau B2)
- [ ] `SRS.md`: đủ FR + NFR, use case chính, truy vết ngược về mục tiêu BRD.
- [ ] `function-map.html`: thẻ module + nền tảng (App/CMS) + trạng thái đặc tả (+ version); **phủ 1-1 danh mục FR của SRS**; tính năng "Chưa đặc tả" đã có kế hoạch làm rõ (không để lọt sang thiết kế).
- [ ] `techstack.md`: mỗi lựa chọn có lý do + phương án thay thế đã cân nhắc; khớp NFR (tải, độ trễ...).
- [ ] `techstack.md`: **mọi dòng có version cụ thể** (ngôn ngữ, framework, nền tảng, CSDL — kèm lý do chọn version) và **đã được anh Thức xác nhận từng dòng**; backend Java đã chốt Maven + cấu trúc package `com.duy.*`.
- [ ] `HLD.md`: sơ đồ component, luồng dữ liệu, điểm tích hợp, hạ tầng; nhất quán với techstack.
- [ ] `architecture.html`: sơ đồ kiến trúc khối phân tầng (khối + công nghệ + luồng); **nhất quán với HLD.md** (cùng component/quyết định kiến trúc). Là bản "dễ nhìn" đi kèm, HLD.md vẫn là nguồn sự thật.
- [ ] 3 tài liệu nhất quán với nhau và với BRD đã duyệt.

## 🚦 GATE-3 — Đóng băng thiết kế (sau B3, nếu bật)
- [ ] `LLD.md`: data model, phân rã module/class, sequence cho luồng chính; khớp HLD.
- [ ] `api-spec.md`: quy ước chung + envelope khai 1 lần; mỗi endpoint đủ request/`result`/mã lỗi nghiệp vụ + **ví dụ JSON** (thành công + lỗi); gắn FR; tên trường khớp LLD. (Không viết OpenAPI YAML tay — sinh từ code ở B4.)
- [ ] `design.md`: user flow + wireframe + design token; phủ các use case trong SRS.
- [ ] `FSD.md`: đặc tả đủ từng màn hình (element, luồng UI, 4 trạng thái, errorCode→message); **ma trận FR↔màn hình↔API↔dữ liệu phủ 100% FR**; không mâu thuẫn SRS; khớp design + api-spec.
- [ ] Scaffold 3 nhánh (backend/frontend/mobile) build được, chạy healthcheck, có README chạy local.

## 🚦 GATE-4 — Nghiệm thu chức năng (sau B4, nếu bật)
- [ ] Code hiện thực đủ các endpoint/luồng trong api-spec/LLD.
- [ ] Unit test của dev xanh; tester đã handover.
- [ ] **Secure coding (shift-left):** mỗi dev đã tự rà theo checklist nền tảng (`secure-coding-*.md`) — Web theo `SEC-WEB`, Mobile theo `SEC-MOBILE`; điểm chưa đạt đã sửa hoặc ghi nhận rủi ro.
- [ ] `test-cases.md`: phủ FR + case biên/lỗi; ánh xạ TC→FR; mỗi case tái lập được.
- [ ] `test-report.md`: phủ use case chính; lỗi còn mở được liệt kê rõ mức độ; không còn lỗi Blocker.

## 🚦 GATE-5 — Ký duyệt bảo mật (sau B5)
- [ ] `pentest-report.md`: có threat model (STRIDE) + checklist OWASP Top 10.
- [ ] **Đối chiếu chuẩn lập trình an toàn** (`SEC-WEB` cho Web, `SEC-MOBILE` cho Mobile) — finding gắn chuẩn có **mã điều khoản**; căn cứ `secure-coding-index.md`.
- [ ] Mọi finding High/Critical đã khắc phục hoặc có phương án chấp nhận rủi ro được anh Thức đồng ý.
- [ ] Không rò rỉ secret trong mã nguồn/cấu hình.

## 🚦 GATE-6 — Xác nhận UAT (sau B6)
- [ ] `docker-compose.yml`: đủ service, healthcheck, port map đúng **dải port anh Thức cấp**.
- [ ] Stack UAT chạy, healthcheck xanh.
- [ ] `deploy-notes.md`: URL truy cập, bảng port, cách khởi động/dừng/rollback.
- [ ] `user-guide.md` (HDSD): phủ chức năng chính, có ảnh UAT, FAQ + xử lý sự cố; không PII.

---
> DoD các cổng luồng hệ thống cũ (**GATE-CR-1/2, GATE-HOTFIX, GATE-RELEASE, GATE-RELEASE-DONE**) nằm trong `WORKFLOW.md` (Track B).
