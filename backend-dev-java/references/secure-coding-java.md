# Secure Coding Checklist — Java (Spring Boot)

Checklist secure coding cho Java (Spring Boot), chắt từ chuẩn **SEC-WEB** (bản đã ký là bản chính thức; file này là checklist làm việc).

> Quy ước: mỗi dòng bám một điều khoản trong `SEC-WEB — Web Application Security Standards`. Cột **Mã chuẩn** = `SEC-WEB §<chương>.<mục>`. Ví dụ dùng dữ liệu giả (PDPL — không PII thật).

---

## 1. Authentication (§1)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Java (Spring Boot) | Mã chuẩn |
|---|---|---|---|---|
| 1 | Username duy nhất, không phân biệt hoa/thường, chỉ gồm chữ–số–gạch dưới | Usernames unique, case-insensitive, only letters/numbers/underscore | `@Pattern("^[A-Za-z0-9_]+$")` (Bean Validation) + chuẩn hoá lowercase; unique index / collation case-insensitive ở DB | SEC-WEB §1.1 |
| 2 | Không dùng chung credential; giữa các process/service phải tách biệt hoàn toàn | No shared credentials; distinct per process/service | Mỗi service/client một credential riêng, lấy từ env/Secret Manager (Vault), không hardcode dùng chung | SEC-WEB §1.2 |
| 3 | Chính sách mật khẩu mạnh; không nằm trong blacklist mật khẩu phổ biến | Strong password policy; not in common-password blacklist | Custom `ConstraintValidator` kiểm độ mạnh + đối chiếu blacklist (Appendix 01) trước khi set | SEC-WEB §1.3 |
| 4 | Mật khẩu hết hạn tối đa 90 ngày; mật khẩu mới khác mật khẩu hiện tại | Max password age 90 days; new ≠ current | Lưu `passwordChangedAt`; enforce 90 ngày; `passwordEncoder.matches(new, currentHash)` để chặn trùng | SEC-WEB §1.4 |
| 5 | Reset/quên mật khẩu: link hết hạn sau lần dùng đầu hoặc 8h; mật khẩu gửi ra phải random+mạnh; OTP theo §9.7; ≤3 lần đổi/ngày | Reset: link expires after first use or 8h; mailed secret random+strong; OTP per §9.7; ≤3 changes/day | Token reset bằng `SecureRandom`/UUID, TTL 8h, one-time (đánh dấu `used`); rate-limit ≤3/ngày (Bucket4j/Redis) | SEC-WEB §1.5 |
| 6 | Chỉ lưu bản băm của mật khẩu/PIN trong DB (SHA-256/512/SHA-3 hoặc tương đương) | Store only hashed passwords/PINs (SHA-256/512/SHA-3 or equivalent) | `BCryptPasswordEncoder` hoặc `Argon2PasswordEncoder` (có salt, adaptive → thoả "or equivalent"); không bao giờ lưu plaintext | SEC-WEB §1.6 |
| 7 | Trả thông báo chung khi định danh đã tồn tại hoặc sai credential | Generic message on existing identity or wrong credentials | Trả message chung (vd "Invalid credentials"); không tiết lộ username/email tồn tại; tránh phân biệt thời gian phản hồi | SEC-WEB §1.7 |
| 8 | Bật Captcha sau 5 lần đăng nhập sai; đếm số lần sai không dựa SessionID, không lưu ở client | Captcha after 5 failed logins; counter not tied to SessionID, not client-side | `AuthenticationFailureHandler` + counter ở Redis theo username/IP; tích hợp reCAPTCHA; không đếm bằng session | SEC-WEB §1.8 |
| 9 | Chỉ dùng POST để gửi thông tin định danh; bắt buộc HTTPS | POST only for identity submission; HTTPS mandatory | `@PostMapping` cho login; `http.requiresChannel().anyRequest().requiresSecure()` + HSTS | SEC-WEB §1.9 |
| 10 | Tắt autocomplete cho form chứa thông tin nhạy cảm | Disable autocomplete on sensitive forms | Với view server-render đặt `autocomplete="off"`; với API thuần thì ràng buộc FE thực hiện | SEC-WEB §1.10 |
| 11 | Xác thực thuê bao phải truy vấn qua hệ thống VAAA | Subscriber auth must query via VAAA | Gọi VAAA client bắt buộc trong luồng xác thực thuê bao trước khi cấp phiên | SEC-WEB §1.11 |
| 12 | Web service cho mobile dùng định danh thuê bao phải cảnh báo 2 mức | Two-level alert for subscriber-identified mobile web services | Level 1: USSD/Captcha + SMS khi login thành công; Level 2: SMS khi login — gửi qua SMS gateway | SEC-WEB §1.12 |
| 13 | Web service server-to-server xác thực ≥3 yếu tố: username, password, Server IP | S2S auth ≥3 factors: username, password, server IP | Filter kiểm IP allowlist + username/password; khuyến nghị thêm mTLS; thận trọng với `X-Forwarded-For` | SEC-WEB §1.13 |

