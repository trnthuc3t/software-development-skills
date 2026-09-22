# INPUT VALIDATION CONVENTION — Custom regex annotation (Java/Spring Boot, chuẩn Duy)

> **Mọi format input phải được tham chiếu theo tài liệu SRS.** Không tự chế regex tuỳ hứng: mỗi ràng buộc
> định dạng phải bám một quy tắc đã ghi trong `SRS.md` (và khớp `api-spec.yaml`). Mỗi loại input có một
> **custom annotation ràng buộc (`@Constraint`)** riêng, kèm `ConstraintValidator` chứa regex.

## 1. Nguyên tắc
1. **Regex lấy từ SRS.** Trong validator, ghi chú rõ mục SRS nguồn (ví dụ `// SRS-3.2 Account Name`).
2. **Mỗi định dạng một annotation** đặt tên `<Field>Regex` (ví dụ `AccountNameRegex`, `RequestIdRegex`,
   `PhoneNumberRegex`). Tái sử dụng annotation ở mọi nơi dùng field đó — **không** rải regex trong code.
3. **Gắn với mã lỗi**: `message()` mặc định đặt bằng **khoá thông điệp** (mã lỗi validate 5 ký tự loại `1`,
   ví dụ `01101`, hoặc khoá chữ như `ACCOUNT_NAME_REGEX`) để `@RestControllerAdvice` lấy làm `errorCode`
   và `Translator.toLocale(...)` dịch — xem `error-code-convention.md`.
4. **An toàn null**: xử lý `null` tường minh; `required=false` thì null hợp lệ, `required=true` thì null là
   không hợp lệ (tránh NPE khi `matcher(null)`).
5. Validate ở **biên controller** bằng `@Valid`; không để logic nghiệp vụ trong validator.

## 2. Khuôn mẫu chuẩn (theo đúng mẫu đội)
```java
@Documented
@Target({ElementType.TYPE_USE, ElementType.PARAMETER, ElementType.FIELD, ElementType.LOCAL_VARIABLE})
@Constraint(validatedBy = {AccountNameValidator.class})
@Retention(RetentionPolicy.RUNTIME)
public @interface AccountNameRegex {
    boolean required() default true;
    String message() default "ACCOUNT_NAME_REGEX";      // khoá i18n / mã lỗi validate (loại 1)
    Class<?>[] groups() default {};
    Class<? extends Payload>[] payload() default {};
}

class AccountNameValidator implements ConstraintValidator<AccountNameRegex, String> {
    // Regex THAM CHIẾU SRS — SRS-3.2 "Account Name": 1..50 ký tự chữ cái và dấu gạch ngang
    private static final String REGEX = "^[a-zA-Z\\-]{1,50}$";
    private boolean required = true;

    @Override
    public void initialize(AccountNameRegex ann) {
        this.required = ann.required();
    }

    @Override
    public boolean isValid(String value, ConstraintValidatorContext cxt) {
        if (value == null) return !required;             // null: hợp lệ khi không bắt buộc
        if (!required && value.isEmpty()) return true;
        return Pattern.compile(REGEX).matcher(value).matches();
    }
}
```
> Khác biệt so với mẫu gốc: thêm **kiểm null trước `matcher`** để tránh `NullPointerException`, và ghi rõ
> **mục SRS nguồn** ngay cạnh regex. Có thể precompile `Pattern` thành `static final` để tránh compile mỗi lần.

## 3. Ví dụ khác — `RequestIdRegex` (dùng trong `BaseRequest`)
```java
@Documented
@Target({ElementType.FIELD, ElementType.PARAMETER})
@Constraint(validatedBy = {RequestIdValidator.class})
@Retention(RetentionPolicy.RUNTIME)
public @interface RequestIdRegex {
    boolean required() default true;
    String message() default "01101";                   // mã lỗi validate: module 01, loại 1, mã 01
    Class<?>[] groups() default {};
    Class<? extends Payload>[] payload() default {};
}

class RequestIdValidator implements ConstraintValidator<RequestIdRegex, String> {
    // SRS: requestId gồm a-z, A-Z, 0-9, độ dài 1..32
    private static final Pattern PATTERN = Pattern.compile("^[a-zA-Z0-9]{1,32}$");
    private boolean required = true;
    @Override public void initialize(RequestIdRegex ann) { this.required = ann.required(); }
    @Override public boolean isValid(String value, ConstraintValidatorContext cxt) {
        if (value == null) return !required;
        return PATTERN.matcher(value).matches();
    }
}
```

## 4. Dùng trong DTO / request
```java
@Data
public class CreateInvoiceRequest extends BaseRequest {   // đã có requestId (@RequestIdRegex) + version
    @AccountNameRegex
    @Schema(example = "John-Doe")
    private String accountName;

    @NotNull
    @Schema(example = "100000")
    private Long amount;
}
```
- Controller: `public TransactionResponse<...> create(@Valid @RequestBody CreateInvoiceRequest req)`.
- Vi phạm regex → `MethodArgumentNotValidException` → handler lấy `message()` (mã/khoá) làm `errorCode` →
  trả `TransactionResponse` với `errorMessage` đã dịch.

## 5. Checklist validation (rà trước handover)
- [ ] Mỗi format input có **custom annotation regex** riêng; regex **ghi rõ mục SRS nguồn**.
- [ ] Không có regex rải rác trong service/controller; tất cả nằm trong validator tái dùng.
- [ ] Validator **an toàn null**, tôn trọng `required`.
- [ ] `message()` là khoá i18n / mã lỗi validate (loại `1`), có trong messages en/my/zh.
- [ ] Có unit test cho validator: giá trị hợp lệ, biên, không hợp lệ, null theo `required`.
