# API Specification — <Tên dự án / service> — lưu thành `api-spec.md`

| Phiên bản | v0.1 | Ngày | <yyyy-mm-dd> | Trạng thái | DRAFT |
|-----------|------|------|--------------|------------|-------|

> **Cách dùng:** copy ra `docs/sa/api-spec.md` (kiểu B microservices: `docs/<service-name>/api-spec.md`) rồi
> điền. Khối `> 💡 Hướng dẫn` / `> 📝 Ví dụ` là chỉ dẫn — **xóa khi hoàn thiện**.
>
> **Vì sao Markdown, không viết YAML tay:** OpenAPI YAML lặp boilerplate + lặp envelope ở mọi endpoint —
> tốn token khi viết lẫn khi các role đọc. Trong pipeline, **file .md này là hợp đồng API** (SA viết, dev
> code theo, tester phủ test theo). **OpenAPI/Swagger sinh tự động từ code** (backend Java: springdoc-openapi,
> annotation `@Schema`) ở B4 — dùng để đối chiếu với file này khi nghiệm thu GATE-4 và khi cần công bố cho
> bên ngoài. **Không ai viết YAML tay.** Mâu thuẫn giữa code và file này → file này (đã duyệt) thắng.

## 1. Quy ước chung *(khai MỘT LẦN — các endpoint không lặp lại)*

- **Base URL:** dev `http://localhost:8080` · uat `<...>` · prod `<...>`. Prefix: `/api/v1`.
- **Auth:** Bearer JWT ở header `Authorization` cho **mọi** endpoint, **trừ** `GET /health`.
- **Định dạng:** JSON UTF-8; ngày `dd/MM/yyyy HH:mm:ss`; tiền tệ số nguyên (đơn vị nhỏ nhất).
- **Envelope (mọi API — theo convention `backend-dev-java`):**
  - Request kế thừa `BaseRequest`: `requestId` (string, bắt buộc, client sinh, echo lại), `version` (string).
  - Response bọc `TransactionResponse<T>`: `requestId` · `errorCode` (5 ký tự, `00000` = thành công) ·
    `errorMessage` · `result` (payload riêng từng endpoint — **phần dưới chỉ đặc tả `result` và field nghiệp vụ của request**).
- **HTTP status:** 2xx thành công · 400 validate/nghiệp vụ · 401/403 authn/authz · 5xx tích hợp/nội bộ.
  Body luôn là `TransactionResponse`.
- **Phân trang** (endpoint danh sách): request `page` (từ 0), `size` (mặc định 20, tối đa 100); `result` có
  `items[]`, `totalElements`, `totalPages`.
- **Mã lỗi chung mọi endpoint** *(không lặp ở từng endpoint)*: `00401` chưa xác thực · `00403` không có quyền ·
  `00500` lỗi hệ thống. Mã lỗi nghiệp vụ theo Annex 5 ký tự (`[module 2][loại 1][mã 2]`).

## 2. Danh mục endpoint

| # | Method | Path | Mô tả | FR | Quyền |
|---|--------|------|-------|----|-------|
| 1 | GET | /health | Healthcheck (không auth) | — | công khai |
| 2 | | | | FR-xx | |

## 3. Đặc tả từng endpoint *(khối lặp cho mỗi endpoint)*

> 💡 Viết đến mức **dev code được, tester viết được case** không phải hỏi lại: đủ ràng buộc từng field
> (tham chiếu bảng validate của SRS, không chép lại), đủ mã lỗi nghiệp vụ, và **ví dụ JSON thật** (dữ liệu
> giả — PDPL). Chỉ tả phần **riêng** của endpoint; quy ước chung ở §1 không lặp lại.

### 3.x `<METHOD> <path>` — <tên ngắn> *(FR-xx)*
- **Mô tả & quyền:** <ai được gọi, làm gì>
- **Request** (ngoài `BaseRequest`):
  | Trường | Kiểu | Bắt buộc | Ràng buộc (ref SRS) | Mô tả |
  |--------|------|:--------:|----------------------|-------|
  | | | | FR-xx bảng validate | |
