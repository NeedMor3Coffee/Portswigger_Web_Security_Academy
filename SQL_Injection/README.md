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

Trong trường hợp này, kẻ tấn công có thể đăng nhập với tư cách là bất kỳ người dùng nào mà không cần mật khẩu. Họ có thể thực hiện việc này bằng cách sử dụng chuỗi chú thích SQL -- để xóa kiểm tra mật khẩu khỏi mệnh đề WHERE của truy vấn. Ví dụ, gửi tên người dùng administrator'-- và mật khẩu trống sẽ dẫn đến truy vấn sau:
````bash
SELECT * FROM users WHERE username = 'administrator'--' AND password = ''
````
username truy vấn này trả về tên người dùng administrator và ghi nhận thành công kẻ tấn công vào tài khoản người dùng đó.

# Tấn công SQL Injection bằng UNION
Khi một ứng dụng dễ bị SQL injection và kết quả của truy vấn được trả về trong phản hồi của ứng dụng, bạn có thể sử dụng từ khóa UNION để truy xuất dữ liệu từ các bảng khác trong cơ sở dữ liệu. Điều này thường được gọi là tấn công SQL injection UNION.

Từ khóa UNION cho phép bạn thực hiện một hoặc nhiều truy vấn SELECT bổ sung và thêm kết quả vào truy vấn ban đầu. Ví dụ:
````bash
SELECT a, b FROM table1 UNION SELECT c, d FROM table2
````
Truy vấn SQL này trả về một tập kết quả duy nhất với hai cột, chứa các giá trị từ các cột a và b trong table1 và các cột c và d trong table2.

Để truy vấn UNION có hiệu quả, hai yêu cầu chính phải được đáp ứng:

* Các truy vấn riêng lẻ phải trả về cùng số cột.
* Các kiểu dữ liệu trong mỗi cột phải tương thích giữa các truy vấn riêng lẻ.

Để thực hiện một cuộc tấn công SQL injection UNION, hãy đảm bảo rằng cuộc tấn công của bạn đáp ứng được hai yêu cầu sau. Điều này thường liên quan đến việc tìm hiểu:

* Có bao nhiêu cột được trả về từ truy vấn ban đầu.
* Những cột nào được trả về từ truy vấn gốc có kiểu dữ liệu phù hợp để lưu trữ kết quả từ truy vấn được đưa vào.

# Xác định số lượng cột cần thiết
Khi bạn thực hiện tấn công SQL injection UNION, có hai phương pháp hiệu quả để xác định có bao nhiêu cột được trả về từ truy vấn gốc.

Một phương pháp bao gồm việc chèn một loạt các mệnh đề ORDER BY và tăng chỉ số cột được chỉ định cho đến khi xảy ra lỗi. Ví dụ, nếu điểm chèn là một chuỗi được trích dẫn trong mệnh đề WHERE của truy vấn gốc, bạn sẽ gửi:
````bash
' ORDER BY 1--
' ORDER BY 2--
' ORDER BY 3--
````
Chuỗi tải trọng này sửa đổi truy vấn gốc để sắp xếp kết quả theo các cột khác nhau trong tập kết quả. Cột trong mệnh đề ORDER BY có thể được chỉ định theo chỉ mục của nó, do đó bạn không cần biết tên của bất kỳ cột nào. Khi chỉ mục cột được chỉ định vượt quá số lượng cột thực tế trong tập kết quả, cơ sở dữ liệu trả về lỗi, chẳng hạn như:
````bash
The ORDER BY position number 3 is out of range of the number of items in the select list.
````
Ứng dụng có thể thực sự trả về lỗi cơ sở dữ liệu trong phản hồi HTTP của nó, nhưng nó cũng có thể đưa ra phản hồi lỗi chung. Trong những trường hợp khác, nó có thể chỉ trả về không có kết quả nào cả. Dù bằng cách nào, miễn là bạn có thể phát hiện ra một số khác biệt trong phản hồi, bạn có thể suy ra có bao nhiêu cột đang được trả về từ truy vấn.

Phương pháp thứ hai bao gồm việc gửi một loạt UNION SELECT các dữ liệu chỉ định số lượng giá trị null khác nhau:
````bash
' UNION SELECT NULL--
' UNION SELECT NULL,NULL--
' UNION SELECT NULL,NULL,NULL--
````
Nếu số lượng giá trị null không khớp với số lượng cột, cơ sở dữ liệu sẽ trả về lỗi, chẳng hạn như:
````bash
All queries combined using a UNION, INTERSECT or EXCEPT operator must have an equal number of expressions in their target lists.
````
Chúng tôi sử dụng NULL làm giá trị được trả về từ truy vấn SELECT được chèn vì các kiểu dữ liệu trong mỗi cột phải tương thích giữa truy vấn gốc và truy vấn được chèn. NULL có thể chuyển đổi thành mọi loại dữ liệu phổ biến, do đó, nó tối đa hóa khả năng tải trọng sẽ thành công khi số cột chính xác.

Tương tự như kỹ thuật ORDER BY , ứng dụng có thể thực sự trả về lỗi cơ sở dữ liệu trong phản hồi HTTP của nó, nhưng có thể trả về lỗi chung hoặc đơn giản là không trả về kết quả nào. Khi số lượng giá trị null khớp với số lượng cột, cơ sở dữ liệu trả về một hàng bổ sung trong tập kết quả, chứa các giá trị null trong mỗi cột. Hiệu ứng đối với phản hồi HTTP phụ thuộc vào mã của ứng dụng. Nếu may mắn, bạn sẽ thấy một số nội dung bổ sung trong phản hồi, chẳng hạn như một hàng bổ sung trên bảng HTML. Nếu không, các giá trị null có thể kích hoạt một lỗi khác, chẳng hạn như NullPointerException. Trong trường hợp xấu nhất, phản hồi có thể trông giống như phản hồi do số lượng giá trị null không chính xác. Điều này sẽ khiến phương pháp này không hiệu quả.
















