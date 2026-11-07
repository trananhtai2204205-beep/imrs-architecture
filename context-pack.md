
# IMRS - Architecture Context Pack

## 1. Tổng quan hệ thống
IMRS (Intelligent Medical Record System) là hệ thống
hồ sơ y tế thông minh có tích hợp AI.

## 2. Tính năng mới của Lab 4
Trợ lý tra cứu phác đồ sử dụng kiến trúc RAG.

Bác sĩ đặt câu hỏi bằng ngôn ngữ tự nhiên.
Hệ thống tìm các đoạn tài liệu phù hợp và sử dụng
LLM để tổng hợp câu trả lời có trích dẫn.

## 3. Nguồn dữ liệu
- Khoảng 5.000 tài liệu PDF nội bộ.
- Phác đồ chuyên môn: chỉ dành cho bác sĩ.
- Tài liệu hướng dẫn: dành cho bệnh nhân.

## 4. Ràng buộc
- Dữ liệu không được rời mạng nội bộ bệnh viện.
- Hạ tầng có 1 GPU RTX 4090.
- Ngân sách: 15.000 USD.
- Thời hạn: 4 tháng.
- Không sử dụng dữ liệu bệnh nhân thật trong Lab.

## 5. Yêu cầu kiến trúc
- Hỗ trợ phân quyền truy xuất tài liệu.
- Hỗ trợ tìm kiếm tương đồng bằng vector.
- Hỗ trợ lọc metadata theo quyền.
- LLM tạo câu trả lời có trích dẫn.
- Có khả năng thay thế mô hình embedding.
- Có cơ chế xử lý thông tin PII.

## 6. ASR từ Lab 1
- TTFT: [Điền giá trị trong LAB1_REPORT.md]
- Bảo mật: [Đối chiếu Lab 1]
- Khả năng sửa đổi: [Đối chiếu Lab 1]
- Các ASR khác: [Đối chiếu Lab 1]

## 7. Thành phần từ Lab 2-3
- QueryOrchestrator
- PIIRedactor
- VectorDBConnector
- [Kiểm tra tên trong báo cáo Lab 2-3]

## 8. Giả định cần xác minh
- PostgreSQL đã được triển khai hay chưa.
- Công nghệ xác thực và phân quyền hiện có.
- Ngưỡng TTFT và mức tải dự kiến.