## 2. Session Management (§2)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Java (Spring Boot) | Mã chuẩn |
|---|---|---|---|---|
| 1 | Session do server quản lý, sinh ngẫu nhiên, tối thiểu 128-bit | Server-managed session, random, ≥128-bit | Dùng session container (Tomcat, ID ≥128-bit) hoặc Spring Session + Redis; entropy từ `SecureRandom` | SEC-WEB §2.1 |
| 2 | Session có timeout cân bằng nghiệp vụ & bảo mật | Session timeout balancing business & security | `server.servlet.session.timeout`; áp dụng idle + absolute timeout | SEC-WEB §2.2 |
| 3 | Sinh session mới sau khi đăng nhập thành công | New session after successful login | `sessionManagement().sessionFixation().newSession()` (chống session fixation) | SEC-WEB §2.3 |
| 4 | Xoá session ID & dữ liệu liên quan khi logout | Delete session ID and data on logout | `logout().invalidateHttpSession(true).deleteCookies("JSESSIONID")` | SEC-WEB §2.4 |
| 5 | Cookie đặt Secure (HTTPS) và HttpOnly | Set Secure (HTTPS) and HttpOnly on cookies | `server.servlet.session.cookie.secure=true`, `http-only=true`, thêm `SameSite=Lax/Strict` | SEC-WEB §2.5 |
| 6 | Hàm quan trọng tương tác DB phải sinh token/session ngẫu nhiên và kiểm tra trước khi xử lý | Critical DB functions: per-session random token verified before processing | Spring Security CSRF token (`CookieCsrfTokenRepository`); verify token mỗi request thay đổi trạng thái | SEC-WEB §2.6 |

## 3. Authorization (§3)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Java (Spring Boot) | Mã chuẩn |
|---|---|---|---|---|
| 1 | Ủy quyền dựa trên đối tượng lưu ở server (session/DB) | Authorize based on server-stored objects (session/DB) | Lấy `Authentication`/authorities từ `SecurityContext` (server), không tin role/ID gửi từ client | SEC-WEB §3.1 |
| 2 | Áp dụng nguyên tắc đặc quyền tối thiểu | Apply least privilege | Cấp role/authority tối thiểu; method security `@PreAuthorize` chi tiết theo chức năng | SEC-WEB §3.2 |
| 3 | UI chỉ hiển thị thành phần/đường dẫn/chức năng đúng quyền | UI shows only components matching permissions | Với SSR dùng `sec:authorize` (Thymeleaf); với API trả capability cho FE — UI hiding **không thay** kiểm tra server | SEC-WEB §3.3 |
| 4 | Server kiểm tra quyền trên chức năng/tài nguyên trước mọi tác vụ | Server verifies permissions before any action | `@PreAuthorize`/`@PostAuthorize`; kiểm tra ownership tài nguyên (chống IDOR) trước khi thao tác | SEC-WEB §3.4 |
| 5 | Cho phép chấm dứt phiên khi quyền thay đổi/bị vô hiệu | Ability to terminate session when permissions change/revoked | `SessionRegistry` + `session.expireNow()`; revoke JWT qua blocklist khi đổi quyền | SEC-WEB §3.5 |
| 6 | Không đặt trang admin trên Internet công cộng; nếu buộc thì hạn chế IP hoặc MFA | Admin page not on public Internet; else IP restriction or MFA | Tách context/port nội bộ + IP allowlist filter; bắt buộc MFA khi truy cập admin | SEC-WEB §3.6 |

