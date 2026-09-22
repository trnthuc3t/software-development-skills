# Secure Coding Checklist — Flutter (Thức Mobile)

Checklist secure coding cho **Flutter**, chắt từ chuẩn **SEC-MOBILE** (bản đã ký là bản chính thức; file này là checklist làm việc).

> Cách đọc: mỗi nhóm bám một chương/điều khoản của chuẩn. Cột **Mã chuẩn** trỏ về điều khoản gốc trong `SEC-MOBILE` (ứng dụng di động). Thuật ngữ kỹ thuật (English) giữ nguyên. Dữ liệu cá nhân/PII tuân thủ **PDPL**.

---

## 1. Quản lý xác thực (Authentication)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Flutter | Mã chuẩn |
|---|--------------|------------------|------------------------------|----------|
| 1 | Phương thức xác thực, mức độ xác thực và quản lý phiên tuân thủ chuẩn xác thực & quản lý mật khẩu (SEC-AUTH) | Authentication method, assurance level and session management must follow the login/password standard (SEC-AUTH) | Dùng OAuth2/OIDC qua `flutter_appauth`; access/refresh token do server cấp; hỗ trợ MFA/biometric qua `local_auth`; **không** tự chế cơ chế auth riêng | SEC-MOBILE §1a |
| 2 | Chính sách mật khẩu mạnh cho tài khoản người dùng thực thi ở backend (chuẩn SEC-PWD) | Strong password policy enforced on the backend (standard SEC-PWD) | Client chỉ validate sơ bộ (độ dài/độ phức tạp) trên form để phản hồi nhanh; **không nới lỏng**, enforcement thật ở backend; **không lưu mật khẩu** trên thiết bị | SEC-MOBILE §1b |

## 2. Bảo mật thông tin liên lạc app↔server (Communication security)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Flutter | Mã chuẩn |
|---|--------------|------------------|------------------------------|----------|
| 1 | Mã hóa toàn bộ dữ liệu app↔server; bắt buộc HTTPS, tối thiểu TLS 1.2+ | Encrypt all app↔server traffic; enforce HTTPS, TLS 1.2+ minimum | Mọi request qua `https://` (dio/http); iOS bật ATS (`NSAllowsArbitraryLoads=false`); Android `network_security_config` đặt `cleartextTrafficPermitted=false`; không override `badCertificateCallback` để bỏ qua lỗi TLS | SEC-MOBILE §2a |
| 2 | Áp dụng certificate pinning chống MITM; kiểm tra Trusted Root CA, hạn cert và CN khớp domain | Apply certificate pinning against MITM; validate Trusted Root CA, expiry and CN matching domain | `dio` + `badCertificateCallback` so khớp **SPKI/fingerprint** (hoặc `http_certificate_pinning`, hoặc `SecurityContext.setTrustedCertificates`); kiểm chain/expiry/SAN-CN; chuẩn bị backup pin để xoay | SEC-MOBILE §2b |
| 3 | Server cấp token (ký bằng giải thuật đủ an toàn) để nhận diện phiên giao tiếp | Server issues a securely-signed token to identify the session | Nhận JWT ký `RS256`/`ES256` từ server; lưu token trong **flutter_secure_storage** (Keychain/Keystore); gắn `Authorization: Bearer`; **không** để token trong URL/log | SEC-MOBILE §2c |
| 4 | Phiên phải kết thúc khi hết thời gian, token hết hạn, hoặc user đăng xuất | Session must end on timeout, token expiry, or user logout | Đặt idle/absolute timeout; refresh khi token còn hạn; **xóa token khỏi secure storage khi logout**; interceptor bắt `401` → điều hướng đăng nhập lại | SEC-MOBILE §2d |

## 3. Nhật ký hệ thống (Logging)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Flutter | Mã chuẩn |
|---|--------------|------------------|------------------------------|----------|
| 1 | Ghi log trên app tuân thủ chuẩn ghi log SEC-LOG; tắt log nhạy cảm ở release | App logging follows the SEC-LOG logging standard; disable sensitive logs in release | Dùng `logger` có cấp độ, gate bằng `kDebugMode`; ở **release** đặt `debugPrint = (m,{wrapWidth}) {}` để nuốt log; **không log** token/mật khẩu/PII (PDPL); nếu gửi log remote thì mã hóa kênh, đồng bộ event-id với backend | SEC-MOBILE §3a |

## 4. An toàn ứng dụng & mã nguồn (App & source code security)

