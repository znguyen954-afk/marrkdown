# Software Requirements Specification (SRS)
# Hệ thống Giám sát Chất lượng Giảng dạy bằng AI

---

## Revision History

| Version | Date | Author | Description |
|---------|------|--------|-------------|
| 1.0 | 2025-05-28 | Team | Initial draft |

---

## 1. Introduction

### 1.1 Purpose

Tài liệu này đặc tả đầy đủ các yêu cầu phần mềm cho hệ thống **Giám sát Chất lượng Giảng dạy bằng AI** (AI-based Teaching Quality Monitoring System). Mục tiêu là cung cấp cơ sở kỹ thuật cho nhóm phát triển (DEV và AI) để thiết kế, xây dựng và kiểm thử hệ thống.

### 1.2 Scope

Hệ thống cung cấp nền tảng giám sát mức độ tương tác giữa giảng viên và sinh viên trong phòng học thông qua camera IP và mô hình AI thời gian thực. Phạm vi bao gồm:

- Quản lý tài khoản, phân quyền (Admin / Giảng viên).
- Quản lý lịch dạy, lớp học, học kỳ.
- Thu thập và xử lý luồng video từ camera.
- Phân tích mức độ tương tác bằng mô hình YOLO + ByteTrack + thuật toán TSII.
- Hiển thị dashboard giám sát thời gian thực và thống kê tổng quan.
- Xuất báo cáo PDF/Excel.

Hệ thống **không** bao gồm: hệ thống điểm danh tự động bằng nhận diện khuôn mặt, quản lý học vụ đầy đủ (LMS), hay tích hợp hệ thống tài chính.

### 1.3 Definitions, Acronyms, Abbreviations

| Thuật ngữ | Định nghĩa |
|-----------|------------|
| TSII | Teacher–Student Interaction Index — chỉ số đo mức độ tương tác GV-SV |
| RBAC | Role-Based Access Control — phân quyền theo vai trò |
| Bounding Box | Hộp giới hạn xác định vị trí đối tượng trong khung hình |
| Homography | Ma trận biến đổi phối cảnh từ tọa độ ảnh sang tọa độ sàn thực tế |
| ByteTrack | Thuật toán đa đối tượng tracking dựa trên ID liên tục |
| Vùng Chưa Phủ | Khu vực trong lớp mà giảng viên chưa tương tác đến trong tiết học |
| Tiết học | Một phiên giảng dạy có xác định lớp, giảng viên, thời gian bắt đầu/kết thúc |
| Admin | Quản trị viên hệ thống cấp trường/khoa |
| GV | Giảng viên |
| SV | Sinh viên |
| JWT | JSON Web Token — cơ chế xác thực stateless |

### 1.4 References

- YOLO v8/v9 Object Detection Documentation
- ByteTrack: Multi-Object Tracking by Associating Every Detection Box (arXiv 2022)
- OpenCV Homography Transformation
- NestJS Documentation — https://docs.nestjs.com
- Next.js Documentation — https://nextjs.org/docs
- FastAPI Documentation — https://fastapi.tiangolo.com

### 1.5 Overview

Tài liệu được tổ chức theo cấu trúc IEEE 830:
- **Mục 2**: Mô tả tổng quan hệ thống, kiến trúc, ràng buộc.
- **Mục 3**: Yêu cầu chức năng chi tiết theo module.
- **Mục 4**: Yêu cầu dữ liệu.
- **Mục 5**: Giao diện ngoài.
- **Mục 6**: Yêu cầu phi chức năng.
- **Mục 10**: Mô hình phân tích và thiết kế.

---

## 2. Overall Description

### 2.1 Product Perspective

Hệ thống là một nền tảng độc lập, tích hợp ba lớp chính:

```
[Camera IP] → [Cụm xử lý AI (FastAPI + YOLO)] → [Backend NestJS] → [Frontend NextJS]
                                                          ↕
                                                    [PostgreSQL]
```

Hệ thống không phụ thuộc vào LMS hiện có của trường, nhưng có thể nhận dữ liệu lịch học từ nguồn ngoài thông qua API.

### 2.2 Product Functions

Các chức năng cốt lõi:

