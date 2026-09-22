# Secure Coding Checklist — Next.js

Checklist secure coding cho Next.js, chắt từ chuẩn **SEC-WEB** (bản đã ký là bản chính thức; file này là checklist làm việc).

> Next.js là **full-stack**: client (React) + server (API Routes, Route Handlers, Server Actions, middleware, SSR). Nguyên tắc xuyên suốt: **không tin client** — validate/authz/injection-defense thực thi ở tầng server. Cột **Mã chuẩn** = `SEC-WEB §<chương>.<mục>`. Ví dụ dùng dữ liệu giả (PDPL — không PII thật).

---

## 1. Authentication (§1)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Next.js | Mã chuẩn |
|---|---|---|---|---|
| 1 | Reset/quên mật khẩu: token hết hạn sau lần dùng đầu hoặc 8h; ≤3 lần đổi/ngày | Reset: token expires after first use or 8h; ≤3 changes/day | Sinh token bằng `crypto.randomBytes` trong Route Handler, TTL 8h, one-time; rate-limit ≤3/ngày | SEC-WEB §1.5 |
| 2 | Chỉ lưu bản băm mật khẩu/PIN (SHA-256/512/SHA-3 hoặc tương đương) | Store only hashed passwords/PINs (or equivalent) | Hash bằng `bcrypt`/`argon2` trong API route/server action (salt, adaptive → thoả "or equivalent") | SEC-WEB §1.6 |
| 3 | Trả thông báo chung khi định danh tồn tại/sai credential | Generic message on existing identity/wrong credentials | API trả message chung; không tiết lộ tài khoản tồn tại; tránh chênh lệch thời gian phản hồi | SEC-WEB §1.7 |
| 4 | Bật Captcha sau nhiều lần sai; đếm số lần sai phía server | Captcha after failures; count failures server-side | reCAPTCHA + verify ở server; counter ở store server (Redis) theo user/IP, không ở client | SEC-WEB §1.8 |
| 5 | Gửi thông tin định danh bằng POST qua HTTPS | Submit identity via POST over HTTPS | Form/Server Action dùng POST; ép HTTPS; không đưa credential vào query/GET | SEC-WEB §1.9 |
| 6 | Tắt autocomplete cho field nhạy cảm | Disable autocomplete on sensitive fields | `autoComplete="off"`/`new-password` trên input mật khẩu/thẻ | SEC-WEB §1.10 |

## 2. Session Management (§2)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Next.js | Mã chuẩn |
|---|---|---|---|---|
| 1 | Session do server quản lý, sinh ngẫu nhiên, đủ entropy | Server-managed, random, sufficient-entropy session | NextAuth/Auth.js (JWT ký hoặc DB session); `AUTH_SECRET` mạnh từ env | SEC-WEB §2.1 |
| 2 | Sinh phiên mới sau khi đăng nhập thành công | New session after successful login | Rotate session/JWT khi login; không tái dùng session ẩn danh (chống fixation) | SEC-WEB §2.3 |
| 3 | Xoá session & dữ liệu khi logout | Delete session & data on logout | `signOut()` xoá cookie session; huỷ record DB session nếu dùng DB strategy | SEC-WEB §2.4 |
| 4 | Cookie phiên đặt Secure + HttpOnly (+ SameSite) | Session cookie Secure + HttpOnly (+ SameSite) | Cấu hình cookie NextAuth `httpOnly:true, secure:true, sameSite:'lax'` | SEC-WEB §2.5 |
| 5 | Hàm quan trọng phải có token chống CSRF, verify trước xử lý | Critical functions need anti-CSRF token verified first | CSRF token của NextAuth cho mutation; verify token trong Server Action/Route Handler thay đổi trạng thái | SEC-WEB §2.6 |

