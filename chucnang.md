## DEV

|  | là làm sau |  |  |  |
| --- | --- | --- | --- | --- |
| Loại | Tên chức năng | Mô tả nghiệp vụ chi tiết bên DEV | Người sử dụng | Đầu ra kỹ thuật (Output) |
| CORE | Xác thực & Phân quyền (RBAC) | - Thiết kế Database lưu trữ thông tin tài khoản và quyền hạn.

- Cấu hình JWT Authentication, viết API Đăng nhập/Đăng xuất.

- Làm giao diện đăng nhập cho 2 nhóm quyền: Admin và Giảng viên (Teacher) | Toàn bộ hệ thống | - Token JWT (Access/Refresh).

- UI Đăng nhập. |
| CORE |  | - Xây dựng UI/UX và các API CRUD cho phép người dùng thay đổi thông tin cá nhân, avatar, đổi mật khẩu.
- Thống kê điểm đánh giá chất lượng giảng dạy của giảng viên | Admin và giảng viên | - UI & API tài khoản cá nhân. |
| CORE | Quản lý Profile/tài khoản giảng viên, admin | Chỉnh sửa thông tin cá nhân | Admin | - RESTful API cho Lớp học.

- Giao diện CRUD lớp học. |
| CORE | Quản lí lịch dạy của giảng viên | Hỏi xem có tập trung làm mấy cái Học kỳ & Năm học,tiết học,lớp học không hay chỉ làm qua qua để có dữ liệu thôi | Giảng viên |  |
| CORE | Lưu trữ điểm tổng kết của tiết học | - nhận json từ mô hình trong fast, tính toán nếu cần, đẩy data vào db bằng nest | Hệ thống | - Endpoint nhận POST JSON dữ liệu.

- Cấu trúc bảng Log trong DB. |
| CORE | Màn hình giám sát có biểu đồ chi tiết buổi dạy | - Lấy dữ liệu điểm đã tính toán để vẽ thành đồ thị trực quan cho Admin xem: Điểm tổng quát (vẽ biểu đồ kim đồng hồ), sự thay đổi độ tập trung của lớp theo thời gian (vẽ biểu đồ đường thẳng).
- Nếu có thể thì stream lại video lớp học (có thể bao gồm video đã được xử lí bằng ai) | Admin | 1) chỉ số real time: là chỉ số từ khi bắt đầu đến thời điểm hiện tại (dùng cho phần giám sát)

số sinh viên đang được tương tác

số lượng sinh viên trong phòng

số sinh viên(có thể %) mà giảng viên tương tác 
chất lượng tương tác giữa giảng viên và sinh viên
mức độ di chuyển trong lớp học
mức độ tương tác tổng



2) tổng kết sau 1 tiết học: trung bình trong cả tiết  (lưu db dùng cho thống kê)

số lượng sinh viên trong phòng

số sinh viên(có thể %) mà giảng viên tương tác
chất lượng tương tác giữa giảng viên và sinh viên
mức độ di chuyển trong lớp học
mức độ tương tác tổng


3) thống kê: ngày/tuần/tháng/kỳ/năm học (lôi từ db ra để hiển thị)

% giảng viên tương tác 
chất lượng tương tác giữa giảng viên và sinh viên
mức độ di chuyển trong lớp học
biểu đồ đường mức độ tương tác tổng(theo từng giảng viên và chung) trong 1 tuần/tháng |
| CORE | Thống kê tổng quan (Admin Dashboard) | - Trang chủ của Admin hiển thị các con số tổng quát của toàn trường: Tổng số tiết đã quét, Tỷ lệ giảng viên đạt điểm Giỏi/Khá/Trung bình, Biểu đồ so sánh chất lượng tương tác giữa các khoa. | Admin |  |
|  | Đăng ký tài khoản microsoft |  |  |  |
| PHỤ | Giám sát trạng thái hoạt động của camera | - danh sách camera đang kết nối, trạng thái, tự động phân tích khi đến thời gian học. | Admin |  |
| PHỤ | Xuất báo cáo (Export) | - Viết module xử lý file ở Backend để tự động tổng hợp số liệu và xuất ra file PDF báo cáo sư phạm hoặc file Excel bảng điểm chuyên cần cuối buổi. | Admin | - API/Nút bấm xuất file .pdf, .xlsx. |
| PHỤ | Triển khai Cloud hệ thống Web | - Cấu hình Web Server Nginx, trỏ Domain, cấu hình chứng chỉ bảo mật SSL.

- Deploy ứng dụng Web (Frontend + Backend + DB) lên môi trường Cloud (Server riêng hoặc AWS/GCP). | Hệ thống | - Hệ thống chạy trên môi trường URL Production. |
| PHỤ |  |  |  |  |
|  | Lưu ý: Các chức năng thêm nên  có thêm bằng file excel |  |  |  |
|  | Về cơ bản thì nó là hệ thống quản lí lịch học( có các thành phần như sinh viên,giảng viên,tiết học,lớp học,học kì...) nhưng tập trung vào việc:
1. Giám sát chất lượng giảng dạy của giảng viên theo từng tiết học bằng hệ thống camera xử lí bằng AI
2. Thống kê |  |  |  |
|  | dùng đúng từ: mức độ tương tác không phải là chấm điểm |  |  |  |

