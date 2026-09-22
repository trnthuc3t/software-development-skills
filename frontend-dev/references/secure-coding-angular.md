# Secure Coding Checklist — Angular

Checklist secure coding cho Angular, chắt từ chuẩn **SEC-WEB** (bản đã ký là bản chính thức; file này là checklist làm việc).

> Angular là **client-side**: các kiểm soát authn/authz, injection, validation "thật" luôn nằm ở server. Phần dưới là những gì client PHẢI làm để không phá vỡ chuẩn + hỗ trợ phòng thủ. Cột **Mã chuẩn** = `SEC-WEB §<chương>.<mục>`. Ví dụ dùng dữ liệu giả (PDPL — không PII thật).

---

## 1. Authentication (§1)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Angular | Mã chuẩn |
|---|---|---|---|---|
| 1 | Chỉ lưu bản băm mật khẩu ở server — client không tự hash thay thế | Passwords hashed server-side — client must not substitute | Không hash mật khẩu ở browser để "thay" server; gửi mật khẩu qua kênh HTTPS cho server xử lý | SEC-WEB §1.6 |
| 2 | Hiển thị thông báo chung khi định danh tồn tại/sai credential | Show generic message on existing identity/wrong credentials | Bind message chung từ API; không suy đoán/tiết lộ tài khoản tồn tại trên UI | SEC-WEB §1.7 |
| 3 | Bật Captcha sau nhiều lần sai; không đếm số lần sai ở client | Captcha after failures; do not count failures client-side | Nhúng reCAPTCHA widget; số lần sai do **server** đếm — client không tự lưu/tự quyết | SEC-WEB §1.8 |
| 4 | Gửi thông tin định danh bằng POST qua HTTPS | Submit identity via POST over HTTPS | `HttpClient.post()` tới endpoint `https://`; không đưa credential vào URL/GET/query | SEC-WEB §1.9 |
| 5 | Tắt autocomplete cho field nhạy cảm | Disable autocomplete on sensitive fields | `autocomplete="off"` (hoặc `new-password`) trên input mật khẩu/thẻ/mã nạp | SEC-WEB §1.10 |

## 2. Session Management (§2)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Angular | Mã chuẩn |
|---|---|---|---|---|
| 1 | Xoá dữ liệu phiên ở client khi logout | Clear client session data on logout | Gọi endpoint logout + clear token in-memory/observable state; điều hướng về login | SEC-WEB §2.4 |
| 2 | Ưu tiên token trong cookie HttpOnly/Secure do server đặt; không lưu token nhạy cảm ở localStorage nếu tránh được | Prefer HttpOnly/Secure cookies; avoid sensitive tokens in localStorage | Để server đặt cookie `HttpOnly+Secure` (JS không đọc được); nếu buộc dùng token thì giữ in-memory, tránh `localStorage` (XSS lấy được) | SEC-WEB §2.5 |
| 3 | Gắn token chống CSRF cho request thay đổi trạng thái | Attach anti-CSRF token on state-changing requests | `HttpClientXsrfModule` + interceptor gắn header `X-XSRF-TOKEN` đọc từ cookie XSRF | SEC-WEB §2.6 |

## 3. Authorization (§3)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Angular | Mã chuẩn |
|---|---|---|---|---|
| 1 | Quyền dựa trên đối tượng do server cấp, không tự suy từ input client | Authorize on server-issued objects, not client-derived | Đọc role/claim từ token do server ký; không tự "nâng quyền" dựa trên biến client | SEC-WEB §3.1 |
| 2 | UI chỉ hiển thị thành phần/route đúng quyền | UI shows only permitted components/routes | `*ngIf`/directive theo role + Route Guard (`CanActivate`) ẩn menu/route không có quyền | SEC-WEB §3.3 |
| 3 | Route Guard chỉ là UX — authz thật phải ở server | Route guards are UX only — real authz on server | Guard chặn điều hướng cho đẹp; **mọi API vẫn phải được server kiểm quyền** trước khi trả dữ liệu | SEC-WEB §3.4 |

