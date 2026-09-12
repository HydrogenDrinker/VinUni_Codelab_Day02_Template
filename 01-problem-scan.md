# 01 - Problem Scan & Quick Problem Cards

## Bối cảnh

Tôi đóng vai AI Product Engineer tại Vin Smart Future. Phạm vi khảo sát tập trung vào các quy trình vận hành có tính lặp lại, tốn thời gian, có thể nâng cấp bằng AI hoặc gây pain cho nhân viên/khách hàng.

## Phase 1: SCAN

| # | Công ty | Lens | Bài toán / bottleneck |
|---|---|---|---|
| 1 | Xanh SM | Tốn thời gian | Điều phối viên xử lý thủ công báo cáo xe điện sắp hết pin hoặc gặp sự cố sạc. |
| 2 | Xanh SM | Pain từ stakeholder | Phân tích lý do khách hủy chuyến từ cuộc gọi và ghi chú tài xế chưa nhất quán. |
| 3 | VinFast | Lặp lại | Đối chiếu dữ liệu phiên sạc với hóa đơn từ các trạm đối tác. |
| 4 | Vinhomes | AI-upgrade | Phân loại phản ánh cư dân và chuyển đúng ban quản lý còn chậm. |
| 5 | Vinmec | Tốn thời gian | Bác sĩ mất thời gian soạn bản tóm tắt xuất viện từ nhiều nguồn hồ sơ. |
| 6 | Vinpearl | Pain từ stakeholder | Quản lý phải đọc nhiều review để phát hiện phàn nàn khẩn cấp. |

Các số phút và tỷ lệ trong các thẻ dưới đây là giả định ban đầu để scoping, cần xác minh bằng log vận hành trước khi triển khai.

## Phase 2: Quick Problem Cards

### Card 1 - Xanh SM: Xử lý sự cố pin thực địa

- **Bài toán:** Khi tài xế báo pin thấp, dispatcher phải tra cứu vị trí, trạm sạc và tự soạn hướng dẫn.
- **Actor:** Tài xế và dispatcher của trung tâm điều vận.
- **Workflow:** Nhận cuộc gọi -> ghi nhận biển số/pin/vị trí -> tìm trạm phù hợp -> soạn tin -> dispatcher duyệt và gửi.
- **Bottleneck:** Tra cứu trạm và soạn tin, ước tính 10-15 phút/lượt.
- **AI hỗ trợ:** Trích xuất thông tin từ báo cáo, tạo draft hướng dẫn và áp dụng rule pin dưới 5%.
- **Metric:** Giảm thời gian xử lý từ 15 xuống dưới 3 phút; 98% draft có đúng biển số, pin và trạm sau review.
- **Architecture:** Rule/State Machine + LLM Feature; không dùng agent tự trị.

### Card 2 - Vinhomes: Phân loại phản ánh cư dân

- **Bài toán:** Phản ánh mất nước, hỏng đèn hoặc tiếng ồn phải được đọc và chuyển thủ công.
- **Actor:** Nhân viên CSKH và ban quản lý tòa nhà.
- **Workflow:** Nhận phản ánh -> đọc nội dung -> xác định loại/vị trí -> chuyển đội xử lý -> cập nhật trạng thái.
- **Bottleneck:** Phân loại không đồng nhất, ước tính 8-12 phút/ticket.
- **AI hỗ trợ:** Phân loại chủ đề, trích xuất tòa nhà/căn hộ và draft route; người phụ trách vẫn duyệt.
- **Metric:** 90% ticket được phân loại dưới 30 giây; giảm chuyển nhầm xuống dưới 3%.
- **Architecture:** Rule router + LLM classification.

### Card 3 - VinFast: Đối chiếu phiên sạc và hóa đơn

- **Bài toán:** Nhân viên tài chính đối chiếu thủ công dữ liệu phiên sạc với hóa đơn đối tác.
- **Actor:** Nhân viên tài chính và vận hành trạm.
- **Workflow:** Tải file -> chuẩn hóa mã phiên -> đối chiếu kWh/thời gian/phí -> tìm lệch -> lập danh sách xử lý.
- **Bottleneck:** Mã và định dạng khác nhau, ước tính vài giờ mỗi đợt.
- **AI hỗ trợ:** Chuẩn hóa mô tả và giải thích các dòng lệch; số tiền cuối cùng vẫn do rule và người duyệt quyết định.
- **Metric:** Xử lý 95% dòng trong 30 phút; tỷ lệ false positive dưới 5%.
- **Architecture:** Rule-based matching + LLM explanation.

## Lựa chọn bài toán

Chọn Card 1 cho deep-dive vì có tác động thời gian thực, metric dễ đo và rủi ro có thể giới hạn bằng rule pin, giới hạn khoảng cách, human review và fallback thủ công. Hai card còn lại phù hợp cho các phase sau nhưng cần thêm dữ liệu lịch sử và kiểm soát quyền truy cập.
