Đặc tả yêu cầu phần mềm (Software Requirements Specification - SRS)
Lịch sử chỉnh sửa tài liệu (Revision History)

→ Ghi lại ai chỉnh sửa, thời gian chỉnh sửa, nội dung thay đổi và phiên bản tài liệu.

1. Giới thiệu (Introduction)
1.1 Mục đích tài liệu (Purpose)

→ Mô tả mục đích của tài liệu SRS và đối tượng sử dụng tài liệu.

1.2 Phạm vi dự án (Scope)

→ Xác định hệ thống sẽ làm gì và không làm gì.

1.3 Định nghĩa, từ viết tắt và thuật ngữ (Definitions, Acronyms, Abbreviations)

→ Giải thích các thuật ngữ kỹ thuật và từ viết tắt.

1.4 Tài liệu tham khảo (References)

→ Danh sách tài liệu, chuẩn, API hoặc nguồn tham khảo liên quan.

1.5 Tổng quan tài liệu (Overview)

→ Tóm tắt cấu trúc và nội dung các phần trong tài liệu.

2. Mô tả tổng quan hệ thống (Overall Description)
2.1 Góc nhìn tổng thể sản phẩm (Product Perspective)

→ Mô tả hệ thống trong bối cảnh tổng thể và cách nó tương tác với các hệ thống khác.

2.2 Các chức năng chính của hệ thống (Product Functions)

→ Liệt kê các chức năng quan trọng của hệ thống.

2.3 Nhóm người dùng và đặc điểm (User Classes and Characteristics)

→ Xác định các nhóm người dùng như admin, giảng viên, sinh viên...

2.4 Môi trường hoạt động (Operating Environment)

→ Mô tả môi trường phần cứng, phần mềm, hệ điều hành, trình duyệt...

2.5 Ràng buộc thiết kế và triển khai (Design and Implementation Constraints)

→ Các giới hạn về công nghệ, tài nguyên hoặc quy định.

2.6 Giả định và phụ thuộc (Assumptions and Dependencies)

→ Các điều kiện giả định để hệ thống hoạt động đúng.

2.7 Tổng quan kiến trúc hệ thống (System Architecture Overview)

→ Mô tả kiến trúc tổng quát của hệ thống như frontend, backend, AI service, database...

3. Yêu cầu chức năng (Functional Requirements)
3.1 Xác thực và phân quyền (Authentication and Authorization)

→ Login, logout, JWT, quản lí quyền truy cập.

3.2 Quản lí lịch học (Academic Schedule Management)

→ Quản lí lớp học, học phần, lịch học, phòng học...

3.3 Giám sát lớp học (Classroom Monitoring)

→ Theo dõi lớp học thông qua camera realtime.

3.4 Nhận diện và theo dõi bằng AI (AI Detection and Tracking)

→ Phát hiện và tracking người dùng bằng AI.

3.5 Phân tích tương tác (Interaction Analytics)

→ Thống kê và đánh giá mức độ tương tác giữa giảng viên và sinh viên.

3.6 Báo cáo và bảng điều khiển (Report and Dashboard)

→ Hiển thị thống kê, biểu đồ và báo cáo.

3.7 Hệ thống cảnh báo và thông báo (Notification and Alert System)

→ Gửi cảnh báo hoặc thông báo khi phát hiện sự cố hoặc bất thường.

4. Yêu cầu dữ liệu (Data Requirements)
4.1 Mô hình dữ liệu logic (Logical Data Model)

→ Mô tả cấu trúc dữ liệu và quan hệ giữa các bảng.

4.2 Từ điển dữ liệu (Data Dictionary)

→ Giải thích ý nghĩa từng bảng và từng cột dữ liệu.

4.3 Lưu trữ dữ liệu (Data Retention)

→ Quy định thời gian lưu trữ dữ liệu.

4.4 Xử lí dữ liệu thời gian thực (Realtime Data Processing)

→ Xử lí stream camera, AI inference và cache realtime.

4.5 Sao lưu và phục hồi dữ liệu (Backup and Recovery)

→ Cơ chế backup và khôi phục dữ liệu khi xảy ra lỗi.

5. Yêu cầu giao tiếp bên ngoài (External Interface Requirements)
5.1 Giao diện người dùng (User Interfaces)

→ Thiết kế giao diện web/app/dashboard.

5.2 Giao tiếp phần mềm (Software Interfaces)

→ API, database, AI service, Redis...

5.3 Giao tiếp phần cứng (Hardware Interfaces)

→ Camera, GPU, server và các thiết bị liên quan.

5.4 Giao tiếp mạng (Communication Interfaces)

→ HTTP, HTTPS, WebSocket, RTSP...

6. Yêu cầu phi chức năng (Non-Functional Requirements)
6.1 Hiệu năng (Performance)

→ Tốc độ xử lí, latency, FPS...

6.2 Khả năng mở rộng (Scalability)

→ Khả năng mở rộng khi tăng số lượng người dùng hoặc camera.

6.3 Bảo mật (Security)

→ JWT, RBAC, mã hóa dữ liệu, bảo vệ API...

6.4 Độ ổn định (Reliability)

→ Hệ thống hoạt động ổn định và ít lỗi.

6.5 Khả năng sẵn sàng hoạt động (Availability)

→ Uptime và khả năng duy trì hoạt động liên tục.

6.6 Khả năng bảo trì (Maintainability)

→ Dễ nâng cấp, sửa lỗi và mở rộng hệ thống.

6.7 Tính dễ sử dụng (Usability)

→ Giao diện dễ dùng và dễ học.

6.8 Yêu cầu AI/Machine Learning (AI/ML Requirements)

→ Accuracy, confidence threshold, inference latency...

7. Yêu cầu đa ngôn ngữ và bản địa hóa (Internationalization and Localization Requirements)

→ Hỗ trợ nhiều ngôn ngữ, timezone, định dạng ngày giờ...

8. Các yêu cầu khác (Other Requirements)

→ Những yêu cầu không thuộc các nhóm bên trên.

9. Bảng thuật ngữ (Glossary)

→ Giải thích các thuật ngữ chuyên ngành được sử dụng trong tài liệu.

10. Mô hình phân tích và thiết kế (Analysis and Design Models)
10.1 Biểu đồ Use Case (Use Case Diagram)
10.2 Biểu đồ tuần tự (Sequence Diagram)
10.3 Biểu đồ hoạt động (Activity Diagram)
10.4 Biểu đồ ERD (Entity Relationship Diagram)
10.5 Biểu đồ triển khai hệ thống (Deployment Diagram)
11. Phụ lục (Appendices)

→ Chứa API mẫu, JSON mẫu, cấu hình camera, output AI hoặc tài liệu bổ sung.
