Đặc tả Use Case 


 
MỤC LỤC
CHƯƠNG I. TỔNG QUAN HỆ THỐNG	3
1.  Giới thiệu và mục tiêu hệ thống	3
2.  Các thành phần chính	4
3.  Phạm vi và giới hạn	4
CHƯƠNG II. THIẾT KẾ HỆ THỐNG	5
1.  Đối tượng sử dụng	5
2.  Xác định và mô tả các ca sử dụng	6
3.  Chi tiết các ca sử dụng	8
4.  Sơ đồ tổng quát ca sử dụng	14
5.  Sơ đồ lớp	15
6.  Sơ đồ thực thể	16
7.  Sơ đồ cơ sở dữ liệu	17
8.  Giao diện người dùng	18
CHƯƠNG III. KIẾN TRÚC AI & XỬ LÝ THỜI GIAN THỰC	19
1.  Pipeline xử lý video real-time	19
2.  Kiến trúc FastAPI và xử lý frame	20
3.  Các chỉ số giám sát và phân tích	21

 
CHƯƠNG I. TỔNG QUAN HỆ THỐNG
1. Giới thiệu và mục tiêu hệ thống
Hệ thống AI Phân tích Tương tác giữa Sinh viên và Giảng viên là một nền tảng quản lý lịch học tích hợp công nghệ trí tuệ nhân tạo, được xây dựng nhằm mục tiêu hỗ trợ giảng viên và nâng cao chất lượng giáo dục tại các cơ sở đào tạo. Hệ thống không chỉ quản lý thông tin cơ bản như sinh viên, giảng viên, tiết học, lớp học, học kỳ mà còn tích hợp hệ thống camera thông minh để giám sát và phân tích chất lượng giảng dạy theo thời gian thực.
 
Mục tiêu chính của hệ thống bao gồm:
•       Giám sát chất lượng giảng dạy từng tiết học thông qua hệ thống camera xử lý bằng AI
•       Thống kê và phân tích tương tác giữa sinh viên và giảng viên trong lớp học
•       Phân tích video real-time với tần suất 5 frame/giây để ghi nhận kết quả sát với thực tế
•       Cung cấp báo cáo đánh giá chất lượng giảng dạy theo từng tiết học, lớp học, học kỳ
•       Hỗ trợ ban quản lý nhà trường ra quyết định cải thiện chất lượng giáo dục dựa trên dữ liệu thực tế
 
2. Các thành phần chính của hệ thống
Hệ thống bao gồm các thành phần nghiệp vụ cốt lõi sau:
 
STT	Thành phần	Mô tả
1	Sinh viên (Student)	Đối tượng học tập, được ghi nhận sự tham gia và mức độ tương tác qua camera
2	Giảng viên (Lecturer)	Đối tượng giảng dạy, được đánh giá chất lượng qua hành vi và tương tác trong lớp
3	Lớp học (Class)	Đơn vị tổ chức học tập, gắn với phòng học và hệ thống camera cụ thể
4	Tiết học (Lesson)	Đơn vị thời gian giảng dạy, là phạm vi phân tích và ghi nhận kết quả AI
5	Học kỳ (Semester)	Khoảng thời gian học tập, là đơn vị tổng hợp báo cáo chất lượng
6	Camera / AI Module	Thiết bị thu hình và mô hình AI phân tích frame theo thời gian thực (5 FPS)
7	FastAPI Backend	Đơn vị tiếp nhận và xử lý frame từ camera
8	NestJS Backend	Đơn vị xử lý request từ frontend, lưu dữ liệu vào Database
9	Dashboard / Báo cáo	Giao diện hiển thị thống kê, biểu đồ và đánh giá chất lượng giảng dạy
 
3. Phạm vi và giới hạn
•       Hệ thống hoạt động trong môi trường lớp học có lắp đặt camera và kết nối mạng ổn định
•       Phân tích video được thực hiện real-time với độ trễ tối đa cho phép là 2-3 giây
•       Dữ liệu phân tích được lưu trữ theo từng tiết học và có thể truy xuất lịch sử
•       Hệ thống không xử lý nhận diện khuôn mặt cá nhân, chỉ phân tích hành vi nhóm và mức độ tương tác tổng thể
 

 
CHƯƠNG II. THIẾT KẾ HỆ THỐNG
1. Đối tượng sử dụng
Hệ thống phục vụ 2 nhóm đối tượng chính với các quyền và chức năng khác nhau:
 
STT	Đối tượng	Vai trò
	Quyền truy cập
1	Quản trị viên(Admin)	Quản lý tài khoản/thông tin giảng viên, camera, lịch học..	Toàn quyền
3	Giảng viên	Xem kết quả phân tích tiết dạy của mình, xem lịch giảng dạy	Xem dữ liệu cá nhân
 
2. Xác định và mô tả các ca sử dụng
2.1. Nhóm ca sử dụng quản lý lịch học
 
Mã UC	Tên ca sử dụng	Tác nhân	Mô tả ngắn
UC-01	Xác thực/ Phân Quyền	Hệ thống	Xác thực đăng nhập và phân quyền
UC-02	Quản lý thông tin giảng viên(profile)	Quản trị viên	CRUD giảng viên/ thông tin giảng viên
UC-03	Quản lý lớp học	Quản trị viên	Tạo, cập nhật lớp học, gắn phòng học và camera
UC-04	Quản lý thời khóa biểu	Quản trị viên	Lập lịch tiết học
UC-05	Xem lịch giảng dạy	Giảng viên	Tra cứu lịch giảng dạy
 
2.2. Nhóm ca sử dụng giám sát AI
 
Mã UC	Tên ca sử dụng	Tác nhân	Mô tả ngắn
UC-06	Xử lý hình ảnh qua camera bằng mô hình AI 	Hệ thống	- Kích hoạt camera khi tiết học bắt đầu
- Gửi 5 frame/giây lên FastAPI để xử lý
- Lưu kết quả phân tích cuối cùng vào db
UC-07	Giám sát tiết dạy	Quản trị viên/
Giảng viên	Gồm video camera và chỉ số tương tác trong tiết dạy
 
2.3. Nhóm ca sử dụng thống kê và báo cáo
 
Mã UC	Tên ca sử dụng	Tác nhân	Mô tả ngắn
UC-08	Xem thống kê cho Quản trị viên	Quản trị viên	Thống kê với bộ lọc theo thời gian: ngày/tuần/tháng/kỳ/năm học, giảng viên
UC-09	Xem thống kê cho Giảng viên	Giảng viên	Giảng viên xem thống kê về các chỉ số của các tiết học đã giảng dạy
UC-10	Xuất báo cáo	Quản trị viên	Xuất báo cáo dạng PDF, Excel
 2.4. Nhóm ca sử dụng khác 