### 4.1 Chống tấn công phía client (Client-side attack protection)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Flutter | Mã chuẩn |
|---|--------------|------------------|------------------------------|----------|
| 1 | Ngăn SQL Injection | Prevent SQL Injection | Truy vấn `sqflite`/`drift` bằng **parameterized query** (`whereArgs`/`?`), không nối chuỗi; ưu tiên `drift` (compile-time checked) | SEC-MOBILE §4.1a |
| 2 | Ngăn XSS (đặc biệt trong WebView) | Prevent XSS (especially in WebView) | Hạn chế `webview_flutter`; đặt `JavaScriptMode.disabled` khi không cần JS; siết `NavigationDelegate`/whitelist URL; sanitize dữ liệu render trong `flutter_html`; không nhúng input chưa sanitize | SEC-MOBILE §4.1b |
| 3 | Ngăn Tapjacking (overlay lừa chạm) | Prevent Tapjacking (overlay hijack) | Android (native/`FlutterActivity`): `filterTouchesWhenObscured=true` trên view nhạy cảm; kiểm overlay; đặt secure flag cho màn hình nhạy cảm | SEC-MOBILE §4.1c |
| 4 | Ngăn Path Traversal | Prevent Path Traversal | Validate & normalize path (`package:path` `p.normalize`) khi dùng `path_provider`/`dart:io`; chặn `../`; whitelist thư mục; không nhận đường dẫn tùy ý từ deep link/input | SEC-MOBILE §4.1d |
| 5 | Ngăn Buffer Overflow | Prevent Buffer Overflow | Dart an toàn bộ nhớ; rủi ro nằm ở **FFI/native** (`dart:ffi`, C/C++): bật bounds-checking, kiểm độ dài buffer, cập nhật thư viện native | SEC-MOBILE §4.1e |

### 4.2 An toàn khi dùng framework/thư viện bên thứ 3 (3rd-party libraries)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Flutter | Mã chuẩn |
|---|--------------|------------------|------------------------------|----------|
| 1 | Loại code thừa, thành phần và thư viện không cần thiết | Remove dead code and unnecessary components/libraries | `dependency_validator` để gỡ package thừa; tách `dev_dependencies`; bật tree-shaking (mặc định khi build release); loại asset/demo code thừa | SEC-MOBILE §4.2a |
| 2 | Dùng phiên bản mới nhất và cập nhật bản vá trước khi dùng | Use latest versions and apply patches before use | `flutter pub outdated` + `dart pub upgrade`; quét CVE bằng OSV-Scanner; theo dõi advisory pub.dev; pin `pubspec.lock` | SEC-MOBILE §4.2b |
| 3 | Tắt chế độ development của framework trước khi triển khai thực tế | Turn off framework development mode before production | Build **release** (`flutter build apk/ipa --release`); tắt debug banner; **không** dùng debug tooling/DevTools ở release; `kReleaseMode` gate | SEC-MOBILE §4.2c |

### 4.3 Bảo mật các thành phần ứng dụng (Component security)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Flutter | Mã chuẩn |
|---|--------------|------------------|------------------------------|----------|
| 1 | Phân quyền truy cập an toàn cho các thành phần (Activity, Broadcast Receiver, Service, Content Provider trên Android) | Secure access control for components (Activity, Broadcast Receiver, Service, Content Provider on Android) | Đặt `android:exported=false` mặc định trong `AndroidManifest.xml`, chỉ export khi cần và kèm `permission`; iOS hạn chế custom URL scheme, ưu tiên **Universal Links** + validate; bảo vệ **platform channels** (không expose API nhạy cảm) | SEC-MOBILE §4.3a |
| 2 | Chỉ ứng dụng được cấp quyền hợp lệ (theo thiết kế) mới truy cập/thực thi thành phần | Only authorized apps (by design) may access/invoke components | Định nghĩa custom permission `signature`; validate caller/intent; kiểm soát deep link (`app_links`/`uni_links`) và validate payload; **không** expose method channel nhạy cảm ra ngoài | SEC-MOBILE §4.3b |

### 4.4 Bảo vệ mã nguồn / obfuscation (Source code protection)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Flutter | Mã chuẩn |
|---|--------------|------------------|------------------------------|----------|
| 1 | Không hardcode thông tin quan trọng (keys, username/password…) | Do not hardcode secrets (keys, username/password…) | **Không** nhúng secret trong code/asset (asset dễ giải nén); dùng backend proxy/remote config; secret runtime qua **flutter_secure_storage**; `--dart-define` chỉ cho non-secret build config; quét `gitleaks` | SEC-MOBILE §4.4a |
| 2 | Tắt debugger và xóa thông tin thừa trong code trước khi xuất bản | Disable debugger and strip leftover info before publishing | Build release (loại `assert`, tắt `debugPrint`); Android `android:debuggable=false`; gỡ debug/test code; không public symbol map | SEC-MOBILE §4.4b |
| 3 | Làm nhiễu mã nguồn (obfuscation) và biên dịch an toàn trước release; công cụ thương mại cho app quan trọng/ATTT cấp 3+ | Obfuscate and compile securely before release; commercial tools for critical apps / security level 3+ | `flutter build --obfuscate --split-debug-info=<dir>` (lưu symbol để giải mã crash); R8/ProGuard cho phần native Android; app từ **Rất quan trọng / ATTT cấp 3+** bổ sung công cụ obfuscation thương mại (vd DexGuard/iXGuard cho lớp native) | SEC-MOBILE §4.4c |
| 4 | Phát hiện root/jailbreak/emulator: không cho cài hoặc dừng app | Detect root/jailbreak/emulator: block install or stop the app | `flutter_jailbreak_detection`/`freeRASP`/`safe_device`: phát hiện root/jailbreak/giả lập → chặn cài hoặc dừng app; kết hợp Play Integrity (Android)/DeviceCheck (iOS) | SEC-MOBILE §4.4d |