## 4. Chống Injection & tương tác Back-end (§4)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Java (Spring Boot) | Mã chuẩn |
|---|---|---|---|---|
| 1 | SQL: dùng prepared statement/parameterized query; nếu không được thì whitelist input | SQL: use prepared/parameterized queries; else whitelist inputs | JPA/Hibernate parameter binding, `@Query` với named param, `PreparedStatement`; tuyệt đối không nối chuỗi vào native query | SEC-WEB §4.2.1 |
| 2 | SQL: khi không thể tham số hoá, whitelist giá trị mong đợi | SQL: whitelist expected inputs when parameterization impossible | ORDER BY/tên cột động → map qua allowlist/enum, không lấy trực tiếp từ input | SEC-WEB §4.2.2 |
| 3 | NoSQL: không expose ra Internet; bật authentication; dùng API truy vấn an toàn/escape | NoSQL: not Internet-exposed; auth enabled; safe query API/escape | Spring Data Mongo `Criteria` API (không dùng `$where`/JS eval); bật auth; cô lập network | SEC-WEB §4.3 |
| 4 | XPath: whitelist ký tự alphanumeric; blacklist `( ) = ' [ ] : , * /` và space | XPath: whitelist alphanumeric; blacklist special chars | Parameterized XPath (`XPathVariableResolver`); validate input theo whitelist trước khi build biểu thức | SEC-WEB §4.4 |
| 5 | LDAP: whitelist alphanumeric; blacklist `( ) ; , * \| & =` và null byte | LDAP: whitelist alphanumeric; blacklist special chars & null byte | Spring LDAP `LdapQueryBuilder`; escape filter/DN (`encodeForLDAP`), không nối chuỗi filter | SEC-WEB §4.5 |
| 6 | OS: dùng API thực thi lệnh, không truyền dữ liệu user trực tiếp; nếu buộc thì whitelist | OS: use exec APIs, no raw user data; else whitelist | `ProcessBuilder` với danh sách arg (không qua shell); tránh `Runtime.exec(String)`; whitelist tham số | SEC-WEB §4.6 |
| 7 | File: không truyền dữ liệu user vào hàm include; whitelist định dạng upload; check extension; lưu ngoài web dir & bỏ quyền execute; map bằng ID; không trả absolute path | File: no user data to include; upload whitelist; extension check; store outside webroot, no execute; map by ID; no absolute paths | Kiểm MIME + extension whitelist; lưu ngoài webroot, đặt filename bằng UUID; canonical-path check chống path traversal; trả qua `Content-Disposition` | SEC-WEB §4.7 |
| 8 | HTTP back-end: không tạo GET/POST param từ dữ liệu user (chống override); không lấy địa chỉ server từ input, blacklist IP nội bộ sau DNS resolve (SSRF) | Back-end HTTP: no params from user data (override); no server address from input, blacklist internal IPs post-DNS | Validate/allowlist URL đích; chặn dải IP private sau khi resolve DNS; dùng HTTP client có SSRF guard | SEC-WEB §4.8 |
| 9 | XML: tắt external entity & remote doctype khi parse; encode `< > ' "` khi tạo XML (XXE) | XML: disable external entity & remote doctype; encode on build | `DocumentBuilderFactory.setFeature("disallow-doctype-decl", true)`, tắt external entities; encode khi sinh XML | SEC-WEB §4.9 |
| 10 | Deserialization: chỉ nhận nguồn tin cậy/dùng JSON; nếu không thì hash(D+S)+whitelist class | Deserialization: trusted sources/JSON only; else hash(D+S)+class whitelist | Tránh Java native serialization; Jackson tắt default typing hoặc dùng `PolymorphicTypeValidator` (whitelist class) + HMAC toàn vẹn | SEC-WEB §4.10 |

