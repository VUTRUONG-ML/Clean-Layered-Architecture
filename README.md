# 1️⃣ Controller Layer – Responsibility

## Controller không xử lý business logic.

### Controller chỉ nên:

- Nhận `req`
- Validate input cơ bản (format, thiếu field…)
- Gọi `service`
- Trả `res`
- Forward error cho global error handler (`next(error)`)

---

## Controller chịu trách nhiệm:

- Parse `params`, `query`, `body`
- HTTP concern (status code, headers)
- Map data → response DTO
- Không quyết định nghiệp vụ

> ⚠️ Controller không nên tự throw `404` nếu chưa gọi service kiểm tra dữ liệu.

---

# 2️⃣ Service Layer – Responsibility

## Service mới là nơi xử lý nghiệp vụ.

### Service chịu trách nhiệm:

- Xử lý business logic
- Kiểm tra dữ liệu có tồn tại hay không
- Kiểm tra rule nghiệp vụ
- Gọi repository / database
- Throw business error

---

## Service không biết gì về HTTP.

Service chỉ:

- `return data`
- hoặc `throw error`

Service không trả về `res`, không xử lý `status code`.

---

# 3️⃣ Viết class cho từng HTTP Error

Nên tạo các custom error class:

- `NotFoundError` (404)
- `ConflictError` (409)
- `ForbiddenError` (403)
- `BadRequestError` (400)
- `InternalServerError` (500)

Tất cả kế thừa từ một base `AppError`.

---

# 4️⃣ Nên bắt lỗi ở đâu?

Đây là phần quan trọng trong kiến trúc.

---

## ✅ Service nên throw:

- `404` → Không tìm thấy dữ liệu
- `409` → Trùng email, trùng username
- `403` → Không có quyền (business permission)
- `400` → Dữ liệu không hợp lệ ở mức business logic
- `500` → Chỉ khi có unexpected error

Service **không bắt rồi xử lý**.  
Service chỉ **throw**.

---

## ❌ Service không nên:

- `res.status(404)`
- `res.json(...)`
- `return statusCode`

Service không nên biết HTTP tồn tại.