Mã UC	Tên ca sử dụng	Tác nhân	Mô tả ngắne
UC-11	Quản lý camera	Quản trị viên	Quản lý danh sách camera đang kết nối, trạng thái

3. Phân tích chi tiết các ca sử dụng
3.1. UC-01: Xác thực đăng nhập và phân quyền.
1.	Mô tả 
Use Case này cho phép người dùng đăng nhập vào hệ thống bằng tài khoản đã được cấp. Sau khi xác thực thành công, hệ thống sẽ kiểm tra vai trò (role) của người dùng để phân quyền truy cập các chức năng tương ứng như quản trị hệ thống, quản lý lớp học, xem báo cáo hoặc tham gia lớp học. 
2.	Tác nhân (Actors)
●	Quản trị viên (Admin)
●	Giảng viên (Teacher)
●	Hệ thống xác thực (Authentication System)
3.	Tiền điều kiện (Preconditions)
●	Người dùng đã được tạo tài khoản trong bảng users
●	Người dùng đã được gán ít nhất một vai trò trong bảng user_roles
●	Người dùng có email và mật khẩu hợp lệ
4.	Hậu điều kiện (Postconditions)
Khi thành công
●	Người dùng đăng nhập thành công vào hệ thống
●	JWT Access Token được cấp
●	Hệ thống xác định role của người dùng
●	Người dùng được chuyển đến giao diện phù hợp với quyền hạn
Khi thất bại
●	Không tạo phiên đăng nhập
●	Hệ thống trả về thông báo lỗi xác thực

5.	Quy tắc nghiệp vụ (Business Rules)
●	Email phải là duy nhất trong hệ thống
●	Mật khẩu được lưu dưới dạng hash, không lưu plain text
●	Hệ thống phân quyền dựa trên role
●	Có 2 loại token access_token(1 giờ) và refresh_token(4 ngày)
●	JWT token tồn tại 1 tiếng, muốn dùng tiếp phải refresh-token
●	Người dùng không có quyền sẽ không truy cập được API tương ứng
●	API yêu cầu xác thực phải kiểm tra token trước khi xử lý
6.	Các bảng dữ liệu liên quan
●	Bảng: users, roles, user_roles
7.	API liên quan 
Method	Endpoint	Chức năng
POST	/auth/login	Đăng nhập hệ thống
POST	/auth/logout	Đăng xuất
GET	/auth/me	Lấy thông tin người dùng hiện tại
POST	/auth/refresh_token	Cấp lại access_token

8.	Sequence diagram
Ảnh: Quy trình đăng nhập
 

Ảnh: Quy trình refresh_token
 
Ảnh: Quy trình đăng xuất
 

9.	Giao diện liên quan (UI Screens)
●	Màn hình đăng nhập
●	Màn hình dashboard Admin
●	Màn hình dashboard Giảng viên










 
 3.2. UC-02: Quản lý thông tin giảng viên (profile)

1. Mô tả
Use Case này cho phép Quản trị viên thực hiện các thao tác CRUD (Tạo, Đọc, Cập nhật, Xóa) đối với hồ sơ giảng viên trong hệ thống. Thông tin giảng viên được liên kết với tài khoản người dùng (users) và bộ môn (departments). Admin có thể tìm kiếm, lọc danh sách giảng viên theo nhiều tiêu chí khác nhau.

2. Tác nhân (Actors)
•	Quản trị viên (Admin)

3. Tiền điều kiện (Preconditions)
•	Admin đã đăng nhập và có JWT token hợp lệ
•	Khoa (department) đã tồn tại trong hệ thống

4. Hậu điều kiện (Postconditions)
Khi thành công:
•	Thông tin giảng viên được thêm mới / cập nhật / xóa trong bảng teachers
•	Dữ liệu liên kết (user_id, department_id) được đảm bảo toàn vẹn tham chiếu
Khi thất bại:
•	Dữ liệu không hợp lệ: hệ thống trả về lỗi 400 Bad Request kèm thông báo chi tiết
•	Không tìm thấy tài nguyên: hệ thống trả về lỗi 404 Not Found

5. Dữ liệu đầu vào (Inputs)
Tên dữ liệu	Kiểu	Mô tả
full_name	String	Họ và tên giảng viên (bắt buộc)
department_id	UUID	ID bộ môn giảng viên thuộc về
gender	String	Giới tính (MALE / FEMALE / OTHER)
date_of_birth	Date	Ngày sinh (định dạng YYYY-MM-DD)
phone	String	Số điện thoại liên hệ
avatar_url	String	URL ảnh đại diện(Tải ảnh đại diện lên lưu vào cloudinary)
academic_rank	String	Chức danh học thuật (GS, PGS,...)
degree	String	Học vị (Tiến sĩ, Thạc sĩ,...)
specialization	String	Chuyên ngành giảng dạy

6. Dữ liệu đầu ra (Outputs)
Tên dữ liệu	Kiểu	Mô tả
id	UUID	ID duy nhất của giảng viên
full_name	String	Họ và tên giảng viên
department	Object	Thông tin bộ môn (id, name)
user	Object	Thông tin tài khoản liên kết (id, email)
academic_rank	String	Chức danh học thuật
degree	String	Học vị

7. Quy tắc nghiệp vụ (Business Rules)
•	Mỗi giảng viên liên kết với đúng một tài khoản (user_id UNIQUE)
•	Khi xóa tài khoản users, bản ghi giảng viên tương ứng bị xóa theo (ON DELETE CASCADE)
•	Không được xóa giảng viên nếu họ đang được gán cho lớp học (classes.teacher_id)
•	Số điện thoại phải đúng định dạng Việt Nam nếu được cung cấp

8. Các bảng dữ liệu liên quan
Bảng: teachers, users, departments, classes

9. API liên quan
Method	Endpoint	Chức năng
GET	/teachers	Lấy danh sách giảng viên (có phân trang, lọc)
GET	/teachers/:id	Lấy thông tin chi tiết một giảng viên
POST	/teachers	Tạo mới hồ sơ giảng viên
PUT	/teachers/:id	Cập nhật toàn bộ thông tin giảng viên
PATCH	/teachers/:id	Cập nhật một phần thông tin giảng viên
DELETE	/teachers/:id	Xóa giảng viên khỏi hệ thống