## 5. Crypto & bảo vệ dữ liệu (§4.1)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Java (Spring Boot) | Mã chuẩn |
|---|---|---|---|---|
| 1 | Dữ liệu nhạy cảm phải mã hoá trước khi lưu bằng AES-256 hoặc tương đương | Sensitive data encrypted at rest with AES-256 or equivalent | JCE AES-256-GCM (hoặc column encryption/Jasypt); key quản lý qua KMS/Vault, không hardcode | SEC-WEB §4.1 |
| 2 | Dữ liệu nhạy cảm phải mã hoá hoặc che (mask) khi trao đổi | Sensitive data encrypted or masked in transit/exchange | Bắt buộc TLS end-to-end; mask field nhạy cảm trong payload/response theo nghiệp vụ | SEC-WEB §4.1 |

## 6. Input Data Control (§5)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Java (Spring Boot) | Mã chuẩn |
|---|---|---|---|---|
| 1 | Validate đầu vào bắt buộc thực hiện phía server | Input validation must be server-side | `@Valid`/`@Validated` trên controller + Bean Validation; không tin validation của client | SEC-WEB §5.1 |
| 2 | Validate mọi nguồn tương tác (GET/POST, HTTP headers, DB, file upload) | Validate all sources (params, headers, DB, uploads) | Validate `@RequestParam`/`@RequestBody`/`@RequestHeader`; coi dữ liệu từ DB/file là untrusted | SEC-WEB §5.2 |
| 3 | Chuẩn hoá encoding thống nhất rồi mới lọc/validate | Normalize to consistent encoding before filtering | Chuẩn hoá UTF-8, `Normalizer.normalize(NFC)` trước khi validate/so khớp | SEC-WEB §5.3 |
| 4 | Validate kiểu, khoảng, kích thước, định dạng | Validate type, range, size, format | `@Min/@Max/@Size/@Pattern/@Email`; giới hạn kích thước request/multipart | SEC-WEB §5.4 |
| 5 | Nếu bắt buộc ký tự đặc biệt thì lập whitelist ký tự mong đợi | If special chars required, whitelist expected chars | `@Pattern` whitelist hoặc allowlist theo enum; từ chối ngoài tập cho phép | SEC-WEB §5.5 |

## 7. Output Data Control / XSS (§6)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Java (Spring Boot) | Mã chuẩn |
|---|---|---|---|---|
| 1 | Chỉ định charset cho dữ liệu đầu ra | Specify output charset | Chuẩn hoá UTF-8 cho response; đặt charset trên `produces` | SEC-WEB §6.1 |
| 2 | Đặt Content-Type đúng định dạng trả về (JSON → application/json) | Set correct Content-Type (JSON → application/json) | `@RestController`/`produces = APPLICATION_JSON_VALUE`; không trả HTML nhầm content-type | SEC-WEB §6.2 |
| 3 | Encode theo ngữ cảnh; HTML-encode `< > ' " &` từ nguồn không tin cậy | Context-aware encode; HTML-encode untrusted output | Thymeleaf auto-escape; API JSON để Jackson escape; dùng OWASP Java Encoder khi ghép HTML | SEC-WEB §6.3 |
| 4 | Lọc `\n \r` khỏi dữ liệu user trong response header (chống header injection) | Filter `\n \r` from user data in response headers | Sanitize giá trị header trước khi set; Spring chặn CRLF mặc định — không tắt | SEC-WEB §6.4 |
| 5 | Cookie tối thiểu thuộc tính; không lưu dữ liệu nhạy cảm; nếu cần thì mã hoá đối xứng mạnh, key ở server | Minimal cookie attributes; no sensitive data; if needed encrypt symmetrically, keys server-side | `ResponseCookie` builder (`httpOnly`, `secure`, `sameSite`, `path`); không nhét PII vào cookie | SEC-WEB §6.5 |
| 6 | Hạn chế redirect/forward tới URI khác; nếu cần thì whitelist địa chỉ | Restrict redirects/forwards; whitelist targets if needed | Allowlist redirect target; không redirect trực tiếp theo param (chống open redirect) | SEC-WEB §6.6 |

