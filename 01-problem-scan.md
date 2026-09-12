# 01 — Problem Scan & Quick Cards

## Phase 1 — Scan

Các số liệu thời gian là giả định để lập baseline cho prototype; cần xác nhận bằng log vận hành trước khi triển khai.

| # | Công ty thành viên | Lens | Bài toán / bottleneck |
|---:|---|---|---|
| 1 | Xanh SM | Tốn thời gian | Điều phối viên tra GPS, trạm sạc và soạn hướng dẫn khi tài xế báo pin thấp; ước tính 12–15 phút/sự cố. |
| 2 | Vinhomes | AI-upgrade | CSKH đọc, phân loại và chuyển khiếu nại cư dân thủ công; phản hồi đầu tiên chậm vào giờ cao điểm. |
| 3 | VinFast | Lặp lại | Đối soát hóa đơn sạc với log trạm và đối tác theo ca, dễ sai mã giao dịch. |
| 4 | Vinmec | Tốn thời gian | Bác sĩ tóm tắt hồ sơ xuất viện từ nhiều ghi chú lâm sàng, cần kiểm tra kỹ trước khi ký. |
| 5 | Vinpearl / VinWonders | Pain từ stakeholder | Tổng đài tra cứu chính sách vé, thời tiết và đổi lịch cho khách quốc tế qua nhiều hệ thống. |
| 6 | Xanh SM | Lặp lại | Tổng hợp lý do hủy chuyến từ ghi chú và cuộc gọi để tìm khu vực có lỗi điều vận. |

## Phase 2 — Quick-assess

### Card 1 — Xanh SM: xử lý sự cố pin tại hiện trường

| Mục | Nội dung |
|---|---|
| Actor | Tài xế đang chờ hỗ trợ và điều phối viên Xanh SM. |
| Workflow hiện tại | 1) Tài xế gọi/tạo ticket → 2) xác thực xe & mức pin → 3) tra GPS → 4) tra trạm sạc → 5) soạn chỉ dẫn hoặc gọi xe sạc di động. |
| Bottleneck | Tra trạm tương thích/còn chỗ và viết chỉ dẫn: khoảng 10 trong 15 phút/lượt. |
| AI hỗ trợ | Rule kiểm ngưỡng pin + truy vấn dữ liệu xác thực; LLM chỉ soạn bản nháp hướng dẫn. |
| Metric | Median time-to-draft <3 phút; ≥98% nháp đúng loại cổng sạc; 0 lần gửi tự động. |
| Architecture | Rule / state machine + LLM feature; không dùng agent tự trị. |

### Card 2 — Vinhomes: phân luồng khiếu nại cư dân

| Mục | Nội dung |
|---|---|
| Actor | Nhân viên CSKH và ban quản lý tòa nhà. |
| Workflow hiện tại | 1) Đọc ticket → 2) xác định tòa/căn → 3) gán nhóm kỹ thuật/tài chính → 4) soạn phản hồi đầu → 5) theo dõi SLA. |
| Bottleneck | Phân loại tiếng Việt không chuẩn và thiếu thông tin căn hộ, khoảng 6 phút/ticket. |
| AI hỗ trợ | LLM trích xuất thực thể, đề xuất nhãn/nháp; nhân viên xác nhận trước khi tạo ticket. |
| Metric | ≥85% ticket được đề xuất nhãn dưới 10 giây; ≥95% nhãn được nhân viên chấp nhận. |
| Architecture | LLM feature với rule router và human review. |

### Card 3 — Vinmec: tóm tắt xuất viện có kiểm duyệt

| Mục | Nội dung |
|---|---|
| Actor | Bác sĩ điều trị và điều dưỡng hoàn tất hồ sơ. |
| Workflow hiện tại | 1) Thu ghi chú → 2) đọc diễn biến → 3) viết tóm tắt → 4) kiểm tra thuốc/lịch hẹn → 5) bác sĩ ký. |
| Bottleneck | Tổng hợp nhiều ghi chú và đối chiếu thuốc, 20–30 phút/bệnh nhân. |
| AI hỗ trợ | LLM tạo bản nháp có trích dẫn nguồn; bác sĩ kiểm tra và ký. |
| Metric | Giảm thời gian soạn nháp 30%; 100% văn bản phải có bác sĩ phê duyệt. |
| Architecture | LLM feature; không dùng để chẩn đoán hay kê đơn. |

## Lựa chọn deep-dive

Chọn **Card 1** vì tác vụ có dữ liệu đầu vào rõ, lợi ích thời gian trực tiếp và ranh giới an toàn có thể kiểm thử. Card 2 cần baseline chất lượng ticket; Card 3 có rủi ro lâm sàng cao hơn nên chỉ phù hợp sau khi hoàn thiện governance dữ liệu y tế.