## 3. Authorization (§3)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Next.js | Mã chuẩn |
|---|---|---|---|---|
| 1 | Ủy quyền dựa trên đối tượng lưu ở server (session/DB) | Authorize on server-stored objects (session/DB) | Đọc session server (`auth()`/`getServerSession`); không tin role/ID từ body/header client | SEC-WEB §3.1 |
| 2 | UI chỉ hiển thị thành phần đúng quyền | UI shows only permitted components | Render có điều kiện theo role ở Server Component; ẩn route/menu — chỉ là UX | SEC-WEB §3.3 |
| 3 | Server kiểm quyền trước mọi tác vụ (middleware + handler) | Server verifies permissions before any action | `middleware.ts` chặn route + kiểm quyền lại trong mỗi Route Handler/Server Action; kiểm ownership (chống IDOR) | SEC-WEB §3.4 |
| 4 | Không đặt trang admin công khai; nếu buộc thì hạn chế IP/MFA | Admin not public; else IP restriction/MFA | Bảo vệ `/admin` bằng middleware + IP allowlist/MFA; không index/expose | SEC-WEB §3.6 |

## 4. Chống Injection & tương tác Back-end (§4) — API Routes / Server Actions

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Next.js | Mã chuẩn |
|---|---|---|---|---|
| 1 | SQL: prepared/parameterized query; nếu không thì whitelist | SQL: parameterized queries; else whitelist | Prisma / `pg` tham số hoá; **tránh** `$queryRawUnsafe`/nối chuỗi; cột động qua allowlist | SEC-WEB §4.2 |
| 2 | NoSQL: không expose ra Internet; bật auth; query an toàn/escape | NoSQL: not exposed; auth on; safe query/escape | DB không public; sanitize input Mongo (chặn operator `$`/`$where`); dùng driver an toàn | SEC-WEB §4.3 |
| 3 | XPath/LDAP: whitelist alphanumeric, blacklist ký tự đặc biệt | XPath/LDAP: whitelist alphanumeric, blacklist special chars | Escape/validate trước khi build filter; whitelist input; tránh nối chuỗi filter | SEC-WEB §4.4, §4.5 |
| 4 | OS: dùng API thực thi, không truyền dữ liệu user trực tiếp; nếu buộc thì whitelist | OS: exec API, no raw user data; else whitelist | `execFile`/`spawn` với mảng arg (không `shell:true`, không `exec` chuỗi); whitelist tham số | SEC-WEB §4.6 |
| 5 | File upload: whitelist định dạng, check extension, lưu ngoài thư mục public, map bằng ID, không trả absolute path | File upload: format whitelist, extension check, store outside public, map by ID, no absolute paths | Kiểm MIME+extension; lưu ngoài `public/` (blob/storage), tên UUID; không lộ path hệ thống | SEC-WEB §4.7 |
| 6 | SSRF: không lấy địa chỉ đích từ input; blacklist IP nội bộ sau DNS resolve | SSRF: no target from input; blacklist internal IPs post-DNS | Với server `fetch` ra ngoài: allowlist domain, chặn dải IP private sau resolve DNS | SEC-WEB §4.8 |
| 7 | XML: tắt external entity & remote doctype; encode khi tạo XML (XXE) | XML: disable external entity & remote doctype; encode on build | Dùng parser cấu hình tắt external entities; ưu tiên JSON thay vì XML khi có thể | SEC-WEB §4.9 |
| 8 | Deserialization: chỉ nguồn tin cậy/JSON; nếu không thì hash(D+S)+whitelist class | Deserialization: trusted/JSON only; else hash(D+S)+class whitelist | Chỉ `JSON.parse` (không `eval`/`Function`); không deserialize object tuỳ ý từ client | SEC-WEB §4.10 |

## 5. Crypto & bảo vệ dữ liệu (§4.1)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Next.js | Mã chuẩn |
|---|---|---|---|---|
| 1 | Dữ liệu nhạy cảm mã hoá trước khi lưu bằng AES-256 hoặc tương đương | Sensitive data encrypted at rest with AES-256 or equivalent | `node:crypto` AES-256-GCM ở server; key từ env/secret manager, không hardcode | SEC-WEB §4.1 |
| 2 | Dữ liệu nhạy cảm mã hoá/che khi trao đổi | Sensitive data encrypted/masked in transit | Bắt buộc TLS; mask field nhạy cảm trong response; crypto chỉ ở server (không ở client bundle) | SEC-WEB §4.1 |

