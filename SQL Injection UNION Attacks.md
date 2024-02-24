SQL Injection UNION attacks
				     
Khi một app xuất hiện lỗ hổng SQL Injection, và kết quả của các câu truy vấn được trả từ các phản hồi của app. Việc sử dụng keyword UNION để tấn công SQL Injection theo nhiều cách khác nhau được gọi chung là SQL Injection UNION attacks
UNION là từ khóa cho phép ta kết nối 1 hoặc nhiều thêm các SELECT và nối thêm với kết quả truy vấn cũ. Ví dụ:
![image](https://github.com/phtuanthanh/LabPortwigger/assets/138991479/b4205ebd-6d4b-4456-8938-0724cf908746)
Trong truy vấn trên, kết quả trả về sẽ là hai cột chứa giá trị của cột a, b với bảng1 và cột c,d với bảng 2. Điều kiện để một UNION hoạt động, bao gồm 2 yếu tố:
+ Hai câu lệnh truy vấn phải có số cột giống nhau.
+ Các cột phải có data type( kiểu dữ liệu ) là như nhau. Chẳng hạn trong ví dụ trên TABLE1(int,char) thì bắt buộc phải là TABLE2(int, char) mới có thể thực hiện.

Vậy nên, để có bước đệm cho SQL Injection UNION attacks, có hai câu hỏi ta cần trả lời được, đó là:
+ Truy vấn nguyên gốc được trả về có số lượng cột là bao nhiêu?
+ Các cột được trả về từ truy vấn nguyên gốc có kiểu dữ liệu là gì?

1. Determining the number of columns required - Xác định số lượng cột

    a. Sử dụng các lần thử với ORDER BY
ORDER BY là từ khóa dùng để sắp xếp tăng dần theo cột, một điều ta có thể khai thác ở ORDER BY là từ khóa này lựa chọn sắp xếp theo cột, nhưng có thể không cần biết tên của cột mà chỉ là chỉ số 1,2...
Vậy nên ta có thể sử dụng ORDER BY mà không cần quan tâm đến tên của cột, ví dụ ORDER BY 1,.. Ta có thể lựa chọn input đầu vào theo kiểu từ dòng như sau:
![image](https://github.com/phtuanthanh/LabPortwigger/assets/138991479/885e2d47-8953-43cf-85e3-bf513e3de1be)
Ta cứ thử từ 1,2,3.... Hiển nhiên rằng không thể nào câu truy vấn chỉ truy xuất ra hai cột mà có thể sắp xếp theo cột thứ 3.
Khi chỉ số cột vượt quá số cột thực tế trong câu lệnh truy vấn, database sẽ báo lỗi:
![image](https://github.com/phtuanthanh/LabPortwigger/assets/138991479/2a7f46f1-40b8-479b-bd35-92623665d32d)
Đối với app, khi database báo lỗi như trên, app sẽ trả về một HTTP. Nhưng thường sẽ là một thông báo về lỗi chung, một số trường hợp khác nó sẽ không trả về kêt quả gì. Hoặc đơn giản, bạn có thể phát hiện ra dựa trên sự khác nhau của các phản hồi từ đó suy ra được số cột.

    b. Sử dụng các lần thử với UNION SELECT
Bởi UNION chỉ có thể kết hợp khi hai truy vấn có số lượng cột là như nhau. Mặc dù ta chưa xác định được tên cũng như kiểu dữ liệu của cột. Nhưng nếu sử dụng NULL, NULL là một đại lượng chung, bao quát hầu hết các giá trị, vậy nên ta có thể sử dụng NULL thay cho tên cột. Như sau:
![image](https://github.com/phtuanthanh/LabPortwigger/assets/138991479/39ae4002-cf41-4c2e-b334-8b1e87205be3)
Nếu số lượng cột ta truy vấn không cùng với truy vấn nguyên gốc, database sẽ báo lỗi:
![image](https://github.com/phtuanthanh/LabPortwigger/assets/138991479/480cb66c-a421-4550-b9d5-e3245688546c)
Cũng giống như ORDER BY, khi database báo lỗi, app sẽ trả lại một HTTP respone, nhưng nó thường là một thông báo lỗi chung. Với UNION SELECT, khi ta kết nối bị thiếu cột, database sẽ trả về một số cột bổ sung, với các giá trị NULL ở trong cột đó. Tùy thuộc vào code của appp, nếu may mắn, bạn có thể sẽ thấy được các thông tin bố sung dựa vào response, ví dụ như số hàng chính xác của bảng? (Cái này tôi cũng chưa hình dung lắm). Trong một số trường hợp khác, nó có thể trả về các từ khóa hay hàm báo lỗi khác nhau, ví dụ NullPointerException. Trong trường hợp xấu hơn, các response sẽ trông rất giống nhau. Điều này gây ra sự không hiệu quả cho phương pháp này.
2. Database-specific syntax - Cú pháp dành riêng database

   Trong Orcale, mỗi câu lệnh SELECT thường phải kết nối với một bảng có thật, Vậy nên bảng DUAL là một lựa chọn tốt cho mục đích đó. Tùy thuộc vào phiên bản, ứng dụng mà bạn đang dùng là gì. Sẽ có những cú pháp khác nhau cho từng loại.
Đọc thêm:https://portswigger.net/web-security/sql-injection/cheat-sheet
4. Finding columns with a useful data type - loại dữ liệu của từng cột

   Một SQL Injection UNION attacks có thể cho pháp bạn truy xuất được kết quả từ các truy vấn. Điều thú vị là các dữ liệu mà ta truy xuất thường là các string form( chuỗi). Vậy nên ta sẽ tìm có một hay nhiều cột trong nguyên gốc truy vẫn là dữ liệu gì, và nó có tương thích là string không. Sau khi bạn xác định được số cột, bạn có thể thử bằng UNION SELECT, để tìm xem dữ liệu ở các cột phải là string data hay không. Ví dụ một bảng có 4 cột:
   ![image](https://github.com/phtuanthanh/LabPortwigger/assets/138991479/ea23c81c-5a9f-4ee9-b6ba-41a2d055b80d)
Nếu dữ liệu cột không tương thích là string data, database sẽ trả về:
![image](https://github.com/phtuanthanh/LabPortwigger/assets/138991479/18de8484-404a-4eab-b578-8c07b1bde59f)
Nếu điều đó không xảy ra, và app trả về cho bạn một response chứa thông tin bổ sung, thì chắc chắn cột đó có dữ liệu là chuỗi.
5. Retrieving multiple values within a single column - Truy xuất nhiều giá trị ở một cột

   Trong một số trường hợp, có thể thực hiện truy xuất nhiều giá trị từ một cột, Ví dụ với Orcale, bạn có thể input:
   ![image](https://github.com/phtuanthanh/LabPortwigger/assets/138991479/9e931cb2-47f4-4cac-84c0-fc669f868bbc)
Dấu "||" dùng để ngăn cách trong Orcale. Và đây là kết quả: 
  ![image](https://github.com/phtuanthanh/LabPortwigger/assets/138991479/565287fa-7675-4690-8bf1-4c8c6cc9e52d)








