
# AI_LOG - NHẬT KÝ SỬ DỤNG AI

## Thông tin chung

- **Hệ thống:** Intelligent Medical Record System (IMRS)
- **Bài thực hành:** Lab 4 - AI-Assisted Analysis & Design
- **Chủ đề:** Thiết kế phân hệ tra cứu tri thức RAG
- **Công cụ AI:** ChatGPT
- **Ngày thực hiện:** 07/11/2026
- **Người thực hiện:** [Họ và tên sinh viên]

---

## 1. Mục đích sử dụng AI

Trong Lab 4, ChatGPT được sử dụng như một công cụ
hỗ trợ kiến trúc sư phần mềm để:

- Phân tích yêu cầu và xác định Architectural Drivers.
- Đề xuất và so sánh các phương án thiết kế RAG.
- Kiểm chứng các khẳng định kỹ thuật.
- Hỗ trợ sinh sơ đồ kiến trúc bằng Mermaid.
- Hỗ trợ xây dựng Architecture Decision Records (ADR).

Sinh viên chịu trách nhiệm kiểm tra, đánh giá,
chỉnh sửa và quyết định nội dung cuối cùng.

---

## 2. Nhật ký tương tác AI - Lab 4

| STT | Công cụ | Mục đích | Prompt (tóm tắt) | Kết quả AI | Đánh giá của SV | Chỉnh sửa |
|---|---|---|---|---|---|---|
| 1 | ChatGPT | Phân tích Architectural Drivers cho phân hệ RAG | Yêu cầu AI đóng vai kiến trúc sư phần mềm, phân tích hệ thống IMRS với 5.000 PDF nội bộ, 1 GPU RTX 4090, ngân sách 15.000 USD, thời hạn 4 tháng; chia drivers thành chức năng, ASR và ràng buộc. | AI hỗ trợ đề xuất các drivers liên quan đến tra cứu tài liệu, trích dẫn nguồn, TTFT, phân quyền theo vai trò, khả năng thay thế embedding, bảo vệ PII và triển khai nội bộ. | Cần đối chiếu từng driver với Lab 1. Chưa có giá trị TTFT cụ thể nên không được tự đặt ngưỡng hiệu năng. Cần phân biệt yêu cầu bắt buộc với đề xuất bổ sung của AI. | Xây dựng bảng drivers có cột Nguồn; đánh dấu những nội dung cần kiểm tra trong LAB1_REPORT.md; bổ sung hoặc sửa các driver sau khi đối chiếu. |

| 2 | ChatGPT | So sánh phương án thiết kế RAG | Yêu cầu AI so sánh D1: Qdrant, pgvector, Managed Cloud; D2: Embedding nội bộ, Cloud API; D3: Pre-filter, Post-filter, Collection theo vai trò | [Tóm tắt kết quả AI thực tế] | [Đánh giá mức phù hợp bảo mật, chi phí, vận hành, hiệu năng; ghi thiếu sót thực tế] | [Ghi thay đổi đã thực hiện trong bảng so sánh] |

| 3 | ChatGPT, Qdrant Docs, pgvector README | Kiểm chứng ba khẳng định kỹ thuật trong bảng so sánh RAG | Đối chiếu khả năng payload filtering của Qdrant, Cosine Distance và SQL WHERE của pgvector với tài liệu chính thức | AI khẳng định Qdrant hỗ trợ lọc metadata; pgvector hỗ trợ tìm kiếm Cosine và SQL filtering | Ba khẳng định đúng về chức năng cơ bản. Tuy nhiên, cần làm rõ việc lọc với approximate indexes của pgvector. AI đã nhận diện phương án Cloud vi phạm ràng buộc dữ liệu nội bộ | Bổ sung lưu ý về ANN filtering, nguồn tài liệu chính thức và yêu cầu kiểm tra quyền phía máy chủ vào LAB4_RAG_DESIGN.md |

| 4 | ChatGPT, Mermaid Live Editor | Sinh và hiệu chỉnh sơ đồ kiến trúc RAG | Yêu cầu AI tạo Flowchart Mermaid gồm Ingestion, Query, AccessPolicy, PIIRedactor, VectorDBConnector, AuditLogger và ResponseCache | AI sinh mã Mermaid mô tả hai luồng xử lý của hệ thống RAG | [Ghi các thành phần thiếu, lỗi cú pháp hoặc lỗi kiến trúc thực sự tìm thấy trong sơ đồ AI ban đầu] | [Ghi rõ các thay đổi đã thực hiện và lưu tại rag-flow-final.mmd] |

