SQL Injection
1. What is SQL Injection? ---> SQL Injection là gì?
SQL Injection là một loại lỗ hổng web cho phép các attacker  can thiệp vào cơ sở dữ liệu của app bằng các câu lệnh truy vấn (query). Trong các trường hợp, các attacker có thể chỉnh sửa, xóa dữ liệu của app, gây ra ảnh hưởng và thay đổi cho app. Trong một số trường hợp leo thang, attacker có thể can thiệp leo thang lên sever hoặc cơ sở hạ tầng( back-end).
2. How to detect SQL Injection vulnerabilities? --> Làm cách nào để phát hiện rằng một app có lổ hổng SQL Injection
Bạn có thể phát hiện SQL Injection bằng việc sử dụng một bộ các kỹ thuật lặp đi lặp lại trên app.  Dưới đây là một số thứ bạn cần để ý:
•	Dấu ' và nhìn được sự bất thường diễn ra.
•	Một số cú pháp SQL và xem xét sự thay đổi.
•	Các điều kiện đúng sai như OR 1=1 and OR 1=2, và quan sát sự khác nhau trong phản hồi của app(Blind)
•	Thời gian chờ khi sử thực thi một số câu lệnh SQL và thời gian trả lời.
•	OSAT?
Ngoài ra, ta có thể sử dụng app Burp Scanner để có thể tìm kiếm SQL Injection.
3. SQL injection in different parts of the query --> Các câu truy vấn khác nhau của SQL Injection
Thường thì SQL Injection thường xảy ra ở truy vấn WHERE một bộ phận của truy vấn SELECT. Sau đây là một số truy vấn:
•	Toán tử UPDATE có thể update giá trị hoặc WHERE truy vấn.
•	Toán tử INSERT nhập giá trị vào.
•	Toán tử SELECT cho ra table hoặc cột.
•	ORDER BY liên kết các table bằng so sánh.
4. Các loại SQL Injection chủ yếu
Có rất nhiều loại tấn công, kĩ thuật, lổ hổng khác nhau về SQL Injection xảy ra ở nhiều trường hợp khác nhau. Tuy nhiên có 4 loại là phổ biến:
a. Retrieving hidden data ( thay đổi câu truy vấn để đưa ra các thông tin thêm)
b. Subverting application logic ( sử dụng câu truy vấn đề có thể can thiệp vào bên trong app)
c. UNION attacks ( láy các dữ liệu từ các bảng khác nhau trong database.
d. Blind SQL Injetion ( dựa trên các chi tiết phản hồi của app).
5. Retrieving hidden data - Truy xuất các dữ liệu ẩn
 ![image](https://github.com/phtuanthanh/LabPortwigger/assets/138991479/0475b206-16ea-4bcc-9d20-441c4735da9a)

Ảnh trên là hình biểu diễn đường dẫn của app shopping, khi user clicks vào Gifts category.  Thực ra, SQL sẽ được truy vấn như sau khi click vào Gifts.
![image](https://github.com/phtuanthanh/LabPortwigger/assets/138991479/6ddeb2b8-fc2a-4292-a555-7ee98b7e3f9a)

 Khi user click vào Gifts, app sẽ tự truy vấn SELECT ở bảng products với điều kiện category="Gifts" và released=1. Released là để kiểm tra món hàng đã được phát hành chưa.
Trong một số phiên bản SQL, comment sẽ được sự dụng với kí hiệu "--". Tức là sau "--" các truy vấn sẽ bị vô hiệu hóa. Điều gì xảy ra nếu ta thay đổi URL như sau:
 ![image](https://github.com/phtuanthanh/LabPortwigger/assets/138991479/d12d7357-6844-4e66-9e1a-4d7a75645ccf)

Kết quả của truy vấn SQL là: 
 ![image](https://github.com/phtuanthanh/LabPortwigger/assets/138991479/5e5500fc-d8e7-4038-a152-ad56a1db287d)

Khi đó, truy vấn chỉ còn điều kiện      phần sau đã bị "--" làm comment.
Xét điều kiện trên, 1=1 luôn luôn là điều kiện đúng. Vậy nên khi ta sửa đổi URL như trên.  Sẽ làm hiện lên tất cả các thông tin của bảng products. Đây chính là kỹ thuật Retrieving hidden data( hiện
các dữ liệu ẩn)
Tùy vào phiên bản SQL của app sử dụng, sẽ có nhiều trường hợp, khả năng xảy ra hơn.
Bài lab liên quan đến Retrieving hidden data: https://portswigger.net/web-security/sql-injection/lab-retrieve-hidden-data

Solution: 

6. Subverting application logic - Phá vỡ logic của app
   
Một trong những nơi quan trọng, và cũng quen thuộc nhất của một application là nơi đăng nhập. Giả sử ta có một app với username và passwordd. Nếu ta đăng nhập với username là wiener và password là bluecheese
thì app sẽ tạo ra một câu truy vấn SQL như sau để truy vấn:
![image](https://github.com/phtuanthanh/LabPortwigger/assets/138991479/7d117a31-9ed0-4251-8dd3-37663dad65a7)
Nếu như kết quả truy vấn trả về là thông tin của user, thì OK, bạn đã login thành công, Còn không, thì login thất bại. Subverting application logic trong trường hợp này là cách các attacker đăng nhập với
chỉ cần username mà không cần password. Giả sự, attacker nhập vào ở username là administrator'-- và để trống password. Điều gì sẽ xảy ra, đây là truy vấn được sinh ra từ app:
![image](https://github.com/phtuanthanh/LabPortwigger/assets/138991479/622cd546-2b49-4ee1-a730-a52a0be5f86b)

Kết quả của câu lệnh truy vấn sẽ trả về với username là administrator và đăng nhập thành công. Đây là một kỹ thuật rất nguy hiểm, nếu ta kết hợp cả kỹ thuật Retrieving hidden data để hiện lên dữ liệu của một bảng user
rồi từ đó, có thông tin của từng username, dùng kỹ thuật trên để đăng nhập vào các user đó để truy xuất các thông tin.
7. Retrieving data from other database tables ( Truy xuất dữ liệu của các bảng khác)
Trong kỹ thuật này, attacker sử dụng câu lênh UNION, đây là câu lệnh để kết hợp hai bảng lại với nhau. Quay lại app về cửa hàng, nếu user click vào Gifts, đây là truy vấn SQL sinh ra từ app:
![image](https://github.com/phtuanthanh/LabPortwigger/assets/138991479/55d6ae5c-9b5f-4a85-81ca-5bf8464150b1)
Nếu input được thay đổi thành: 
![image](https://github.com/phtuanthanh/LabPortwigger/assets/138991479/26c8d28e-468c-4c2d-b13f-f7bbea52678c)
Với input như trên, app sẽ trả lại toàn bộ username và password dọc theo tên và miêu tả của các sản phẩm. Chúng không liên quan gì đến nhau, chỉ là hai bảng được gộp lại.
Về kỹ thuật sử dụng UNION tôi sẽ đề cập thêm ở bài viết này:
https://github.com/phtuanthanh/LabPortwigger/blob/SQL-Injection/SQL%20Injection%20UNION%20Attacks.md