## 8. Exception Handling & Logging (§7)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Java (Spring Boot) | Mã chuẩn |
|---|---|---|---|---|
| 1 | try-catch, trả lỗi chung, không lộ thông tin nhạy cảm | try-catch, generic errors, no sensitive info | `@ControllerAdvice`/`@ExceptionHandler` → `ProblemDetail` chung; `server.error.include-stacktrace=never` | SEC-WEB §7.1 |
| 2 | Log lỗi/exception phục vụ bảo trì & phân tích nguyên nhân | Log errors/exceptions for maintenance & RCA | SLF4J/Logback có correlation/trace id; log đủ để điều tra | SEC-WEB §7.2 |
| 3 | File log đặt ở thư mục an toàn ngoài web directory | Log files in secure directory outside webroot | Cấu hình Logback path ngoài webroot; quyền OS hạn chế | SEC-WEB §7.3 |
| 4 | Không log dữ liệu nhạy cảm (thông tin user, session ID, thông tin hệ thống) | Do not log sensitive data (user info, session IDs, system info) | Mask/omit PII và token; log scrubbing pattern; không log body chứa credential | SEC-WEB §7.4 |
| 5 | Hạn chế người dùng truy cập file log | Restrict user access to log files | Quyền file hệ điều hành; không phục vụ log qua HTTP | SEC-WEB §7.5 |

## 9. Third-party libraries / SCA (§8)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Java (Spring Boot) | Mã chuẩn |
|---|---|---|---|---|
| 1 | Bỏ code, thành phần, thư viện thừa | Remove redundant code/components/libraries | `mvn dependency:analyze`; loại dependency không dùng, thu gọn starter | SEC-WEB §8.1 |
| 2 | Dùng bản mới nhất, tải từ nguồn chính thức/tin cậy | Use latest versions from official/trusted sources | Maven Central / Nexus nội bộ; pin version; verify checksum/signature | SEC-WEB §8.2 |
| 3 | Thường xuyên cập nhật patch cho framework/thư viện | Regularly patch frameworks/libraries | OWASP Dependency-Check/Snyk (SCA) trong CI; Renovate/Dependabot tự động | SEC-WEB §8.3 |
| 4 | Tắt development mode ở môi trường production | Disable framework dev mode in production | Profile `prod`; loại Spring DevTools khỏi build; tắt actuator nhạy cảm; `show-sql=false` | SEC-WEB §8.4 |

## 10. Business Logic (§9)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Java (Spring Boot) | Mã chuẩn |
|---|---|---|---|---|
| 1 | Hiểu toàn bộ luồng nghiệp vụ & xác định ngoại lệ từng bước | Understand full business flow & identify exceptions | Thiết kế theo state machine; kiểm tiền/hậu điều kiện mỗi bước | SEC-WEB §9.1 |
| 2 | Hàm quan trọng (chuyển tiền) dùng lock chống race condition | Critical functions use locks against race conditions | Optimistic lock (`@Version`) hoặc pessimistic (`SELECT ... FOR UPDATE`); distributed lock (Redisson) | SEC-WEB §9.2 |
| 3 | Tiền ảo/điện tử: chỉ trừ tài khoản sau xác thực đa bước | Virtual/e-currency: debit only after multi-step auth | Bắt buộc MFA (Password + SMS OTP/USSD) trước khi commit giao dịch | SEC-WEB §9.3 |
| 4 | Dịch vụ viễn thông: đăng ký VAS phải gửi thông báo cho KH | Telecom: VAS registration must notify customer | Publish event → notification service (idempotent) gửi SMS xác nhận | SEC-WEB §9.4 |
| 5 | Chuyển tiền: ghi nợ A thành công trước khi ghi có B | Money transfer: debit A before credit B | Transaction đúng thứ tự, atomic; saga/bù trừ khi lỗi | SEC-WEB §9.5 |
| 6 | Giới hạn số tin/ngày/số nhận; hàm quan trọng ≤3 tin/ngày | Limit messages/day/number; critical ≤3/day | Rate limiter theo số nhận (Redis counter), ngưỡng ≤3 cho đăng ký/khôi phục | SEC-WEB §9.6 |
| 7 | OTP: ≤3 lần sai/ngày (xoá cũ, sinh mới); OTP không dùng làm mật khẩu; gửi tách kênh (SMS) | OTP: ≤3 wrong/day (regenerate); not used as password; delivered out-of-band | OTP `SecureRandom`, TTL ngắn, attempt counter; gửi qua SMS gateway; không trả OTP qua kênh đăng nhập | SEC-WEB §9.7 |