## 4. Tương tác Back-end (§4 — vai trò client)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Angular | Mã chuẩn |
|---|---|---|---|---|
| 1 | Chống injection (SQL/NoSQL/LDAP/XPath/OS) là trách nhiệm server; client không dựng câu truy vấn | Injection defense is server's job; client must not build queries | Client chỉ gửi tham số qua `HttpClient` (tự URL-encode param); không ghép query/không `eval()` dữ liệu | SEC-WEB §4.2 |
| 2 | Dữ liệu nhạy cảm mã hoá/che khi trao đổi | Sensitive data encrypted/masked in transit | Chỉ gọi HTTPS; không tự "mã hoá" bằng key nhúng trong bundle (client public); mask khi hiển thị | SEC-WEB §4.1 |
| 3 | Chỉ nhận JSON từ nguồn tin cậy; không deserialize kiểu nguy hiểm | Only trusted JSON; no unsafe deserialization | `JSON.parse` (không `eval`); tin dữ liệu từ API của mình, coi mọi response là dữ liệu chứ không phải code | SEC-WEB §4.10 |

## 5. Input Data Control (§5)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Angular | Mã chuẩn |
|---|---|---|---|---|
| 1 | Validate ở client là UX — validate bảo mật vẫn ở server | Client validation is UX — security validation stays server-side | Reactive Forms `Validators` cho trải nghiệm; **không** coi đó là lớp bảo mật; server luôn validate lại | SEC-WEB §5.1 |
| 2 | Ràng buộc kiểu/khoảng/kích thước/định dạng trên form | Constrain type/range/size/format on forms | `Validators.pattern/min/max/maxLength/email`; giới hạn kích thước file trước khi upload | SEC-WEB §5.4 |
| 3 | Nếu cần ký tự đặc biệt thì whitelist trên form | Whitelist expected chars if special chars required | `Validators.pattern` whitelist; chặn ký tự ngoài tập cho phép ở UI | SEC-WEB §5.5 |

## 6. Output Data Control / XSS (§6)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Angular | Mã chuẩn |
|---|---|---|---|---|
| 1 | Encode theo ngữ cảnh; escape ký tự đặc biệt từ nguồn không tin cậy | Context-aware encode; escape untrusted output | Dùng interpolation `{{ }}` (Angular **auto-escape** mặc định); không đưa dữ liệu user vào template thô | SEC-WEB §6.3 |
| 2 | Tránh render HTML thô từ dữ liệu người dùng | Avoid raw HTML from user data | Hạn chế `[innerHTML]`; nếu bắt buộc thì sanitize qua `DomSanitizer.sanitize(SecurityContext.HTML, …)` | SEC-WEB §6.3 |
| 3 | Không vô hiệu hoá cơ chế chống XSS của framework | Do not disable the framework's XSS protection | **Không** dùng `bypassSecurityTrustHtml/Url/Script/…` cho dữ liệu do người dùng nhập | SEC-WEB §6.3 |
| 4 | Áp Content-Security-Policy giảm rủi ro XSS | Apply CSP to reduce XSS impact | Bật CSP (server header/meta); tránh inline script/`eval`; dùng build hash cho script | SEC-WEB §6.3 |
| 5 | Hạn chế redirect tới URL ngoài whitelist | Restrict redirects to whitelisted URLs | `Router` không điều hướng theo URL lấy từ input chưa kiểm; whitelist domain đích | SEC-WEB §6.6 |
| 6 | Không lưu dữ liệu nhạy cảm ở cookie/thiết bị client | No sensitive data in client-side cookies/storage | Không ghi PII/token nhạy cảm vào cookie non-HttpOnly hay localStorage | SEC-WEB §6.5 |

## 7. Exception Handling & Logging (§7)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Angular | Mã chuẩn |
|---|---|---|---|---|
| 1 | Hiển thị lỗi chung, không lộ chi tiết hệ thống | Show generic errors, no system detail | `ErrorHandler` toàn cục + interceptor → hiển thị message chung, nuốt stack/chi tiết API | SEC-WEB §7.1 |
| 2 | Không log dữ liệu nhạy cảm ở client | Do not log sensitive data client-side | Tắt `console.log` token/PII ở production build; không gửi log chứa PII lên bên thứ ba | SEC-WEB §7.4 |

