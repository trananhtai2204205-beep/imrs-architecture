
# LAB 4 - RAG Architecture Design

## 1. Architectural Drivers

### 1.1. Bối cảnh

Hệ thống IMRS bổ sung tính năng Trợ lý tra cứu
phác đồ sử dụng kiến trúc RAG trên khoảng
5.000 tài liệu PDF nội bộ.

Hệ thống phải thực hiện tra cứu theo quyền
người dùng và tạo câu trả lời có trích dẫn.

Các ràng buộc:
- Dữ liệu không rời mạng nội bộ bệnh viện.
- 1 GPU RTX 4090.
- Ngân sách 15.000 USD.
- Thời hạn triển khai 4 tháng.

### 1.2. Bảng Architectural Drivers

| Mã | Nhóm | Mô tả | Liên quan ASR/ràng buộc Lab 1 | Mức ưu tiên | Nguồn |
|---|---|---|---|---|---|
| F01 | Chức năng | Tìm kiếm đoạn phác đồ liên quan | Yêu cầu tra cứu IMRS; cần đối chiếu Lab 1 | Cao | Cần xác nhận nguồn từ lịch sử AI |
| F02 | Chức năng | Trả lời có trích dẫn tài liệu nguồn | Yêu cầu chức năng Lab 4 | Cao | Cần xác nhận nguồn từ lịch sử AI |
| F03 | Chức năng | Nạp, trích xuất và chia đoạn PDF | Phân hệ RAG mới | Cao | Cần xác nhận nguồn từ lịch sử AI |
| Q01 | ASR | Đáp ứng yêu cầu TTFT | ASR hiệu năng Lab 1; chưa có ngưỡng cụ thể | Cao | Cần xác nhận nguồn từ lịch sử AI |
| Q02 | ASR | Phân quyền tài liệu theo vai trò | ASR bảo mật; cần đối chiếu Lab 1 | Rất cao | Cần xác nhận nguồn từ lịch sử AI |
| Q03 | ASR | Có khả năng thay thế mô hình embedding | ASR Modifiability; cần đối chiếu Lab 1 | Cao | Cần xác nhận nguồn từ lịch sử AI |
| Q04 | ASR | Kiểm soát thông tin PII | Ràng buộc bảo mật dữ liệu | Rất cao | Cần xác nhận nguồn từ lịch sử AI |
| C01 | Ràng buộc | Dữ liệu không rời mạng nội bộ | Ràng buộc Lab 1 | Rất cao | Cần xác nhận nguồn từ lịch sử AI |
| C02 | Ràng buộc | Hạ tầng có 1 GPU RTX 4090 | Ràng buộc hạ tầng Lab 1 | Cao | Cần xác nhận nguồn từ lịch sử AI |
| C03 | Ràng buộc | Ngân sách 15.000 USD | Ràng buộc ngân sách Lab 1 | Cao | Cần xác nhận nguồn từ lịch sử AI |
| C04 | Ràng buộc | Thời hạn 4 tháng | Ràng buộc tiến độ Lab 1 | Cao | Cần xác nhận nguồn từ lịch sử AI |

### 1.3. Nhận xét

Các drivers được phân thành ba nhóm:
yêu cầu chức năng, ASR và ràng buộc.

Q01, Q02 và Q03 ảnh hưởng trực tiếp đến
lựa chọn công nghệ và tổ chức các thành phần RAG.

C01 yêu cầu embedding, VectorStore và LLM
được triển khai hoàn toàn trong mạng nội bộ.

### 1.4. Những nội dung cần đối chiếu Lab 1

- Ngưỡng TTFT cụ thể.
- ASR hiệu năng và bảo mật.
- Công nghệ database hiện có.
- Các thành phần kiến trúc từ Lab 2-3.

### 1.5. Đánh giá đề xuất của AI

Cần đối chiếu đầu ra AI ban đầu với báo cáo
Lab 1 để xác định driver bị thiếu, bị diễn đạt
sai hoặc được AI tự đề xuất không có căn cứ.

## 2. So sánh phương án kiến trúc RAG

### 2.1. D1 - Lựa chọn Vector Database

