# OOP & SOLID — Áp dụng cho Spring Boot (Duy)

Mục tiêu: mã dễ đọc, dễ test, dễ đổi. Dưới đây là cách áp OOP + SOLID **cụ thể trong Spring**, kèm ví dụ
nên/không nên (rút gọn).

## OOP nền tảng
- **Đóng gói (encapsulation):** entity/domain giữ bất biến (invariant) của mình; không phơi setter bừa
  bãi. Logic thuộc dữ liệu nào đặt cạnh dữ liệu đó (rich domain hơn anemic khi hợp lý).
- **Trừu tượng:** tầng trên phụ thuộc **interface**, không phụ thuộc lớp cụ thể.
- **Kế thừa có chừng mực:** ưu tiên **composition over inheritance**; kế thừa chỉ khi thật sự "is-a".
- **Đa hình thay cho if/else kiểu:** dùng polymorphism/strategy thay cho chuỗi `if (type == ...)`.

## S — Single Responsibility
- `@RestController` chỉ: nhận request, validate biên, gọi service, map response. **Không** logic nghiệp vụ.
- `@Service` mỗi lớp một nhóm use case; tránh "god service" 2000 dòng — tách theo nghiệp vụ.
- Tách mapping ra **Mapper** (MapStruct), tách truy vấn ra **Repository**.

## O — Open/Closed
- Thêm hành vi bằng **mở rộng**, không sửa lớp cũ. Ví dụ nhiều cách tính phí:

```java
public interface FeeCalculator { boolean supports(PlanType t); Money calculate(Invoice i); }
// PrepaidFeeCalculator, PostpaidFeeCalculator ... Spring inject List<FeeCalculator>
```
Thay vì `if (plan == PREPAID) ... else if (POSTPAID) ...` phình dần.

## L — Liskov Substitution
- Lớp con **không** siết chặt tiền điều kiện / phá hậu điều kiện của lớp/interface cha. Nếu một impl
  không thể thực hiện đúng hợp đồng interface → nó không nên implement interface đó.

## I — Interface Segregation
- Interface **nhỏ, theo nhu cầu người dùng nó**. Tránh interface "khổng lồ" buộc impl viết method rỗng.
- Ví dụ tách `InvoiceReader` (query) và `InvoiceWriter` (command) nếu bên đọc không cần bên ghi.

## D — Dependency Inversion
- Tầng nghiệp vụ phụ thuộc **abstraction**; chi tiết (JPA, HTTP client, hàng đợi) nằm sau interface.
- **Constructor injection + `final`**, **cấm** field `@Autowired`:

```java
@Service
public class InvoiceServiceImpl implements InvoiceService {
    private final InvoiceRepository repository;
    private final FeeCalculatorResolver feeResolver;
    public InvoiceServiceImpl(InvoiceRepository repository, FeeCalculatorResolver feeResolver) {
        this.repository = repository; this.feeResolver = feeResolver;
    }
}
```
Lợi ích: bất biến, dễ mock trong unit test, phát hiện phụ thuộc vòng sớm.

## Nên / Không nên (nhanh)
| Không nên | Nên |
|-----------|-----|
| `@Autowired` field | Constructor injection, `final` |
| Logic nghiệp vụ trong controller | Đẩy xuống `@Service` |
| Trả entity JPA ra API | Map sang DTO (`record`) |
| `if/else`/`switch` theo type phình dần | Strategy + polymorphism, inject `List<T>` |
| Interface to gộp mọi thứ | Interface nhỏ theo nhu cầu |
| Bắt `Exception` rồi nuốt | Ném lỗi có mã (xem `error-code-convention.md`) |
| Static util giữ trạng thái | Bean có vòng đời rõ ràng |

## Liên hệ kiểm thử
Thiết kế theo SOLID để **unit test không cần Spring context**: service test bằng Mockito mock repository;
chỉ dùng slice test (`@WebMvcTest`, `@DataJpaTest`) khi cần khung Spring.