10. Luồng xử lý chính
Luồng tạo mới giảng viên:
1.	Admin gửi POST /teachers kèm thông tin giảng viên và JWT token
2.	NestJS xác thực token và kiểm tra quyền Admin
3.	Hệ thống kiểm tra user_id và department_id tồn tại trong DB
4.	Nếu hợp lệ, tạo bản ghi mới trong bảng teachers
5.	Trả về thông tin giảng viên vừa tạo kèm HTTP 201 Created
Luồng cập nhật thông tin:
6.	Admin gửi PUT/PATCH /teachers/:id kèm dữ liệu cần cập nhật
7.	Hệ thống kiểm tra sự tồn tại của giảng viên theo id
8.	Xác thực dữ liệu đầu vào, cập nhật bản ghi trong DB
9.	Trả về thông tin sau cập nhật kèm HTTP 200 OK

11. Giao diện liên quan (UI Screens)
•	Màn hình danh sách giảng viên (có tìm kiếm, phân trang)
•	Màn hình thêm mới / chỉnh sửa giảng viên (form nhập liệu)
•	Màn hình xem chi tiết hồ sơ giảng viên

 

3.3. UC-03: Quản lý lớp học

1. Mô tả
Use Case này cho phép Quản trị viên tạo mới, cập nhật và quản lý các lớp học trong hệ thống. Mỗi lớp học được liên kết với một giảng viên phụ trách và một học kỳ cụ thể. Admin cũng có thể quản lý danh sách sinh viên thuộc lớp (thêm/xóa sinh viên), đồng thời xem lịch tiết học và camera gắn với phòng học tương ứng.

2. Tác nhân (Actors)
•	Quản trị viên (Admin)

3. Tiền điều kiện (Preconditions)
•	Admin đã đăng nhập và có JWT token hợp lệ
•	Giảng viên (teachers), học kỳ (semesters) và phòng học (rooms) đã tồn tại trong hệ thống
•	Sinh viên (students) đã được tạo trước khi thêm vào lớp

4. Hậu điều kiện (Postconditions)
Khi thành công:
•	Lớp học được tạo mới / cập nhật trong bảng classes
•	Bảng class_students được cập nhật đúng khi thêm/xóa sinh viên
Khi thất bại:
•	Trùng class_code: hệ thống trả về lỗi 409 Conflict
•	Số sinh viên vượt max_students: hệ thống từ chối với lỗi 400 Bad Request

5. Dữ liệu đầu vào (Inputs)
Tên dữ liệu	Kiểu	Mô tả
class_code	String	Mã lớp học (duy nhất, bắt buộc)
class_name	String	Tên lớp học (bắt buộc)
teacher_id	UUID	ID giảng viên phụ trách
semester_id	UUID	ID học kỳ tương ứng
max_students	Integer	Sĩ số tối đa (> 0)
student_ids	UUID[]	Danh sách ID sinh viên (khi thêm vào lớp)

6. Dữ liệu đầu ra (Outputs)
Tên dữ liệu	Kiểu	Mô tả
id	UUID	ID lớp học
class_code	String	Mã lớp học
class_name	String	Tên lớp học
teacher	Object	Thông tin giảng viên (id, full_name)
semester	Object	Thông tin học kỳ (id, name)
student_count	Integer	Số sinh viên hiện tại trong lớp
created_at	Timestamp	Thời điểm tạo lớp học

7. Quy tắc nghiệp vụ (Business Rules)
•	class_code phải là duy nhất trong toàn hệ thống
•	max_students phải lớn hơn 0 và không được nhỏ hơn số sinh viên hiện có
•	Một sinh viên không thể được thêm vào cùng một lớp hai lần (ràng buộc PRIMARY KEY trong class_students)
•	Không thể xóa lớp học nếu đã có lịch tiết học (schedules) được tạo

8. Các bảng dữ liệu liên quan
Bảng: classes, class_students, teachers, semesters, students, schedules

9. API liên quan
Method	Endpoint	Chức năng
GET	/classes	Lấy danh sách lớp học (lọc theo semester, teacher)
GET	/classes/:id	Lấy thông tin chi tiết lớp học
POST	/classes	Tạo mới lớp học
PUT	/classes/:id	Cập nhật thông tin lớp học
DELETE	/classes/:id	Xóa lớp học
GET	/classes/:id/students	Lấy danh sách sinh viên trong lớp
POST	/classes/:id/students	Thêm sinh viên vào lớp
DELETE	/classes/:id/students/:studentId	Xóa sinh viên khỏi lớp

10. Luồng xử lý chính
Luồng tạo lớp học:
1.	Admin gửi POST /classes kèm thông tin lớp học
2.	NestJS xác thực token, kiểm tra quyền Admin
3.	Hệ thống kiểm tra class_code chưa tồn tại, teacher_id và semester_id hợp lệ
4.	Tạo bản ghi mới trong bảng classes, trả về HTTP 201 Created
Luồng thêm sinh viên vào lớp:
5.	Admin gửi POST /classes/:id/students kèm danh sách student_ids
6.	Hệ thống kiểm tra số lượng sinh viên không vượt max_students
7.	Thêm các bản ghi vào bảng class_students
8.	Trả về danh sách sinh viên cập nhật kèm HTTP 200 OK

11. Giao diện liên quan (UI Screens)
•	Màn hình danh sách lớp học (lọc theo học kỳ, giảng viên)
•	Màn hình tạo mới / chỉnh sửa lớp học
•	Màn hình quản lý sinh viên trong lớp



3.4. UC-04: Quản lý thời khóa biểu

1. Mô tả
Use Case này cho phép Quản trị viên lập lịch tiết học (schedules) cho các lớp học. Mỗi tiết học xác định ngày dạy, giờ bắt đầu, giờ kết thúc và phòng học. Khi tiết học bắt đầu, hệ thống tự động kích hoạt camera gắn với phòng học để phân tích AI theo thời gian thực (5 frame/giây gửi lên FastAPI). Kết quả phân tích được lưu vào bảng schedule_reports sau khi tiết học kết thúc.

2. Tác nhân (Actors)
•	Quản trị viên (Admin)
•	Hệ thống (tự động kích hoạt camera và ghi nhận kết quả AI)

3. Tiền điều kiện (Preconditions)
•	Admin đã đăng nhập và có JWT token hợp lệ
•	Lớp học (classes), phòng học (rooms) đã tồn tại trong hệ thống
•	Camera đã được gắn với phòng học tương ứng trong bảng cameras
•	Không có tiết học khác trong cùng phòng tại cùng thời điểm (tránh trùng lịch)