## 8. Third-party libraries / SCA (§8)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Angular | Mã chuẩn |
|---|---|---|---|---|
| 1 | Bỏ package/thành phần thừa | Remove redundant packages/components | Loại dependency không dùng; tree-shaking; kiểm bundle với `source-map-explorer` | SEC-WEB §8.1 |
| 2 | Dùng bản mới nhất từ npm chính thức | Use latest versions from official npm | Cập nhật Angular/deps; pin version qua `package-lock.json`; nguồn registry tin cậy | SEC-WEB §8.2 |
| 3 | Cập nhật patch thường xuyên | Regularly patch dependencies | `npm audit`/Dependabot/Snyk trong CI; `ng update` định kỳ | SEC-WEB §8.3 |
| 4 | Tắt development mode ở production | Disable dev mode in production | `ng build --configuration production` (bật `enableProdMode` tự động); tắt source map public | SEC-WEB §8.4 |

## 9. Business Logic (§9 — vai trò client)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Angular | Mã chuẩn |
|---|---|---|---|---|
| 1 | Logic nghiệp vụ quan trọng do server quyết định; client không tự quyết | Critical business logic decided server-side | Client phản ánh trạng thái từ server; không tự tính/duyệt giao dịch tiền ở client | SEC-WEB §9.1 |
| 2 | Chống double-submit cho thao tác quan trọng | Prevent double-submit on critical actions | Disable nút khi đang submit; debounce; dựa idempotency-key do server hỗ trợ | SEC-WEB §9.2 |

## 10. Config & Security Headers (build/deploy)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Angular | Mã chuẩn |
|---|---|---|---|---|
| 1 | Chỉ phục vụ qua HTTPS | Serve over HTTPS only | Ép HTTPS ở host/CDN; chỉ gọi API `https://`; bật HSTS ở server | SEC-WEB §1.9 |
| 2 | Security headers (CSP, nosniff, anti-clickjacking) do server đặt | Security headers set by server | Phối hợp server đặt CSP, `X-Content-Type-Options: nosniff`, `X-Frame-Options: DENY` | SEC-WEB §6.3 |
| 3 | Không nhúng secret trong bundle client (bundle là public) | No secrets in client bundle (it is public) | `environment.prod.ts` chỉ chứa cấu hình công khai; API key/secret ở server, không ở Angular | SEC-WEB §4.1 |

---

## ✅ Checklist tự rà (dev, trước khi handover — B4)

- [ ] Chỉ dùng interpolation `{{ }}` (auto-escape); rà sạch `[innerHTML]` thô và mọi `bypassSecurityTrust*` với dữ liệu người dùng (§6.3).
- [ ] Không lưu token/PII nhạy cảm ở `localStorage`; ưu tiên cookie HttpOnly/Secure do server đặt (§2.5, §6.5).
- [ ] Interceptor gắn token + `X-XSRF-TOKEN`; mọi call là HTTPS, credential không lên GET/URL (§1.9, §2.6).
- [ ] Route Guard + `*ngIf` theo role để ẩn UI — nhưng đã xác nhận server vẫn kiểm quyền mọi API (§3.3, §3.4).
- [ ] Reactive Forms validate cho UX; đã hiểu server mới là lớp validate bảo mật (§5.1, §5.4).
- [ ] Không tự dựng query/`eval` dữ liệu; chỉ `JSON.parse` response từ API tin cậy (§4.2, §4.10).
- [ ] Logout xoá sạch state client; hiển thị lỗi generic qua `ErrorHandler` (§2.4, §7.1).
- [ ] Không `console.log` token/PII ở production; không nhét secret vào bundle (§7.4, §4.1).
- [ ] CSP bật + tránh inline script/`eval`; redirect chỉ tới domain whitelist (§6.3, §6.6).
- [ ] `npm audit` sạch, deps mới nhất; build `--configuration production`, không lộ source map (§8.2, §8.3, §8.4).
- [ ] Field nhạy cảm đặt `autocomplete="off"`; hiển thị thông báo đăng nhập chung (§1.10, §1.7).
- [ ] Thao tác quan trọng chống double-submit (disable nút/idempotency) (§9.2).