| Tiêu chí | Qdrant nội bộ | pgvector nội bộ | Managed Cloud |
|---|---|---|---|
| Độ trễ | Phụ thuộc cấu hình và tải hệ thống | Phụ thuộc truy vấn SQL, index và tài nguyên PostgreSQL | Phụ thuộc kết nối mạng và dịch vụ |
| Bảo mật | Dữ liệu được lưu trong mạng bệnh viện | Dữ liệu được lưu trong mạng bệnh viện | Không phù hợp nếu dữ liệu rời mạng |
| Chi phí | Cần tài nguyên triển khai và vận hành riêng | Có thể tận dụng PostgreSQL nếu đã có | Chi phí dịch vụ và truyền dữ liệu |
| Vận hành | Quản lý một dịch vụ Vector DB riêng | Có thể tận dụng kinh nghiệm PostgreSQL | Nhà cung cấp quản lý hạ tầng |
| Khả năng thay thế | Có thể trừu tượng qua VectorDBConnector | Có thể trừu tượng qua VectorDBConnector | Có nguy cơ phụ thuộc nhà cung cấp |
| Ràng buộc | Phù hợp | Phù hợp có điều kiện | Không phù hợp với triển khai cloud thông thường |

**Đề xuất:** Sử dụng pgvector nếu PostgreSQL
đã có trong hạ tầng IMRS và đáp ứng yêu cầu
về tài nguyên, hiệu năng.

**Lý do:** Có thể giảm số lượng dịch vụ cần
vận hành, tích hợp SQL và quản lý metadata.

**Rủi ro:** Truy vấn vector có thể cạnh tranh
tài nguyên với các nghiệp vụ khác của database.

**Cần xác minh:** PostgreSQL có sẵn trong
kiến trúc Lab 1-3 hay không.

### 2.2. D2 - Lựa chọn Embedding Service

| Tiêu chí | Embedding nội bộ | Embedding Cloud API |
|---|---|---|
| Độ trễ | Phụ thuộc CPU/GPU nội bộ | Phụ thuộc mạng và nhà cung cấp |
| Bảo mật | Có thể giữ dữ liệu trong bệnh viện | Cần gửi nội dung đến API bên ngoài |
| Chi phí | Tài nguyên máy chủ, bảo trì | Phí API theo mức sử dụng |
| Vận hành | Tự triển khai và bảo trì | Nhà cung cấp vận hành |
| Khả năng thay thế | Có thể thay qua interface chung | Phụ thuộc API và định dạng |
| Ràng buộc | Phù hợp | Không phù hợp |

**Đề xuất:** Sử dụng mô hình embedding
mã nguồn mở đa ngôn ngữ chạy nội bộ.

**Lý do:** Không phải gửi tài liệu hoặc câu hỏi
ra API bên ngoài bệnh viện.

**Rủi ro:** Cần đánh giá tài nguyên tính toán.
Khi đổi mô hình embedding, có thể phải tạo
lại vector cho toàn bộ kho tài liệu.

### 2.3. D3 - Lựa chọn cơ chế phân quyền

| Tiêu chí | Pre-filter | Post-filter | Collection theo vai trò |
|---|---|---|---|
| Bảo mật | Kiểm soát quyền trong truy vấn | Rủi ro nếu dữ liệu trái quyền đi vào xử lý trung gian | Cách ly logic theo nhóm |
| Độ trễ | Phụ thuộc hiệu quả truy vấn có lọc | Có thể cần truy xuất bổ sung | Phụ thuộc số collection |
| Số kết quả | Có thể tìm trong tập hợp hợp lệ | Có thể ít hơn k | Phụ thuộc collection được chọn |
| Vận hành | Quản lý metadata và policy | Đơn giản ban đầu nhưng có rủi ro | Phức tạp khi có nhiều vai trò |
| Khả năng thay đổi | Linh hoạt theo metadata | Dễ sửa logic nhưng khó bảo đảm an toàn | Có thể phải tổ chức lại dữ liệu |
| Phù hợp IMRS | Đề xuất lựa chọn | Không ưu tiên | Phù hợp một số trường hợp |

**Đề xuất:** Pre-filter theo allowed_role,
kết hợp AccessPolicy phía máy chủ.

