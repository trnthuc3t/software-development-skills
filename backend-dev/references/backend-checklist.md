# BACKEND CHECKLIST — Definition of Done & Self-review (Senior)

> Backend dev tự rà checklist này **trước khi handover tester** (kết thúc B4). "Đủ điều kiện" ở đây phục
> vụ **GATE-4** của kit — nhưng vẫn chỉ con người (anh Thức) duyệt cổng. Đánh dấu ✔ và nêu ngoại lệ có lý do.

## 1. Đặc tả chi tiết API
- [ ] Mọi endpoint khớp **đặc tả chi tiết API** (`api-spec.yaml`): path, method, request/response schema, **mã lỗi**, auth.
- [ ] Không phá tương thích ngược ngoài phạm vi đã duyệt (CR).
- [ ] Lỗi trả về có cấu trúc nhất quán; không lộ stacktrace/chi tiết nội bộ ra client.

## 2. Tầng dữ liệu
- [ ] Thay đổi schema qua **migration** tiến/lùi được; không sửa DB thủ công.
- [ ] Thao tác nhiều bước nằm trong **transaction**; ranh giới nhất quán rõ ràng.
- [ ] Có **index** cho cột lọc/sort/join; không `SELECT *`; đã kiểm **không còn N+1**.
- [ ] Dùng connection pool có giới hạn; phân trang cursor/keyset cho tập lớn.

## 3. Xử lý lỗi & độ bền
- [ ] Mô hình lỗi thống nhất (mã nội bộ ↔ HTTP status ↔ thông điệp an toàn).
- [ ] Thao tác ghi có thể lặp là **idempotent** (hoặc có khoá idempotency).
- [ ] Mọi call ra hệ ngoài có **timeout**; **retry backoff+jitter** cho lỗi tạm thời; cân nhắc circuit breaker.
- [ ] **Graceful shutdown** đóng pool/consumer sạch, không mất request đang chạy.

## 4. Bảo mật (chuẩn bị B5)
- [ ] Authn + **authz theo từng endpoint** kiểm ở tầng service, đúng LLD.
- [ ] Truy vấn tham số hoá; chống mass-assignment; validate kích thước/định dạng input.
- [ ] Rate limit ở endpoint nhạy cảm; chống brute-force đăng nhập.
- [ ] Mật khẩu băm bằng argon2/bcrypt; **không** log PII/secret; secret qua env/secret manager.
- [ ] Header bảo mật, CORS đúng phạm vi, cookie an toàn (nếu dùng).

## 5. Hiệu năng & mở rộng
- [ ] Việc nặng/chậm đẩy ra async/worker; request path không blocking.
- [ ] Cache (nếu có) kèm **chiến lược vô hiệu hoá** + TTL rõ; nêu nguy cơ dữ liệu cũ.
- [ ] Có giới hạn tài nguyên (payload size, timeout, pool) để tránh cạn kiệt.

## 6. Observability
- [ ] Structured log + **correlation id** xuyên request.
- [ ] Metrics **RED** (Rate/Errors/Duration) cho endpoint chính.
- [ ] `/health` + `/ready` phản ánh đúng tình trạng phụ thuộc.

## 7. Kiểm thử
- [ ] Unit phủ **logic nghiệp vụ + biên + luồng lỗi** (không chỉ happy path).
- [ ] Integration cho DB thật + tích hợp hệ ngoài (mock/contract).
- [ ] Kiểm thử đối chiếu **đặc tả chi tiết API** (`api-spec.yaml`: schema + mã lỗi).
- [ ] Toàn bộ test **xanh**; dùng dữ liệu ẩn danh; test độc lập & tái lập.

## 8. Handover
- [ ] Đã cập nhật `PROJECT_STATE.md` (`src (backend) → DONE`).
- [ ] Gói handover cho tester: cách chạy, endpoint, seed data, biến env, **giới hạn/nợ kỹ thuật đã biết**.
- [ ] Không secret trong mã/lịch sử git; `.env.example` đầy đủ.

---
**Quy tắc vàng:** nếu một mục không đạt mà vẫn muốn handover → **ghi rõ lý do + rủi ro** cho PO, không im lặng bỏ qua.