1. Xác thực và phân quyền người dùng (RBAC: Admin / Giảng viên).
2. Quản lý hồ sơ giảng viên, lớp học, học kỳ, tiết học.
3. Kết nối và giám sát trạng thái camera.
4. Phát hiện và tracking đối tượng (GV, SV) bằng YOLO + ByteTrack.
5. Tính toán TSII và xác định Vùng Chưa Phủ theo thời gian thực.
6. Dashboard giám sát real-time và thống kê lịch sử (ngày/tuần/tháng/kỳ).
7. Xuất báo cáo sư phạm (PDF) và bảng chuyên cần (Excel).

### 2.3 User Classes and Characteristics

| Vai trò | Đặc điểm | Quyền chính |
|---------|-----------|-------------|
| **Admin** | Quản trị viên trường/khoa, thành thạo hệ thống | Toàn quyền: quản lý tài khoản, xem dashboard, xuất báo cáo, cấu hình camera |
| **Giảng viên** | Giảng dạy, ít am hiểu kỹ thuật | Xem lịch dạy, xem kết quả tiết học của bản thân, chỉnh sửa hồ sơ |
| **Hệ thống AI** | Module tự động, không phải người dùng cuối | Đẩy kết quả phân tích vào DB qua API nội bộ |

### 2.4 Operating Environment

- **Frontend**: Trình duyệt hiện đại (Chrome, Edge, Firefox) — Next.js SSR.
- **Backend**: Server Linux Ubuntu 22.04+, Node.js ≥ 20, NestJS.
- **AI Engine**: Python 3.10+, FastAPI, PyTorch, OpenCV — chạy trong Docker container.
- **Database**: PostgreSQL 15+.
- **Camera**: Camera IP hỗ trợ RTSP stream, độ phân giải tối thiểu 1080p.
- **Hạ tầng**: Cloud server (VPS/AWS/GCP) hoặc on-premise server có GPU.
- **Nginx**: Reverse proxy, SSL termination, load balancer.

### 2.5 Design and Implementation Constraints

- Module AI phải được đóng gói trong Docker để cách ly môi trường Python/PyTorch.
- Tất cả API nội bộ giữa NestJS và FastAPI phải đi qua internal network, không expose public.
- JWT Access Token TTL ≤ 15 phút; Refresh Token TTL ≤ 7 ngày.
- Dữ liệu video không được lưu trữ dài hạn trên server (chỉ stream, không lưu file thô).
- Hệ thống phải hỗ trợ nhiều camera đồng thời (tối thiểu 10 camera song song).
- Ngôn ngữ giao diện: Tiếng Việt.

### 2.6 Assumptions and Dependencies

- Mỗi phòng học có tối thiểu 1 camera IP được định vị cố định, bao phủ toàn bộ diện tích lớp.
- Lịch học (tiết học, phòng, giảng viên) được nhập vào hệ thống trước khi tiết bắt đầu.
- Hệ thống giả định mỗi khung hình camera chỉ chứa 1 giảng viên.
- Dataset huấn luyện mô hình hiện có ~8.000 ảnh, mục tiêu 12.000 ảnh.
- Server có GPU (NVIDIA CUDA) để đảm bảo inference time ≤ 100ms/frame.

### 2.7 System Architecture Overview

Kiến trúc hệ thống bao gồm hai cụm độc lập giao tiếp qua PostgreSQL và REST API nội bộ:

**Cụm Web:**
- **Next.js** (Frontend) ↔ **Load Balancer (Nginx)** ↔ **NestJS × N instances** ↔ **PostgreSQL**

**Cụm Xử lý Ảnh:**
- **Camera (RTSP)** → **FastAPI + Mô hình AI × N instances**
  - FastAPI tự hỏi danh sách camera cần kết nối từ NestJS khi khởi động.
  - Kết quả phân tích được trả về cho NestJS để lưu DB.

**Luồng dữ liệu chính:**
```
Camera RTSP → FastAPI (YOLO + ByteTrack + TSII) → POST JSON → NestJS → PostgreSQL
                                                                    ↓
                                              Next.js ← WebSocket/SSE ← NestJS
```

---

