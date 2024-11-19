# Giới thiệu 
Trong thử thách này chứa lỗ hổng SQL injection trong bộ lọc danh mục sản phẩm. Kết quả từ truy vấn được trả về trong phản hồi của ứng dụng, do đó bạn có thể sử dụng tấn công UNION để truy xuất dữ liệu từ các bảng khác. Bước đầu tiên của một cuộc tấn công như vậy là xác định số lượng cột đang được truy vấn trả về. Sau đó, bạn sẽ sử dụng kỹ thuật này trong các phòng thí nghiệm tiếp theo để xây dựng toàn bộ cuộc tấn công.

Để giải quyết bài tập này, hãy xác định số cột được truy vấn trả về bằng cách thực hiện tấn công SQL injection UNION trả về một hàng bổ sung chứa các giá trị null.

# Phân tích và giải quyết
Hãy cùng bắt đầu thử thách này. Tôi có liên kết đến thử thách tại: https://0a4900a503ddf30a80dc9ee9008e00dd.web-security-academy.net/. Bước đầu tiên là truy cập vào liên kết để xem chi tiết và hiểu các yêu cầu. Let’s go!

![image](https://github.com/user-attachments/assets/82c8393f-2ed3-42c8-897f-30863a128d4c)

Sau khi thực hiện các bước thăm dò và tìm hiểu chức năng của trang web, tôi đã phát hiện ra một điểm thú vị tại mục /filter?category=Accessories. Ở đây, tham số category đang có giá trị là Accessories. Để kiểm tra tính an toàn và cách xử lý các tham số này, tôi quyết định sử dụng công cụ BurpSuite - một công cụ mạnh mẽ và phổ biến trong việc kiểm tra bảo mật ứng dụng web.

Đầu tiên, tôi chèn ký tự đặc biệt ' vào giá trị của tham số category. Sau khi gửi yêu cầu, tôi nhận được phản hồi từ server với lỗi "Internal Server Error". Đây là dấu hiệu cảnh báo rằng giá trị của tham số không được xử lý đúng cách từ phía server, dẫn đến việc xảy ra lỗi nghiêm trọng.

![image](https://github.com/user-attachments/assets/a3c1e924-46cd-4aad-a4be-f898bd624240)

Lỗi "Internal Server Error" thường xuất hiện khi server gặp phải tình huống không mong muốn, do việc xử lý dữ liệu đầu vào không đúng cách hoặc thiếu kiểm tra an toàn. Việc chèn ký tự ' là một kỹ thuật phổ biến để kiểm tra lỗ hổng SQL Injection, một trong những lỗ hổng bảo mật nguy hiểm nhất. Điều này có thể cho phép kẻ tấn công chèn mã độc vào các truy vấn SQL, từ đó truy cập, thay đổi hoặc xóa dữ liệu trong cơ sở dữ liệu của ứng dụng.

Kết quả kiểm tra cho thấy hệ thống chưa xử lý tham số category một cách chặt chẽ và an toàn, mở ra cơ hội cho những cuộc tấn công tiềm ẩn. Từ đó, tôi nhận thấy cần phải có các biện pháp bảo mật nghiêm ngặt hơn để đảm bảo an toàn cho dữ liệu và hệ thống.

Sau khi đã xác định bước đầu, tôi tiếp tục kiểm tra số lượng cột trong bảng bằng cách sử dụng payload ' +ORDER+BY+3--. Khi gửi yêu cầu này, tôi nhận thấy trang web trả về mã trạng thái 200, tức là yêu cầu đã thành công. Điều này cho thấy bảng dữ liệu có ít nhất 3 cột, vì nếu số cột lớn hơn 3, truy vấn SQL sẽ gây lỗi và không trả về mã trạng thái 200.

![image](https://github.com/user-attachments/assets/5e1d4b4b-8a92-40fc-9e41-d7024afe6679)

Dựa trên thông tin này, tôi tiếp tục điều chỉnh payload để xác định chính xác số cột. Ví dụ, tôi thử với ' +ORDER+BY+4-- và ' +ORDER+BY+5--, để xem khi nào truy vấn bắt đầu trả về lỗi. Nếu mã trạng thái 200 vẫn xuất hiện, điều này cho thấy số cột thực tế lớn hơn giá trị hiện tại. Ngược lại, nếu mã trạng thái 500 hoặc một lỗi khác xuất hiện, có nghĩa là số cột đã vượt quá giới hạn và tôi có thể xác định được số cột chính xác.

![image](https://github.com/user-attachments/assets/e0198bce-b589-4fba-9180-a27644c8f9b3)

Dựa trên kết quả trả về từ việc thử nghiệm với payload ' +ORDER+BY+3--, tôi xác định được rằng bảng dữ liệu có 3 cột. Điều này có nghĩa là truy vấn SQL đã thành công mà không gây ra lỗi, chứng tỏ bảng dữ liệu có ít nhất 3 cột.

Với thông tin này, tôi sẽ tiếp tục các bước kiểm tra khác để xác minh xem bảng dữ liệu có lỗ hổng bảo mật hay không. Thử nghiệm các payload khác như chèn giá trị ' UNION SELECT NULL,NULL,3-- sẽ giúp xác định xem có thể thực hiện các lệnh SQL độc hại nào khác hay không.

Nếu các giá trị được trả về, điều này có thể chứng tỏ rằng trang web có lỗ hổng SQL injection và cần được xử lý ngay lập tức để bảo đảm an toàn cho dữ liệu và hệ thống.
