# PACKAGE STRUCTURE — Convention Java/Spring Boot (Thức)

## 1. Gốc package (bắt buộc)
```
com.thuc.<tên dự án>.<tên service>
```
- Tất cả **lowercase**, không dấu tiếng Việt, không gạch dưới, không số mở đầu.
- `groupId` (Maven) = `com.thuc.<tên dự án>`; `artifactId` = `<tên dự án>-<tên service>`.
- Ví dụ dự án `billing`, service `invoice` → gốc `com.thuc.billing.invoice`.

## 2. Package-by-feature (mặc định khuyến nghị)
Chia trước hết theo **feature/nghiệp vụ**, trong mỗi feature mới chia theo tầng. Tránh chia theo tầng ở
cấp cao nhất (package-by-layer) vì làm phình coupling khi hệ thống lớn.

```
com.thuc.billing.invoice
├── InvoiceServiceApplication.java        // @SpringBootApplication (đặt ở gốc service)
├── config/                               // cấu hình liên feature: Security, OpenAPI, Jackson, Async...
│   ├── SecurityConfig.java
│   └── OpenApiConfig.java
├── common/                               // dùng chung nội bộ service (KHÔNG chứa nghiệp vụ feature)
│   ├── error/                            // GlobalExceptionHandler, ApiError, BusinessException
│   ├── util/
│   └── audit/
├── invoice/                              // <feature 1>
│   ├── web/                              // @RestController + DTO + mapper (adapter vào)
│   │   ├── InvoiceController.java
│   │   ├── dto/  (CreateInvoiceRequest.java, InvoiceResponse.java — dùng record)
│   │   └── InvoiceMapper.java            // MapStruct: entity <-> DTO
│   ├── application/                      // (hoặc 'service') use case / logic nghiệp vụ
│   │   ├── InvoiceService.java           // interface
│   │   └── InvoiceServiceImpl.java
│   ├── domain/                           // entity JPA, value object, enum, domain logic
│   │   ├── Invoice.java
│   │   └── InvoiceStatus.java
│   └── repository/                       // Spring Data JPA interface
│       └── InvoiceRepository.java
└── customer/                             // <feature 2> — cùng khuôn
    ├── web/ ...
    ├── application/ ...
    ├── domain/ ...
    └── repository/ ...
```

**Quy tắc phụ thuộc (một chiều):** `web → application → domain`; `repository` phục vụ `application`.
`domain` **không** phụ thuộc `web`/Spring MVC. Feature A **không** gọi thẳng repository/entity của feature
B — giao tiếp qua interface service của feature B (hoặc sự kiện).

## 3. Biến thể được chấp nhận
- **Package-by-layer** (service nhỏ): `web/`, `service/`, `repository/`, `domain/` ngay dưới gốc service.
  Chỉ dùng khi service thật sự nhỏ, ít feature.
- **Hexagonal** (service phức tạp/DDD): `domain/`, `application/` (port in/out), `adapter/in/web`,
  `adapter/out/persistence`. Chọn ở `techstack.md`/`HLD.md`, áp nhất quán toàn service.

## 4. Quy ước đặt tên lớp (Java)
| Loại | Quy ước | Ví dụ |
|------|---------|-------|
| Class/Interface | PascalCase, danh từ | `InvoiceService`, `CustomerRepository` |
| Interface + 1 impl | interface tên nghiệp vụ; impl `...Impl` | `InvoiceService` / `InvoiceServiceImpl` |
| Controller | hậu tố `Controller` | `InvoiceController` |
| Service | hậu tố `Service` | `PaymentService` |
| Repository | hậu tố `Repository` | `InvoiceRepository` |
| DTO | `record`, hậu tố `Request`/`Response` | `CreateInvoiceRequest`, `InvoiceResponse` |
| Mapper | hậu tố `Mapper` | `InvoiceMapper` |
| Config | hậu tố `Config` | `SecurityConfig` |
| Exception | hậu tố `Exception` | `InvoiceNotFoundException` |
| Enum | PascalCase; hằng UPPER_SNAKE | `InvoiceStatus.PAID` |
| Method | camelCase, động từ | `createInvoice`, `findByCustomerId` |
| Hằng số | `static final` UPPER_SNAKE | `MAX_RETRY` |

- **Không** tiền tố `I` cho interface (dùng `InvoiceService`, không `IInvoiceService`).
- Một lớp một trách nhiệm; file test đặt gương: `InvoiceServiceTest`, `InvoiceControllerTest`.

## 5. Ranh giới không được vi phạm
- Controller **không** chứa logic nghiệp vụ, **không** trả entity JPA.
- Entity JPA **không** ra khỏi tầng service (map sang DTO trước khi trả API).
- `@Transactional` chỉ ở tầng service.
- Không đặt logic nghiệp vụ trong `common/`/`util/`.
