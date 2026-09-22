# ERROR CODE & I18N CONVENTION — Backend Java/Spring Boot (chuẩn Duy)

> **Mã lỗi là một phần của đặc tả chi tiết API.** Client phụ thuộc vào **`errorCode`**, không phụ thuộc câu
> chữ. Toàn hệ thống dùng envelope chuẩn `TransactionResponse<T>`; thông điệp đa ngôn ngữ qua `Translator`.
> Đây là hạng mục bắt buộc kiểm ở GATE-4 và GATE-5.

## 1. Envelope chuẩn (nguồn: chuẩn nội bộ đội)

### `BaseRequest` — mọi request kế thừa
```java
@AllArgsConstructor
@NoArgsConstructor
@Data
public class BaseRequest {
    @RequestIdRegex                         // custom annotation regex, xem input-validation.md
    @Schema(example = "1234567890")
    private String requestId;

    @NotBlank
    @Schema(example = "1.0")
    private String version = "1.0";
}
```

### `TransactionResponse<T>` — mọi response bọc trong đây
```java
@Data @AllArgsConstructor @NoArgsConstructor @Builder
public class TransactionResponse<T> {
    @Schema(description = "Request ID, format: az, AZ 09")
    private String requestId;
    @Schema(description = "Error Code of request: 00000 ... refer to Annex Description")
    private String errorCode;
    @Schema(description = "Error message describe about error code")
    private String errorMessage;
    @Schema(description = "The result content includes JSON object or JSONArray")
    private T result;
}
```
- Thành công: `errorCode = "00000"`, `result` có dữ liệu.
- Lỗi: `result = null`, `errorCode` theo Annex, `errorMessage` lấy theo ngôn ngữ (mục 4).
- `requestId` trong response **phải khớp** `requestId` client gửi.

## 2. Định dạng mã lỗi — 5 ký tự `xxxxx` (BẮT BUỘC)
Đúng **5 ký tự**, cấu trúc `[MM][T][NN]`:

| Vị trí | Ký tự | Ý nghĩa |
|--------|-------|---------|
| 1–2 | `MM` | **Module** — 2 chữ số, tăng tuần tự theo module (`00` = dùng chung/cross-cutting, `01`, `02`, …) |
| 3 | `T` | **Loại lỗi** (xem bảng dưới) |
| 4–5 | `NN` | **Mã lỗi thực sự** trong module+loại, 2 chữ số tăng tuần tự (`01`, `02`, …) |

### Bảng ký tự loại lỗi (vị trí thứ 3)
| `T` | Loại lỗi |
|-----|----------|
| `0` | (dành riêng) Thành công / không lỗi — chỉ dùng cho `00000` |
| `1` | Lỗi **validate input** |
| `2` | Lỗi **nghiệp vụ** |
| `3` | Lỗi **tích hợp bên thứ 3** |
| `4` | Lỗi **nội bộ** (hệ thống) |

### Ví dụ
| Mã | Giải nghĩa |
|----|-----------|
| `00000` | Thành công |
| `00499` | Lỗi nội bộ dùng chung (fallback — module `00`, loại `4`, mã `99`) |
| `01101` | Module `01` (invoice), validate input, mã `01` — ví dụ `requestId` sai định dạng |
| `01201` | Module `01`, nghiệp vụ, mã `01` — ví dụ hoá đơn đã thanh toán |
| `01301` | Module `01`, tích hợp bên thứ 3, mã `01` — ví dụ cổng thanh toán timeout |
| `01401` | Module `01`, nội bộ, mã `01` |

> **Annex Description là nguồn sự thật.** Mã mới phải đăng ký vào Annex (mã, module, loại, ý nghĩa) + thêm
> khoá vào messages trước khi dùng. **Không tái dùng** mã đã bỏ; luôn giữ kiểu `String` (số 0 đầu có nghĩa).

## 3. Ánh xạ mã lỗi ↔ HTTP status (BẮT BUỘC — phục vụ monitor)
**Trả HTTP status THẬT theo kết cục, KHÔNG gộp tất cả về 200.** Lý do: nếu mọi phản hồi đều `200` rồi chỉ
phân định qua `errorCode`, thì monitor/APM/ingress đều thấy "giao dịch đẹp" và không phát hiện được lỗi/tỉ
lệ thất bại. Vì vậy status phản ánh đúng kết cục để tách bạch khi giám sát:

