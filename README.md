Báo cáo thực hiện kiểm tra API bằng Postman
Thông tin sinh viên
Họ và tên: Bùi Minh Quân
Mã sinh viên: 23010725
Trường: Đại học Phenikaa

Tên dự án: postman-testing-lab
1. Mục tiêu bài thực hành
Tại đó, bạn có thể sử dụng Postman để lấy các yêu cầu HTTP (GET, POST, PUT, DELETE) và API thực tế.
Use Environment để quản lý chung các biến ( baseUrl).
Viết tập lệnh kiểm tra tự động bằng JavaScript để kiểm tra mã trạng thái, thời gian phản hồi và phản hồi nội dung.
Chạy toàn bộ bộ sưu tập bằng Collection Runner và lưu sản phẩm, báo cáo lên GitHub.
2. Môi trường và phương pháp
Công cụ: Postman Desktop [phiên bản]
API cho việc này: JSONPlaceholder – https://jsonplaceholder.typicode.com
Môi trường: Dev tốtbaseUrl = https://jsonplaceholder.typicode.com
Bộ sưu tập: Postman-Lab
Phương pháp: kiểm tra thử thủ công theo yêu cầu, sau đó kiểm tra tự động bằng tập lệnh kiểm tra đi tới Collection Runner.

Hiển thị hình ảnh

3. Kho lưu trữ cấu trúc
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
4. Bản kiểm tra chi tiết và kết quả
4.1. bản 1: Lấy danh sách bài viết (GET)
Phương thức: GET
URL: {{baseUrl}}/posts
Kết quả mong đợi: 200 OK, trả về mảng JSON bao gồm 100 bài viết.

Kịch bản kiểm thử:

JavaScript
pm.test("Status code là 200", () => pm.response.to.have.status(200));
pm.test("Thời gian < 1000ms", () => pm.expect(pm.response.responseTime).to.be.below(1000));
pm.test("Trả về mảng", () => pm.expect(pm.response.json()).to.be.an("array"));

Kết quả thực tế: 200 OK, [3/3] test pass. Trạng thái: PASS

Hiển thị hình ảnh

4.2. bản 2: Lấy một bài viết (GET)
Phương thức: GET
URL: {{baseUrl}}/posts/1
Kết quả mong đợi: 200 OK, object có đủ các trường userId, id, title, body.

Kịch bản kiểm thử:

JavaScript
pm.test("Status code là 200", () => pm.response.to.have.status(200));
pm.test("Thời gian < 1000ms", () => pm.expect(pm.response.responseTime).to.be.below(1000));
pm.test("Có id = 1", () => pm.expect(pm.response.json().id).to.eql(1));
pm.test("Có đủ các trường", () => {
  const data = pm.response.json();
  pm.expect(data).to.have.all.keys("userId", "id", "title", "body");
});

Kết quả thực tế: 200 OK, [4/4] test pass. Trạng thái: PASS

Hiển thị hình ảnh

4.3. bản 3: Tạo bài viết mới (POST)
Phương thức: POST
URL: {{baseUrl}}/posts
Tiêu đề: Content-Type: application/json
Nội dung (dạng thô, JSON):
json
{
  "title": "Test Postman",
  "body": "Noi dung",
  "userId": 1
}
Kết quả mong đợi: 201 Đã tạo, phản hồi có trường id.

Kịch bản kiểm thử:

JavaScript
pm.test("Status code là 201", () => pm.response.to.have.status(201));
pm.test("Có id trả về", () => pm.expect(pm.response.json()).to.have.property("id"));
pm.test("Title đúng với dữ liệu gửi", () => pm.expect(pm.response.json().title).to.eql("Test Postman"));

Kết quả thực tế: 201 Created, API return id = 101, [3/3] test pass. Trạng thái: PASS

Lưu ý: JSONPlaceholder có API giả lập, dữ liệu gửi lên không được lưu thật.

Hiển thị hình ảnh

4.4. bản 4: Cập nhật bài viết (PUT)
Phương thức: ĐẶT
URL: {{baseUrl}}/posts/1
Tiêu đề: Content-Type: application/json
Nội dung (dạng thô, JSON):
json
{
  "id": 1,
  "title": "Tieu de da cap nhat",
  "body": "Noi dung da cap nhat",
  "userId": 1
}
Kết quả mong đợi: 200 OK, phản hồi trả lại dữ liệu đã cập nhật.

Kịch bản kiểm thử:

JavaScript
pm.test("Status code là 200", () => pm.response.to.have.status(200));
pm.test("Title đã được cập nhật", () => pm.expect(pm.response.json().title).to.eql("Tieu de da cap nhat"));
pm.test("id vẫn là 1", () => pm.expect(pm.response.json().id).to.eql(1));

Kết quả thực tế: 200 OK, [3/3] test pass. Trạng thái: PASS

Hiển thị hình ảnh

4.5. bản 5: Xóa bài viết (DELETE)
Phương thức: XÓA
URL: {{baseUrl}}/posts/1
Kết quả mong đợi: 200 OK, phản hồi ở đó đối tượng trống {}.

Kịch bản kiểm thử:

JavaScript
pm.test("Status code là 200", () => pm.response.to.have.status(200));
pm.test("Thời gian < 1000ms", () => pm.expect(pm.response.responseTime).to.be.below(1000));
pm.test("Response là object rỗng", () => pm.expect(pm.response.json()).to.eql({}));

Kết quả thực tế: 200 OK, phản hồi {}, [3/3] test pass. Trạng thái: PASS

Hiển thị hình ảnh

5. Chạy Bộ sưu tập Runner

Chạy toàn bộ bộ sưu tập Postman-Labvới môi trường Dev: 5 yêu cầu, [16/16] test pass, 0 failed.

Hiển thị hình ảnh

6. Kiểm tra tổng kết quả
Chỉ số	Kết quả
Tổng số tiêu chuẩn	5 (2 GET, POST, PUT, DELETE)
Số để PASS	5
Số đến THẤT BẠI	0
Tỷ lệ thành công	100%
7. Khó khăn và cách giải quyết
Ban đầu yêu cầu con trỏ vào trang chủ https://jsonplaceholder.typicode.com, phản hồi trả về HTML và kiểm tra "Trả về mảng" không thành công ( JSONError: Unexpected token '<'). Đã sửa URL thành công {{baseUrl}}/postsvà vượt qua bài kiểm tra.
Yêu cầu GET không cần body nên đã chuyển Body sang none.
[Bổ sung khó khăn khác nếu có.]
8. Kết luận
Đã tạo Môi trường, Bộ sưu tập và 5 yêu cầu cho các phương thức GET, POST, PUT, DELETE.
Tập lệnh kiểm tra tự động hoạt động chính xác, đã xác định phản hồi đúng như mong đợi.
Quy trình được thực hiện: sử dụng môi trường biến, viết thử nghiệm bằng pm.test, chạy hàng loạt bằng Collection Runner, xuất bộ sưu tập để lưu trữ.
9. Tài liệu tham khảo
Video hướng dẫn: https://www.youtube.com/watch?v=MFxk5BZulVU
Trung tâm đào tạo Postman: https://learning.postman.com
JSONPlaceholder: https://jsonplaceholder.typicode.com