## 11. Config & Security Headers (deployment)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Java (Spring Boot) | Mã chuẩn |
|---|---|---|---|---|
| 1 | Bắt buộc HTTPS/TLS cho toàn ứng dụng | Enforce HTTPS/TLS application-wide | `requiresSecure()` + HSTS header; TLS 1.2+; redirect HTTP→HTTPS | SEC-WEB §1.9 |
| 2 | Cấu hình cờ cookie Secure/HttpOnly/SameSite ở tầng config | Configure Secure/HttpOnly/SameSite at config layer | `server.servlet.session.cookie.*=true`; SameSite qua `CookieSameSiteSupplier` | SEC-WEB §2.5 |
| 3 | Thêm security headers trình duyệt (CSP, nosniff, anti-clickjacking) | Add browser security headers (CSP, nosniff, anti-clickjacking) | Spring Security `headers()`: CSP, `X-Content-Type-Options: nosniff`, `X-Frame-Options: DENY`, `Referrer-Policy` | SEC-WEB §6.2, §6.3 |
| 4 | Tắt dev mode & không lộ stack trace ở production | Disable dev mode & hide stack traces in prod | Profile `prod`, `include-stacktrace=never`, tắt actuator/DevTools; quản lý secret qua env/Vault | SEC-WEB §8.4 |

---

## ✅ Checklist tự rà (dev, trước khi handover — B4)

- [ ] Mọi truy vấn DB dùng PreparedStatement/JPA binding — không nối chuỗi (§4.2).
- [ ] Mật khẩu/PIN hash bằng BCrypt/Argon2, không plaintext; secret qua env/Vault (§1.6, §4.1).
- [ ] Authn/authz bằng Spring Security; mọi endpoint có `@PreAuthorize` + kiểm ownership chống IDOR (§3.1, §3.4).
- [ ] Input validate phía server bằng `@Valid` cho mọi nguồn (param/header/body/file) (§5.1, §5.2).
- [ ] Output encode đúng ngữ cảnh; Content-Type chuẩn; không header injection (§6.2, §6.3, §6.4).
- [ ] CSRF token bật cho request thay đổi trạng thái; session rotate sau login, cookie Secure/HttpOnly (§2.3, §2.5, §2.6).
- [ ] Lỗi trả generic, `include-stacktrace=never`; log không chứa PII/token; log ngoài webroot (§7.1, §7.4, §7.3).
- [ ] Upload: whitelist định dạng, lưu ngoài webroot, tên bằng UUID, chống path traversal (§4.7).
- [ ] XXE tắt (disallow-doctype); SSRF chặn IP nội bộ; deserialization chỉ JSON/whitelist class (§4.9, §4.8, §4.10).
- [ ] SCA (OWASP Dependency-Check) pass; dependency mới nhất; DevTools/actuator nhạy cảm tắt ở prod (§8.2, §8.3, §8.4).
- [ ] Security headers (CSP, HSTS, nosniff, X-Frame-Options) đã bật (§6.2, §6.3).
- [ ] Hàm nghiệp vụ quan trọng có lock chống race + rate-limit OTP/tin nhắn ≤3/ngày (§9.2, §9.6, §9.7).