4. Hậu điều kiện (Postconditions)
Khi thành công:
•	Bản ghi tiết học được tạo trong bảng schedules với status = 'UPCOMING'
•	Khi tiết học bắt đầu: status chuyển thành 'ONGOING', camera được kích hoạt
•	Khi tiết học kết thúc: status chuyển thành 'COMPLETED', kết quả AI lưu vào schedule_reports
Khi thất bại:
•	Trùng lịch phòng học: hệ thống trả về lỗi 409 Conflict
•	Camera không hoạt động: hệ thống ghi log cảnh báo, tiết học vẫn được ghi nhận

5. Dữ liệu đầu vào (Inputs)
Tên dữ liệu	Kiểu	Mô tả
class_id	UUID	ID lớp học cần lập lịch (bắt buộc)
room_id	UUID	ID phòng học tổ chức tiết dạy (bắt buộc)
session_date	Date	Ngày diễn ra tiết học (YYYY-MM-DD)
start_time	Time	Giờ bắt đầu tiết học (HH:MM)
end_time	Time	Giờ kết thúc tiết học (HH:MM)

6. Dữ liệu đầu ra (Outputs)
Tên dữ liệu	Kiểu	Mô tả
id	UUID	ID tiết học
class_id	UUID	ID lớp học
room_id	UUID	ID phòng học
session_date	Date	Ngày dạy
start_time	Time	Giờ bắt đầu
end_time	Time	Giờ kết thúc
status	String	Trạng thái: UPCOMING / ONGOING / COMPLETED
created_at	Timestamp	Thời điểm tạo lịch

7. Quy tắc nghiệp vụ (Business Rules)
•	end_time phải sau start_time ít nhất 30 phút
•	Một phòng học không thể có hai tiết học chồng chéo về thời gian
•	Chỉ được chỉnh sửa/xóa tiết học khi status = 'UPCOMING'
•	Khi status = 'ONGOING', hệ thống tự động bắt đầu gửi frame từ camera lên FastAPI
•	Kết quả AI từ FastAPI (interaction_score, movement_score,...) được tổng hợp và lưu vào schedule_reports khi status chuyển sang 'COMPLETED'

8. Các bảng dữ liệu liên quan
Bảng: schedules, schedule_reports, classes, rooms, cameras

9. API liên quan
Method	Endpoint	Chức năng
GET	/schedules	Lấy danh sách lịch tiết học (lọc theo class, room, ngày)
GET	/schedules/:id	Lấy thông tin chi tiết một tiết học
POST	/schedules	Tạo mới tiết học
PUT	/schedules/:id	Cập nhật tiết học (chỉ khi UPCOMING)
DELETE	/schedules/:id	Xóa tiết học (chỉ khi UPCOMING)
PATCH	/schedules/:id/status	Cập nhật trạng thái tiết học
GET	/schedules/:id/report	Lấy báo cáo AI của tiết học

10. Luồng xử lý chính
Luồng tạo tiết học:
1.	Admin gửi POST /schedules kèm thông tin lịch
2.	NestJS kiểm tra trùng lịch phòng học, xác thực class_id và room_id
3.	Tạo bản ghi trong bảng schedules với status = 'UPCOMING'
4.	Trả về thông tin tiết học kèm HTTP 201 Created
Luồng xử lý AI tự động khi tiết học bắt đầu:
5.	Hệ thống (Scheduler/Cron job) phát hiện tiết học đến giờ bắt đầu
6.	Cập nhật status = 'ONGOING', kích hoạt camera qua stream_url trong bảng cameras
7.	Camera Client gửi 5 frame/giây lên endpoint FastAPI để phân tích
8.	FastAPI trả về kết quả phân tích: interaction_score, movement_score, student_count theo từng frame
9.	NestJS nhận và tổng hợp kết quả theo thời gian thực
10.	Khi hết giờ (end_time), cập nhật status = 'COMPLETED', tính điểm tổng, lưu vào schedule_reports

11. Giao diện liên quan (UI Screens)
•	Màn hình thời khóa biểu (dạng calendar/tuần)
•	Màn hình tạo mới / chỉnh sửa tiết học
•	Màn hình giám sát tiết dạy đang diễn ra (live)


3.5. UC-05: Xem lịch giảng dạy (Giảng viên)

1. Mô tả
Use Case này cho phép Giảng viên tra cứu lịch giảng dạy của bản thân theo ngày, tuần hoặc tháng. Giảng viên có thể xem thông tin chi tiết của từng tiết học bao gồm: lớp học, phòng học, thời gian và trạng thái tiết dạy. Ngoài ra, giảng viên có thể xem nhanh điểm tóm tắt kết quả AI của các tiết đã hoàn thành trực tiếp từ giao diện lịch.

2. Tác nhân (Actors)
•	Giảng viên (Teacher)

3. Tiền điều kiện (Preconditions)
•	Giảng viên đã đăng nhập và có JWT token hợp lệ với role = TEACHER
•	Giảng viên đã được gán vào ít nhất một lớp học (classes.teacher_id)
•	Đã có lịch tiết học (schedules) được tạo bởi Admin cho lớp của giảng viên

4. Hậu điều kiện (Postconditions)
Khi thành công:
•	Giảng viên thấy danh sách tiết học theo bộ lọc thời gian đã chọn
•	Với tiết đã hoàn thành (COMPLETED), giảng viên thấy được điểm tổng hợp từ schedule_reports
Khi thất bại:
•	Không có lịch: hệ thống hiển thị thông báo 'Chưa có tiết học nào trong khoảng thời gian này'
•	Token hết hạn: hệ thống trả về lỗi 401 Unauthorized, yêu cầu đăng nhập lại

5. Dữ liệu đầu vào (Inputs)
Tên dữ liệu	Kiểu	Mô tả
from_date	Date	Ngày bắt đầu khoảng tra cứu (mặc định: đầu tuần hiện tại)
to_date	Date	Ngày kết thúc khoảng tra cứu
view_mode	String	Chế độ xem: day / week / month

6. Dữ liệu đầu ra (Outputs)
Tên dữ liệu	Kiểu	Mô tả
schedule_id	UUID	ID tiết học
class_name	String	Tên lớp học
room_code	String	Mã phòng học
session_date	Date	Ngày dạy
start_time	Time	Giờ bắt đầu
end_time	Time	Giờ kết thúc
status	String	Trạng thái tiết học
total_score	Float	Điểm tổng hợp AI (nếu COMPLETED)