## 3. Functional Requirements

### 3.1 Authentication and Authorization

#### 3.1.1 Đăng nhập / Đăng xuất

- **FR-AUTH-01**: Hệ thống cung cấp endpoint `POST /auth/login` nhận `{email, password}`, trả về `{accessToken, refreshToken}` dạng JWT.
- **FR-AUTH-02**: `POST /auth/refresh` nhận Refresh Token hợp lệ, trả về Access Token mới.
- **FR-AUTH-03**: `POST /auth/logout` vô hiệu hóa Refresh Token hiện tại (blacklist hoặc xóa khỏi DB).
- **FR-AUTH-04**: Giao diện đăng nhập phân biệt 2 luồng: Admin và Giảng viên (redirect đến dashboard tương ứng sau khi đăng nhập thành công).

#### 3.1.2 Phân quyền RBAC

- **FR-AUTH-05**: Hệ thống định nghĩa 2 role: `ADMIN` và `TEACHER`.
- **FR-AUTH-06**: Mỗi API endpoint phải khai báo guard kiểm tra role. Các route của Admin không thể truy cập bởi Teacher và ngược lại.
- **FR-AUTH-07**: Schema DB lưu bảng `users`, `roles`, `user_roles` theo chuẩn RBAC.

#### 3.1.3 Quản lý tài khoản

- **FR-AUTH-08**: Admin có thể tạo, sửa, xóa mềm (soft delete) tài khoản Giảng viên.
- **FR-AUTH-09**: Người dùng có thể tự cập nhật thông tin cá nhân: họ tên, số điện thoại, avatar.
- **FR-AUTH-10**: Chức năng đổi mật khẩu yêu cầu nhập mật khẩu cũ để xác thực.
- **FR-AUTH-11**: Hồ sơ Giảng viên hiển thị thống kê điểm đánh giá chất lượng giảng dạy tổng hợp theo từng học kỳ.

---

### 3.2 Academic Schedule Management

#### 3.2.1 Quản lý Năm học & Học kỳ

- **FR-SCH-01**: Admin quản lý (CRUD) Năm học (`academic_years`) và Học kỳ (`semesters`) với các thuộc tính: tên, ngày bắt đầu, ngày kết thúc, trạng thái.
- **FR-SCH-02**: Chỉ có một Học kỳ ở trạng thái `ACTIVE` tại một thời điểm.

#### 3.2.2 Quản lý Lớp học & Phòng học

- **FR-SCH-03**: Admin quản lý (CRUD) Lớp học (`classes`): mã lớp, tên lớp, khoa, sĩ số dự kiến.
- **FR-SCH-04**: Admin quản lý (CRUD) Phòng học (`rooms`): mã phòng, tòa nhà, sức chứa, danh sách camera ID gắn với phòng.

#### 3.2.3 Quản lý Tiết học

- **FR-SCH-05**: Admin gán lịch dạy (`sessions`): Giảng viên + Lớp + Phòng + Thời gian bắt đầu/kết thúc + Học kỳ.
- **FR-SCH-06**: Giảng viên xem lịch dạy cá nhân theo tuần/tháng/học kỳ dưới dạng lịch (calendar view).
- **FR-SCH-07**: Hệ thống AI tự động kích hoạt phân tích camera khi đến thời điểm bắt đầu tiết học và dừng khi kết thúc.

---

### 3.3 Classroom Monitoring

#### 3.3.1 Giám sát trạng thái Camera

- **FR-CAM-01**: Admin xem danh sách tất cả camera, trạng thái kết nối (Online/Offline/Error), và phòng học tương ứng.
- **FR-CAM-02**: Hệ thống kiểm tra heartbeat camera định kỳ mỗi 30 giây và cập nhật trạng thái.
- **FR-CAM-03**: Khi camera mất kết nối trong giờ học, hệ thống phát cảnh báo tới Admin.

#### 3.3.2 Màn hình giám sát real-time