- **Response `result`:**
  | Trường | Kiểu | Mô tả |
  |--------|------|-------|
  | | | |
- **Mã lỗi nghiệp vụ:**
  | errorCode | HTTP | Ý nghĩa | errorMessage (vi) |
  |-----------|:----:|---------|-------------------|
  | | | | |
- **Ví dụ:** 1 request + 1 response thành công + 1 response lỗi (JSON, ngắn gọn).

> 📝 **Ví dụ điền sẵn — `POST /api/v1/subscriptions` — Đăng ký gói cước data *(FR-02, màn SCR-05)***
> - **Mô tả & quyền:** thuê bao đã đăng nhập tự đăng ký gói cho chính số của mình.
> - **Request:**
>   | Trường | Kiểu | Bắt buộc | Ràng buộc (ref SRS) | Mô tả |
>   |--------|------|:--------:|----------------------|-------|
>   | packageCode | string | ✓ | `^PKG[0-9]{3}$` — FR-02 bảng validate | Mã gói đang mở bán |
>   | autoRenew | boolean | ✗ | mặc định `false` | Tự gia hạn khi hết chu kỳ |
> - **Response `result`:**
>   | Trường | Kiểu | Mô tả |
>   |--------|------|-------|
>   | subscriptionId | string | Mã đăng ký vừa tạo |
>   | packageCode | string | Echo mã gói |
>   | expireAt | string | Hết hạn, `dd/MM/yyyy HH:mm:ss` |
> - **Mã lỗi nghiệp vụ:**
>   | errorCode | HTTP | Ý nghĩa | errorMessage (vi) |
>   |-----------|:----:|---------|-------------------|
>   | SB101 | 400 | Mã gói sai định dạng/không tồn tại | Gói cước không tồn tại hoặc đã ngừng |
>   | SB201 | 400 | Số dư không đủ (BR-02) | Số dư không đủ để đăng ký gói này |
>   | SB202 | 400 | Đã có gói cùng loại còn hiệu lực | Bạn đang có gói cùng loại còn hiệu lực |
> - **Ví dụ:**
>   ```json
>   // Request
>   { "requestId": "a1b2c3", "version": "1.0", "packageCode": "PKG010", "autoRenew": false }
>   // 200 — thành công
>   { "requestId": "a1b2c3", "errorCode": "00000", "errorMessage": "Success",
>     "result": { "subscriptionId": "SUB-88123", "packageCode": "PKG010", "expireAt": "05/09/2026 23:59:59" } }
>   // 400 — số dư không đủ
>   { "requestId": "a1b2c3", "errorCode": "SB201", "errorMessage": "Số dư không đủ để đăng ký gói này", "result": null }
>   ```

---

## ✅ Checklist trước GATE-3
- [ ] §1 quy ước chung đầy đủ (base URL, auth, envelope, HTTP status, phân trang, mã lỗi chung) — **khai 1 lần, endpoint không lặp**.
- [ ] Danh mục endpoint (§2) phủ đủ FR trong phạm vi; mỗi endpoint gắn FR-xx.
- [ ] Mỗi endpoint: đủ bảng request (ràng buộc tham chiếu SRS, không chép lại) + `result` + **mã lỗi nghiệp vụ** (errorCode 5 ký tự + HTTP + errorMessage).
- [ ] Mỗi endpoint có **ví dụ JSON**: request + thành công + ít nhất 1 lỗi; dữ liệu giả, không PII thật (PDPL).
- [ ] Tên trường khớp **LLD** (nguồn sự thật data model); mã lỗi khớp convention `backend-dev-java`.
- [ ] Ghi chú: OpenAPI sinh từ code (springdoc) sẽ đối chiếu file này ở GATE-4 — không viết YAML tay.
- [ ] Đã xóa hết khối 💡 Hướng dẫn / 📝 Ví dụ.