### 4.5 Đánh giá ATTT (Security assessment)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Flutter | Mã chuẩn |
|---|--------------|------------------|------------------------------|----------|
| 1 | Đánh giá ATTT tổng thể (blackbox + whitebox) cho backend và mobile trước khi đưa vào sử dụng | Full security assessment (blackbox + whitebox) for backend and mobile before go-live | Pentest trước release theo **OWASP MASVS/MASTG**; quét `.ipa`/`.apk` bằng MobSF; dùng đơn vị kiểm định được cấp phép | SEC-MOBILE §4.5a |
| 2 | Định kỳ đánh giá ATTT tối thiểu 1 lần/năm với app Rất quan trọng / ATTT cấp 3+ | Periodic assessment at least yearly for critical apps / security level 3+ | Lập lịch pentest ≥ **1 lần/năm**; retest sau khi vá; lưu vết báo cáo theo phiên bản release | SEC-MOBILE §4.5b |
| 3 | Bộ phận ATTT đánh giá phần thay đổi ở mỗi lần nâng cấp trước khi phát hành | Security team assesses changed parts on every upgrade before release | Tích hợp `dart analyze` + SAST/DAST + OSV-Scanner trong CI; đánh giá diff mỗi lần nâng cấp; cổng release chặn nếu còn High/Critical | SEC-MOBILE §4.5c |

## 5. Bảo đảm an toàn dữ liệu (Data security)

| # | Yêu cầu (VI) | Requirement (EN) | Cách hiện thực trong Flutter | Mã chuẩn |
|---|--------------|------------------|------------------------------|----------|
| 1 | Khuyến nghị không dùng tính năng ghi nhớ mật khẩu | Recommend not using the "remember password" feature | Không lưu mật khẩu; thay bằng token + biometric unlock (`local_auth`); nếu buộc "remember" thì chỉ giữ refresh token trong `flutter_secure_storage`, không lưu plaintext password | SEC-MOBILE §5a |
| 2 | Mã hóa và phân quyền truy cập cho **tất cả** dữ liệu app lưu trên thiết bị | Encrypt and access-control **all** app data stored on device | Bí mật → **flutter_secure_storage** (Keychain/Keystore); DB nhạy cảm → `drift`/`sqflite` + **SQLCipher** (`sqlcipher_flutter_libs`); **KHÔNG dùng shared_preferences** cho dữ liệu nhạy cảm (không mã hóa); tắt backup (`android:allowBackup=false`); che màn hình nền (`FLAG_SECURE`/`secure_application`) (PDPL) | SEC-MOBILE §5b |

---

## ✅ Checklist tự rà (dev, trước khi handover — B4)

- [ ] **Token & bí mật** lưu ở **flutter_secure_storage** (Keychain/Keystore); **không** dùng `shared_preferences` cho dữ liệu nhạy cảm. — §2c, §5b
- [ ] **Không nhúng** secret/API key trong code/asset; đã quét `gitleaks`, sạch. — §4.4a
- [ ] Mọi call qua **HTTPS TLS 1.2+**; ATS/iOS + `network_security_config`/Android chặn cleartext; không bypass `badCertificateCallback`. — §2a
- [ ] **Certificate pinning** bật (SPKI/fingerprint), có backup pin; đã kiểm Root CA/expiry/CN-SAN. — §2b
- [ ] Phiên tự kết thúc khi **timeout / token hết hạn / logout**; interceptor xử lý `401`; xóa token khi logout. — §2d
- [ ] **Release** đã tắt `debugPrint`/log nhạy cảm (`kReleaseMode`), `debuggable=false`, không public symbol map. — §3a, §4.4b
- [ ] Build `--obfuscate --split-debug-info`; app quan trọng/ATTT cấp 3+ đã bổ sung obfuscation thương mại. — §4.4c
- [ ] **Root/jailbreak/emulator detection** hoạt động: chặn cài hoặc dừng app. — §4.4d
- [ ] `sqflite`/`drift` dùng **parameterized query**; WebView `JavaScriptMode.disabled`/whitelist, không nhúng input chưa sanitize. — §4.1a, §4.1b
- [ ] Android components `exported=false` (trừ khi cần + có permission); deep link đã validate; **platform channels** không expose API nhạy cảm. — §4.3a, §4.3b
- [ ] `flutter pub outdated`/OSV-Scanner sạch High/Critical; package ở bản mới nhất/đã vá; gỡ package thừa; build `--release`. — §4.2a, §4.2b, §4.2c
- [ ] Đã đặt lịch/pentest **MASVS/MASTG** (MobSF) và đánh giá diff cho phần thay đổi trước release. — §4.5a, §4.5c