## 6. Input Data Control (§5)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Next.js | Mã chuẩn |
|---|---|---|---|---|
| 1 | Validate bắt buộc ở server (API route/server action) — không tin client | Validate server-side (route/action) — never trust client | `zod`/`valibot` parse & validate trong Route Handler/Server Action; validate client chỉ là UX | SEC-WEB §5.1 |
| 2 | Validate mọi nguồn (params, headers, body, file) | Validate all sources (params, headers, body, file) | Schema hoá `searchParams`, `headers`, `formData`, file; coi mọi input là untrusted | SEC-WEB §5.2 |
| 3 | Validate kiểu/khoảng/kích thước/định dạng; whitelist ký tự đặc biệt | Validate type/range/size/format; whitelist special chars | zod `.min/.max/.regex/.email`; giới hạn kích thước body/upload; whitelist qua regex | SEC-WEB §5.4, §5.5 |

## 7. Output Data Control / XSS (§6)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Next.js | Mã chuẩn |
|---|---|---|---|---|
| 1 | Đặt Content-Type đúng định dạng trả về | Set correct Content-Type | `NextResponse.json()` cho JSON; đặt đúng content-type cho từng response | SEC-WEB §6.2 |
| 2 | Encode theo ngữ cảnh; tránh render HTML thô | Context-aware encode; avoid raw HTML | React **auto-escape** JSX mặc định; **tránh** `dangerouslySetInnerHTML`; nếu buộc thì sanitize (DOMPurify) | SEC-WEB §6.3 |
| 3 | Lọc `\n \r` khỏi dữ liệu user khi set response header | Filter `\n \r` from user data in response headers | Không set header trực tiếp từ input; sanitize giá trị trước khi `headers.set(...)` | SEC-WEB §6.4 |
| 4 | Cookie tối thiểu thuộc tính; không lưu dữ liệu nhạy cảm | Minimal cookie attributes; no sensitive data | `cookies().set` với `httpOnly/secure/sameSite/path`; không nhét PII vào cookie | SEC-WEB §6.5 |
| 5 | Hạn chế redirect; whitelist địa chỉ đích | Restrict redirects; whitelist targets | Không `redirect()` theo `?next=`/URL từ input chưa kiểm; whitelist domain (chống open redirect) | SEC-WEB §6.6 |
| 6 | SSR không lộ dữ liệu nhạy cảm ra client | SSR must not leak sensitive data to client | Props truyền xuống Client Component đều **public** — chỉ trả field cần thiết, lọc secret trước khi serialize | SEC-WEB §6.5 |

## 8. Exception Handling & Logging (§7)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Next.js | Mã chuẩn |
|---|---|---|---|---|
| 1 | Trả lỗi chung, không lộ stack/thông tin nhạy cảm | Generic errors, no stack/sensitive info | `error.tsx`/try-catch trả message chung; production ẩn chi tiết (chỉ digest), không trả stack ra client | SEC-WEB §7.1 |
| 2 | Ghi log lỗi để phân tích; đặt log an toàn | Log errors for analysis; store logs securely | Logger server (pino/…) đẩy tới sink an toàn, ngoài thư mục public | SEC-WEB §7.2, §7.3 |
| 3 | Không log dữ liệu nhạy cảm (PII, session, secret) | Do not log sensitive data (PII, session, secret) | Redact PII/token trong log; không `console.log` secret ở server/client | SEC-WEB §7.4 |

## 9. Third-party libraries / SCA (§8)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Next.js | Mã chuẩn |
|---|---|---|---|---|
| 1 | Bỏ package/thành phần thừa | Remove redundant packages | Loại dependency không dùng; kiểm bundle (`@next/bundle-analyzer`) | SEC-WEB §8.1 |
| 2 | Dùng bản mới nhất từ npm chính thức | Use latest versions from official npm | Cập nhật Next/React/deps; `package-lock.json` pin; registry tin cậy | SEC-WEB §8.2 |
| 3 | Cập nhật patch thường xuyên | Regularly patch dependencies | `npm audit`/Dependabot/Snyk trong CI | SEC-WEB §8.3 |
| 4 | Tắt development mode ở production | Disable dev mode in production | `next build` + `NODE_ENV=production`; tắt source map lộ; không bật debug/telemetry nhạy cảm | SEC-WEB §8.4 |

