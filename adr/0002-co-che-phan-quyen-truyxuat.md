
# ADR-0002: Chọn cơ chế phân quyền truy xuất RAG

## Status

Proposed (2026-11-07)

Đề xuất sử dụng pre-filter theo quyền,
kết hợp AccessPolicy phía máy chủ.

## Context

Hệ thống IMRS có kho khoảng 5.000 PDF
nội bộ phục vụ tính năng tra cứu phác đồ
bằng RAG.

Dữ liệu được chia thành hai nhóm:
- Phác đồ chuyên môn chỉ dành cho bác sĩ.
- Tài liệu hướng dẫn dành cho bệnh nhân.

Các tài liệu phải được truy xuất theo
quyền của người dùng.

Nếu hệ thống đưa tài liệu trái quyền vào
ngữ cảnh của LLM, câu trả lời có thể
tiết lộ thông tin không được phép.

Yêu cầu kiến trúc:
- Xác thực người dùng.
- Phân quyền theo vai trò.
- Không tin tưởng role do client tự khai báo.
- Lọc quyền trước khi tạo ngữ cảnh LLM.
- Bảo vệ thông tin nhạy cảm.
- Ghi nhận sự kiện truy vấn cần thiết.
- Không đưa dữ liệu ra ngoài mạng nội bộ.

Các drivers liên quan:
- F01: Truy xuất tài liệu.
- Q02: Bảo mật và phân quyền.
- Q04: Kiểm soát PII.
- C01: Dữ liệu không rời mạng nội bộ.

Mã driver phải thống nhất với báo cáo Lab 4.

## Decision

Đề xuất chọn pre-filter theo quyền
làm cơ chế phân quyền truy xuất chính.

Quy trình thực hiện:

1. Người dùng gửi câu hỏi đến QueryOrchestrator.

2. AccessPolicy xác định danh tính và
   quyền từ phiên xác thực phía máy chủ.

3. QueryOrchestrator tạo truy vấn tìm kiếm
   thông qua VectorDBConnector.

4. Truy vấn chứa điều kiện lọc metadata
   dựa trên quyền truy cập hợp lệ.

5. VectorStore chỉ trả về các đoạn tài liệu
   đáp ứng chính sách truy cập.

6. Hệ thống thực hiện kiểm tra phòng vệ
   trước khi tạo ngữ cảnh LLM.

7. PIIRedactor xử lý thông tin nhạy cảm
   trong ngữ cảnh trước khi gọi LLM.

8. PromptBuilder xây dựng prompt.

9. LLM nội bộ tạo câu trả lời có trích dẫn.

Metadata tài liệu phải có allowed_role
hoặc cơ chế ACL tương đương.

Áp dụng nguyên tắc deny by default:
tài liệu thiếu metadata quyền hợp lệ
không được truy xuất.

ResponseCache phải áp dụng chính sách
phân quyền, tránh rò rỉ dữ liệu chéo vai trò.

AuditLogger ghi nhận các sự kiện cần thiết
mà không lưu PII không cần thiết.

## Alternatives Considered

### Phương án 1: Post-filter

Mô tả:
Hệ thống tìm top-k tài liệu trước,
sau đó loại bỏ các đoạn không được phép.

Ưu điểm:
- Có thể triển khai đơn giản ở tầng ứng dụng.
- Có thể dùng như bước kiểm tra bổ sung.

Lý do không chọn làm cơ chế chính:
- Tài liệu trái quyền có thể đi vào các bước
  xử lý trung gian trước khi được lọc.
- Nếu không bảo vệ đầy đủ các bước trung gian,
  có thể làm tăng nguy cơ rò rỉ thông tin.
- Sau khi loại kết quả trái quyền,
  số tài liệu hợp lệ có thể nhỏ hơn k.

Post-filter chỉ được xem là biện pháp
kiểm tra phòng vệ bổ sung.

### Phương án 2: Tách Collection theo vai trò

Mô tả:
Lưu tài liệu trong các collection hoặc
phân vùng riêng theo nhóm quyền.

Ưu điểm:
- Có thể hỗ trợ cách ly dữ liệu theo nhóm.
- Giúp phân chia dữ liệu theo phạm vi truy xuất.

Lý do không ưu tiên:
- Có thể làm trùng lặp tài liệu dùng chung.
- Tăng công việc đồng bộ và quản lý quyền.
- Phức tạp khi người dùng có nhiều vai trò.
- Không thay thế việc xác thực danh tính
  và kiểm tra quyền ở phía máy chủ.

Phương án này có thể được cân nhắc nếu
hệ thống cần cách ly mạnh hơn.

## Consequences

### Positive Consequences

1. Giảm nguy cơ đưa tài liệu trái quyền
   vào quá trình tạo ngữ cảnh cho LLM.

2. Phù hợp với chiến thuật bảo mật
   Authorize Actors.

3. Có thể sử dụng metadata allowed_role
   trong truy vấn tìm kiếm.

4. Có thể triển khai trên Qdrant hoặc
   pgvector thông qua VectorDBConnector.

5. Giúp tập trung quản lý chính sách
   phân quyền ở tầng máy chủ.

### Negative Consequences

1. Cần duy trì metadata phân quyền chính xác
   và đồng bộ khi quyền tài liệu thay đổi.

2. Truy vấn có lọc quyền có thể làm tăng
   độ phức tạp triển khai và kiểm thử.

3. Cần xem xét ảnh hưởng của điều kiện lọc
   đến độ trễ và số kết quả truy xuất.

4. Nếu AccessPolicy hoặc metadata bị cấu hình
   sai, người dùng có thể bị từ chối tài liệu
   hợp lệ hoặc truy xuất sai quyền.

5. Khi quyền thay đổi, ResponseCache có thể
   chứa kết quả không còn phù hợp và cần
   được làm mới hoặc vô hiệu hóa.

### Risks and Mitigations

- Metadata quyền không chính xác:
  Kiểm tra khi nạp dữ liệu và cập nhật ACL.

- Người dùng giả mạo role:
  Không sử dụng role do client tự khai báo.

- Cache trả dữ liệu trái quyền:
  Kiểm tra quyền và phiên bản ACL khi đọc cache.

- Thiếu kết quả khi truy vấn ANN có lọc:
  Đánh giá index, iterative scans hoặc
  chiến lược tìm kiếm phù hợp.

- Tài liệu trái quyền xuất hiện trong LLM:
  Kiểm tra quyền trước PromptBuilder và LLM.

### Follow-up Actions

1. Xác định nguồn dữ liệu RBAC/ACL của IMRS.
2. Chuẩn hóa metadata allowed_role.
3. Xây dựng kiểm thử bác sĩ và bệnh nhân.
4. Kiểm thử tài liệu không có metadata quyền.
5. Kiểm thử khi thay đổi quyền truy cập.
6. Kiểm thử cache và nguồn trích dẫn.
7. Đánh giá truy vấn top-k có lọc quyền.

## References

- https://qdrant.tech/documentation/search/filtering/
- https://github.com/pgvector/pgvector#filtering
- https://www.postgresql.org/docs/current/ddl-rowsecurity.html
- ../lab4/LAB4_RAG_DESIGN.md
