Lab: SQL injection attack, listing the database contents on non-Oracle databases

Bài lab cho ta biết được trang wed có lổ hổng SQL Injection ở nơi truy vấn category, cụ thể là UNION attack. Ta hãy sử dụng nó để lấy dữ liệu, làm sao để đăng nhập vào với vai trò là admin.

Truy câp vào trang web, bán sẽ thấy các mục Category để ta có thể lựa chọn: 
![image](https://github.com/phtuanthanh/LabPortwigger/assets/138991479/07cb6521-a920-4f84-9e42-e360dc0cbc2b)

Sau khi lựa chọn một category, đây là URL của trang wed:
![Uploading image.png…]()

Dễ dàng ta có thể đoán rằng đây là một truy vấn mà request của người dùng được trả về ở câu lệnh WHERE. Vậy làm sao để tấn công, như đã nói ở các phần trước, để tấn công UNION có hai thứ cần xác định:
+ Số lượng cột của bảng nguyên gốc truy vấn.
+ Kiểu dữ liệu của các cột đó.

Đọc thêm tại:
