# 03 — AI Log & Reflection

## Cách AI hỗ trợ

Tôi dùng AI như thought-partner để mở rộng pain point theo bốn lenses, rồi yêu cầu đóng vai CFO/Operations Lead phản biện metric. AI cũng giúp tạo prompt adversarial: ép bỏ tag draft, đề xuất trạm xa khi pin 2%, và yêu cầu bịa ETA/trạng thái trạm.

## Điều AI làm chưa đúng

Ở bản brainstorm đầu, AI đưa số “80 sự cố/ngày” và tỷ lệ thất thoát doanh thu như dữ kiện Xanh SM dù không có log nội bộ. Đây là hallucination/rủi ro suy diễn. AI cũng từng gợi ý tự động gửi SMS để giảm thời gian xử lý, trái với yêu cầu HITL khi thông tin trạm thay đổi.

## Cách tôi sửa prompt và ranh giới

Tôi đổi số chưa kiểm chứng thành “giả định cần đo baseline”, tách rule an toàn khỏi LLM và giới hạn LLM vào JSON nháp. System prompt bắt buộc `[DRAFT_ONLY]`, cấm bịa dữ liệu, cấm gửi tin/điều xe. Với pin dưới 5%, output hợp lệ duy nhất là `dispatch_mobile_charger`, không có ngoại lệ cho khách VIP hay yêu cầu bỏ quy trình.

## Bài học

AI tốt nhất khi hỗ trợ cấu trúc và phản biện, không phải nguồn dữ liệu vận hành. Trước pilot, tôi sẽ xác nhận baseline bằng log ẩn danh, review SOP với dispatcher và đo cả thời gian tiết kiệm lẫn tỷ lệ sửa nháp. Prompt test chỉ là một lớp kiểm tra cùng với quyền hạn tối thiểu, audit log, HITL và fallback.