- **FR-MON-01**: Admin xem màn hình giám sát trực tiếp của một tiết học đang diễn ra, hiển thị các chỉ số real-time:
  - Số sinh viên đang có mặt trong phòng.
  - Số sinh viên đã được giảng viên tương tác (số và %).
  - Chỉ số chất lượng tương tác GV-SV (TSII score, hiển thị dạng đồng hồ đo).
  - Mức độ di chuyển của giảng viên trong lớp (%).
  - Mức độ tương tác tổng (biểu đồ đường theo thời gian, cập nhật mỗi phút).
- **FR-MON-02**: Nếu khả thi về hạ tầng, stream video đã được annotate (bounding box) từ module AI hiển thị trực tiếp trên giao diện Admin.

---

### 3.4 AI Detection and Tracking

#### 3.4.1 Khởi tạo và Định vị không gian

- **FR-AI-01**: Khi khởi động phiên phân tích, module AI nhận cấu hình phòng học từ API của NestJS: kích thước phòng (mét), số lượng sinh viên, tọa độ các điểm mốc vật lý.
- **FR-AI-02**: Module AI tính toán ma trận Homography từ tọa độ ảnh sang tọa độ mặt sàn thực tế (đơn vị mét).

#### 3.4.2 Phát hiện và Tracking đối tượng

- **FR-AI-03**: Mô hình YOLO phát hiện (detect) giảng viên và sinh viên trong mỗi khung hình, phân biệt 2 class: `teacher` và `student`.
- **FR-AI-04**: Thuật toán ByteTrack gán ID duy nhất và tracking liên tục từng đối tượng qua các frame. ID không được thay đổi khi đối tượng bị che khuất tạm thời ≤ 2 giây.
- **FR-AI-05**: Luồng output mỗi frame bao gồm: `[{id, class, bbox_pixel, bbox_floor_meter, timestamp}]`.

#### 3.4.3 Thuật toán TSII Engine

- **FR-AI-06**: Hệ thống theo dõi thời gian giảng viên ở vùng lân cận (≤ R mét, R được cấu hình) của từng ID sinh viên liên tục.
- **FR-AI-07**: Áp dụng logic màng lọc nhị phân: nếu GV di chuyển qua SV (thời gian lưu ≤ 10 giây) → áp dụng hàm suy giảm điểm; nếu GV dừng cạnh SV ≥ 30 giây → chặn trần tích điểm.
- **FR-AI-08**: Xác định trạng thái tĩnh/động dựa trên vùng bục giảng: nếu GV đứng trong vùng bục giảng → trạng thái `static`; ra ngoài → `dynamic`.
- **FR-AI-09**: Tự động tính mật độ lớp học để scale ngưỡng tương tác theo sĩ số thực tế.
- **FR-AI-10**: Output TSII Engine gồm:
  - TSII score (0–100) theo từng khoảng thời gian.
  - Danh sách ID sinh viên thuộc Vùng Chưa Phủ.
  - Tỷ lệ thời gian GV di chuyển khỏi vùng bục trong suốt tiết.

---

### 3.5 Interaction Analytics

#### 3.5.1 Lưu trữ kết quả tiết học

- **FR-ANL-01**: Module AI đẩy kết quả phân tích dưới dạng JSON chuẩn về endpoint NestJS `POST /sessions/:id/analysis-result` sau mỗi khoảng thời gian quy định (mỗi 1 phút cho real-time và 1 lần khi kết thúc tiết).
- **FR-ANL-02**: NestJS lưu dữ liệu real-time vào bảng `session_logs` và dữ liệu tổng kết vào bảng `session_summaries`.

#### 3.5.2 Cấu trúc dữ liệu JSON chuẩn từ AI

```json
{
  "session_id": "uuid",
  "timestamp": "ISO8601",
  "realtime": {
    "student_count": 35,
    "students_interacted_count": 18,
    "students_interacted_pct": 51.4,
    "tsii_score": 72.5,
    "teacher_mobility_pct": 68.0,
    "overall_interaction_score": 65.0
  },
  "uncovered_zones": ["student_id_3", "student_id_12"],
  "timeseries": [
    { "t": 0, "score": 45.0 },
    { "t": 60, "score": 58.0 }
  ]
}
```

---

### 3.6 Report and Dashboard

#### 3.6.1 Admin Dashboard — Thống kê tổng quan