## AI

| Loại | Tên chức năng | Mô tả nghiệp vụ chi tiết bên AI | Đầu ra kỹ thuật (Output) |  |
| --- | --- | --- | --- | --- |
| CORE | Khởi tạo & Định vị không gian | - Tiếp nhận thông tin cấu hình phòng học từ hệ thống để làm tham chiếu cho thuật toán. | - File/Biến cấu hình không gian lớp học trong Python. |  |
| CORE | Định vị & Tracking đối tượng | - Sử dụng mô hình YOLO để phát hiện (Detect) Giáo viên và từng Sinh viên trong khung hình.

- Triển khai thuật toán ByteTrack hoặc SORT để gán ID duy nhất và theo dõi (Tracking) đối tượng liên tục, tránh mất dấu khi bị che khuất. | - Luồng dữ liệu (Stream) tọa độ dạng Bounding Box kèm ID theo từng frame/giây. |  |
| CORE | Biến đổi tọa độ vật lý | - Xác định các điểm mốc của phòng học vật lý thực tế.

- Tính toán và áp dụng ma trận Homography để chuyển đổi tọa độ góc nhìn Camera từ dạng điểm ảnh sang tọa độ mặt sàn thực tế (mét). | - Hàm chuyển đổi tọa độ lý thuyết mét thực tế từ ảnh camera. |  |
| CORE | Bộ lọc tương tác (TSII Engine) | - Viết các thuật toán logic quản lý thời gian nán lại liên tục của Giáo viên bên cạnh từng ID Sinh viên.

- Cài đặt logic màng lọc nhị phân, kiểm tra vùng bục giảng để kích hoạt trạng thái tĩnh/động.

- Áp dụng hàm suy giảm điểm sau 10 giây lướt qua và chặn trần tích điểm sau 30 giây cắm chốt.

- Tự động tính mật độ lớp học để scale ngưỡng động. | - Hàm thuật toán lõi tính điểm tương tác 
-  tìm ra các ID sinh viên thuộc "Vùng Chưa Phủ"
- Tính tỉ lệ thời gian giáo viên di chuyển khỏi vùng bục trong suốt tiết học |  |
| CORE | Chuẩn hóa cấu trúc dữ liệu trả về | - Đóng gói toàn bộ kết quả phân tích (Điểm số, danh sách vùng chưa phủ, chuỗi thời gian tương tác) và cấu trúc lại thành một file JSON/Mảng object chuẩn để đẩy sang cho bên DEV gọi vẽ đồ thị. | - File JSON hoặc Payload API chuẩn định dạng mảng (Array). |  |
| CORE | Đóng gói Docker module AI | - Viết Dockerfile cách ly môi trường riêng cho Python và các thư viện nặng (OpenCV, PyTorch, YOLO) để đảm bảo không bị xung đột phiên bản khi deploy. | - File Dockerfile của module AI Python. |  |
| PHỤ | Phân tích hành vi Sinh viên | - Khởi tạo và huấn luyện mô hình Phân loại hành vi (Classification/Pose Estimation).

- Phân tích và gán nhãn trạng thái sinh viên theo thời gian thực: Tập trung (Focus), Mất tập trung (Gục mặt/Bấm điện thoại) | - Nhãn trạng thái hành vi (Focus, Distract, Hand-raising) kèm ID sinh viên theo thời gian. | Dự tính sẽ làm nếu đủ dữ liệu để thực hiện |
| PHỤ | Kết nối tham số động | - Viết module kết nối và đọc các hệ số thực nghiệm từ API/DB do bên DEV cung cấp để nạp vào hàm tính điểm thay vì khai báo biến cứng trong code Python. | - Hàm gọi API nhận tham số cấu hình hệ thống. |  |
| PHỤ | Tối ưu hóa mô hình & Deploy Server | - Chuyển đổi mô hình AI sang định dạng ONNX hoặc TensorRT để tăng tốc độ suy luận (Inference Time).

- Cấu hình deploy module AI chạy mượt mà trên môi trường Server (Tối ưu hóa hiệu năng phần cứng có GPU hoặc chạy thuần CPU). | - File Weights mô hình đã tối ưu và ứng dụng AI Engine chạy ổn định trên Server. |  |
| Hiện tại mô hình đang có bộ dataset lên đến 8000 ảnh chứa bounding box của giáo viên và sinh viên, cũng như là thông tin lớp học thu thập |  |  |  |  |
| Mục tiêu thu thập đủ 12000 bộ dataset chứa các lớp học khác nhau |  |  |  |  |