7. Quy tắc nghiệp vụ (Business Rules)
•	Giảng viên chỉ được xem lịch của chính mình (lọc theo teacher_id từ JWT token)
•	Không được xem lịch của giảng viên khác dù biết teacher_id
•	Điểm tổng hợp (total_score) chỉ hiển thị khi trạng thái tiết học là COMPLETED
•	Khoảng thời gian tra cứu tối đa là 3 tháng để đảm bảo hiệu năng

8. Các bảng dữ liệu liên quan
Bảng: schedules, classes, rooms, teachers, schedule_reports

9. API liên quan
Method	Endpoint	Chức năng
GET	/schedules/my	Lấy lịch giảng dạy của giảng viên hiện tại
GET	/schedules/:id	Xem chi tiết một tiết học cụ thể
GET	/schedules/:id/report	Xem kết quả AI tóm tắt của tiết học đã hoàn thành

10. Luồng xử lý chính
Luồng xem lịch giảng dạy:
1.	Giảng viên chọn khoảng thời gian hoặc chế độ xem (ngày/tuần/tháng)
2.	Frontend gửi GET /schedules/my?from_date=...&to_date=... kèm JWT token
3.	NestJS giải mã token, lấy teacher_id tương ứng với user hiện tại
4.	Hệ thống truy vấn bảng schedules JOIN classes WHERE teacher_id = [id từ token]
5.	Với mỗi tiết học có status = 'COMPLETED', JOIN thêm schedule_reports để lấy total_score
6.	Trả về danh sách tiết học đã sắp xếp theo thời gian kèm HTTP 200 OK
7.	Frontend hiển thị dưới dạng calendar với màu sắc phân biệt trạng thái

11. Giao diện liên quan (UI Screens)
•	Màn hình lịch giảng dạy của giảng viên (calendar view theo ngày/tuần/tháng)
•	Popup chi tiết tiết học khi click vào từng ô lịch
•	Màn hình xem nhanh kết quả AI tiết học đã hoàn thành
3.8. UC-08: Xem thống kê cho Quản trị viên

1. Mô tả
Use Case này cho phép Quản trị viên xem thống kê tổng quan về chất lượng giảng dạy trên toàn hệ thống. Admin có thể lọc dữ liệu theo nhiều chiều: khoảng thời gian (ngày / tuần / tháng / học kỳ / năm học) và theo từng giảng viên cụ thể. Dữ liệu thống kê được tổng hợp từ bảng schedule_reports và hiển thị dưới dạng biểu đồ, bảng xếp hạng và chỉ số tổng hợp, hỗ trợ ban quản lý ra quyết định cải thiện chất lượng giáo dục.

2. Tác nhân (Actors)
•	Quản trị viên (Admin)

3. Tiền điều kiện (Preconditions)
•	Admin đã đăng nhập và có JWT token hợp lệ với role = ADMIN
•	Đã có dữ liệu phân tích AI được lưu trong bảng schedule_reports (các tiết học có status = COMPLETED)

4. Hậu điều kiện (Postconditions)
Khi thành công:
•	Hệ thống trả về dữ liệu thống kê đã được tổng hợp theo bộ lọc Admin chỉ định
•	Biểu đồ và bảng số liệu hiển thị đúng khoảng thời gian và đối tượng được lọc
Khi thất bại:
•	Không có dữ liệu trong khoảng thời gian được chọn: hệ thống trả về thông báo 'Chưa có dữ liệu'
•	Tham số lọc không hợp lệ: hệ thống trả về lỗi 400 Bad Request

5. Dữ liệu đầu vào (Inputs)
Tên dữ liệu	Kiểu	Mô tả
from_date	Date	Ngày bắt đầu khoảng thống kê
to_date	Date	Ngày kết thúc khoảng thống kê
period	String	Đơn vị tổng hợp: day / week / month / semester / year
teacher_id	UUID	Lọc theo giảng viên cụ thể (tùy chọn)
semester_id	UUID	Lọc theo học kỳ cụ thể (tùy chọn)

6. Dữ liệu đầu ra (Outputs)
Tên dữ liệu	Kiểu	Mô tả
total_lessons	Integer	Tổng số tiết học đã hoàn thành trong khoảng lọc
avg_interaction_score	Float	Điểm tương tác trung bình
avg_movement_score	Float	Điểm chuyển động trung bình
avg_total_score	Float	Điểm tổng hợp trung bình
top_teachers	Array	Danh sách giảng viên có điểm cao nhất
trend_data	Array	Dữ liệu xu hướng theo đơn vị thời gian (dùng vẽ biểu đồ)
class_rankings	Array	Xếp hạng lớp học theo điểm tổng hợp

7. Quy tắc nghiệp vụ (Business Rules)
•	Chỉ tổng hợp dữ liệu từ các tiết học có status = COMPLETED và đã có bản ghi trong schedule_reports
•	Nếu không chọn teacher_id, hệ thống tổng hợp dữ liệu của toàn bộ giảng viên
•	Khoảng thời gian tối đa cho phép lọc là 1 năm học để đảm bảo hiệu năng truy vấn
•	Điểm trung bình được tính theo số tiết học có dữ liệu, không bao gồm tiết không có kết quả AI
•	Dữ liệu thống kê chỉ Admin mới được truy cập; Giảng viên không có quyền xem thống kê toàn hệ thống

8. Các bảng dữ liệu liên quan
Bảng: schedule_reports, schedules, classes, teachers, semesters

9. API liên quan
Method	Endpoint	Chức năng
GET	/statistics/admin/overview	Lấy tổng quan thống kê toàn hệ thống
GET	/statistics/admin/by-teacher	Thống kê theo từng giảng viên
GET	/statistics/admin/by-class	Thống kê theo từng lớp học
GET	/statistics/admin/trend	Dữ liệu xu hướng theo thời gian (vẽ biểu đồ)
GET	/statistics/admin/top-teachers	Danh sách giảng viên xếp hạng cao nhất

10. Luồng xử lý chính
Luồng xem thống kê tổng quan:
1.	Admin chọn bộ lọc thời gian và giảng viên (nếu cần), nhấn 'Xem thống kê'
2.	Frontend gửi GET /statistics/admin/overview kèm query params và JWT token
3.	NestJS xác thực token, kiểm tra role = ADMIN
4.	Hệ thống truy vấn schedule_reports JOIN schedules JOIN classes, áp dụng điều kiện lọc
5.	Tính toán các chỉ số tổng hợp: avg_interaction_score, avg_movement_score, avg_total_score
6.	Tổng hợp trend_data theo đơn vị thời gian (period) để vẽ biểu đồ đường
7.	Trả về dữ liệu JSON kèm HTTP 200 OK
8.	Frontend render biểu đồ và bảng xếp hạng từ dữ liệu nhận được