**Lý do:** Tránh đưa các đoạn tài liệu không
được phép truy cập vào ngữ cảnh LLM.

**Rủi ro:** Sai sót metadata hoặc cấu hình
AccessPolicy có thể làm truy xuất sai quyền.

**Biện pháp:** Kiểm thử phân quyền, bảo đảm
vai trò lấy từ phiên xác thực của máy chủ,
và giới hạn quyền truy cập trực tiếp Vector DB.

### 2.4. Giải thích Pre-filter và Post-filter

Pre-filter đưa điều kiện phân quyền vào truy vấn
trước khi kết quả được chuyển cho các bước xử lý
tiếp theo. Cơ chế này hỗ trợ chiến thuật bảo mật
Authorize Actors vì chỉ dữ liệu đáp ứng chính sách
truy cập mới được sử dụng để tạo câu trả lời.

Post-filter thực hiện tìm kiếm trước rồi mới
loại bỏ các kết quả không được phép xem.

Ví dụ: Hệ thống tìm top-5 đoạn tài liệu nhưng
có 2 đoạn không thuộc quyền truy cập của bác sĩ.
Sau khi lọc, chỉ còn 3 đoạn hợp lệ.

Do vậy, post-filter có thể trả về ít hơn k nếu
không có bước truy xuất bổ sung.

Lưu ý: Pre-filter trong logic ứng dụng không
đồng nghĩa mọi chỉ mục ANN đều thực hiện lọc
vật lý trước khi tìm kiếm. Việc bảo vệ dữ liệu
cần được bảo đảm ở các lớp truy cập và xử lý.


## 3. Kiểm chứng khẳng định kỹ thuật của AI

### 3.1. Mục đích

Kiểm chứng các khẳng định kỹ thuật do ChatGPT
đề xuất trong quá trình so sánh phương án
kiến trúc RAG cho hệ thống IMRS.

Việc kiểm chứng dựa trên tài liệu chính thức
của Qdrant và pgvector.

### 3.2. Bảng kết quả kiểm chứng

| Mã | Khẳng định của AI | Kết quả | Giải thích | Nguồn kiểm chứng |
|---|---|---|---|---|
| K01 | Qdrant hỗ trợ lọc metadata bằng payload filtering | Đúng | Qdrant hỗ trợ sử dụng điều kiện lọc payload khi tìm kiếm hoặc truy xuất vector. | https://qdrant.tech/documentation/search/filtering/ |
| K02 | pgvector hỗ trợ tìm kiếm tương đồng Cosine | Đúng | pgvector hỗ trợ toán tử cosine distance và chỉ mục tương ứng. | https://github.com/pgvector/pgvector#querying |
| K03 | pgvector có thể kết hợp SQL WHERE với tìm kiếm vector | Đúng, có lưu ý | pgvector hỗ trợ lọc SQL WHERE. Với approximate indexes, việc lọc có thể xảy ra sau khi quét chỉ mục, ảnh hưởng số kết quả. | https://github.com/pgvector/pgvector#filtering |

### 3.3. Đánh giá khả năng phát hiện vi phạm PII

Trong kết quả so sánh, ChatGPT đã phát hiện
hai phương án không phù hợp với ràng buộc
dữ liệu nội bộ của IMRS:

1. Managed Vector Database Cloud:
   Có thể yêu cầu chuyển dữ liệu hoặc vector
   ra ngoài mạng nội bộ bệnh viện.

2. Embedding Cloud API:
   Yêu cầu gửi văn bản đầu vào đến dịch vụ
   của nhà cung cấp để tạo embedding.

Hai phương án trên không đáp ứng ràng buộc
dữ liệu không được rời mạng nội bộ bệnh viện
trong cách triển khai cloud thông thường.

Do đó, ChatGPT đã nhận diện được ràng buộc
bảo mật quan trọng này trong bài toán.

### 3.4. Nhận xét và điều chỉnh

Qua kiểm chứng, ba khẳng định được lựa chọn
đều đúng về chức năng cơ bản.

Tuy nhiên, đối với pgvector, cần phân biệt
giữa việc áp dụng điều kiện quyền trong truy
vấn SQL và thứ tự thực thi bộ lọc trong
approximate nearest neighbor search.

