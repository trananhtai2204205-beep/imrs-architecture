
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
| 1 | ChatGPT | Phân tích Architectural Drivers cho phân hệ RAG | Yêu cầu AI đóng vai kiến trúc sư phần mềm, phân tích hệ thống IMRS với 5.000 PDF nội bộ, 1 GPU RTX 4090, ngân sách 15.000 USD, thời hạn 4 tháng; chia drivers thành chức năng, ASR và ràng buộc. | AI hỗ trợ đề xuất các drivers liên quan đến tra cứu tài liệu, trích dẫn nguồn, TTFT, phân quyền theo vai trò, khả năng thay thế embedding, bảo vệ PII và triển khai nội bộ. | Cần đối chiếu từng driver với Lab 1. Chưa có giá trị TTFT cụ thể nên không được tự đặt ngưỡng hiệu năng. Cần phân biệt yêu cầu bắt buộc với đề xuất bổ sung của AI. | Xây dựng bảng drivers có cột Nguồn; đánh dấu những nội dung cần kiểm tra trong LAB1_REPORT.md; bổ sung hoặc sửa các driver sau khi đối chiếu. |
| 2 | ChatGPT | So sánh D1–D3 | Yêu cầu so sánh Qdrant/pgvector/cloud, embedding nội bộ/cloud, pre-/post-filter/collections | Đầu ra AI đã được lưu trong bản Markdown so sánh D1–D3; AI nêu ưu tiên pgvector có điều kiện, embedding nội bộ và pre-filter | AI nhận diện cloud vi phạm giới hạn dữ liệu nội bộ; giả định PostgreSQL cần xác minh; so sánh hiệu năng chỉ định tính | Bổ sung ghi chú điều kiện và phân tích lọc ANN vào báo cáo |
| 3 | ChatGPT, Qdrant Docs, pgvector README | Kiểm chứng ba khẳng định kỹ thuật trong bảng so sánh RAG | Đối chiếu khả năng payload filtering của Qdrant, Cosine Distance và SQL WHERE của pgvector với tài liệu chính thức | AI khẳng định Qdrant hỗ trợ lọc metadata; pgvector hỗ trợ tìm kiếm Cosine và SQL filtering | Ba khẳng định đúng về chức năng cơ bản. Tuy nhiên, cần làm rõ việc lọc với approximate indexes của pgvector. AI đã nhận diện phương án Cloud vi phạm ràng buộc dữ liệu nội bộ | Bổ sung lưu ý về ANN filtering, nguồn tài liệu chính thức và yêu cầu kiểm tra quyền phía máy chủ vào LAB4_RAG_DESIGN.md |
| 4 | ChatGPT, Mermaid Live | Vẽ Flowchart RAG | Yêu cầu sơ đồ hai luồng, phân quyền, cache, PII, audit | Bản gốc lưu tại `lab4/rag-flow-ai-original.mmd` | Qua đối chiếu tệp thấy chưa có nhánh từ chối quyền/không có kết quả; cần phân biệt lỗi từ AI gốc và lỗi chỉnh tay | Bổ sung nhánh trong `rag-flow-final.mmd`, làm rõ luồng ingestion và cache; cần xuất lại ảnh |
| 5 | ChatGPT, Mermaid Live | Vẽ Sequence Diagram | Yêu cầu kịch bản hỏi đáp RAG có kiểm tra quyền, cache, no-result, PII, citation | Có bản cuối `rag-sequence-final.mmd`, nhưng tệp bản AI gốc đang rỗng | Không thể xác nhận chính xác những lỗi AI ban đầu nếu không khôi phục lịch sử | Đã cải thiện luồng cache, nhánh kiểm tra trích dẫn; cần xuất lại ảnh và bổ sung bản AI gốc nếu có |
| 6 | ChatGPT | Phản biện hai ADR (chưa đủ bằng chứng hoàn thành) | Prompt phản biện ADR được nêu trong hướng dẫn | Chưa lưu phản hồi AI thực tế trong ZIP | Chưa thể kết luận đã thực hiện | Cần thực hiện/lưu phản biện thật trước khi tính đây là một mục hoàn tất |

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

Các kết quả đã kiểm chứng được trình bày trong
tệp lab4/LAB4_RAG_DESIGN.md.

---

## 5. Các tương tác tiếp theo

Tình trạng hồ sơ các tương tác đã có, các bước cần hoàn thiện thêm:

- Nhiệm vụ 2: So sánh D1, D2, D3.
- Nhiệm vụ 2: Kiểm chứng khẳng định kỹ thuật.
- Nhiệm vụ 3: Sinh và hiệu chỉnh Flowchart.
- Nhiệm vụ 3: Sinh và hiệu chỉnh Sequence Diagram.
- Nhiệm vụ 4: Soạn thảo và phản biện ADR.