| Kết cục | HTTP | Body |
|---------|------|------|
| Thành công (`errorCode = 00000`) | **2xx** (`200`; `201` khi tạo mới) | `result` có dữ liệu |
| Lỗi xác thực / uỷ quyền | **401 / 403** | thường do Spring Security (entry point / access denied) |
| Lỗi **validate input** (loại `1`) | **400** | `errorMessage` + chi tiết field lỗi |
| Lỗi **nghiệp vụ / giao dịch thất bại** (loại `2`) | **400** | `result = null`, `errorCode` + `errorMessage` chi tiết |
| Lỗi **tích hợp bên thứ 3** (loại `3`) | **502 / 503 / 504** (5xx) | lỗi phía phụ thuộc — tách khỏi lỗi client |
| Lỗi **nội bộ** (loại `4`, ví dụ `00499`) | **500** (5xx) | lỗi hệ thống |

- **Body luôn là `TransactionResponse`** kể cả 4xx/5xx (`errorCode` + `errorMessage`, `result = null`).
- Client dùng **HTTP status để monitor** và **`errorCode` để xử lý chi tiết** — hai lớp bổ sung nhau.
- **Quy tắc suy ra status từ mã 5 ký tự**: `00000` → 200; ký tự loại (vị trí 3): `1`→400, `2`→400,
  `3`→5xx (mặc định 502), `4`→500. Mã xác thực/uỷ quyền set override 401/403.

## 4. Đa ngôn ngữ (i18n) — dùng `Translator` làm mặc định
Ngôn ngữ hỗ trợ: **en, my, zh** (và biến thể hoa EN/MY/ZH); **fallback English**.

```java
@Component
public class Translator {
    private static ResourceBundleMessageSource messageSource;
    private static final List<String> language = new ArrayList<>();

    @Autowired
    Translator(ResourceBundleMessageSource messageSource) {
        Translator.messageSource = messageSource;
        language.add("en"); language.add("my"); language.add("zh");
        language.add("EN"); language.add("MY"); language.add("ZH");
    }

    public static String toLocale(String keyword) {
        Locale locale = LocaleContextHolder.getLocale();
        if (language.contains(locale.getLanguage()))
            return messageSource.getMessage(keyword, null, locale);
        else
            return messageSource.getMessage(keyword, null, Locale.ENGLISH);
    }
}
```
Cấu hình `ResourceBundleMessageSource` (UTF-8, basename `messages`):
```java
@Bean
public ResourceBundleMessageSource messageSource() {
    var ms = new ResourceBundleMessageSource();
    ms.setBasename("messages");
    ms.setDefaultEncoding("UTF-8");
    ms.setUseCodeAsDefaultMessage(false);   // thiếu khoá -> ném lỗi để phát hiện sớm khi build/test
    return ms;
}
```

### 4.1 File messages theo ngôn ngữ — **khoá chính là mã lỗi**
`src/main/resources/messages_en.properties`
```properties
00000=Success
00499=System error, please try again later
01101=Invalid request id format
01201=Invoice has already been paid
01301=Payment gateway timeout, please try again
ACCOUNT_NAME_REGEX=Account name is invalid
```
`src/main/resources/messages_my.properties` *(bản dịch cần localization team rà lại)*
```properties
00000=အောင်မြင်ပါသည်
00499=စနစ်အမှားရှိနေသည်၊ ကျေးဇူးပြု၍ နောက်မှ ထပ်စမ်းကြည့်ပါ
01101=တောင်းဆိုမှု id ပုံစံ မမှန်ကန်ပါ
01201=ဤဘီလ်ကို ငွေရှင်းပြီးဖြစ်သည်
01301=ငွေပေးချေမှုဂိတ်ဝေး တုံ့ပြန်မှုနှေးနေသည်၊ ထပ်စမ်းကြည့်ပါ
ACCOUNT_NAME_REGEX=အကောင့်အမည် မမှန်ကန်ပါ
```
`src/main/resources/messages_zh.properties`
```properties
00000=成功
00499=系统错误，请稍后重试
01101=请求ID格式无效
01201=该账单已支付
01301=支付网关超时，请重试
ACCOUNT_NAME_REGEX=账户名称无效
```
`src/main/resources/messages.properties` (mặc định = English) — copy nội dung `messages_en`.

