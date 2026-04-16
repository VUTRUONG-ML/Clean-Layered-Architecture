# 🧠 SQL Index – Core Notes (Backend Mindset)

---

# 1. Index là gì?

- Index là cấu trúc dữ liệu (thường là B-Tree)
- Lưu:
  ```
  key (giá trị cột) + pointer (trỏ tới row)
  ```

👉 Mục tiêu:

- Giảm số lượng row cần đọc
- Tăng tốc độ truy vấn

---

# 2. Scan vs Lookup

## 🔹 Scan (Sequential)

- Đọc dữ liệu liên tục
- Có 2 loại:
  - Full Table Scan
  - Index Scan

👉 Đặc điểm:

- Sequential I/O (rất nhanh)
- Cache-friendly

---

## 🔹 Lookup (Random Access)

- Xảy ra khi:
  - Dùng index để filter
  - Nhưng cần lấy thêm dữ liệu không nằm trong index

👉 Flow:

```
index → pointer → nhảy vào table → lấy data
```

👉 Đặc điểm:

- Random I/O
- Chậm nếu xảy ra nhiều lần

---

# 3. Khi nào Index nhanh?

## ✅ 1. Selectivity cao

- Ít row match
- Ví dụ:

```
WHERE email = 'abc@gmail.com'
```

---

## ✅ 2. Ít lookup

- Số row trả về nhỏ

---

## ✅ 3. Covering index

- Index chứa luôn dữ liệu cần SELECT

---

# 4. Khi nào Index chậm?

## ❌ 1. Selectivity thấp

```
WHERE gender = 'male'
```

- Trả về nhiều row
- DB có thể bỏ index → dùng scan

---

## ❌ 2. Không covering + nhiều row

```
SELECT name FROM users WHERE age = 20
```

👉 Xảy ra:

- index → nhiều pointer
- lookup nhiều lần → random I/O

---

## ❌ 3. Query trả về quá nhiều dữ liệu

- Lookup nhiều lần
- Chậm hơn scan

---

# 5. Covering Index

## 🔹 Khái niệm

Index chứa đủ dữ liệu cho query

## 🔹 Ví dụ

```
CREATE INDEX idx_email_cover ON users(email, name, age);
```

```
SELECT name, age FROM users WHERE email = 'a@gmail.com';
```

👉 Không cần lookup
👉 Chỉ đọc index

---

# 6. Selectivity

## 🔹 Định nghĩa

Mức độ phân biệt của dữ liệu

## 🔹 Ví dụ

| Cột    | Selectivity |
| ------ | ----------- |
| email  | cao         |
| age    | trung bình  |
| gender | thấp        |

👉 Selectivity càng cao → index càng hiệu quả

---

# 7. Composite Index

## 🔹 Tạo

```
CREATE INDEX idx_name_age ON users(name, age);
```

## 🔹 Rule quan trọng

👉 Leftmost Prefix Rule

### Dùng được:

```
WHERE name = 'A'
WHERE name = 'A' AND age = 20
```

### Không tối ưu:

```
WHERE age = 20
```

---

# 8. Mental Model Chuẩn

```
1. Index dùng để giảm số row cần đọc

2. Nếu index không cover:
   → phải lookup (random access)

3. Nếu số row ít:
   → lookup OK

4. Nếu số row nhiều:
   → lookup nhiều → chậm

5. Khi đó:
   → scan (sequential) có thể nhanh hơn

6. Nếu selectivity thấp:
   → DB có thể bỏ index
```

---

# 9. Nguyên tắc thiết kế index

👉 Khi nhìn query, nghĩ theo thứ tự:

```
1. WHERE (filter cột nào?)
2. SELECT (cần cột nào?)
3. ORDER BY / GROUP BY (nếu có)
```

---

# 10. Câu chốt

👉 Index không phải cứ thêm là nhanh

👉 Quan trọng là:

```
DB đọc bao nhiêu dữ liệu và đọc theo kiểu gì
```

- Ít data + index → nhanh
- Nhiều data + lookup → chậm
- Nhiều data + scan → có thể nhanh hơn

