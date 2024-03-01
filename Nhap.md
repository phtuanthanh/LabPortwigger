Lab: SQL injection attack, listing the database contents on non-Oracle databases

Bài lab cho ta biết được trang wed có lổ hổng SQL Injection ở nơi truy vấn category, cụ thể là UNION attack. Ta hãy sử dụng nó để lấy dữ liệu, làm sao để đăng nhập vào với vai trò là admin.

Truy câp vào trang web, bán sẽ thấy các mục Category để ta có thể lựa chọn: 
![image](https://github.com/phtuanthanh/LabPortwigger/assets/138991479/07cb6521-a920-4f84-9e42-e360dc0cbc2b)

Sau khi lựa chọn một category, đây là URL của trang wed:
![image](https://github.com/phtuanthanh/LabPortwigger/assets/138991479/ac5c1e81-1ca6-40ba-8cfa-cb32dd292ea0)

Dễ dàng ta có thể đoán rằng đây là một truy vấn mà request của người dùng được trả về ở câu lệnh WHERE. Vậy làm sao để tấn công, như đã nói ở các phần trước, để tấn công UNION có hai thứ cần xác định:
+ Số lượng cột của bảng nguyên gốc truy vấn.
+ Kiểu dữ liệu của các cột đó.

Đọc thêm tại: https://github.com/phtuanthanh/LabPortwigger/blob/SQL-Injection/SQL%20Injection%20UNION%20Attacks.md

Thực hiện yêu cầu 1, tôi sử dụng ORDER BY, ta cứ thử sắp xếp theo cột 1, cột 2... đến khi báo lỗi. BurpSuite là một công cụ tuyệt vời:
![image](https://github.com/phtuanthanh/LabPortwigger/assets/138991479/11e71dbf-5ff7-4502-a4bf-633e8ae8df74)
Nếu đúng, phản hồi của http sẽ là 200 OK, còn nếu sai thì là 500 Internal Server Error. Sau khi xác đinh, tôi xác định được có 2 bảng ở truy vấn gốc. Vậy còn dữ liệu, ta sử dụng UNION để xem thử. Chủ yếu các nội dung cần đọc là chuỗi, vậy nên ta có thể thử xem có bao nhiêu cột có kiểu dữ liệu là chuỗi:
![image](https://github.com/phtuanthanh/LabPortwigger/assets/138991479/6d610b60-3817-4a5c-a279-980da77a848b)
Vậy dữ liệu của hai cột đều là string. 

Đạt được hai yêu cầu trên, ta cần xác định, bảng nào bảng chứa mật khẩu của admin, và cột mật khẩu đó có tên là gì. Sử dụng UNION với bảng INFORMATION_SCHEMA, đây là một bảng chứa thông tin về tất cả các bảng trong database.

Đầu tiên, tìm xem bảng nào là bảng có thông tin của các user, và đặc biệt là chứa admin, thực hiện truy vấn:
![image](https://github.com/phtuanthanh/LabPortwigger/assets/138991479/19aa6d36-352c-46c3-ab6d-5df1bf2de23a)
Tương tự đó với cột:
![image](https://github.com/phtuanthanh/LabPortwigger/assets/138991479/66431655-a116-4087-b7b0-b6f1d6cf3d1a)
Sau khi xem xét bảng và cột, ta nhận ra một bảng có tên users_fnygeg và có cột username_jppajz,password_uwzyie. Ta thử truy vấn:
![image](https://github.com/phtuanthanh/LabPortwigger/assets/138991479/b7e3d4e9-3f6f-437a-941e-96d1bf630e99)
Tìm mật khẩu của admin và đăng nhập.


