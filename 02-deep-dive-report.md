# 02 — Deep-Dive Report: Xanh SM Battery-Incident Co-pilot

## 1. Current-state workflow

Sơ đồ nộp bài: [04-workflow-diagram.pdf](04-workflow-diagram.pdf).

Tổng thời gian baseline giả định: **15 phút/sự cố**. Điểm nghẽn là tra cứu trạm sạc (5 phút) và soạn/kiểm tra hướng dẫn (5 phút). Handoff chính: tài xế → tổng đài → dispatcher; dispatcher → đội xe sạc di động khi xe ở ngưỡng nguy hiểm.

## 2. Problem statement (6-field)

| Field | Nội dung |
|---|---|
| 1. Actor / Operator | Điều phối viên tại Trung tâm Điều vận Xanh SM và tài xế báo sự cố pin. |
| 2. Current Workflow | Dispatcher nhận ticket, kiểm tra xe/mức pin, tra GPS và dashboard trạm sạc, soạn chỉ dẫn rồi gọi đội hỗ trợ khi cần. Dữ liệu ở nhiều màn hình, mất khoảng 15 phút/lượt. |
| 3. Bottleneck | Ghép đúng vị trí, loại xe/cổng sạc, trạng thái trạm và diễn đạt chỉ dẫn. Sai tại đây có thể hướng xe pin yếu đi quá xa. |
| 4. Business Impact | Với giả định 80 sự cố/ngày, 15 phút/lượt tương đương 20 giờ dispatcher/ngày; cần xác nhận lại bằng log trước pilot. |
| 5. Success Metric | Median time-to-draft <3 phút; ≥98% đề xuất đúng loại cổng sạc khi dữ liệu đủ; ≥95% nháp được chấp nhận; **0** tin nhắn/điều xe được AI tự gửi. |
| 6. Operational Boundary | AI chỉ đọc dữ liệu xác thực và tạo JSON nháp `[DRAFT_ONLY]`. Pin <5% phải đề xuất `dispatch_mobile_charger`; không chỉ trạm sạc, không bịa dữ liệu, không gửi tin/điều xe. Dispatcher duyệt mọi kết quả. |

## 3. AI fit và future-state flow

**AI fit:** Rule/state machine cho ngưỡng pin, tương thích; LLM feature cho tóm tắt và câu chữ. Không chọn agentic loop vì hành động ảnh hưởng vận hành thực địa và quy trình đã có cấu trúc.

```text
Ticket tài xế → Rule: kiểm tra schema, xe, pin, GPS
  → pin <5%? có → [DRAFT_ONLY] dispatch_mobile_charger → 🟢 Dispatcher duyệt → đội hỗ trợ
  → không → 🔵 API trạm đã xác thực + rule tương thích → 🔵 LLM soạn JSON [DRAFT_ONLY]
           → 🟢 Dispatcher kiểm tra → gửi qua hệ thống hiện hữu
  ↩️ Fallback: thiếu dữ liệu/API lỗi/confidence thấp/dispatcher từ chối → SOP thủ công.
```

API hành động phải tách khỏi service tạo nháp. Audit log lưu input đã che dữ liệu cá nhân, output, phiên bản prompt, người duyệt và quyết định.

## 4. Evaluate

| AI readiness checklist | Trạng thái | Bằng chứng / việc cần làm |
|---|---|---|
| Có dữ liệu mẫu/log sạch để test? | ⚠️ Một phần | Trích xuất ticket 4 tuần, chuẩn hóa xe/cổng sạc và gắn nhãn kết quả đúng. |
| Rủi ro AI sai có kiểm soát? | ✅ | Rule cứng pin <5%, không quyền gửi/dispatch và HITL bắt buộc. |
| Stakeholder sẵn sàng đổi quy trình? | ⚠️ Cần xác nhận | Pilot 2 tuần ở một ca trực, đào tạo dispatcher, đo tỷ lệ chấp nhận. |

### Quyết định: GO — prototype phạm vi hẹp

Chỉ chạy shadow/draft ở một khu vực, dùng dữ liệu trạm read-only, fallback SOP và dashboard audit. Chỉ mở rộng nếu các metric đạt trong 2 tuần liên tiếp và không có vi phạm ranh giới an toàn.
