# SQL injection (SQLi) là gì?
SQL injection (SQLi) là một lỗ hổng bảo mật web cho phép kẻ tấn công can thiệp vào các truy vấn mà ứng dụng thực hiện đối với cơ sở dữ liệu của ứng dụng. Điều này có thể cho phép kẻ tấn công xem dữ liệu mà thông thường chúng không thể truy xuất được. Dữ liệu này có thể bao gồm dữ liệu thuộc về người dùng khác hoặc bất kỳ dữ liệu nào khác mà ứng dụng có thể truy cập. Trong nhiều trường hợp, kẻ tấn công có thể sửa đổi hoặc xóa dữ liệu này, gây ra những thay đổi liên tục đối với nội dung hoặc hành vi của ứng dụng.

Trong một số trường hợp, kẻ tấn công có thể leo thang tấn công SQL injection để xâm phạm máy chủ cơ sở hoặc cơ sở hạ tầng phụ trợ khác. Nó cũng có thể cho phép chúng thực hiện các cuộc tấn công từ chối dịch vụ.

# Cách phát hiện lỗ hổng SQL injection
Bạn có thể phát hiện SQL injection theo cách thủ công bằng cách sử dụng một tập hợp các bài kiểm tra có hệ thống đối với mọi điểm vào trong ứng dụng. Để thực hiện việc này, bạn thường sẽ gửi:

* Ký tự dấu nháy đơn ' và tìm kiếm lỗi hoặc các bất thường khác.
* Một số cú pháp SQL cụ thể đánh giá theo giá trị cơ sở (gốc) của điểm nhập và một giá trị khác, đồng thời tìm kiếm sự khác biệt có hệ thống trong phản hồi của ứng dụng.
* Các điều kiện Boolean như OR 1=1và OR 1=2, và tìm kiếm sự khác biệt trong phản hồi của ứng dụng.
* Tải trọng được thiết kế để kích hoạt độ trễ thời gian khi được thực hiện trong truy vấn SQL và tìm kiếm sự khác biệt về thời gian phản hồi.
* Tải trọng OAST được thiết kế để kích hoạt tương tác mạng ngoài băng tần khi được thực hiện trong truy vấn SQL và giám sát mọi tương tác phát sinh.
  
Ngoài ra, bạn có thể tìm thấy phần lớn các lỗ hổng SQL injection một cách nhanh chóng và đáng tin cậy bằng Burp Scanner.

# Tiêm SQL vào các phần khác nhau của truy vấn
Hầu hết các lỗ hổng SQL injection xảy ra trong mệnh đề WHERE của truy vấn SELECT. Hầu hết các kiểm thử viên có kinh nghiệm đều quen thuộc với loại SQL injection này.

Tuy nhiên, lỗ hổng SQL injection có thể xảy ra ở bất kỳ vị trí nào trong truy vấn và trong các loại truy vấn khác nhau. Một số vị trí phổ biến khác mà SQL injection phát sinh là:

* Trong các câu lệnh UPDATE, trong các giá trị được cập nhật hoặc mệnh đề WHERE.<br>
* Trong các câu lệnh INSERT, bên trong các giá trị được chèn vào.<br>
* Trong các câu lệnh SELECT, bên trong tên bảng hoặc cột.<br>
* Trong các câu lệnh SELECT, trong mệnh đề ORDER BY.<br>

# Lấy dữ liệu ẩn
Hãy tưởng tượng một ứng dụng mua sắm hiển thị sản phẩm theo các danh mục khác nhau. Khi người dùng nhấp vào danh mục Quà tặng , trình duyệt của họ yêu cầu URL:
````bash
https://insecure-website.com/products?category=Gifts
````

Điều này khiến ứng dụng thực hiện truy vấn SQL để lấy thông tin chi tiết về các sản phẩm có liên quan từ cơ sở dữ liệu:
````bash
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
````
Ứng dụng không triển khai bất kỳ biện pháp phòng thủ nào chống lại các cuộc tấn công SQL injection. Điều này có nghĩa là kẻ tấn công có thể xây dựng các cuộc tấn công sau, ví dụ:
````bash
https://insecure-website.com/products?category=Gifts'--
````
Kết quả cho ra truy vấn SQL như sau:
````bash
SELECT * FROM products WHERE category = 'Gifts'--' AND released = 1
````
Quan trọng, lưu ý rằng đó -- là một chỉ báo chú thích trong SQL. Điều này có nghĩa là phần còn lại của truy vấn được hiểu là một chú thích, về cơ bản là xóa nó. Trong ví dụ này, điều này có nghĩa là truy vấn không còn bao gồm AND released = 1. Kết quả là, tất cả các sản phẩm đều được hiển thị, bao gồm cả những sản phẩm chưa được phát hành.

Bạn có thể sử dụng một cuộc tấn công tương tự để khiến ứng dụng hiển thị tất cả các sản phẩm trong bất kỳ danh mục nào, bao gồm cả những danh mục mà ứng dụng không biết:
````bash
https://insecure-website.com/products?category=Gifts'+OR+1=1--
````
Kết quả cho ra truy vấn SQL như sau:

````bash
SELECT * FROM products WHERE category = 'Gifts' OR 1=1--' AND released = 1
````
Truy vấn đã sửa đổi trả về tất cả các mục có giá trị là category, Gifts hoặc 1 bằng 1. Như 1=1 luôn đúng, truy vấn trả về tất cả các mục.

# Lật đổ logic ứng dụng

Hãy tưởng tượng một ứng dụng cho phép người dùng đăng nhập bằng tên người dùng và mật khẩu. Nếu người dùng gửi tên người dùng wiener và mật khẩu bluecheese, ứng dụng sẽ kiểm tra thông tin xác thực bằng cách thực hiện truy vấn SQL sau:
````bash
SELECT * FROM users WHERE username = 'wiener' AND password = 'bluecheese'
````
Nếu truy vấn trả về thông tin chi tiết của người dùng thì đăng nhập thành công. Nếu không, đăng nhập sẽ bị từ chối.

Trong trường hợp này, kẻ tấn công có thể đăng nhập với tư cách là bất kỳ người dùng nào mà không cần mật khẩu. Họ có thể thực hiện việc này bằng cách sử dụng chuỗi chú thích SQL --để xóa kiểm tra mật khẩu khỏi mệnh đề WHERE của truy vấn. Ví dụ, gửi tên người dùng administrator'-- và mật khẩu trống sẽ dẫn đến truy vấn sau:
````bash
SELECT * FROM users WHERE username = 'administrator'--' AND password = 'bluecheese'
````
