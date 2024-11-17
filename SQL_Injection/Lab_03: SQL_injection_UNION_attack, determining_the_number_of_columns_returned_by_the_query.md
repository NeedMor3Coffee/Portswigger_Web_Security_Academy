# Giới thiệu 
Trong thử thách này chứa lỗ hổng SQL injection trong bộ lọc danh mục sản phẩm. Kết quả từ truy vấn được trả về trong phản hồi của ứng dụng, do đó bạn có thể sử dụng tấn công UNION để truy xuất dữ liệu từ các bảng khác. Bước đầu tiên của một cuộc tấn công như vậy là xác định số lượng cột đang được truy vấn trả về. Sau đó, bạn sẽ sử dụng kỹ thuật này trong các phòng thí nghiệm tiếp theo để xây dựng toàn bộ cuộc tấn công.

Để giải quyết bài tập này, hãy xác định số cột được truy vấn trả về bằng cách thực hiện tấn công SQL injection UNION trả về một hàng bổ sung chứa các giá trị null.

# Phân tích và giải quyết