- **FR-DASH-01**: Trang chủ Admin hiển thị:
  - Tổng số tiết học đã được phân tích (theo học kỳ hiện tại).
  - Tỷ lệ phân phối chất lượng: Giỏi / Khá / Trung bình / Yếu (dựa trên TSII score trung bình mỗi tiết).
  - Biểu đồ so sánh chất lượng tương tác trung bình giữa các khoa.
  - Danh sách tiết học đang diễn ra thời gian thực.

#### 3.6.2 Màn hình thống kê chi tiết

- **FR-DASH-02**: Thống kê lọc theo ngày / tuần / tháng / học kỳ / năm học, hiển thị:
  - % sinh viên được tương tác trung bình.
  - Điểm chất lượng tương tác GV-SV trung bình.
  - Mức độ di chuyển trung bình trong lớp.
  - Biểu đồ đường mức độ tương tác tổng theo từng giảng viên và chung cả trường.
- **FR-DASH-03**: Giảng viên xem thống kê của bản thân theo cùng các chiều lọc trên.

#### 3.6.3 Xuất báo cáo

- **FR-EXP-01**: Admin xuất **báo cáo sư phạm PDF** cho một tiết học hoặc một khoảng thời gian, bao gồm: thông tin tiết học, biểu đồ TSII, danh sách vùng chưa phủ, nhận xét tổng hợp.
- **FR-EXP-02**: Admin xuất **bảng chuyên cần Excel** liệt kê danh sách sinh viên và số tiết được tương tác trong kỳ.

---

### 3.7 Notification and Alert System

- **FR-NTF-01**: Hệ thống gửi cảnh báo tới Admin khi camera mất kết nối trong giờ học.
- **FR-NTF-02**: Hệ thống gửi thông báo khi TSII score của một tiết học giảm xuống dưới ngưỡng cảnh báo (cấu hình bởi Admin, mặc định = 40).
- **FR-NTF-03**: Thông báo hiển thị dạng in-app notification trên giao diện, và tùy chọn gửi email.

---

## 4. Data Requirements

### 4.1 Logical Data Model

Các entity chính và quan hệ:

```
academic_years (1) ──< semesters (1) ──< sessions (N)
users (GV) ──< sessions
rooms ──< sessions
rooms (1) ──< cameras (N)
sessions (1) ──< session_logs (N)       [real-time per minute]
sessions (1) ──  session_summaries (1)  [end-of-session aggregate]
```

### 4.2 Data Dictionary

| Bảng | Cột chính | Mô tả |
|------|-----------|-------|
| `users` | id, email, password_hash, role, full_name, avatar_url, created_at | Tài khoản người dùng |
| `academic_years` | id, name, start_date, end_date | Năm học |
| `semesters` | id, academic_year_id, name, start_date, end_date, status | Học kỳ |
| `classes` | id, code, name, department, expected_size | Lớp học |
| `rooms` | id, code, building, capacity | Phòng học |
| `cameras` | id, room_id, rtsp_url, status, last_ping_at | Camera IP |
| `sessions` | id, class_id, room_id, teacher_id, semester_id, start_time, end_time, status | Tiết học |
| `session_logs` | id, session_id, timestamp, payload JSONB | Log real-time (1/phút) |
| `session_summaries` | id, session_id, student_count, interacted_pct, tsii_avg, mobility_pct, overall_score | Tổng kết tiết |

### 4.3 Data Retention

- `session_logs` (real-time): lưu tối đa 1 năm, sau đó archive hoặc xóa.
- `session_summaries`: lưu vĩnh viễn (dùng cho thống kê dài hạn).
- Video stream: không lưu file thô. Ảnh annotate chỉ giữ trong bộ nhớ tạm trong phiên xử lý.

### 4.4 Realtime Data Processing

- Module AI đẩy kết quả mỗi **60 giây** qua `POST /sessions/:id/analysis-result`.
- NestJS nhận payload, validate schema, ghi vào `session_logs`, đồng thời broadcast qua **WebSocket/Server-Sent Events** tới frontend đang giám sát tiết đó.
- Frontend subscribe event theo `session_id`, cập nhật biểu đồ không cần reload trang.

