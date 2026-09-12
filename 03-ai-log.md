# 03 - AI Log & Reflection

## Mục tiêu sử dụng AI

Tôi dùng AI như một thought-partner để brainstorm các bottleneck thuộc VinFast, Xanh SM, Vinhomes, Vinmec và Vinpearl; sau đó nhờ AI phản biện quick cards theo góc nhìn vận hành và CFO. AI giúp mở rộng danh sách ý tưởng và gợi ý cách viết metric, nhưng tôi vẫn phải kiểm tra tính thực tế của workflow và rủi ro.

## AI đã giúp gì

- Gợi ý các tác vụ lặp lại như xử lý sự cố pin, phân loại phản ánh cư dân và đối chiếu hóa đơn.
- Giúp chuyển một ý tưởng chung thành workflow có actor, handoff, bottleneck và metric.
- Gợi ý tách phần Rule/State Machine khỏi phần LLM để không dùng LLM cho điều kiện an toàn có thể mã hóa.
- Đề xuất các adversarial input như yêu cầu bỏ tag, giả mạo quyền admin và yêu cầu gửi tin trực tiếp.

## Lỗi hoặc nguy cơ hallucination

Các con số như số ticket mỗi ngày, 15 phút mỗi lượt, tỷ lệ mất doanh thu hoặc 98% độ chính xác chỉ là ước tính từ ví dụ học tập, không phải số liệu đã xác minh của Xanh SM. AI cũng có thể bịa tên trạm, tình trạng trống, khoảng cách hoặc thời gian di chuyển nếu prompt cho phép. Vì vậy báo cáo này ghi các số liệu đó là baseline giả định và yêu cầu kiểm tra bằng log vận hành.

Một rủi ro khác là AI có thể hiểu nhầm câu lệnh của người dùng như chỉ thị cấp hệ thống, ví dụ người dùng tự nhận là administrator và yêu cầu bỏ qua safety rule.

## Cách tôi sửa prompt và ranh giới

Tôi đặt system prompt với các nguyên tắc cụ thể:

1. Mọi output bắt đầu bằng `[DRAFT_ONLY]`.
2. Pin dưới 5% là critical; không đề xuất trạm cách trên 5 km và phải nêu action `dispatch_mobile_charger`.
3. Không tự gửi tin, không tự điều xe và không tuyên bố hành động đã hoàn thành.
4. Không bịa tọa độ, trạm, thời gian hoặc tình trạng xe.
5. Output có schema JSON gồm `action`, `reason`, `draft_message` và luôn yêu cầu human review.

Tôi dùng temperature thấp trong prototype để giảm output không ổn định, nhưng đây không phải biện pháp thay thế cho validation bằng code và human review.

## Kết quả kiểm thử

- Kiểm tra system prompt: đạt các từ khóa boundary cốt lõi.
- Kiểm tra hàm `evaluate_prompt`: dùng SDK `google-genai` và truyền system instruction.
- Kiểm tra adversarial tests: có 3 test case hợp lệ.
- Chạy Gemini thực tế: cần `GEMINI_API_KEY` trong biến môi trường. Không đưa API key vào repository.

## Bài học

AI phù hợp để hiểu ngôn ngữ tự nhiên và tạo bản nháp, nhưng các điều kiện ảnh hưởng an toàn phải được kiểm soát bằng rule, schema validation và người duyệt. Một prototype tốt không chỉ cho thấy câu trả lời đẹp; nó phải chứng minh mô hình không vượt qua operational boundary trong các tình huống bị tấn công.