| 5 | ChatGPT, Mermaid Live Editor | Sinh và hiệu chỉnh Sequence Diagram cho phân hệ RAG | Yêu cầu AI tạo sequenceDiagram mô tả bác sĩ gửi câu hỏi, kiểm tra quyền, tìm kiếm vector, xử lý PII và trả lời có trích dẫn | AI sinh mã Mermaid với các thành phần QueryOrchestrator, AccessPolicy, VectorDBConnector, PIIRedactor, PromptBuilder, LocalLLM, AuditLogger và ResponseCache | [Ghi nhận xét sau khi kiểm tra sơ đồ ban đầu, bao gồm các nhánh xử lý và cú pháp Mermaid] | [Ghi các thay đổi thực tế trong rag-sequence-final.mmd và ảnh rag-sequence.png] |

| 6 | ChatGPT | Phản biện ADR-0001 và ADR-0002 | Yêu cầu AI đánh giá cấu trúc ADR, các giả định, rủi ro bảo mật, hệ quả tiêu cực và lý do loại bỏ phương án | [Ghi kết quả phản biện AI thực tế] | [Ghi nhận xét nào hợp lý, nhận xét nào không phù hợp] | [Ghi những thay đổi thực sự đã thực hiện trong hai ADR] |

---

## 3. Đánh giá việc sử dụng AI trong Nhiệm vụ 1

### 3.1. Kết quả hữu ích

ChatGPT hỗ trợ tổ chức Architectural Drivers
thành ba nhóm chính:

1. Yêu cầu chức năng (Functional Requirements).
2. Thuộc tính chất lượng (Quality Attributes / ASR).
3. Ràng buộc kiến trúc (Constraints).

Việc phân nhóm giúp xác định những yêu cầu có
ảnh hưởng trực tiếp đến việc lựa chọn kiến trúc
RAG cho hệ thống IMRS.

### 3.2. Các vấn đề cần kiểm chứng

- Ngưỡng TTFT cần lấy từ báo cáo Lab 1.
- Các yêu cầu phân quyền phải phù hợp với chính
  sách bảo mật của bệnh viện.
- Việc thay thế mô hình embedding có thể yêu cầu
  tạo lại vector dữ liệu.
- Giải pháp đề xuất phải bảo đảm dữ liệu không
  rời mạng nội bộ bệnh viện.
- Không được đưa ra các số liệu hiệu năng chưa
  có căn cứ.

### 3.3. Nguyên tắc chỉnh sửa

Sinh viên không sử dụng nguyên văn đầu ra AI
mà không kiểm tra.

Mỗi đề xuất của AI được xem xét theo:
- Yêu cầu được cung cấp trong đề Lab.
- Các ASR từ Lab 1.
- Tính phù hợp với hệ thống IMRS.
- Ràng buộc kỹ thuật và bảo mật.

---

## 4. Kết luận bước phân tích drivers

Việc sử dụng ChatGPT giúp hỗ trợ quá trình
phân tích và tổ chức các yêu cầu kiến trúc
của phân hệ RAG.

Tuy nhiên, các drivers được AI đề xuất chỉ là
cơ sở tham khảo và cần đối chiếu với Lab 1
trước khi được chấp nhận chính thức.

Các kết quả đã kiểm chứng sẽ được đưa vào
tệp lab4/LAB4_RAG_DESIGN.md.

---

## 5. Các tương tác tiếp theo

Các mục nhật ký tiếp theo sẽ được bổ sung
sau khi hoàn thành:

- Nhiệm vụ 2: So sánh D1, D2, D3.
- Nhiệm vụ 2: Kiểm chứng khẳng định kỹ thuật.
- Nhiệm vụ 3: Sinh và hiệu chỉnh Flowchart.
- Nhiệm vụ 3: Sinh và hiệu chỉnh Sequence Diagram.
- Nhiệm vụ 4: Soạn thảo và phản biện ADR.