Không thể kết luận rằng mọi truy vấn có WHERE
đều thực hiện lọc trước khi quét vector index.

Vì vậy, kiến trúc cần có AccessPolicy phía
máy chủ, kiểm tra metadata phân quyền,
và các thử nghiệm xác nhận rằng nội dung
trái quyền không đi vào ngữ cảnh LLM.

### 3.5. Kết luận

Các tài liệu chính thức hỗ trợ lựa chọn
Qdrant hoặc pgvector làm Vector Store nội bộ.

Quyết định cuối cùng cần căn cứ vào hạ tầng
IMRS hiện có, các ASR của Lab 1 và kết quả
đánh giá hiệu năng thực tế.

## 4. Truy vết quyết định thiết kế theo drivers

| Quyết định | Drivers liên quan | Lựa chọn đề xuất | Điều kiện chưa xác minh |
|---|---|---|---|
| D1 - Vector Store | F01, Q01, Q02, Q03, C01, C03 | Ưu tiên pgvector nội bộ có điều kiện | PostgreSQL hiện có, quyền cài extension, tải thực tế |
| D2 - Embedding | F01, Q01, Q03, C01, C02 | Embedding đa ngôn ngữ chạy nội bộ | Chất lượng tiếng Việt chuyên ngành, tài nguyên RTX 4090 |
| D3 - Phân quyền | Q02, Q04, C01 | Pre-filter và AccessPolicy phía máy chủ | Nguồn RBAC/ACL, đồng bộ metadata và cache |

## 5. Sơ đồ kiến trúc RAG - Flowchart

### 5.1. Hai luồng kiến trúc

Luồng Ingestion nạp PDF, trích xuất văn bản, chia đoạn, giảm thiểu PII cần thiết, tạo embedding và ghi chunk cùng metadata quyền vào VectorStore. Metadata quyền cần được xác minh từ nguồn nội bộ trước khi ghi chỉ mục.

Luồng Query xác nhận phiên đăng nhập và quyền ở phía máy chủ, chỉ sử dụng cache còn hợp lệ với ACL và phiên bản tài liệu, tìm kiếm top-k theo bộ lọc quyền, xử lý PII trong ngữ cảnh trước khi gọi LocalLLM và kiểm tra trích dẫn trước khi trả kết quả.

### 5.2. Mã Mermaid đã hiệu chỉnh

```mermaid
flowchart TB
  subgraph ING["Ingestion - Nap tri thuc noi bo"]
    PDF["PDF noi bo"] -->|"Doc tai lieu"| PARSER["DocumentParser"]
    PARSER -->|"Van ban va so trang"| CHUNK["Chunker"]
    CHUNK -->|"Doan van ban"| IPII["PIIRedactor - Ingestion"]
    IPII -->|"Chunk da xu ly PII"| EMBI["EmbeddingService"]
    CHUNK -->|"document_id, page, allowed_role, version"| META["Metadata da kiem tra quyen"]
    EMBI -->|"Vector"| WRITE["VectorDBConnector"]
    IPII -->|"Noi dung chunk"| WRITE
    META -->|"Metadata"| WRITE
    WRITE -->|"SQL/REST - Luu chi muc"| STORE[("VectorStore noi bo")]
  end

  subgraph QUERY["Query - Hoi dap co phan quyen"]
    DOCTOR["Doctor"] -->|"HTTPS - Cau hoi"| ORCH["QueryOrchestrator"]
    ORCH -->|"Kiem tra session phia may chu"| AUTH["AccessPolicy"]
    AUTH -->|"Tu choi"| DENIED["HTTP 403"]
    AUTH -->|"role va ACL version hop le"| CACHE["ResponseCache"]
    CACHE -->|"Cache hit duoc kiem tra quyen"| ANSWER["Tra loi kem trich dan"]
    CACHE -->|"Cache miss/invalid"| EMBQ["EmbeddingService - query"]
    EMBQ -->|"Vector cau hoi"| SEARCH["VectorDBConnector - query"]
    AUTH -->|"allowed_role/ACL scope tu server"| SEARCH
    SEARCH -->|"SQL/REST - top-k + filter"| STORE
    STORE -->|"Chunk va source metadata"| SEARCH
    SEARCH -->|"Khong co nguon hop le"| NORESULT["Khong tim thay tai lieu phu hop"]
    SEARCH -->|"Co nguon, da kiem tra ACL"| QPII["PIIRedactor - Query"]
    QPII -->|"Context da xu ly PII"| PROMPT["PromptBuilder"]
    PROMPT -->|"Prompt + source IDs"| LLM["LocalLLM"]
    LLM -->|"Ban nhap"| VALIDATE["QueryOrchestrator - kiem tra trich dan"]
    VALIDATE -->|"Ket qua hop le"| ANSWER
    VALIDATE -->|"Cache theo user/scope/ACL version"| CACHE
    ANSWER -->|"HTTPS"| DOCTOR
    DENIED -->|"HTTPS"| DOCTOR
    NORESULT -->|"HTTPS"| DOCTOR
    ORCH -->|"Audit metadata toi thieu"| AUDIT["AuditLogger"]
    VALIDATE -->|"Audit ket qua"| AUDIT
  end
```

