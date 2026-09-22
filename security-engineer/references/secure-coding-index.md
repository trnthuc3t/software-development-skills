# Secure Coding — Chỉ mục chuẩn & ánh xạ nền tảng (Thức SDLC Kit)

> **Cơ sở tuân thủ lập trình an toàn** cho toàn đội dev. Đây là điểm vào (index) trỏ tới checklist theo từng
> nền tảng, và là **căn cứ đối chiếu** cho `security-engineer` ở **B5** (`pentest-report.md`) cũng như cho
> mỗi dev **tự rà ở B4** (shift-left).
>
> **Bản đã ký (PDF) là bản chính thức/pháp lý.** Các file `.md` trong kit là **checklist làm việc** chắt từ
> chuẩn — khi có khác biệt, ưu tiên bản ký.

## 1. Hai chuẩn áp dụng

| Chuẩn | Phạm vi | Ngôn ngữ gốc | Áp cho |
|-------|---------|--------------|--------|
| **SEC-WEB** — Web Application Security Standards | Web / server-side: authentication, session, access control, input validation & injection, cryptography, error handling & logging, file upload, communication/TLS, cấu hình | English | **Java (Spring Boot)**, **Angular**, **Next.js** |
| **SEC-MOBILE** — Chuẩn an toàn ứng dụng di động | Mobile: xác thực, bảo mật liên lạc app↔server, lưu trữ dữ liệu trên thiết bị, tương tác nền tảng, chống dịch ngược/giả mạo, chất lượng mã | Tiếng Việt | **React Native**, **Flutter** |

Chuẩn liên quan được trích dẫn bên trong: **SEC-AUTH** (đăng nhập & quản lý mật khẩu), **SEC-PWD**
(chính sách mật khẩu), **SEC-LOG** (ghi log ứng dụng).

### 1.1 Truy vết mã chuẩn → bản gốc đã ký

Mã `SEC-*` là mã nội bộ của kit. Bản gốc đã ký nằm ở `reference-docs-signed/` — **giữ nguyên tên file gốc**
vì đây là tài liệu pháp lý:

| Mã kit | Bản gốc đã ký |
|--------|---------------|
| **SEC-WEB** | `ST.TIM.ITC.16.WEB APPLICATION SECURITY STANDARDS.pdf` |
| **SEC-MOBILE** | `TC_CNVTQĐ_CNTT_19_Tieu_chuan_an_toan_thong_tin_ung_dung_di_dong.pdf` |
| **SEC-AUTH** · **SEC-PWD** · **SEC-LOG** | Chuẩn liên quan do SEC-MOBILE trích dẫn — bổ sung bản ký khi cần viện dẫn |

> Khi đổi sang bộ chuẩn khác, **chỉ cần sửa bảng này**; mã `SEC-*` trong toàn bộ checklist giữ nguyên.

> **Ngoài phạm vi:** chuẩn lập trình an toàn **C/C++** (*Tiêu chuẩn lập trình an toàn*) — stack của kit
> (Java / Angular / Next.js / React Native / Flutter) **không dùng C/C++** nên không đưa vào kit. Khi phát
> sinh dự án C/C++, bổ sung checklist riêng từ chuẩn tương ứng.

## 2. Ánh xạ nền tảng → checklist

| Nền tảng | Skill | Checklist làm việc | Chuẩn gốc |
|----------|-------|--------------------|-----------|
| Java (Spring Boot) | `backend-dev-java` | `backend-dev-java/references/secure-coding-java.md` | SEC-WEB |
| Angular | `frontend-dev` | `frontend-dev/references/secure-coding-angular.md` | SEC-WEB |
| Next.js | `frontend-dev` | `frontend-dev/references/secure-coding-nextjs.md` | SEC-WEB |
| React Native | `mobile-dev` | `mobile-dev/references/secure-coding-react-native.md` | SEC-MOBILE |
| Flutter | `mobile-dev` | `mobile-dev/references/secure-coding-flutter.md` | SEC-MOBILE |

**Mã trích dẫn điều khoản** (mỗi dòng checklist đều có cột *Mã chuẩn*):
- Web: `SEC-WEB §<chương>.<mục>` — ví dụ `SEC-WEB §1.6` (chỉ lưu bản băm mật khẩu).
- Mobile: `SEC-MOBILE §<điều khoản>` — ví dụ `SEC-MOBILE §1a` (xác thực).

## 3. Cách dùng trong pipeline

**B4 — shift-left (mỗi dev tự rà, trước khi handover):** trước khi bàn giao tester, dev đối chiếu code với
checklist nền tảng của mình (mục *"✅ Checklist tự rà"* cuối mỗi file). Đây là **mục Definition of Done ở
GATE-4**; điểm chưa đạt phải sửa hoặc ghi nhận rủi ro.

**B5 — căn cứ tuân thủ (security-engineer):** ngoài threat model (STRIDE) + OWASP Top 10, `security-engineer`
dùng **chính hai chuẩn này** làm căn cứ đối chiếu. Mọi finding gắn với một yêu cầu chuẩn phải **trích mã
điều khoản** (vd `SEC-WEB §4.x`) trong `pentest-report.md`, để truy vết ngược về chuẩn.

## 4. Nguyên tắc
- **Song ngữ:** thực hiện theo ngôn ngữ hội thoại (Việt/Anh), mặc định tiếng Việt; **giữ nguyên** mã chuẩn,
  mã điều khoản, tên API/thư viện. Bản chuẩn đã ký giữ ngôn ngữ gốc.
- Checklist là mức **tối thiểu**, không thay thế review thủ công hay bản chuẩn đã ký.
- Không commit secret/PII; ví dụ dùng dữ liệu ẩn danh (PDPL).