11. Giao diện liên quan (UI Screens)
•	Màn hình dashboard thống kê tổng quan (biểu đồ xu hướng, KPI cards)
•	Màn hình xếp hạng giảng viên theo điểm tổng hợp
•	Màn hình thống kê chi tiết theo lớp học / học kỳ


3.9. UC-09: Xem thống kê cho Giảng viên

1. Mô tả
Use Case này cho phép Giảng viên xem thống kê về các chỉ số AI của các tiết học mà mình đã giảng dạy. Giảng viên có thể xem điểm trung bình tương tác, chuyển động và điểm tổng hợp theo từng lớp học hoặc theo khoảng thời gian, giúp họ tự đánh giá và cải thiện chất lượng giảng dạy của bản thân.

2. Tác nhân (Actors)
•	Giảng viên (Teacher)

3. Tiền điều kiện (Preconditions)
•	Giảng viên đã đăng nhập và có JWT token hợp lệ với role = TEACHER
•	Giảng viên đã có ít nhất một tiết học ở trạng thái COMPLETED với dữ liệu trong schedule_reports

4. Hậu điều kiện (Postconditions)
Khi thành công:
•	Giảng viên thấy được các chỉ số AI tổng hợp theo các tiết học của mình
•	Dữ liệu được lọc đúng theo teacher_id lấy từ JWT token, không lộ dữ liệu của người khác
Khi thất bại:
•	Chưa có tiết nào hoàn thành: hệ thống thông báo 'Chưa có dữ liệu thống kê'
•	Token hết hạn: hệ thống trả về lỗi 401 Unauthorized

5. Dữ liệu đầu vào (Inputs)
Tên dữ liệu	Kiểu	Mô tả
from_date	Date	Ngày bắt đầu khoảng thống kê (tùy chọn)
to_date	Date	Ngày kết thúc khoảng thống kê (tùy chọn)
class_id	UUID	Lọc theo lớp học cụ thể (tùy chọn)
semester_id	UUID	Lọc theo học kỳ (tùy chọn)

6. Dữ liệu đầu ra (Outputs)
Tên dữ liệu	Kiểu	Mô tả
total_lessons	Integer	Tổng số tiết đã hoàn thành
avg_interaction_score	Float	Điểm tương tác trung bình
avg_interaction_quality_score	Float	Điểm chất lượng tương tác trung bình
avg_movement_score	Float	Điểm chuyển động trung bình
avg_total_score	Float	Điểm tổng hợp trung bình
lesson_details	Array	Danh sách chi tiết điểm từng tiết học
trend_data	Array	Xu hướng điểm theo thời gian (dùng vẽ biểu đồ)

7. Quy tắc nghiệp vụ (Business Rules)
•	Giảng viên chỉ được xem thống kê của chính mình; teacher_id được lấy từ JWT token, không nhận từ request
•	Chỉ tính trung bình từ các tiết học có status = COMPLETED và có bản ghi schedule_reports
•	Nếu không truyền from_date/to_date, hệ thống mặc định lấy dữ liệu của học kỳ hiện tại
•	Khoảng thời gian lọc tối đa là 1 năm

8. Các bảng dữ liệu liên quan
Bảng: schedule_reports, schedules, classes, teachers, semesters

9. API liên quan
Method	Endpoint	Chức năng
GET	/statistics/teacher/overview	Tổng quan thống kê của giảng viên hiện tại
GET	/statistics/teacher/by-class	Thống kê theo từng lớp học của giảng viên
GET	/statistics/teacher/trend	Xu hướng điểm theo thời gian của giảng viên
GET	/statistics/teacher/lessons	Danh sách chi tiết điểm từng tiết học

10. Luồng xử lý chính
1.	Giảng viên truy cập trang thống kê, chọn bộ lọc (học kỳ, lớp học, khoảng thời gian)
2.	Frontend gửi GET /statistics/teacher/overview kèm query params và JWT token
3.	NestJS xác thực token, lấy teacher_id từ payload JWT
4.	Hệ thống truy vấn schedule_reports JOIN schedules JOIN classes WHERE teacher_id = [từ token]
5.	Tính toán avg_interaction_score, avg_movement_score, avg_total_score
6.	Xây dựng trend_data theo từng tiết học để hiển thị biểu đồ đường
7.	Trả về dữ liệu kèm HTTP 200 OK
8.	Frontend hiển thị KPI cards và biểu đồ xu hướng

11. Giao diện liên quan (UI Screens)
•	Màn hình thống kê cá nhân của giảng viên (KPI cards, biểu đồ đường)
•	Màn hình chi tiết thống kê theo từng lớp học
•	Màn hình danh sách điểm từng tiết học (bảng, có thể sắp xếp)

3.10. UC-10: Xuất báo cáo

1. Mô tả
Use Case này cho phép Quản trị viên xuất báo cáo tổng hợp về chất lượng giảng dạy dưới dạng file PDF hoặc Excel. Báo cáo có thể được tạo theo nhiều phạm vi khác nhau: toàn trường, theo học kỳ, theo giảng viên hoặc theo lớp học cụ thể. Dữ liệu xuất ra bao gồm các chỉ số AI tổng hợp, bảng xếp hạng và biểu đồ minh họa, phục vụ mục đích lưu trữ và báo cáo lên ban quản lý.

2. Tác nhân (Actors)
•	Quản trị viên (Admin)

3. Tiền điều kiện (Preconditions)
•	Admin đã đăng nhập và có JWT token hợp lệ với role = ADMIN
•	Đã có dữ liệu thống kê hợp lệ (schedule_reports) trong khoảng thời gian được chọn

4. Hậu điều kiện (Postconditions)
Khi thành công:
•	File báo cáo (PDF hoặc Excel) được tạo và trả về để tải xuống
•	Tên file bao gồm thông tin phạm vi và thời gian báo cáo (ví dụ: BaoCao_HK1_2024.pdf)
Khi thất bại:
•	Không có dữ liệu: hệ thống trả về lỗi 404 kèm thông báo
•	Định dạng không hỗ trợ: hệ thống trả về lỗi 400 Bad Request