### 5.3. Ảnh sơ đồ

![RAG Architecture Flowchart](images/rag-flow.png)

**Lưu ý:** Ảnh PNG là bản lưu trước khi hiệu chỉnh mã. Cần xuất lại PNG từ `rag-flow-final.mmd` bằng mermaid.live để đồng bộ hình với mã hiện tại.

### 5.4. Bản gốc để đối chiếu

- `rag-flow-ai-original.mmd`: mã AI ban đầu, lưu nguyên trạng.
- `rag-flow-final.mmd`: bản đã hiệu chỉnh theo yêu cầu Lab 4.

## 6. Sequence Diagram - RAG Query

### 6.1. Mục đích

Sequence Diagram mô tả quá trình tương tác
giữa các thành phần của phân hệ RAG khi
bác sĩ gửi câu hỏi tra cứu phác đồ.

### 6.2. Mô tả quy trình

1. Bác sĩ gửi câu hỏi đến QueryOrchestrator.
2. AccessPolicy xác thực quyền phía máy chủ.
3. Nếu không đủ quyền, hệ thống từ chối truy cập.
4. Nếu đủ quyền, hệ thống kiểm tra ResponseCache.
5. Nếu cache hợp lệ, trả câu trả lời đã lưu.
6. Nếu cache miss, EmbeddingService tạo vector.
7. VectorDBConnector tìm kiếm với allowed_role.
8. Nếu không có tài liệu, trả thông báo phù hợp.
9. Nếu có tài liệu, PIIRedactor xử lý PII.
10. PromptBuilder tạo prompt có thông tin nguồn.
11. LocalLLM sinh câu trả lời.
12. QueryOrchestrator kiểm tra trích dẫn.
13. AuditLogger ghi lại sự kiện cần thiết.
14. Hệ thống trả câu trả lời có trích dẫn.

### 6.3. Hình Sequence Diagram

![RAG Sequence Diagram](images/rag-sequence.png)

### 6.4. Mã Mermaid

