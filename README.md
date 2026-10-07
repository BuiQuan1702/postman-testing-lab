# Báo cáo thực hành kiểm thử API bằng Postman

## Thông tin sinh viên

- **Họ và tên:** Bùi Minh Quân
- **Mã sinh viên:** 23010725
- **Trường:** Đại học Phenikaa
- **Môn học:** Đánh giá và kiểm định chất lượng phần mềm
- **Giảng viên:** Trịnh Thanh Bình
- **Tên dự án:** postman-testing-lab

## 1. Mục tiêu bài thực hành

- Làm quen với công cụ Postman và gửi các HTTP request (GET, POST, PUT, DELETE) tới một API thực tế.
- Sử dụng Environment để quản lý biến dùng chung (`baseUrl`).
- Viết các test script tự động bằng JavaScript để kiểm tra status code, thời gian phản hồi và nội dung response.
- Chạy toàn bộ collection bằng Collection Runner và lưu sản phẩm, báo cáo lên GitHub.

## 2. Môi trường và phương pháp

- **Công cụ:** Postman Desktop [phiên bản]
- **API kiểm thử:** JSONPlaceholder – https://jsonplaceholder.typicode.com
- **Environment:** `Dev`, biến `baseUrl = https://jsonplaceholder.typicode.com`
- **Collection:** `Postman-Lab`
- **Phương pháp:** kiểm thử thủ công từng request, sau đó kiểm thử tự động bằng test script và Collection Runner.



## 3. Cấu trúc repository

```
.
├── README.md                                   # Báo cáo thực hiện
├── images/                                     # Ảnh minh hoạ kết quả
│   ├── 01-environment.png
│   ├── 02-get-list.png
│   ├── 03-get-one.png
│   ├── 04-post.png
│   ├── 05-put.png
│   ├── 06-delete.png
│   └── 07-runner.png
└── postman/
    ├── Postman-Lab.postman_collection.json     # Collection (request + test script)
    └── Dev.postman_environment.json            # Environment
```

## 4. Kịch bản kiểm thử chi tiết và kết quả

### 4.1. Kịch bản 1: Lấy danh sách bài viết (GET)

- **Phương thức:** GET
- **URL:** `{{baseUrl}}/posts`
- **Kết quả mong đợi:** 200 OK, trả về mảng JSON gồm 100 bài viết.

**Test script:**

```javascript
pm.test("Status code là 200", () => pm.response.to.have.status(200));
pm.test("Thời gian < 1000ms", () => pm.expect(pm.response.responseTime).to.be.below(1000));
pm.test("Trả về mảng", () => pm.expect(pm.response.json()).to.be.an("array"));
```

**Kết quả thực tế:** 200 OK, [3/3] test pass.
**Trạng thái:** PASS



### 4.2. Kịch bản 2: Lấy một bài viết (GET)

- **Phương thức:** GET
- **URL:** `{{baseUrl}}/posts/1`
- **Kết quả mong đợi:** 200 OK, object có đủ các trường `userId`, `id`, `title`, `body`.

**Test script:**

```javascript
pm.test("Status code là 200", () => pm.response.to.have.status(200));
pm.test("Thời gian < 1000ms", () => pm.expect(pm.response.responseTime).to.be.below(1000));
pm.test("Có id = 1", () => pm.expect(pm.response.json().id).to.eql(1));
pm.test("Có đủ các trường", () => {
  const data = pm.response.json();
  pm.expect(data).to.have.all.keys("userId", "id", "title", "body");
});
```

**Kết quả thực tế:** 200 OK, [4/4] test pass.
**Trạng thái:** PASS



### 4.3. Kịch bản 3: Tạo mới bài viết (POST)

- **Phương thức:** POST
- **URL:** `{{baseUrl}}/posts`
- **Headers:** `Content-Type: application/json`
- **Body (raw, JSON):**

```json
{
  "title": "Test Postman",
  "body": "Noi dung",
  "userId": 1
}
```

