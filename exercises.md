# Ngày 1 - Bài Tập & Phản Ánh
## Nền Tảng LLM API | Phiếu Thực Hành

**Thời lượng:** 1:30 giờ  
**Cấu trúc:** Lập trình cốt lõi (60 phút) -> Bài tập mở rộng (30 phút)

---

## Phần 1 - Lập Trình Cốt Lõi (0:00-1:00)

Đã triển khai đầy đủ các TODO trong `template.py` và `solution/solution.py`.

Các phần đã hoàn thành:
- `call_openai`: gọi OpenAI Chat Completions API, truyền model, prompt, temperature, top_p, max_tokens và trả về `(response_text, latency_seconds)`.
- `call_openai_mini`: tái sử dụng `call_openai` với model `gpt-4o-mini`.
- `compare_models`: gọi GPT-4o và GPT-4o-mini với cùng prompt, trả về phản hồi, độ trễ và chi phí ước tính của GPT-4o.
- `streaming_chatbot`: chatbot dòng lệnh có streaming, in từng chunk phản hồi và duy trì lịch sử hội thoại gần nhất.
- `retry_with_backoff`: retry hàm khi lỗi với exponential backoff.
- `batch_compare`: chạy `compare_models` cho nhiều prompt.
- `format_comparison_table`: định dạng kết quả so sánh thành bảng text dễ đọc.

Kết quả kiểm thử:

```powershell
& "$env:LOCALAPPDATA\Programs\Python\Python312\python.exe" -m pytest tests/ -v
```

Kết quả: `19 passed in 0.57s`.

---

## Phần 2 - Bài Tập Mở Rộng (1:00-1:30)

### Bài tập 2.1 - Độ Nhạy Của Temperature
Gọi `call_openai` với các giá trị temperature 0.0, 0.5, 1.0 và 1.5 sử dụng prompt **"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2-3 câu)
> Khi temperature thấp như 0.0, phản hồi thường ổn định, trực tiếp và ít thay đổi giữa các lần gọi. Khi tăng lên 0.5 và 1.0, câu trả lời đa dạng hơn về cách diễn đạt và có thể chọn các sự thật khác nhau. Ở mức 1.5, phản hồi sáng tạo hơn nhưng cũng dễ lan man hoặc kém nhất quán hơn.

**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> Tôi sẽ đặt temperature khoảng 0.2 đến 0.4 cho chatbot hỗ trợ khách hàng. Mức này giúp câu trả lời nhất quán, ít bịa đặt và phù hợp hơn với các tình huống cần độ chính xác, nhưng vẫn đủ tự nhiên để không quá máy móc.

---

### Bài tập 2.2 - Đánh Đổi Chi Phí
Xem xét kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người thực hiện 3 lần gọi API, mỗi lần trung bình ~350 token.

**Ước tính xem GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này:**
> Tổng số token mỗi ngày là `10.000 * 3 * 350 = 10.500.000 token`, tương đương `10.500` nhóm 1K token. Với giá output trong đề bài, GPT-4o tốn khoảng `10.500 * $0.010 = $105/ngày`, còn GPT-4o-mini tốn khoảng `10.500 * $0.0006 = $6.30/ngày`. Vì vậy GPT-4o đắt hơn khoảng `105 / 6.30 = 16.67 lần`.

**Mô tả một trường hợp mà chi phí cao hơn của GPT-4o là xứng đáng, và một trường hợp GPT-4o-mini là lựa chọn tốt hơn:**
> GPT-4o xứng đáng hơn khi tác vụ cần suy luận phức tạp, độ chính xác cao, xử lý tình huống nhạy cảm hoặc câu trả lời có ảnh hưởng lớn đến người dùng, ví dụ tư vấn kỹ thuật chuyên sâu hoặc phân tích tài liệu quan trọng. GPT-4o-mini phù hợp hơn cho các tác vụ số lượng lớn, lặp lại, rủi ro thấp như phân loại tin nhắn, trả lời FAQ đơn giản, tóm tắt ngắn hoặc chatbot hỗ trợ bước đầu.

---

### Bài tập 2.3 - Trải Nghiệm Người Dùng với Streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì non-streaming lại phù hợp hơn?** (1 đoạn văn)
> Streaming quan trọng nhất khi phản hồi dài hoặc người dùng cần cảm giác hệ thống đang xử lý ngay lập tức, ví dụ chatbot hội thoại, viết nội dung dài, giải thích từng bước hoặc trợ lý lập trình. Việc hiển thị từng phần giúp giảm cảm giác chờ đợi và cho phép người dùng đọc sớm trước khi phản hồi hoàn tất. Non-streaming phù hợp hơn khi cần nhận kết quả hoàn chỉnh để xử lý tiếp, ví dụ gọi API nền, phân loại dữ liệu, sinh JSON có cấu trúc, kiểm thử tự động hoặc các tác vụ mà giao diện chỉ nên hiển thị kết quả sau khi đã hợp lệ đầy đủ.


## Danh Sách Kiểm Tra Nộp Bài
- [x] Tất cả tests pass: `pytest tests/ -v`
- [x] `call_openai` đã triển khai và kiểm thử
- [x] `call_openai_mini` đã triển khai và kiểm thử
- [x] `compare_models` đã triển khai và kiểm thử
- [x] `streaming_chatbot` đã triển khai và kiểm thử
- [x] `retry_with_backoff` đã triển khai và kiểm thử
- [x] `batch_compare` đã triển khai và kiểm thử
- [x] `format_comparison_table` đã triển khai và kiểm thử
- [x] `exercises.md` đã điền đầy đủ
- [x] Sao chép bài làm vào folder `solution` và đặt tên theo quy định