```mermaid
sequenceDiagram
    autonumber
    actor Doctor
    participant Q as QueryOrchestrator
    participant A as AccessPolicy
    participant C as ResponseCache
    participant E as EmbeddingService
    participant V as VectorDBConnector
    participant DB as VectorStore
    participant R as PIIRedactor
    participant P as PromptBuilder
    participant L as LocalLLM
    participant G as AuditLogger

    Doctor->>Q: HTTPS - Gui cau hoi
    activate Q
    Q->>A: Xac minh server session, role, ACL version
    A-->>Q: Quyen va pham vi hien tai
    alt Khong du quyen
        Q->>G: Ghi su kien tu choi, khong ghi PII
        G-->>Q: Da ghi nhan
        Q-->>Doctor: HTTP 403 - Access denied
    else Du quyen
        Q->>C: Lookup theo user/scope/ACL va document version
        C-->>Q: Cached response hoac cache miss
        alt Cache hit
            Q->>A: Tai kiem tra quyen va hieu luc tai lieu trong cache
            A-->>Q: Ket qua tai xac nhan
        else Cache miss
            Note over Q,C: Chuyen sang tim kiem moi
        end
        alt Cache hit va van hop le
            Q->>G: Ghi su kien cache hit
            G-->>Q: Da ghi nhan
            Q-->>Doctor: Cau tra loi cache va trich dan hop le
        else Cache miss hoac cache khong hop le
            Q->>E: Tao embedding cua cau hoi
            E-->>Q: Query vector
            Q->>V: top-k + allowed_role va ACL tu server
            V->>DB: Tim kiem co dieu kien quyen
            DB-->>V: Chunks va document_id/page/version
            V-->>Q: Chi chunks duoc phep
            alt Khong tim thay nguon phu hop
                Q->>G: Ghi su kien khong co ket qua
                G-->>Q: Da ghi nhan
                Q-->>Doctor: Khong tim thay tai lieu phu hop
            else Tim thay tai lieu hop le
                Q->>R: Xu ly PII trong ngu canh
                R-->>Q: Context da xu ly PII
                Q->>P: Tao prompt kem source IDs
                P-->>Q: Prompt da rang buoc nguon
                Q->>L: Sinh cau tra loi tren LLM noi bo
                L-->>Q: Ban nhap cau tra loi
                Q->>Q: Kiem tra trich dan, ACL va phien ban nguon
                alt Trich dan khong hop le
                    Q->>G: Ghi su kien validation failed
                    G-->>Q: Da ghi nhan
                    Q-->>Doctor: Khong du nguon de tra loi tin cay
                else Trich dan hop le
                    Q->>C: Luu cache theo scope/ACL/document version
                    C-->>Q: Da ghi nhan
                    Q->>G: Audit toi thieu, khong luu PII
                    G-->>Q: Da ghi nhan
                    Q-->>Doctor: Cau tra loi kem trich dan
                end
            end
        end
    end
    deactivate Q
```

Mã nguồn: `rag-sequence-final.mmd`. Ảnh PNG cần xuất lại từ mã này sau khi kiểm tra trên mermaid.live. Tệp `rag-sequence-ai-original.mmd` trong bản nộp ban đầu trống; không được xem đây là bằng chứng đầu ra AI nguyên gốc.

### 6.5. Nhận xét

Sequence Diagram thể hiện cơ chế kiểm tra
quyền truy cập trước khi truy xuất dữ liệu.

Dữ liệu không được phép truy cập không
được đưa vào ngữ cảnh LLM.

PIIRedactor xử lý thông tin nhạy cảm trước
bước xây dựng prompt và sinh câu trả lời.

Sơ đồ cũng thể hiện các nhánh từ chối truy cập,
cache hit, cache miss và không tìm thấy tài liệu.

Các thành phần được triển khai trong mạng
nội bộ bệnh viện theo ràng buộc của IMRS.

## 7. Đánh giá và hiệu chỉnh sơ đồ do AI sinh

### 7.1. Phương pháp kiểm tra

Sơ đồ Flowchart và Sequence Diagram do ChatGPT
sinh được kiểm tra bằng Mermaid Live Editor.

Các tiêu chí kiểm tra gồm:
- Cú pháp Mermaid hợp lệ.
- Tên thành phần nhất quán với Lab 2-3.
- Kiểm tra quyền truy cập phía máy chủ.
- PIIRedactor đứng trước LocalLLM.
- Có AuditLogger và ResponseCache.
- Các mũi tên có nhãn hành động hoặc giao thức.
- Có nhánh xử lý khi không tìm thấy tài liệu.

### 7.2. Bảng đối chiếu và vấn đề đã phát hiện khi rà soát tệp