### 4.5 Backup and Recovery

- PostgreSQL: backup tự động hàng ngày (pg_dump), lưu trữ 30 ngày rolling.
- Recovery Time Objective (RTO): ≤ 4 giờ.
- Recovery Point Objective (RPO): ≤ 24 giờ.

---

## 5. External Interface Requirements

### 5.1 User Interfaces

- **UI-01**: Giao diện web responsive (desktop-first), hỗ trợ màn hình ≥ 1280px.
- **UI-02**: Màn hình đăng nhập riêng cho Admin và Giảng viên.
- **UI-03**: Admin Dashboard: sidebar navigation, biểu đồ dạng gauge (kim đồng hồ) cho TSII score, biểu đồ đường (line chart) cho mức độ tương tác theo thời gian.
- **UI-04**: Màn hình giám sát real-time cập nhật dữ liệu mỗi 60 giây không reload trang.
- **UI-05**: Giao diện xuất báo cáo có nút bấm tải file .pdf và .xlsx.

### 5.2 Software Interfaces

| Interface | Protocol | Mô tả |
|-----------|----------|-------|
| NestJS ↔ PostgreSQL | TCP/SQL | ORM (TypeORM/Prisma) |
| FastAPI ↔ NestJS | REST/HTTP | AI push kết quả về NestJS |
| NestJS ↔ FastAPI | REST/HTTP | NestJS trả danh sách camera cần kết nối |
| NestJS ↔ Frontend | WebSocket / SSE | Real-time dashboard update |
| FastAPI ↔ Camera | RTSP over TCP | Đọc video stream |

### 5.3 Hardware Interfaces

- **Camera IP**: chuẩn ONVIF, stream RTSP H.264, độ phân giải ≥ 1920×1080, frame rate ≥ 15fps.
- **GPU Server** (AI cluster): NVIDIA GPU hỗ trợ CUDA 11+ (khuyến nghị RTX 3080 trở lên hoặc tương đương server-grade).

### 5.4 Communication Interfaces

- HTTPS (TLS 1.2+) cho tất cả traffic client ↔ server.
- Internal services giao tiếp qua mạng nội bộ Docker (không expose public).
- WebSocket over WSS cho real-time stream.
- RTSP/TCP cho camera stream.

---

## 6. Non-Functional Requirements

### 6.1 Performance

- **NFR-PERF-01**: Inference time AI ≤ 100ms/frame tại độ phân giải 1080p với GPU.
- **NFR-PERF-02**: API response time ≤ 500ms cho 95% request tại tải bình thường.
- **NFR-PERF-03**: Dashboard real-time cập nhật độ trễ ≤ 5 giây so với thời điểm AI tạo kết quả.

### 6.2 Scalability

- **NFR-SCAL-01**: NestJS backend chạy ≥ 2 instance sau Load Balancer (horizontal scaling).
- **NFR-SCAL-02**: AI cluster chạy ≥ 2 FastAPI instance để xử lý song song nhiều camera.
- **NFR-SCAL-03**: Kiến trúc cho phép mở rộng lên 50+ camera mà không cần refactor lõi.

### 6.3 Security

- **NFR-SEC-01**: Mật khẩu lưu trữ bằng bcrypt (cost factor ≥ 12).
- **NFR-SEC-02**: Access Token TTL = 15 phút; Refresh Token TTL = 7 ngày, lưu HttpOnly cookie hoặc DB.
- **NFR-SEC-03**: Toàn bộ traffic qua HTTPS; HSTS enabled.
- **NFR-SEC-04**: Input validation chống SQL Injection và XSS ở mọi endpoint.
- **NFR-SEC-05**: Rate limiting trên endpoint đăng nhập: tối đa 10 lần/phút/IP.

### 6.4 Reliability

- **NFR-REL-01**: Module AI tự động reconnect camera khi mất kết nối, retry tối đa 3 lần với backoff 30 giây.
- **NFR-REL-02**: Nếu module AI không đẩy kết quả trong 5 phút, NestJS đánh dấu tiết học là `ANALYSIS_FAILED` và thông báo Admin.

### 6.5 Availability

