Blind SQL Injection
1. What is Blind SQL Injection? - Blind SQL Injection là gì?

Blind SQL Injection xảy ra khi app có lỗ hổng SQL Injection, tuy nhiên các HTTP responses không chứa các kết quả liên quan đến truy vấn SQL hoặc chi tiết của lỗi database. Có nhiều trường hợp của UNION attacks trở nên không hiệu quả với blind SQL injection vulnerabilities. Để khắc phục tình trạng này, có thể sử dụng Blind SQL Injection với nhiều cách khác nhau.

2. Exploiting blind SQL injection by triggering conditional responses - Khai thác Blind SQL Injection bằng các điều kiện phản hồi

Xem xét một app sử dụng cookies để lưu trữ và sử dụng thông tin. Các request từ các user gửi đến app bao gồm một cookie header như sau:
![image](https://github.com/phtuanthanh/LabPortwigger/assets/138991479/45d18f87-4041-4514-8064-8c86a5d9ae10)
Khi một request sử dụng TrackingId để xử lý cookie. App sẽ sử dụng nó để truy vấn SQL để xác nhận rằng user truy cập là ai:
![image](https://github.com/phtuanthanh/LabPortwigger/assets/138991479/8beafa0a-5eae-4bbe-a1d5-a6843d1748b9)
Đây là một lổ hổng SQL Injection, tuy nhiên kết quả của truy vấn lại không trả về ngay lập tức user. Tuy nhiên, tùy vào các app sẽ có các cách khác nhau để trả về các lượng dữ liệu. Nếu bạn gửi một TrackingId hợp lệ, kết quả truy vấn sẽ thành công và trả về một message trong response là " Welcome back", và ngược lại với một TrackingId sẽ là một massage thông báo không thành công.

Đây chính là một lổ hổng đủ để ta khai thác blind SQL injection vulnerability. Bạn có thể lấy được các thông tin bằng việc sử dụng các điều kiện kết hợp với điều kiện đúng sai. Để hiểu cách thức khai thác, ta xem xét 2 request chứa TrackingId cookie theo kiểu:
![image](https://github.com/phtuanthanh/LabPortwigger/assets/138991479/8048a0f7-5401-402c-85a0-3f2ddd061b76)
+ Với giá trị đầu tiên sẽ trả về kết quả của câu lệnh truy vấn, bởi vì AND '1'='1' điều kiện là luôn đúng. Vậy nên kết quả trả về là một message thông báo thành công.
+ Với giá trị thứ hai  sẽ không trả về kết quả của câu lệnh truy vấn, bởi vì điều kiện là sai. Vậy nên kết quả trả về là một message thông báo không thành công.

Đây là một cách để ta xác định từng phần từng phần bằng một điều kiện đúng sai. Có vẻ hơi khó hiểu. Ví dụ như sau, mộ bảng Users có hai cột là Username và password, và ta cần tìm mật khẩu của một user có username là Administrator. Bạn có thể xác minh mật khẩu của user bằng việc gửi một bộ input để có thể tìm từng ký tự của mật khẩu.

Để bắt đầu, ta thực hiện câu truy vấn như sau:
![image](https://github.com/phtuanthanh/LabPortwigger/assets/138991479/e543d08b-83e7-402c-ac93-c7339ecb127e)
Nếu trả về là một massage "Welcome back", tức là điều kiện là đúng, và ký tự đầu tiên trong mật khẩu phải lớn hơn ký tự m.
Tiếp theo, ta thực hiện tiếp:
![image](https://github.com/phtuanthanh/LabPortwigger/assets/138991479/41300f65-6976-413d-afd2-af30cfaa08e6)
Nếu trả về không phải là massage như lúc nãy, thì ngược lại, ký tự đầu tiên trong mật khẩu không lớn hơn ký tự t. Từ đây ta có một khoảng không gian các ký tự để có thể tìm. sau đó ta cứ truy vấn dần dần như sau:
![image](https://github.com/phtuanthanh/LabPortwigger/assets/138991479/458d7114-ec44-41ae-87c7-4c0d376caa62)
Nếu trả về là một massage "Welcome back", thì ký tự đầu tiên của password là ký tự 's'.
Như vậy để suy luận ra được mật khẩu của user Administrator.

3. Error-Based SQL Injection - Dựa trên báo lỗi của SQL Injection

Tùy vào loại cơ sở dữ liệu bạn đang dùng, và kiểu lỗi được bạn nhắm đến, Error-Based SQL Injection là kỹ thuật bạn có thể dựa vào các thông báo lỗi để có thể lấy được các thông tin nhạy cảm, hay các thông tin không cho phép. Có hai dạng:
a. Exploiting blind SQL injection by triggering conditional errors - Khai thác blind SQL injection bởi kích hoạt điều kiện gây lỗi

Một vài app tạo ra các câu lệnh truy vấn tuy nhiên lại không có hành động thay đổi, tức là các truy vấn được trả về không khác nhau cho dù cú pháp khác nhau hay là boolean cũng không hề khác nhau.
Điều này khiến cho các kỹ thuật ở mục 2 bị vô tác dụng bởi ta không phát hiện được sự thay đổi của response app. Bởi vậy, bạn có thể sửa đổi câu lệnh truy vấn để nó chỉ gây ra lỗi database ở điều kiện đúng. Thông thường, một lỗi chưa qua xử lý bởi database sẽ gây ra một số khác biệt trong app, ví dụ như một error message. Điều này giúp ích cho việc ta tìm ra được sự thật. Để biết cách thức làm việc ra sao, ta xét hai truy vấn:
![image](https://github.com/phtuanthanh/LabPortwigger/assets/138991479/465240f0-f89b-47b3-9a71-89f48c6cb846)
+ Truy vấn 1 sẽ đưa ra ký tự 'a', không gây ra lỗi
+ Truy vấn 2 sẽ đưa ra lỗi, bởi vì chia cho 0 là sai.
  
Tức là nếu điều kiện là sai, sẽ in ra 'a' và ngược lại, sau đó so sánh với ký tự 'a' bên ngoài. Dựa vào đây, ta có thể thực hiện attacks như sau:
![image](https://github.com/phtuanthanh/LabPortwigger/assets/138991479/6d62f563-55cb-4970-8872-eed1fd8e095a)
Sử dụng cách này tương tự với mục 2, có thể tìm ra được password. Nếu gây ra lỗi tức là dự đoán của ta trong câu truy vấn là đúng, và ngược lại.