| STT | Tệp | Vấn đề quan sát được | Cách hiệu chỉnh | Tình trạng |
|---|---|---|---|---|
| 1 | `rag-flow-ai-original.mmd` -> `rag-flow-final.mmd` | Bản AI gốc gửi từ AccessPolicy sang cache, chưa biểu diễn rõ nhánh từ chối truy cập | Thêm nhánh từ chối và phản hồi đến Doctor | Đã sửa trong mã cuối |
| 2 | `rag-flow-ai-original.mmd` -> `rag-flow-final.mmd` | Bản gốc không có nhánh không tìm thấy tài liệu | Bổ sung nhánh No Results | Đã sửa trong mã cuối |
| 3 | `rag-flow-final.mmd` | Bản cuối cũ chưa thể hiện bước PIIRedactor trong ingestion | Bổ sung bước giảm thiểu PII trước khi tạo embedding | Đã sửa trong mã cuối |
| 4 | `rag-sequence-ai-original.mmd` | Tệp AI gốc rỗng, không thể chứng minh quá trình sửa từ AI gốc | Giữ nguyên tệp rỗng, yêu cầu khôi phục lịch sử nếu còn | **Chưa khắc phục bằng chứng** |
| 5 | `LAB4_RAG_DESIGN.md` | Mục 5 bị mất và có thẻ `<CodeBlock>` không hợp lệ | Chèn mục 4, mục 5 và khối `mermaid` hợp lệ | Đã sửa |
| 6 | Các PNG | Hình ảnh được xuất từ phiên bản sơ đồ trước đây | Xuất lại hình từ mã Mermaid cuối | **Cần thao tác thủ công** |

### 7.2.1. Điểm cần phân biệt về nguồn lỗi

Bảng trên mô tả vấn đề được xác nhận khi so sánh các **tệp lưu trong ZIP**, không tự khẳng định tất cả là lỗi do AI. Phần đánh giá của sinh viên phải ghi rõ vấn đề nào xuất hiện trong phản hồi AI ban đầu và vấn đề nào do thao tác sao chép/chỉnh sửa hoặc quản lý tệp.

### 7.3. Kết luận

Phiên bản sơ đồ cuối cùng được rà soát theo
các yêu cầu chức năng, ASR và ràng buộc của IMRS.

Các chỉnh sửa xác nhận được ghi trong bảng trên.
Những tương tác chưa có bằng chứng phải được kiểm tra lại
trong AI_LOG.md trước khi nộp.

## 8. Tổng kết và quyết định kiến trúc

### 8.1. Kết quả đạt được

Trong Lab 4, phân hệ Trợ lý tra cứu phác đồ
của IMRS đã được phân tích và thiết kế
ở mức kiến trúc thành phần.

Các kết quả chính gồm:

1. Xác định Architectural Drivers cho RAG.
2. So sánh ba điểm quyết định D1, D2, D3.
3. Kiểm chứng các khẳng định kỹ thuật của AI.
4. Thiết kế Flowchart cho Ingestion và Query.
5. Thiết kế Sequence Diagram cho truy vấn RAG.
6. Xây dựng hai Architecture Decision Records.
7. Ghi nhật ký sử dụng AI và các chỉnh sửa.

### 8.2. Phương án kiến trúc đề xuất

D1 - Vector Store:
Ưu tiên pgvector trên PostgreSQL nội bộ,
với điều kiện hạ tầng hiện có và ASR phù hợp.
Qdrant self-hosted là phương án dự phòng.

D2 - Embedding:
Ưu tiên mô hình embedding đa ngôn ngữ
mã nguồn mở, triển khai trong mạng nội bộ.

D3 - Phân quyền:
Sử dụng pre-filter kết hợp AccessPolicy
phía máy chủ và kiểm tra quyền phòng vệ.

### 8.3. Các vấn đề chưa được xác minh

- Hạ tầng PostgreSQL thực tế của IMRS.
- Ngưỡng TTFT cụ thể trong Lab 1.
- Khả năng đáp ứng tải khi chia sẻ RTX 4090.
- Độ trễ truy vấn vector theo bộ dữ liệu thực tế.
- Chất lượng embedding trên thuật ngữ y khoa.
- Chi phí triển khai và vận hành chi tiết.

### 8.4. Bài học về AI-Assisted Architecting

AI hỗ trợ đề xuất phương án, tổ chức bảng
so sánh và sinh mã sơ đồ kiến trúc.

Tuy nhiên, các kết quả AI cần được kiểm chứng,
phản biện và điều chỉnh để phù hợp với ràng buộc
của hệ thống IMRS.

Sinh viên chịu trách nhiệm về các quyết định
kiến trúc cuối cùng và ghi nhận quá trình
sử dụng AI trong AI_LOG.md.