- **NFR-AVAIL-01**: Uptime mục tiêu ≥ 99% (downtime ≤ 7.3 giờ/năm) cho môi trường production.
- **NFR-AVAIL-02**: Maintenance window thực hiện ngoài giờ học (sau 22:00).

### 6.6 Maintainability

- **NFR-MAIN-01**: Code backend theo kiến trúc module (NestJS modules), mỗi domain tách riêng.
- **NFR-MAIN-02**: Module AI đóng gói Docker, cập nhật model không ảnh hưởng đến web stack.
- **NFR-MAIN-03**: API versioning (`/api/v1/`).

### 6.7 Usability

- **NFR-USE-01**: Admin có thể xem dashboard giám sát tiết học trong vòng ≤ 3 click từ trang chủ.
- **NFR-USE-02**: Giảng viên có thể xem lịch dạy tuần hiện tại ngay sau khi đăng nhập.

### 6.8 AI/ML Requirements

- **NFR-AI-01**: Độ chính xác phát hiện đối tượng (mAP@0.5) ≥ 85% trên test set nội bộ.
- **NFR-AI-02**: Tỷ lệ mất track ID (ID Switch) ≤ 5% trên video chuẩn nội bộ.
- **NFR-AI-03**: Mô hình phải được huấn luyện lại khi dataset đạt mốc 12.000 ảnh; version mới phải vượt benchmark của phiên bản trước.
- **NFR-AI-04**: Model weights được lưu theo versioning, rollback về phiên bản trước trong ≤ 10 phút.

---

## 7. Internationalization and Localization

- Ngôn ngữ mặc định: **Tiếng Việt**.
- Định dạng ngày giờ: `DD/MM/YYYY HH:mm` theo chuẩn Việt Nam.
- Múi giờ: `Asia/Ho_Chi_Minh` (UTC+7), lưu DB dưới dạng UTC.
- Số thập phân: dấu phẩy (`,`) phân cách nghìn, dấu chấm (`.`) thập phân (theo thông lệ UI quốc tế để tương thích biểu đồ).

---

## 8. Other Requirements

- **OTH-01**: Hệ thống phải có trang quản lý cấu hình tham số TSII Engine (Admin): ngưỡng R mét, hệ số suy giảm điểm, ngưỡng cảnh báo TSII) mà không cần sửa code.
- **OTH-02**: Toàn bộ thao tác Admin (tạo/xóa tài khoản, thay đổi cấu hình) phải được ghi vào audit log.
- **OTH-03**: Hệ thống hỗ trợ đăng ký/đăng nhập bằng tài khoản Microsoft (Microsoft OAuth 2.0) — tính năng phụ, triển khai sau.

---

## 9. Glossary

| Thuật ngữ | Giải thích |
|-----------|------------|
| Tiết học (Session) | Đơn vị thời gian giảng dạy, gắn với 1 GV, 1 lớp, 1 phòng, 1 khoảng thời gian xác định |
| TSII Score | Điểm tổng hợp (0–100) đo mức độ tương tác GV-SV trong một khoảng thời gian |
| Vùng Chưa Phủ | Tập hợp vị trí SV mà GV chưa đến gần trong suốt tiết học |
| Bục giảng | Khu vực đầu lớp, được định nghĩa trong cấu hình phòng, là vùng "tĩnh" của GV |
| Mức độ tương tác tổng | Điểm tổng hợp từ TSII score, mobility %, và interaction quality |
| Annotation | Quá trình vẽ bounding box lên khung hình video để hiển thị kết quả tracking |

---

## 10. Analysis and Design Models

### 10.1 Use Case Diagram

**Actors:** Admin, Giảng viên, Hệ thống AI (automated actor)

**Use Cases chính:**
- Admin: Quản lý tài khoản, Xem Dashboard, Giám sát tiết học real-time, Xem thống kê, Xuất báo cáo, Cấu hình camera, Cấu hình tham số TSII.
- Giảng viên: Đăng nhập, Xem lịch dạy, Xem kết quả tiết học của bản thân, Chỉnh sửa hồ sơ.
- Hệ thống AI: Nhận cấu hình phòng, Đẩy kết quả phân tích real-time, Đẩy tổng kết tiết học.