> Quy ước khoá: **dùng chính mã lỗi 5 ký tự làm khoá** (`Translator.toLocale(errorCode)`), riêng thông điệp
> lỗi validate của annotation dùng **khoá chữ** (ví dụ `ACCOUNT_NAME_REGEX`) — xem `input-validation.md`.
> `errorCode` **không đổi** theo ngôn ngữ; chỉ `errorMessage` đổi.

## 5. Hiện thực (khuôn mẫu)

### 5.1 Enum danh mục mã lỗi (khớp Annex; message lấy từ bundle)
```java
@Getter
@RequiredArgsConstructor
public enum ErrorCode {
    SUCCESS("00000"),
    SYSTEM_ERROR("00499"),
    INVALID_REQUEST_ID("01101"),
    INVOICE_ALREADY_PAID("01201"),
    PAYMENT_GATEWAY_TIMEOUT("01301");

    private final String code;              // 5 ký tự [MM][T][NN]
}
```

### 5.2 Factory dựng response (điền errorMessage qua Translator)
```java
public final class ApiResponses {
    private ApiResponses() {}

    public static <T> TransactionResponse<T> ok(String requestId, T result) {
        return TransactionResponse.<T>builder()
            .requestId(requestId)
            .errorCode(ErrorCode.SUCCESS.getCode())
            .errorMessage(Translator.toLocale(ErrorCode.SUCCESS.getCode()))
            .result(result).build();
    }

    public static <T> TransactionResponse<T> error(String requestId, String errorCode) {
        return TransactionResponse.<T>builder()
            .requestId(requestId)
            .errorCode(errorCode)
            .errorMessage(Translator.toLocale(errorCode))     // en/my/zh theo Locale, fallback English
            .result(null).build();
    }
}
```

### 5.3 Exception nghiệp vụ mang mã
```java
@Getter
public class BusinessException extends RuntimeException {
    private final String errorCode;        // 5 ký tự
    public BusinessException(ErrorCode ec) { super(ec.getCode()); this.errorCode = ec.getCode(); }
    public BusinessException(String errorCode) { super(errorCode); this.errorCode = errorCode; }
}
// Dùng: throw new BusinessException(ErrorCode.INVOICE_ALREADY_PAID);
```

### 5.4 Suy HTTP status từ mã lỗi
```java
public final class HttpStatusMapper {
    private HttpStatusMapper() {}
    // Ghi đè cho mã xác thực/uỷ quyền (không nằm trong 4 loại nghiệp vụ)
    private static final Map<String, HttpStatus> OVERRIDES = Map.of(
        "00201", HttpStatus.UNAUTHORIZED,   // chưa/không xác thực được
        "00202", HttpStatus.FORBIDDEN       // đã đăng nhập nhưng thiếu quyền
    );
    public static HttpStatus of(String errorCode) {
        if ("00000".equals(errorCode)) return HttpStatus.OK;
        HttpStatus override = OVERRIDES.get(errorCode);
        if (override != null) return override;
        char type = (errorCode != null && errorCode.length() == 5) ? errorCode.charAt(2) : '4';
        return switch (type) {
            case '1' -> HttpStatus.BAD_REQUEST;             // validate input
            case '2' -> HttpStatus.BAD_REQUEST;             // nghiệp vụ / giao dịch thất bại
            case '3' -> HttpStatus.BAD_GATEWAY;             // tích hợp bên thứ 3 (5xx)
            case '4' -> HttpStatus.INTERNAL_SERVER_ERROR;   // nội bộ
            default  -> HttpStatus.BAD_REQUEST;
        };
    }
}
```

