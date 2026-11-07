
# AI_LOG - NHẬT KÝ SỬ DỤNG AI

## Thông tin chung

- **Hệ thống:** Intelligent Medical Record System (IMRS)
- **Bài thực hành:** Lab 4 - AI-Assisted Analysis & Design
- **Chủ đề:** Thiết kế phân hệ tra cứu tri thức RAG
- **Ngày thực hiện:** 07/11/2026
- **Người thực hiện:** Trần Anh Tài

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
| 1 | ChatGPT | Phân tích Architectural Drivers cho phân hệ RAG | Yêu cầu phân loại drivers của IMRS RAG thành chức năng, ASR và ràng buộc; bối cảnh gồm 5.000 PDF, 1 GPU RTX 4090, ngân sách 15.000 USD, thời hạn 4 tháng. | AI đề xuất các drivers về tra cứu, trích dẫn, TTFT, phân quyền, thay thế embedding, PII và triển khai nội bộ. | Không có căn cứ để tự đặt ngưỡng TTFT. Cần phân biệt đề xuất của AI với dữ kiện đầu vào và nội dung sinh viên bổ sung. | Phân loại nguồn theo ba nhãn đề bài; hiệu chỉnh mô tả quyền truy cập theo vai trò xác thực phía máy chủ; bổ sung luồng ingestion và các giới hạn đầu vào. |
| 2 | ChatGPT | So sánh D1–D3 | Yêu cầu so sánh Qdrant/pgvector/cloud, embedding nội bộ/cloud, pre-/post-filter/collections | Đầu ra AI đã được lưu trong bản Markdown so sánh D1–D3; AI nêu ưu tiên pgvector có điều kiện, embedding nội bộ và pre-filter | AI nhận diện cloud vi phạm giới hạn dữ liệu nội bộ; giả định PostgreSQL cần xác minh; so sánh hiệu năng chỉ định tính | Bổ sung ghi chú điều kiện và phân tích lọc ANN vào báo cáo |
| 3 | ChatGPT, Qdrant Docs, pgvector README | Kiểm chứng ba khẳng định kỹ thuật trong bảng so sánh RAG | Đối chiếu khả năng payload filtering của Qdrant, Cosine Distance và SQL WHERE của pgvector với tài liệu chính thức | AI khẳng định Qdrant hỗ trợ lọc metadata; pgvector hỗ trợ tìm kiếm Cosine và SQL filtering | Ba khẳng định đúng về chức năng cơ bản. Tuy nhiên, cần làm rõ việc lọc với approximate indexes của pgvector. AI đã nhận diện phương án Cloud vi phạm ràng buộc dữ liệu nội bộ | Bổ sung lưu ý về ANN filtering, nguồn tài liệu chính thức và yêu cầu kiểm tra quyền phía máy chủ vào LAB4_RAG_DESIGN.md |
| 4 | ChatGPT, Mermaid | Vẽ Flowchart RAG | Yêu cầu sơ đồ hai luồng, phân quyền, cache, PII, audit | Bản gốc lưu tại `lab4/rag-flow-ai-original.mmd` | Bản gốc thiếu nhánh từ chối truy cập và nhánh không tìm thấy tài liệu. Đây là hai trường hợp cần có để thể hiện đầy đủ xử lý lỗi và kiểm soát truy cập. | Bổ sung hai nhánh vào `rag-flow-final.mmd`; đã xuất lại ảnh PNG từ mã cuối. |
| 5 | ChatGPT, Mermaid | Vẽ Sequence Diagram | Yêu cầu kịch bản hỏi đáp RAG có kiểm tra quyền, cache, no-result, PII, citation | Có bản cuối `rag-sequence-final.mmd`, nhưng tệp bản AI gốc đang rỗng | Không thể xác định các lỗi của bản AI ban đầu từ tệp hiện có; không dùng bản cuối làm bằng chứng cho đầu ra AI gốc. | Giữ rõ giới hạn bằng chứng; bản cuối thể hiện lời gọi/phản hồi, nhánh không có nguồn và kiểm tra trích dẫn; đã xuất lại ảnh PNG từ mã cuối. |
| 6 | ChatGPT | Hỗ trợ soạn ADR | Dùng cấu trúc Michael Nygard cho quyết định D1 và D3 | Hai ADR có Context, Decision, Alternatives Considered và Consequences | Cần bảo đảm lý do loại từng phương án và hệ quả tiêu cực phản ánh phân tích của sinh viên; trạng thái Proposed được giữ khi chưa chốt. | Bổ sung điều kiện lựa chọn, lý do loại phương án và rủi ro/hệ quả tiêu cực trong từng ADR. |

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

- Không tự đặt ngưỡng TTFT khi đề bài không cung cấp con số.
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
- Yêu cầu và dữ kiện trong đề Lab 4.
- Tính phù hợp với hệ thống IMRS.
- Ràng buộc kỹ thuật và bảo mật.

---

## 4. Kết luận bước phân tích drivers

Việc sử dụng ChatGPT giúp hỗ trợ quá trình
phân tích và tổ chức các yêu cầu kiến trúc
của phân hệ RAG.

Tuy nhiên, drivers do AI đề xuất chỉ là cơ sở
tham khảo. Sinh viên phân loại nguồn, hiệu chỉnh
nội dung và không tự đặt số liệu ngoài bối cảnh.

Các kết quả đã kiểm chứng được trình bày trong
tệp lab4/LAB4_RAG_DESIGN.md.

---

## 5. Tự đánh giá và giới hạn bằng chứng

### 5.1. Hai thiếu sót quan sát được ở đầu ra sơ đồ AI

Trong `lab4/rag-flow-ai-original.mmd`, bản sơ đồ ban đầu
không thể hiện rõ nhánh từ chối truy cập khi AccessPolicy
không cho phép người dùng và nhánh trả lời khi không có
tài liệu phù hợp. Đây là hai thiếu sót riêng; chúng được
bổ sung trong sơ đồ cuối.

Không thể xác nhận thiếu sót tương ứng trong Sequence
Diagram AI ban đầu vì `lab4/rag-sequence-ai-original.mmd`
đang rỗng. Nhật ký không suy diễn lỗi của AI từ bản cuối.

### 5.2. Trạng thái sản phẩm

- Nhiệm vụ 1: bảng drivers đã có nguồn theo ba nhãn và
  không tự đặt ngưỡng TTFT.
- Nhiệm vụ 2: đã có bảng D1-D3 và ba khẳng định được
  đối chiếu với tài liệu Qdrant/pgvector.
- Nhiệm vụ 3: có mã Mermaid cuối và PNG đã xuất lại từ
  đúng hai tệp Mermaid cuối.
- Nhiệm vụ 4: đã có hai ADR; trạng thái Proposed được giữ
  cho đến khi nhóm chốt lựa chọn.