5. Dữ liệu đầu vào (Inputs)
Tên dữ liệu	Kiểu	Mô tả
report_type	String	Loại báo cáo: overview / by_teacher / by_class / by_semester
format	String	Định dạng xuất: pdf / excel
from_date	Date	Ngày bắt đầu khoảng báo cáo
to_date	Date	Ngày kết thúc khoảng báo cáo
teacher_id	UUID	Lọc theo giảng viên (tùy chọn)
semester_id	UUID	Lọc theo học kỳ (tùy chọn)
class_id	UUID	Lọc theo lớp học (tùy chọn)

6. Dữ liệu đầu ra (Outputs)
Tên dữ liệu	Kiểu	Mô tả
file	Binary	File PDF hoặc Excel chứa nội dung báo cáo
filename	String	Tên file gợi ý khi tải xuống
content_type	String	MIME type: application/pdf hoặc application/vnd.openxmlformats-officedocument.spreadsheetml.sheet

7. Quy tắc nghiệp vụ (Business Rules)
•	Chỉ Admin mới có quyền xuất báo cáo
•	Báo cáo chỉ bao gồm dữ liệu từ các tiết học có status = COMPLETED
•	Khoảng thời gian báo cáo tối đa là 1 năm học
•	File PDF được tạo phía server (sử dụng thư viện render); file Excel chứa nhiều sheet tương ứng các nhóm dữ liệu
•	Tên file tự động gắn kèm timestamp để tránh trùng lặp

8. Các bảng dữ liệu liên quan
Bảng: schedule_reports, schedules, classes, teachers, semesters, rooms

9. API liên quan
Method	Endpoint	Chức năng
GET	/reports/export	Xuất báo cáo theo bộ lọc (trả về file stream)
GET	/reports/preview	Xem trước dữ liệu báo cáo trước khi xuất
GET	/reports/history	Xem lịch sử các báo cáo đã xuất

10. Luồng xử lý chính
1.	Admin cấu hình báo cáo: chọn loại, định dạng, khoảng thời gian và các bộ lọc
2.	Frontend gửi GET /reports/export?format=pdf&report_type=overview&... kèm JWT token
3.	NestJS xác thực token, kiểm tra role = ADMIN
4.	Hệ thống truy vấn và tổng hợp dữ liệu từ schedule_reports theo các điều kiện lọc
5.	Nếu format = pdf: render HTML template với dữ liệu, chuyển đổi sang PDF
6.	Nếu format = excel: tạo workbook với nhiều sheet (tổng quan, chi tiết, xếp hạng)
7.	Trả về file dưới dạng stream với header Content-Disposition: attachment
8.	Trình duyệt tự động tải file về máy người dùng

11. Giao diện liên quan (UI Screens)
•	Màn hình cấu hình và xuất báo cáo (form chọn loại, định dạng, bộ lọc)
•	Màn hình xem trước dữ liệu báo cáo
•	Nút 'Tải xuống PDF' / 'Tải xuống Excel' trên màn hình thống kê

3.11. UC-11: Quản lý camera

1. Mô tả
Use Case này cho phép Quản trị viên quản lý danh sách camera được lắp đặt trong các phòng học. Admin có thể thêm, cập nhật, xóa thông tin camera và theo dõi trạng thái kết nối theo thời gian thực. Mỗi camera được gắn với một phòng học cụ thể và cung cấp stream_url để hệ thống kích hoạt luồng video khi tiết học bắt đầu. Màn hình quản lý cũng hiển thị trạng thái hoạt động của từng camera (online / offline / error).

2. Tác nhân (Actors)
•	Quản trị viên (Admin)

3. Tiền điều kiện (Preconditions)
•	Admin đã đăng nhập và có JWT token hợp lệ với role = ADMIN
•	Phòng học (rooms) đã tồn tại trong hệ thống trước khi gắn camera

4. Hậu điều kiện (Postconditions)
Khi thành công:
•	Thông tin camera được tạo mới / cập nhật / xóa trong bảng cameras
•	Trạng thái camera được làm mới khi Admin yêu cầu kiểm tra kết nối
Khi thất bại:
•	stream_url không hợp lệ hoặc không kết nối được: hệ thống trả về cảnh báo
•	Phòng học chưa tồn tại: hệ thống trả về lỗi 404 Not Found
•	Một phòng đã có camera: hệ thống cảnh báo trùng và cho phép Admin xác nhận thêm camera thứ hai

5. Dữ liệu đầu vào (Inputs)
Tên dữ liệu	Kiểu	Mô tả
room_id	UUID	ID phòng học cần gắn camera (bắt buộc)
camera_name	String	Tên định danh camera (bắt buộc)
stream_url	Text	URL luồng video RTSP/HTTP của camera (bắt buộc)

6. Dữ liệu đầu ra (Outputs)
Tên dữ liệu	Kiểu	Mô tả
id	UUID	ID camera
camera_name	String	Tên camera
room_id	UUID	ID phòng học liên kết
room_code	String	Mã phòng học (join từ bảng rooms)
stream_url	Text	URL luồng video
status	String	Trạng thái kết nối: ONLINE / OFFLINE / ERROR
last_checked_at	Timestamp	Thời điểm kiểm tra trạng thái gần nhất

7. Quy tắc nghiệp vụ (Business Rules)
•	stream_url phải là URL hợp lệ theo định dạng RTSP hoặc HTTP(S)
•	Không thể xóa camera nếu đang có tiết học với status = ONGOING sử dụng phòng học đó
•	Khi tiết học chuyển sang ONGOING, hệ thống tự động dùng stream_url của camera trong phòng để gửi frame lên FastAPI
•	Nếu camera offline trong lúc tiết học đang diễn ra, hệ thống ghi log cảnh báo nhưng tiết học vẫn tiếp tục được ghi nhận
•	Admin có thể chủ động ping kiểm tra trạng thái camera bất kỳ lúc nào

8. Các bảng dữ liệu liên quan
Bảng: cameras, rooms, schedules

9. API liên quan
Method	Endpoint	Chức năng
GET	/cameras	Lấy danh sách camera (lọc theo room, trạng thái)
GET	/cameras/:id	Lấy thông tin chi tiết một camera
POST	/cameras	Thêm mới camera vào hệ thống
PUT	/cameras/:id	Cập nhật thông tin camera
DELETE	/cameras/:id	Xóa camera khỏi hệ thống
GET	/cameras/:id/status	Kiểm tra và làm mới trạng thái kết nối camera
GET	/cameras/room/:roomId	Lấy danh sách camera theo phòng học

