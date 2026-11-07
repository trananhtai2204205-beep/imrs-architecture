
# ADR-0001: Chọn kho lưu trữ vector cho phân hệ RAG

## Status

Proposed (2026-11-07)

Quyết định đang được đề xuất, chờ xác minh
hạ tầng PostgreSQL hiện có và khả năng đáp ứng
các ASR của IMRS.

## Context

Hệ thống Intelligent Medical Record System
(IMRS) được bổ sung tính năng Trợ lý tra cứu
phác đồ sử dụng Retrieval-Augmented Generation
(RAG).

Phân hệ RAG cần xử lý khoảng 5.000 tài liệu
PDF nội bộ của bệnh viện.

Các tài liệu bao gồm:
- Phác đồ chuyên môn dành cho bác sĩ.
- Tài liệu hướng dẫn dành cho bệnh nhân.

Quá trình nạp tri thức gồm:
PDF -> DocumentParser -> Chunker ->
EmbeddingService -> VectorStore.

Khi truy vấn, hệ thống sinh embedding cho
câu hỏi, tìm các đoạn tài liệu tương đồng
và sử dụng LLM nội bộ để tạo câu trả lời
có trích dẫn.

Các ràng buộc quan trọng:
- Dữ liệu không được rời mạng nội bộ bệnh viện.
- Hệ thống có 1 GPU NVIDIA RTX 4090.
- Ngân sách tối đa 15.000 USD.
- Thời gian triển khai 4 tháng.
- Tài liệu phải được truy xuất theo quyền.
- Cần hỗ trợ thay thế mô hình embedding.
- Cần đáp ứng yêu cầu độ trễ của tính năng tra cứu; không tự đặt ngưỡng khi chưa có số liệu.

Các Architectural Drivers liên quan:
- F01: Tìm kiếm tài liệu liên quan.
- F02: Trả lời có trích dẫn nguồn.
- Q01: Quan tâm đến TTFT; chưa đặt ngưỡng số.
- Q02: Phân quyền truy xuất tài liệu.
- Q03: Khả năng thay thế embedding.
- C01: Dữ liệu không rời mạng nội bộ.
- C02: Giới hạn hạ tầng.
- C03: Giới hạn ngân sách.
- C04: Thời hạn triển khai.

Mã driver cần đồng bộ với bảng drivers
chính thức trong LAB4_RAG_DESIGN.md.

Cần lựa chọn một kho vector có thể lưu
embedding cùng metadata, hỗ trợ tìm kiếm
tương đồng và lọc theo allowed_role.

## Decision

Đề xuất ưu tiên sử dụng pgvector trên
PostgreSQL triển khai nội bộ cho phân hệ RAG.

Phương án này được lựa chọn với điều kiện:
1. IMRS đã có PostgreSQL nội bộ phù hợp.
2. Có thể cài đặt pgvector extension.
3. Tài nguyên cơ sở dữ liệu đáp ứng tải RAG.
4. Truy vấn vector có lọc quyền đáp ứng ASR.
5. Không làm ảnh hưởng không chấp nhận được
   đến các chức năng nghiệp vụ của IMRS.

VectorDBConnector được sử dụng làm lớp
trừu tượng giữa QueryOrchestrator và
công nghệ lưu trữ vector.

Metadata dự kiến gồm:
- document_id
- chunk_id
- page
- allowed_role
- document_version
- embedding_model_version

Điều kiện phân quyền được áp dụng trong
truy vấn phía máy chủ.

Nếu không đáp ứng các điều kiện trên,
quyết định sẽ được đánh giá lại, với
Qdrant self-hosted là phương án thay thế.

## Alternatives Considered

### Phương án 1: Qdrant self-hosted

Ưu điểm:
- Là Vector Database chuyên dụng.
- Hỗ trợ truy vấn vector và payload filtering.
- Có thể triển khai trong mạng nội bộ.
- Tách biệt dịch vụ vector khỏi PostgreSQL.

Lý do chưa ưu tiên:
- Cần vận hành thêm một dịch vụ riêng.
- Phát sinh công việc triển khai, sao lưu,
  giám sát và quản lý tài nguyên.
- Chưa có benchmark chứng minh Qdrant phù hợp
  hơn với tải IMRS hiện tại.

Qdrant vẫn là phương án dự phòng nếu
PostgreSQL không đáp ứng yêu cầu.

### Phương án 2: Managed Vector Database Cloud

Ưu điểm:
- Nhà cung cấp quản lý nhiều hoạt động
  vận hành và hạ tầng.
- Có khả năng mở rộng tùy dịch vụ.

Lý do loại bỏ:
- Việc truyền nội dung hoặc vector ra
  dịch vụ cloud thông thường không phù hợp
  ràng buộc dữ liệu nội bộ của bệnh viện.
- Tăng phụ thuộc vào mạng và nhà cung cấp.
- Có thêm chi phí dịch vụ.

Phương án này không được lựa chọn trong
điều kiện triển khai IMRS hiện tại.

## Consequences

### Positive Consequences

1. Có thể tận dụng PostgreSQL hiện có,
   nếu giả định về hạ tầng được xác nhận.

2. Giảm số lượng dịch vụ cơ sở dữ liệu
   cần quản lý so với việc triển khai
   thêm Vector Database chuyên dụng.

3. Có thể sử dụng SQL để kết hợp điều kiện
   phân quyền và truy vấn vector.

4. Metadata tài liệu có thể được quản lý
   cùng hệ sinh thái PostgreSQL.

5. VectorDBConnector giúp giảm sự phụ thuộc
   trực tiếp của nghiệp vụ vào pgvector.

### Negative Consequences

1. Truy vấn vector có thể cạnh tranh CPU,
   RAM và I/O với các truy vấn nghiệp vụ
   trên PostgreSQL dùng chung.

2. Cần thêm công việc quản trị extension,
   index, sao lưu và giám sát.

3. Truy vấn ANN có lọc metadata có thể
   trả về ít kết quả hơn yêu cầu nếu không
   cấu hình và kiểm thử phù hợp.

4. Việc thay đổi mô hình embedding có thể
   yêu cầu tạo lại vector hoặc index.

### Risks and Mitigations

- Rủi ro tranh chấp tài nguyên:
  Đánh giá tải, giới hạn tài nguyên và
  xem xét database instance riêng nếu cần.

- Rủi ro truy xuất sai quyền:
  Kết hợp AccessPolicy phía máy chủ,
  điều kiện SQL và kiểm thử phân quyền.

- Rủi ro không đáp ứng TTFT:
  Thử nghiệm hiệu năng với bộ câu hỏi mẫu,
  không tự đặt ngưỡng khi chưa có số liệu.

- Rủi ro phụ thuộc công nghệ:
  Giữ giao diện VectorDBConnector ổn định.

### Follow-up Actions

1. Xác minh PostgreSQL hiện có trong hạ tầng IMRS.
2. Kiểm tra phiên bản PostgreSQL và pgvector.
3. Thử nghiệm truy vấn có lọc allowed_role.
4. Đánh giá tài nguyên và độ trễ.
5. Kiểm tra chính sách sao lưu và bảo mật.
6. Chuyển ADR sang Accepted khi nhóm xác nhận
   các điều kiện lựa chọn đã được đáp ứng.

## References

- https://github.com/pgvector/pgvector
- https://qdrant.tech/documentation/search/filtering/
- ../lab4/LAB4_RAG_DESIGN.md