### 5.5 Xử lý tập trung `@RestControllerAdvice` (trả `ResponseEntity` để set status)
```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    private static final Logger log = LoggerFactory.getLogger(GlobalExceptionHandler.class);

    @ExceptionHandler(BusinessException.class)
    public ResponseEntity<TransactionResponse<Void>> handleBusiness(BusinessException ex, HttpServletRequest req) {
        String code = ex.getErrorCode();
        HttpStatus status = HttpStatusMapper.of(code);
        log.warn("business_error requestId={} code={} status={}", requestId(req), code, status.value());
        return ResponseEntity.status(status).body(ApiResponses.error(requestId(req), code));
    }

    // Lỗi @Valid: annotation regex đặt message() = mã lỗi validate (loại 1, ví dụ "01101") -> HTTP 400
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<TransactionResponse<Void>> handleValidation(MethodArgumentNotValidException ex, HttpServletRequest req) {
        String code = ex.getBindingResult().getFieldErrors().stream()
            .map(FieldError::getDefaultMessage).filter(Objects::nonNull).findFirst()
            .orElse("00100");                                  // mã validate dùng chung nếu thiếu
        log.warn("validation_error requestId={} code={}", requestId(req), code);
        return ResponseEntity.status(HttpStatusMapper.of(code)).body(ApiResponses.error(requestId(req), code));
    }

    @ExceptionHandler(Exception.class)                          // fallback: 500, KHÔNG lộ nội bộ
    public ResponseEntity<TransactionResponse<Void>> handleUnexpected(Exception ex, HttpServletRequest req) {
        log.error("unexpected_error requestId={}", requestId(req), ex);   // stacktrace CHỈ ở log
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR)
            .body(ApiResponses.error(requestId(req), ErrorCode.SYSTEM_ERROR.getCode()));
    }

    private String requestId(HttpServletRequest req) {
        String id = MDC.get("requestId");
        return id != null ? id : req.getHeader("X-Request-Id");
    }
}
```
> **Xác thực/uỷ quyền**: xử lý ở tầng Spring Security — `AuthenticationEntryPoint` (401) và
> `AccessDeniedHandler` (403) tự ghi body `TransactionResponse` với mã tương ứng (`00201`/`00202`), vì các
> lỗi này xảy ra **trước** controller nên không đi qua `@RestControllerAdvice` nghiệp vụ.

### 5.6 Controller trả envelope
```java
@PostMapping("/invoices")
public ResponseEntity<TransactionResponse<InvoiceResponse>> create(@Valid @RequestBody CreateInvoiceRequest req) {
    InvoiceResponse res = invoiceService.create(req);           // thành công -> 2xx
    return ResponseEntity.status(HttpStatus.CREATED).body(ApiResponses.ok(req.getRequestId(), res));
}
```
> Thành công trả **2xx**: dùng `200` cho truy vấn/giao dịch thường, `201` khi tạo mới tài nguyên.

## 6. Quy tắc vận hành & bảo trì
- Mọi mã dùng trong code phải có trong **Annex** + có khoá trong **cả 3 file messages** (en/my/zh) và mặc định.
- Log: `WARN` cho lỗi nghiệp vụ/validate, `ERROR` cho `00499`/loại nội bộ; luôn kèm `requestId`.
- **Không** để lỗi nghiệp vụ rơi vào fallback nội bộ (`00499`) — nghĩa là quên map, coi là bug.
- **Không** lộ stacktrace/SQL/exception nội bộ vào `errorMessage`.

## 7. Checklist mã lỗi & i18n (rà trước handover)
- [ ] Mọi endpoint trả `TransactionResponse<T>`; thành công `errorCode="00000"`, `result` có dữ liệu.
- [ ] **HTTP status phản ánh đúng kết cục** (2xx thành công; 400 validate/nghiệp vụ; 401/403 xác thực; 5xx tích hợp bên thứ 3/nội bộ) — không gộp hết về 200.
- [ ] Mã lỗi đúng **5 ký tự** `[MM][T][NN]`; loại (ký tự 3) đúng bảng; giữ số 0 đầu (kiểu String).
- [ ] `errorMessage` lấy qua `Translator.toLocale(errorCode)`; có khoá trong en/my/zh + mặc định.
- [ ] Locale ngoài en/my/zh → fallback English (đã test).
- [ ] `requestId` response khớp request; mọi log gắn `requestId`.
- [ ] Enum `ErrorCode` ↔ Annex ↔ messages đồng bộ; có test kiểm `errorCode` cho các loại 1/2/3/4.
- [ ] Không lỗi nào rơi nhầm vào `00499`; không trả `00000` khi thực chất lỗi.
