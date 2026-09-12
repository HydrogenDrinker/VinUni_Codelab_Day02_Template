# Phase 6 — AI Log & Reflection (Cá nhân)

> **AI Engineer:** Vin Smart Future | **Buổi:** Day 02 — AI Product Scoping Lab
> **Bài toán:** Vinhomes AI Resident Ticket Triage

---

## 1. AI Đã Giúp Gì?

### Tư duy thiết kế bài toán (Thought-Partner)
AI đã giúp mình refine bài toán từ "chatbot CSKH" (scope quá rộng, khó defend) thành "AI Ticket Triage System" (scope hẹp, rõ ràng, defend được).

Cụ thể:
- **Chốt bottleneck đúng chỗ:** AI giúp mình nhận ra bottleneck không phải việc *giải quyết* complaint (sửa thang máy, billing) mà là bước *triage trước* đó — classify + prioritize + route. Đây mới là tác vụ repetitive phù hợp AI.
- **Loại bỏ confidence threshold 0.85:** AI phân tích rằng nếu dùng "confidence ≥ 85%" làm success metric, giảng viên sẽ hỏi ngay "số này lấy ở đâu?" → cần calibration data, validation set → scope phình ra. Thay bằng `uncertainty: low/high` do model tự báo cáo + honest note rằng cần historical data cho production.
- **Phân biệt LLM Feature vs Agent:** AI giải thích rõ tại sao không nên dùng Agentic Loop (overkill, autonomous multi-step execution không cần thiết) — chỉ cần LLM classify + deterministic routing policy.

### Viết System Prompt
AI giúp mình soạn SYSTEM_PROMPT với các boundaries rõ ràng:
- Rule 1: chỉ triage, không resolve
- Rule 2: safety-first — mùi lạ dù cư dân downplay vẫn phải ESCALATE
- Rule 3: không communicate trực tiếp với cư dân

### Debug Code
AI giúp phát hiện và fix 3 bugs trong prototype:
1. Model name sai (gemini-2.5-flash → cần check available models với key)
2. `max_output_tokens=512` quá nhỏ → JSON bị cắt giữa chừng
3. Windows terminal UnicodeEncodeError với emoji

---

## 2. AI Sai Gì? Mình Đã Sửa Gì?

### Sai #1 — Model name không kiểm tra trước
**Vấn đề:** AI liên tục đề xuất model names (`gemini-2.5-flash`, `gemini-2.0-flash`, `gemini-3.0-flash`) mà không check xem key đó có access vào model nào.

**Hậu quả:** Chạy 3–4 lần đều 404 NOT_FOUND, mất thời gian.

**Bài học:** Phải list available models trước (`client.models.list()`) rồi mới hardcode model name vào code. Không thể đoán mò model name.

---

### Sai #2 — Không anticipate rate limit
**Vấn đề:** Sau nhiều lần test, API trả 429 RESOURCE_EXHAUSTED vì free tier của `gemini-3.6-flash` chỉ có **20 requests/ngày**.

**Hậu quả:** Phải switch model sang `gemini-3.5-flash` giữa chừng.

**Bài học:** Khi prototype cần test nhiều cases, phải check quota limit của model trước. Nên add retry logic với exponential backoff từ đầu, không phải sau khi bị lỗi mới thêm.

---

### Sai #3 — ADV-002 safety detection fail lần đầu
**Vấn đề:** Model ban đầu classify "mùi lạ... chắc không sao" là maintenance/low thay vì ESCALATE.

**Hậu quả:** Test fail — AI bị dụ bởi cụm "chắc không sao đâu" của cư dân.

**Phân tích:** Đây là adversarial case điển hình: resident downplays early warning sign. Model không có rule explicit nên follow mô tả của resident.

**Fix:** Thêm vào SYSTEM_PROMPT một paragraph rõ ràng:
> *"If a resident reports an unusual smell... you MUST set is_critical_safety=true REGARDLESS of how the resident describes it."*

**Bài học quan trọng:** LLM không tự nhiên "err on the side of safety" — phải viết rule explicit. Safety-critical behavior cần được specify tường minh trong prompt, không thể dựa vào "common sense" của model.

---

## 3. Nhận Xét Về Việc Dùng AI Làm Thought-Partner

### Điểm mạnh
- **Tốt trong việc brainstorm trade-offs:** AI phân tích nhanh "nếu dùng A thì giảng viên có thể hỏi X, dùng B thì defend bằng Y" — rất hữu ích khi cần prepare cho Q&A.
- **Tốt trong việc phát hiện scope creep:** Khi mình muốn thêm tính năng (confidence score, auto-reply cho cư dân), AI nhắc ngay rằng scope đang phình ra ngoài boundary của bài.
- **Tốt trong việc debug logic:** Phát hiện `_text` vs `raw_text` bug (clean markdown sang biến khác nhưng parse biến cũ).

### Điểm yếu / Cần verify
- **Không biết environment của mình:** AI đề xuất model name mà không biết key có access vào model nào → phải tự check.
- **Không nhớ context giữa các session:** Mỗi lần mới phải re-explain bài toán.
- **Đôi khi over-confident:** AI đề xuất fix nhưng fix đó lại tạo ra bug mới (replacement chunks bị overlap) → luôn cần review diff trước khi apply.

### Tổng kết
AI hiệu quả nhất khi dùng như **thought-partner để reason về trade-offs** và **rubber duck để test logic của mình**, không phải như oracle ra quyết định. Mọi quyết định quan trọng (scope, metric, architecture) vẫn cần mình review và confirm.

---

## 4. Timeline Sử Dụng AI Trong Buổi Lab

| Giai đoạn | AI hỗ trợ gì | Mình tự làm gì |
|-----------|-------------|----------------|
| Scoping | Refine bottleneck, loại bỏ confidence threshold | Quyết định chọn Vinhomes và bài toán triage |
| Thiết kế flow | Draft current/future flow diagrams | Verify flow logic với thực tế vận hành |
| Viết prompt | Draft SYSTEM_PROMPT boundaries | Thêm SAFETY-FIRST rule sau khi ADV-002 fail |
| Code | Implement llm_triage + routing_policy | Debug model name, fix encoding, test runner |
| Debug | Phân tích lỗi JSON parse, rate limit | Quyết định switch model, thêm retry logic |
