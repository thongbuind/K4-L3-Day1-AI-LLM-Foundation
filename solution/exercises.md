# K4 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 4 tiếng
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay dòng `*Câu trả lời của bạn*` bằng câu
trả lời thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
Ở những temperature thấp như 0.0 và 0.5 câu trả lời nhìn chung khá nhất quán về việc Việt Nam khá nổi tiếng về cà phê và ẩm thực. Ở những temperature cao như 1.0 và 1.5, mô hình có xu hướng nói nhiều hơn 1 thông tin duy nhất, có mô tả chi tiết hơn về sự thật đó thay vì chỉ nêu ra như trên

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
Nên đặt temperature khoảng 0.3-0.5. Chatbot hỗ trợ khách hàng cần trả lời chính xác, nhất quán và bám sát chính sách/thông tin sản phẩm - không cần sự sáng tạo hay đa dạng trong cách diễn đạt. Temperature thấp giảm rủi ro model hallucination hoặc trả lời khác nhau cho cùng một câu hỏi, điều này quan trọng để giữ uy tín và tính chuyên nghiệp của dịch vụ.

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
GPT-4o phù và hiệu quả với tác vụ phức tạp, yêu cầu suy luận cao còn phiên bản mini phù hợp với tác vụ đơn giản nhưng với khối lượng lớn. 
---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
Phản hồi đầu tiên ngắn hơn, sử dụng những từ ngữ đơn giản, có dùng phép so sánh với 1 sự vật hiện tượng đời sống dễ liên tưởng. Trong khi đó phản hồi thứ 2 dài hơn, chi tiết hơn và bao gồm nhiều từ vựng liên quan đến kỹ thuật kèm giả định người dùng đã có một lượng kiến thức nhất định trong lĩnh vực tài chính.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
Số token thực tế từ tiktoken thường cao hơn công thức ước lượng khoảng 40-60%. Nguyên nhân là bộ mã hoá BPE được huấn luyện chủ yếu trên dữ liệu tiếng Anh, nên các âm tiết tiếng Việt có dấu (như "ư", "ơ", "ế") thường bị tách thành nhiều token con thay vì một token trọn vẹn cho mỗi từ.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
Streaming quan trọng nhất trong các ứng dụng chat trực tiếp, nơi người dùng cần thấy phản hồi xuất hiện ngay lập tức để giảm cảm giác chờ đợi, đặc biệt với câu trả lời dài. Ngược lại, non-streaming phù hợp hơn khi kết quả cần được xử lý/kiểm tra toàn bộ trước khi hiển thị (ví dụ parse JSON, kiểm duyệt nội dung, hoặc gọi API nền không có giao diện tương tác trực tiếp với người dùng).

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
Exponential backoff giãn dần thời gian chờ giữa các lần retry, giúp giảm áp lực lên server đang quá tải và tăng cơ hội request thành công ở lần sau. Nếu hàng nghìn client cùng dùng delay cố định, tất cả sẽ đồng loạt retry lại cùng một thời điểm, tạo ra "thundering herd" khiến server tiếp tục quá tải hoặc sập hẳn.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
Tôi chọn persona "trợ lý học tập cá nhân, thân thiện và kiên nhẫn". System prompt: "Bạn là một gia sư thân thiện, luôn trả lời bằng tiếng Việt, giải thích ngắn gọn (tối đa 3-4 câu) và đưa ví dụ cụ thể khi có thể." Yêu cầu "trả lời ngắn gọn" giúp tránh phản hồi dài dòng gây khó theo dõi cho người học mới, còn chỉ định ngôn ngữ đảm bảo tính nhất quán khi người dùng luôn hỏi bằng tiếng Việt.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
Hạn chế lớn nhất là trợ lý chỉ nhớ 3 lượt hội thoại gần nhất nên dễ quên ngữ cảnh trong các cuộc trò chuyện dài. Cải thiện đề xuất: lưu trữ tóm tắt hội thoại (summary) vào một biến riêng sau mỗi vài lượt, rồi đưa tóm tắt đó vào system prompt của các lượt tiếp theo thay vì giữ toàn bộ lịch sử thô, giúp mở rộng "bộ nhớ" mà không tốn quá nhiều token.

---

## Danh Sách Kiểm Tra Nộp Bài

- [x] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [x] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/`, push lên fork và dán link trên trang bài Lab ở VLearn trước 23:59 ngày 11/09/2026