## 10. Business Logic (§9)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Next.js | Mã chuẩn |
|---|---|---|---|---|
| 1 | Logic quan trọng thực thi & kiểm tra ở server | Critical logic executed & checked server-side | Đặt luồng nhạy cảm trong Server Action/Route Handler; client chỉ hiển thị trạng thái | SEC-WEB §9.1 |
| 2 | Hàm quan trọng dùng lock chống race; đúng thứ tự giao dịch | Critical functions use locks; correct transaction order | Transaction DB + lock (Prisma `$transaction`, row lock); ghi nợ trước ghi có; idempotency-key | SEC-WEB §9.2, §9.5 |
| 3 | Giới hạn tin nhắn/OTP; OTP tách kênh, ≤3 lần sai/ngày | Limit messages/OTP; OTP out-of-band, ≤3 wrong/day | Rate-limit theo số nhận (≤3/ngày cho hàm quan trọng); OTP `randomBytes`, TTL ngắn, gửi qua SMS | SEC-WEB §9.6, §9.7 |

## 11. Config & Security Headers (next.config / deploy)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Next.js | Mã chuẩn |
|---|---|---|---|---|
| 1 | Thêm security headers (CSP, HSTS, nosniff, anti-clickjacking) | Add security headers (CSP, HSTS, nosniff, anti-clickjacking) | `headers()` trong `next.config.js`: CSP, `Strict-Transport-Security`, `X-Content-Type-Options: nosniff`, `X-Frame-Options: DENY` | SEC-WEB §6.2, §6.3 |
| 2 | Bắt buộc HTTPS/TLS | Enforce HTTPS/TLS | HSTS + redirect HTTP→HTTPS ở host/edge; chỉ cookie `secure` | SEC-WEB §1.9 |
| 3 | Secret không lộ ra client — chỉ `NEXT_PUBLIC_` mới public | Secrets not exposed — only `NEXT_PUBLIC_` is public | Biến không prefix chỉ đọc ở server; **không** đặt secret dưới `NEXT_PUBLIC_`; không import module server vào client | SEC-WEB §4.1 |

---

## ✅ Checklist tự rà (dev, trước khi handover — B4)

- [ ] Mọi mutation/đọc nhạy cảm validate bằng zod trong Route Handler/Server Action — không tin client (§5.1, §5.2).
- [ ] Truy vấn DB tham số hoá (Prisma/`pg`); không `$queryRawUnsafe`/nối chuỗi (§4.2).
- [ ] Auth qua NextAuth/middleware; server kiểm quyền + ownership mọi handler (không chỉ ẩn UI) (§3.1, §3.4).
- [ ] React auto-escape; đã rà sạch `dangerouslySetInnerHTML` (hoặc sanitize DOMPurify) (§6.3).
- [ ] Cookie phiên `HttpOnly+Secure+SameSite`; CSRF token cho form/mutation (§2.5, §2.6).
- [ ] Secret chỉ ở server; không biến nhạy cảm nào mang prefix `NEXT_PUBLIC_`; không import server-only vào client (§4.1).
- [ ] SSR/Server Component chỉ trả field cần thiết — không serialize secret xuống props client (§6.5).
- [ ] Mật khẩu hash bằng bcrypt/argon2 ở server; reset token one-time TTL 8h, ≤3 lần/ngày (§1.6, §1.5).
- [ ] Lỗi trả generic, production ẩn stack; log redact PII/token, lưu ngoài `public/` (§7.1, §7.4, §7.3).
- [ ] SSRF: allowlist domain + chặn IP nội bộ cho server `fetch`; upload whitelist + lưu ngoài `public/` (§4.8, §4.7).
- [ ] Security headers (CSP/HSTS/nosniff/X-Frame-Options) cấu hình trong `next.config`; HTTPS ép buộc (§6.2, §6.3, §1.9).
- [ ] `npm audit` sạch, deps mới nhất; `next build` production, không lộ source map (§8.2, §8.3, §8.4).
