
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
- TTFT: chưa có LAB1_REPORT.md để đối chiếu; không tự đặt con số.
- Bảo mật: phân quyền theo vai trò và dữ liệu nội bộ theo đề Lab 4; ASR chi tiết Lab 1 chưa được đối chiếu.
- Khả năng sửa đổi: cần hỗ trợ thay thế embedding theo Lab 4; ASR Lab 1 chưa được đối chiếu.
- Các ASR khác: chưa được cung cấp từ Lab 1.

## 7. Thành phần từ Lab 2-3
- QueryOrchestrator
- PIIRedactor
- VectorDBConnector
- Chưa có báo cáo Lab 2-3 để xác minh tên thành phần.

## 8. Giả định cần xác minh
- PostgreSQL đã được triển khai hay chưa.
- Công nghệ xác thực và phân quyền hiện có.
- Ngưỡng TTFT và mức tải dự kiến.
