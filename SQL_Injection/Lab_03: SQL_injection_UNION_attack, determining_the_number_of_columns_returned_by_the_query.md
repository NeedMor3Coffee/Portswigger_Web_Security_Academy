# Giới thiệu 
Trong thử thách này chứa lỗ hổng SQL injection trong bộ lọc danh mục sản phẩm. Kết quả từ truy vấn được trả về trong phản hồi của ứng dụng, do đó bạn có thể sử dụng tấn công UNION để truy xuất dữ liệu từ các bảng khác. Bước đầu tiên của một cuộc tấn công như vậy là xác định số lượng cột đang được truy vấn trả về. Sau đó, bạn sẽ sử dụng kỹ thuật này trong các phòng thí nghiệm tiếp theo để xây dựng toàn bộ cuộc tấn công.

Để giải quyết bài tập này, hãy xác định số cột được truy vấn trả về bằng cách thực hiện tấn công SQL injection UNION trả về một hàng bổ sung chứa các giá trị null.

# Phân tích và giải quyết
Hãy cùng bắt đầu thử thách này. Tôi có liên kết đến thử thách tại: https://0a4900a503ddf30a80dc9ee9008e00dd.web-security-academy.net/. Bước đầu tiên là truy cập vào liên kết để xem chi tiết và hiểu các yêu cầu. Let’s go!

![image](https://github.com/user-attachments/assets/82c8393f-2ed3-42c8-897f-30863a128d4c)

Sau khi thực hiện các bước thăm dò và tìm hiểu chức năng của trang web, tôi đã phát hiện ra một điểm thú vị tại mục /filter?category=Accessories. Ở đây, tham số category đang có giá trị là Accessories. Để kiểm tra tính an toàn và cách xử lý các tham số này, tôi quyết định sử dụng công cụ BurpSuite - một công cụ mạnh mẽ và phổ biến trong việc kiểm tra bảo mật ứng dụng web.

Đầu tiên, tôi chèn ký tự đặc biệt ' vào giá trị của tham số category. Sau khi gửi yêu cầu, tôi nhận được phản hồi từ server với lỗi "Internal Server Error". Đây là dấu hiệu cảnh báo rằng giá trị của tham số không được xử lý đúng cách từ phía server, dẫn đến việc xảy ra lỗi nghiêm trọng.

![image](https://github.com/user-attachments/assets/a3c1e924-46cd-4aad-a4be-f898bd624240)