10. Luồng xử lý chính
Luồng thêm mới camera:
1.	Admin gửi POST /cameras kèm room_id, camera_name và stream_url
2.	NestJS xác thực token, kiểm tra role = ADMIN
3.	Hệ thống kiểm tra room_id tồn tại trong bảng rooms
4.	Hệ thống thử kết nối tới stream_url để xác nhận tính hợp lệ
5.	Tạo bản ghi mới trong bảng cameras, trả về HTTP 201 Created kèm thông tin camera
Luồng kiểm tra trạng thái camera:
6.	Admin nhấn 'Kiểm tra kết nối' trên giao diện quản lý camera
7.	Frontend gửi GET /cameras/:id/status kèm JWT token
8.	NestJS thực hiện ping / kết nối thử tới stream_url của camera
9.	Cập nhật trường status và last_checked_at trong bảng cameras
10.	Trả về trạng thái hiện tại: ONLINE / OFFLINE / ERROR

11. Giao diện liên quan (UI Screens)
•	Màn hình danh sách camera (hiển thị trạng thái real-time, lọc theo phòng)
•	Màn hình thêm mới / chỉnh sửa camera (form nhập stream_url, gắn phòng)
•	Badge trạng thái (ONLINE / OFFLINE / ERROR) hiển thị trực tiếp trên danh sách
 
Phân tích chi tiết API 
Tài liệu phân tích chi tiết API

Mục lục











Phần I: Danh sách API

STT	Method	Endpoint	Chức năng
1	POST	/auth/login	Đăng nhập hệ thống
2	POST	/auth/logout	Đăng xuất
3	GET	/auth/me	Lấy thông tin người dùng hiện tại
4	POST	/auth/refresh_token	Cấp lại access_token
5	GET	/teachers	Lấy danh sách giảng viên (có phân trang, lọc)
6	GET	/teachers/:id	Lấy thông tin chi tiết một giảng viên
7	POST	/teachers	Tạo mới hồ sơ giảng viên
8	PUT	/teachers/:id	Cập nhật toàn bộ thông tin giảng viên
9	PATCH	/teachers/:id	Cập nhật một phần thông tin giảng viên
10	DELETE	/teachers/:id	Xóa giảng viên khỏi hệ thống
11	GET	/classes	Lấy danh sách lớp học (lọc theo semester, teacher)
12	GET	/classes/:id	Lấy thông tin chi tiết lớp học
13	POST	/classes	Tạo mới lớp học
14	PUT	/classes/:id	Cập nhật thông tin lớp học
15	DELETE	/classes/:id	Xóa lớp học
16	GET	/classes/:id/students	Lấy danh sách sinh viên trong lớp
17	POST	/classes/:id/students	Thêm sinh viên vào lớp
18	DELETE	/classes/:id/students/:studentId	Xóa sinh viên khỏi lớp
	GET	/schedules	Lấy danh sách lịch tiết học (lọc theo class, room, ngày)
	GET	/schedules/:id	Lấy thông tin chi tiết một tiết học
	POST	/schedules	Tạo mới tiết học
	PUT	/schedules/:id	Cập nhật tiết học (chỉ khi UPCOMING)
	DELETE	/schedules/:id	Xóa tiết học (chỉ khi UPCOMING)
	PATCH	/schedules/:id/status	Cập nhật trạng thái tiết học
	GET	/schedules/:id/report	Lấy báo cáo AI của tiết học
	GET	/schedules/my	Lấy lịch giảng dạy của giảng viên hiện tại
	GET	/schedules/:id	Xem chi tiết một tiết học cụ thể
	GET	/schedules/:id/report	Xem kết quả AI tóm tắt của tiết học đã hoàn thành
	GET	/statistics/admin/overview	Lấy tổng quan thống kê toàn hệ thống
	GET	/statistics/admin/by-teacher	Thống kê theo từng giảng viên
	GET	/statistics/admin/by-class	Thống kê theo từng lớp học
	GET	/statistics/admin/trend	Dữ liệu xu hướng theo thời gian (vẽ biểu đồ)
	GET	/statistics/admin/top-teachers	Danh sách giảng viên xếp hạng cao nhất
	GET	/statistics/teacher/overview	Tổng quan thống kê của giảng viên hiện tại
	GET	/statistics/teacher/by-class	Thống kê theo từng lớp học của giảng viên
	GET	/statistics/teacher/trend	Xu hướng điểm theo thời gian của giảng viên
	GET	/statistics/teacher/lessons	Danh sách chi tiết điểm từng tiết học
	GET	/reports/export	Xuất báo cáo theo bộ lọc (trả về file stream)
	GET	/reports/preview	Xem trước dữ liệu báo cáo trước khi xuất
	GET	/reports/history	Xem lịch sử các báo cáo đã xuất
	GET	/cameras	Lấy danh sách camera (lọc theo room, trạng thái)
	GET	/cameras/:id	Lấy thông tin chi tiết một camera
	POST	/cameras	Thêm mới camera vào hệ thống
	PUT	/cameras/:id	Cập nhật thông tin camera
	DELETE	/cameras/:id	Xóa camera khỏi hệ thống
	GET	/cameras/:id/status	Kiểm tra và làm mới trạng thái kết nối camera
	GET	/cameras/room/:roomId	Lấy danh sách camera theo phòng học
Phần II: Phân tích chi tiết
1. POST /auth/login	
a)	 Dữ liệu vào (Input)
Tên dữ liệu	Kiểu	Mô tả
email	String	Email đăng nhập
password 	String 	Mật khẩu đăng nhập 

b)	 Dữ liệu ra (Output)
Tên dữ liệu	Kiểu	Mô tả
access_token	String	JWT token xác thực ngắn hạn (1 giờ)
refresh_token	String	JWT token để làm mới access_token ()
user_id	UUID	ID người dùng
role	String	Danh sách quyền của người dùng 

c)	 Xác thực (Validation)

d)	 Biểu đồ tuần tự (Sequence diagram )
e)	 
2. POST /auth/logout
a)	Dữ liệu vào (Input)
b)	Dữ liệu ra (Output)
c)	Xác thực (Validation)
d)	Biểu đồ tuần tự (Sequence diagram )
3. GET /auth/me
a)	Dữ liệu vào (Input)
b)	Dữ liệu ra (Output)
c)	Xác thực (Validation)
d)	Biểu đồ tuần tự (Sequence diagram )
4. POST /auth/refresh_token
a)	Dữ liệu vào (Input)
b)	Dữ liệu ra (Output)
c)	Xác thực (Validation)
d)	Biểu đồ tuần tự (Sequence diagram )(cái nào đơn giản kiểu CRUD thì không cần làm)
