# Phân tích kết quả A/B Testing: Prompt V1 vs Prompt V2

Báo cáo này phân tích sự khác biệt về hiệu suất của hai phiên bản system prompt dựa trên các chỉ số đánh giá của RAGAS.

## 1. Kết quả RAGAS

Dựa trên quá trình đánh giá 50 câu hỏi, điểm số thu được như sau:

| Metric | Prompt V1 (Ngắn gọn) | Prompt V2 (Cấu trúc, Chuyên sâu) | Winner |
|---|---|---|---|
| **Faithfulness** | 0.9237 | **0.9397** | V2 |
| **Answer Relevancy** | **0.9089** | 0.8929 | V1 |
| **Context Recall** | 1.0000 | 1.0000 | Hòa |
| **Context Precision**| 0.9417 | 0.9417 | Hòa |

## 2. Phân tích chi tiết

### a. Context Recall & Context Precision (Bằng nhau)
Hai chỉ số đo lường hiệu năng của khâu truy xuất tài liệu (Retrieval) đạt kết quả giống hệt nhau ở cả hai phiên bản (Recall = 1.0, Precision = 0.9417). 
**Lý do:** Khâu truy xuất tài liệu diễn ra *trước* khi câu hỏi và tài liệu được đưa vào Prompt. Cả V1 và V2 đều sử dụng chung một `vectorstore` và cấu hình retriever (`k=3`), do đó chất lượng tài liệu lấy ra là như nhau.

### b. Faithfulness (Tính trung thực - V2 thắng)
Chỉ số này đo lường mức độ câu trả lời bám sát vào nội dung context được cung cấp (không bịa đặt hay suy diễn). **Prompt V2 đạt điểm cao hơn (0.9397 so với 0.9237)**.
**Lý do:** Prompt V2 được thiết kế với persona là "chuyên gia phân tích thông tin" kèm theo yêu cầu khắt khe: *"xác định các facts liên quan"*, *"có tổ chức"*, và đặc biệt là chỉ thị *"Không suy đoán ngoài context"*. Sự nghiêm ngặt trong cách hành văn này giúp mô hình LLM hạn chế tối đa việc hallucinate (ảo giác) và tập trung trích xuất chính xác fact từ tài liệu.

### c. Answer Relevancy (Độ liên quan - V1 thắng)
Chỉ số này đo lường việc câu trả lời có trực tiếp giải quyết câu hỏi của người dùng hay không, hay lại trả lời vòng vo. **Prompt V1 đạt điểm cao hơn (0.9089 so với 0.8929)**.
**Lý do:** Prompt V1 có yêu cầu *"Trả lời ngắn gọn (2-4 câu)"*. Việc giới hạn độ dài ép mô hình phải đi thẳng vào trọng tâm câu hỏi. Trong khi đó, Prompt V2 yêu cầu câu trả lời "có tổ chức" và "phân tích", đôi khi khiến LLM sinh ra các câu mào đầu hoặc thông tin bổ sung rườm rà, làm loãng đi trọng tâm trực tiếp của câu hỏi (dẫn đến điểm relevancy bị giảm nhẹ).

## 3. Kết luận
- **Prompt V2** an toàn hơn và phù hợp cho các hệ thống đòi hỏi tính chính xác thông tin tuyệt đối (ví dụ: y tế, tài chính, tra cứu luật) vì nó có tính trung thực (faithfulness) rất cao.
- **Prompt V1** mang lại trải nghiệm người dùng tốt hơn cho các chatbot hỏi đáp thông thường nhờ vào sự ngắn gọn, trực diện (answer relevancy cao). 
- Sự khác biệt điểm số này chứng minh rõ ràng vai trò quan trọng của kỹ thuật Prompt Engineering và Versioning trong việc tối ưu hóa LLM cho từng Use-case cụ thể.