### 10.2 Sequence Diagram

**Luồng: Phân tích tiết học thời gian thực**

```
Admin           NestJS              FastAPI (AI)         Camera
  |                |                     |                  |
  |   GET /sessions/active               |                  |
  |────────────────>|                    |                  |
  |                 |── GET camera list ─>|                  |
  |                 |                    |─── RTSP connect ─>|
  |                 |                    |<── video stream ──|
  |                 |                    | [YOLO+Track+TSII] |
  |                 |<─ POST /analysis ──|                  |
  |                 |── save DB          |                  |
  |                 |── WebSocket push ─>|                  |
  |<── SSE update ──|                    |                  |
```

### 10.3 Activity Diagram

**Luồng: Kết thúc tiết học**

```
[Đến giờ kết thúc tiết]
        ↓
[AI dừng stream camera]
        ↓
[AI tính tổng kết: avg TSII, mobility%, interaction%]
        ↓
[AI POST JSON tổng kết → NestJS]
        ↓
[NestJS lưu session_summaries]
        ↓
[NestJS cập nhật session status = COMPLETED]
        ↓
[Frontend hiển thị kết quả tổng kết tiết học]
```

### 10.4 ERD

```
academic_years ──< semesters ──< sessions >── users (teacher)
                                    |
                              rooms >── cameras
                                    |
                            session_logs (JSONB, 1/phút)
                                    |
                          session_summaries (1 bản ghi/tiết)

users >── user_roles >── roles
```

### 10.5 Deployment Diagram

```
┌─────────────────────────── Cloud Server ────────────────────────────┐
│                                                                       │
│  ┌──────────┐     ┌─────────────┐     ┌──────────┐  ┌──────────┐  │
│  │  Nginx   │────>│  Next.js    │     │ NestJS 1 │  │ NestJS 2 │  │
│  │(LB+SSL)  │     │  (SSR)      │     │ (Auth)   │  │ (Main)   │  │
│  └──────────┘     └─────────────┘     └────┬─────┘  └────┬─────┘  │
│                                             └──────┬───────┘        │
│                                              ┌─────▼──────┐         │
│                                              │ PostgreSQL │         │
│                                              └────────────┘         │
└───────────────────────────────────────────────────────────────────┘

┌─────────────── Cụm Xử lý Ảnh (AI Cluster) ────────────────────┐
│                                                                   │
│  ┌──────────────────┐    ┌──────────────────┐                   │
│  │ FastAPI + YOLO 1 │    │ FastAPI + YOLO 2 │                   │
│  │ (GPU instance)   │    │ (GPU instance)   │                   │
│  └────────┬─────────┘    └────────┬─────────┘                   │
│           └─────────────┬──────────┘                             │
│                  ┌──────▼──────┐                                 │
│                  │  Camera IPs │ (RTSP stream, LAN)              │
│                  └─────────────┘                                 │
└───────────────────────────────────────────────────────────────┘
```

---

## 11. Appendices

### Appendix A: Cấu trúc JSON chuẩn từ AI Engine

Xem mục **FR-ANL-02** — payload mẫu đã được định nghĩa trong phần Functional Requirements.

### Appendix B: Thông số mặc định TSII Engine

| Tham số | Giá trị mặc định | Mô tả |
|---------|-----------------|-------|
| `R_proximity_meter` | 1.5m | Bán kính tính là GV đang tương tác với SV |
| `decay_threshold_sec` | 10s | Dưới ngưỡng này áp dụng suy giảm điểm |
| `cap_threshold_sec` | 30s | Trên ngưỡng này chặn trần tích điểm |
| `static_zone_label` | "podium" | Tên vùng bục giảng trong config phòng |
| `alert_tsii_threshold` | 40 | Ngưỡng TSII cảnh báo chất lượng thấp |

### Appendix C: Phân loại chất lượng TSII

| Mức | TSII Score | Nhãn |
|-----|------------|------|
| Giỏi | 80 – 100 | Excellent |
| Khá | 60 – 79 | Good |
| Trung bình | 40 – 59 | Average |
| Yếu | 0 – 39 | Below Average |