- **Kết quả mong đợi:** 201 Created, response có trường `id`.

**Test script:**

```javascript
pm.test("Status code là 201", () => pm.response.to.have.status(201));
pm.test("Có id trả về", () => pm.expect(pm.response.json()).to.have.property("id"));
pm.test("Title đúng với dữ liệu gửi", () => pm.expect(pm.response.json().title).to.eql("Test Postman"));
```

**Kết quả thực tế:** 201 Created, API trả về `id = 101`, [3/3] test pass.
**Trạng thái:** PASS

> Lưu ý: JSONPlaceholder là API giả lập, dữ liệu gửi lên không được lưu thật.



### 4.4. Kịch bản 4: Cập nhật bài viết (PUT)

- **Phương thức:** PUT
- **URL:** `{{baseUrl}}/posts/1`
- **Headers:** `Content-Type: application/json`
- **Body (raw, JSON):**

```json
{
  "id": 1,
  "title": "Tieu de da cap nhat",
  "body": "Noi dung da cap nhat",
  "userId": 1
}
```

- **Kết quả mong đợi:** 200 OK, response trả lại dữ liệu đã cập nhật.

**Test script:**

```javascript
pm.test("Status code là 200", () => pm.response.to.have.status(200));
pm.test("Title đã được cập nhật", () => pm.expect(pm.response.json().title).to.eql("Tieu de da cap nhat"));
pm.test("id vẫn là 1", () => pm.expect(pm.response.json().id).to.eql(1));
```

**Kết quả thực tế:** 200 OK, [3/3] test pass.
**Trạng thái:** PASS



### 4.5. Kịch bản 5: Xoá bài viết (DELETE)

- **Phương thức:** DELETE
- **URL:** `{{baseUrl}}/posts/1`
- **Kết quả mong đợi:** 200 OK, response là object rỗng `{}`.

**Test script:**

```javascript
pm.test("Status code là 200", () => pm.response.to.have.status(200));
pm.test("Thời gian < 1000ms", () => pm.expect(pm.response.responseTime).to.be.below(1000));
pm.test("Response là object rỗng", () => pm.expect(pm.response.json()).to.eql({}));
```

**Kết quả thực tế:** 200 OK, response `{}`, [3/3] test pass.
**Trạng thái:** PASS



## 5. Chạy Collection Runner

Chạy toàn bộ collection `Postman-Lab` với environment `Dev`: 5 request, [16/16] test pass, 0 fail.



## 6. Tổng kết kết quả kiểm thử

| Chỉ số | Kết quả |
|---|---|
| Tổng số kịch bản | 5 (2 GET, POST, PUT, DELETE) |
| Số kịch bản PASS | 5 |
| Số kịch bản FAIL | 0 |
| Tỷ lệ thành công | 100% |

## 7. Khó khăn và cách khắc phục

- Ban đầu request trỏ vào trang chủ `https://jsonplaceholder.typicode.com` nên response trả về HTML và test "Trả về mảng" bị fail (`JSONError: Unexpected token '<'`). Đã sửa URL thành `{{baseUrl}}/posts` và test pass.
- Request GET không cần body nên đã chuyển Body sang `none`.
- [Bổ sung khó khăn khác nếu có.]

## 8. Kết luận

- Đã tạo Environment, Collection và 5 request cho các phương thức GET, POST, PUT, DELETE.
- Các test script tự động hoạt động chính xác, xác nhận phản hồi đúng như mong đợi.
- Nắm được quy trình: dùng biến môi trường, viết test bằng `pm.test`, chạy hàng loạt bằng Collection Runner, export collection để lưu trữ.

## 9. Tài liệu tham khảo

- Video hướng dẫn: https://www.youtube.com/watch?v=MFxk5BZulVU
- Postman Learning Center: https://learning.postman.com
- JSONPlaceholder: https://jsonplaceholder.typicode.com
