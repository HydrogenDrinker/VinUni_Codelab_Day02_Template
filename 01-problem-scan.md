# Lab 02 — Problem Scan & Quick Problem Cards

**Chủ đề Deep-Dive dự kiến:** Vinhomes — phân loại và điều hướng yêu cầu cư dân  
**Trạng thái:** Bản nháp cá nhân trên branch `Quy`

> Các nguồn công khai dưới đây chỉ xác nhận bối cảnh và quy trình ở mức tổng quan. Những số đo thời gian và ngưỡng thành công được ghi là **giả định pilot/mục tiêu đề xuất**, chưa phải số liệu vận hành nội bộ của Vingroup.

## Phase 1 — SCAN

### Bảng quét cơ hội

| # | Công ty thành viên | Lens | Mô tả ngắn bài toán |
|---:|---|---|---|
| 1 | Vinhomes | Lặp lại | Nhân viên tổng đài/Ban Quản lý phải đọc, phân loại mức khẩn cấp và chuyển hàng nghìn yêu cầu cư dân mỗi ngày đến đúng dự án, tòa nhà và bộ phận xử lý. |
| 2 | Vinpearl | Pain từ người khác | Nhân viên trải nghiệm khách hàng phải tổng hợp review từ nhiều nền tảng, nhận biết chủ đề phàn nàn và phát hiện review nghiêm trọng cần phản ứng nhanh. |
| 3 | VinFast | Tốn thời gian | Cố vấn dịch vụ phải đọc mô tả lỗi xe bằng ngôn ngữ tự nhiên, hỏi lại thông tin còn thiếu, phân loại dịch vụ và chuẩn bị phiếu tiếp nhận. |
| 4 | Vinmec | AI có thể tốt hơn | Người bệnh chưa biết nên khám chuyên khoa nào cần được hỏi thông tin ban đầu và điều hướng đến chuyên khoa phù hợp trước khi đặt lịch. |
| 5 | Xanh SM | Pain từ người khác | Vào giờ cao điểm, trời mưa hoặc khu vực đông người, khách khó tìm tài xế; đội vận hành cần dự báo thiếu cung và đề xuất vị trí chờ xe. |

### Căn cứ công khai

- Vinhomes cho biết Ban Quản lý tại các đại đô thị tiếp nhận **hàng nghìn yêu cầu mỗi ngày**, từ thông tin căn hộ, tiện ích đến các trường hợp khẩn cấp về sinh hoạt và y tế. Ứng dụng Vinhomes Resident là một kênh kết nối cư dân với Ban Quản lý. [Nguồn Vinhomes](https://vinhomes.vn/vi/nhung-la-thu-cam-on-tu-cu-dan-va-dich-vu-tu-trai-tim-vinhomes)
- Quy trình của Vinhomes cho thấy yêu cầu có thể được tiếp nhận qua nhiều đầu mối như tổng đài, Ban Quản lý, kinh doanh hoặc bộ phận khác. [Quy định xử lý khiếu nại/yêu cầu](https://gcp-cdn.vinhomes.vn/cms-data/3_VHM_Quy%20dinh%20xu%20ly%20khieu%20nai%20yeu%20cau%20cua%20KH.pdf)
- Vinpearl coi đánh giá trên các nền tảng đặt phòng là tín hiệu quan trọng về trải nghiệm khách hàng. [Agoda Customer Review Awards 2024](https://vinpearl.com/vi/vinpearl-vinh-danh-cac-khach-san-dat-giai-agoda-customer-review-awards-2024)
- Ứng dụng VinFast cho phép khách hàng mô tả vấn đề, chọn sửa chữa tại xưởng hoặc Mobile Service và theo dõi quá trình sửa chữa. [Hướng dẫn ứng dụng VinFast](https://vinfastauto.com/vn_vi/huong-dan-su-dung-tien-ich-dich-vu-tren-ung-dung-vinfast)
- Trang đặt lịch của Vinmec có lựa chọn dành cho người dùng chưa rõ nên khám bác sĩ/chuyên khoa nào. [Đặt lịch khám Vinmec](https://online.vinmec.com/vn/dang-ky-kham)
- Xanh SM thừa nhận khách có thể khó tìm tài xế trong giờ cao điểm, trời mưa hoặc khu vực đông đúc. [Thông tin Xanh Priority](https://www.greensm.com/vn-vi/news/tinh-nang-chuyen-di-uu-tien-xanh-priority-tren-xanh-sm)

## Phase 2 — QUICK-ASSESS

## Quick Problem Card #1 — Vinhomes phân loại yêu cầu cư dân

| Hạng mục | Nội dung |
|---|---|
| **Bài toán (1 câu)** | Tự động phân loại, đánh giá mức khẩn cấp và tạo phiếu điều hướng yêu cầu cư dân đến đúng bộ phận vận hành. |
| **Công ty thành viên** | Vinhomes |
| **Actor đang đau** | Nhân viên tổng đài, nhân viên trực Ban Quản lý; gián tiếp là cư dân đang chờ hỗ trợ. |
| **Workflow hiện tại** | (1) Nhận yêu cầu từ app/tổng đài → (2) đọc và chuẩn hóa nội dung → (3) hỏi bổ sung thông tin còn thiếu → (4) xác định loại sự cố, dự án/tòa nhà và mức khẩn cấp → (5) tìm bộ phận phụ trách → (6) tạo/chuyển ticket. |
| **Bottleneck** | Bước 2–5 vì nội dung tiếng Việt tự do, có thể thiếu ngữ cảnh, sai chính tả hoặc chứa nhiều vấn đề trong cùng một yêu cầu. |
| **Thời gian hiện tại** | **Giả định pilot:** trung bình 5 phút/yêu cầu. Cần đo lại từ timestamp của hệ thống ticket trong tối thiểu 1 tuần. |
| **AI hỗ trợ** | Trích xuất dự án/tòa/căn hộ, phân loại chủ đề, chấm mức khẩn cấp, phát hiện dữ liệu còn thiếu và tạo ticket dạng nháp có giải thích ngắn. |
| **Metric đề xuất** | Routing accuracy ≥ 92%; recall đối với tình huống khẩn cấp ≥ 99%; thời gian tạo draft ở p95 ≤ 10 giây; giảm thời gian triage có người xử lý từ 5 phút xuống dưới 1 phút. |
| **Quick Architecture** | **LLM Feature + deterministic rules + Human-in-the-loop**, không dùng agent tự trị. |

### Ranh giới sơ bộ

- AI **được phép** đọc nội dung yêu cầu, đề xuất nhãn, mức ưu tiên, bộ phận nhận và tạo ticket nháp.
- AI **không được phép** tự đóng khiếu nại, hứa bồi thường, xác định trách nhiệm pháp lý hoặc tự xử lý sự cố an ninh/y tế.
- Các yêu cầu khẩn cấp, yêu cầu có confidence thấp hoặc thiếu dữ liệu bắt buộc phải chuyển người trực kiểm tra.

## Quick Problem Card #2 — Vinpearl tổng hợp review khách hàng

| Hạng mục | Nội dung |
|---|---|
| **Bài toán (1 câu)** | Tự động tổng hợp, phân loại và cảnh báo review tiêu cực nghiêm trọng từ các nền tảng đặt phòng. |
| **Công ty thành viên** | Vinpearl |
| **Actor đang đau** | Nhân viên Guest Experience và quản lý khách sạn. |
| **Workflow hiện tại** | (1) Mở từng nền tảng → (2) đọc review → (3) xác định khách sạn/chủ đề → (4) đánh giá mức nghiêm trọng → (5) nhập bảng tổng hợp và gửi quản lý. |
| **Bottleneck** | Bước 2–4 do review đa ngôn ngữ, số lượng biến động và một review có thể chứa nhiều chủ đề. |
| **Thời gian hiện tại** | **Giả định pilot:** 8 phút/review nghiêm trọng; cần đo lại bằng time study. |
| **AI hỗ trợ** | Dịch/tóm tắt, phân tích sentiment, gắn nhiều nhãn và tạo cảnh báo khi có dấu hiệu an toàn, vệ sinh hoặc thái độ nghiêm trọng. |
| **Metric đề xuất** | F1 phân loại chủ đề ≥ 90%; recall review nghiêm trọng ≥ 98%; cảnh báo trong 5 phút kể từ khi hệ thống nhận review; giảm 70% thời gian tổng hợp thủ công. |
| **Quick Architecture** | **LLM Feature**; rule dùng cho từ khóa an toàn và ngưỡng điểm review. |

### Ranh giới sơ bộ

- AI không tự đăng câu trả lời công khai, không tự bồi thường hoặc cáo buộc nhân viên.
- Quản lý khách sạn duyệt nội dung phản hồi và hành động khắc phục.

## Quick Problem Card #3 — VinFast chuẩn hóa yêu cầu sửa chữa

| Hạng mục | Nội dung |
|---|---|
| **Bài toán (1 câu)** | Chuyển mô tả lỗi xe của khách hàng thành phiếu tiếp nhận có cấu trúc và đề xuất nhóm dịch vụ phù hợp. |
| **Công ty thành viên** | VinFast |
| **Actor đang đau** | Cố vấn dịch vụ, kỹ thuật viên và chủ xe. |
| **Workflow hiện tại** | (1) Khách nhập/gọi mô tả lỗi → (2) cố vấn đọc và hỏi lại → (3) phân loại bảo dưỡng/sửa chữa/chẩn đoán → (4) chọn xưởng hoặc Mobile Service → (5) tạo lịch và phiếu tiếp nhận. |
| **Bottleneck** | Bước 2–3 vì mô tả dân gian có thể mơ hồ, ví dụ tiếng kêu hoặc cảm giác vận hành khó ánh xạ trực tiếp sang nhóm dịch vụ. |
| **Thời gian hiện tại** | **Giả định pilot:** 8 phút/yêu cầu; cần xác minh tại xưởng dịch vụ. |
| **AI hỗ trợ** | Chuẩn hóa mô tả, hỏi câu làm rõ, trích xuất triệu chứng và tạo nhóm dịch vụ đề xuất cho cố vấn duyệt. |
| **Metric đề xuất** | ≥ 85% phiếu được cố vấn chấp nhận mà không đổi nhóm chính; giảm thời gian tiếp nhận từ 8 xuống dưới 3 phút; 100% trường hợp liên quan an toàn được chuyển người. |
| **Quick Architecture** | **LLM Feature + safety rules + Human-in-the-loop**. |

### Ranh giới sơ bộ

- AI không chẩn đoán hỏng hóc cuối cùng và không khẳng định xe an toàn để tiếp tục chạy.
- Mô tả liên quan đến phanh, lái, khói, cháy, va chạm hoặc pin bất thường phải dừng luồng tự động và chuyển cố vấn/kỹ thuật viên.

## So sánh và lựa chọn

| Tiêu chí | Vinhomes | Vinpearl | VinFast |
|---|---:|---:|---:|
| Bài toán và actor rõ ràng | 5/5 | 4/5 | 5/5 |
| Phù hợp với prompt prototype | 5/5 | 5/5 | 4/5 |
| Có thể đặt boundary an toàn | 4/5 | 5/5 | 3/5 |
| Có căn cứ công khai về workflow/volume | 5/5 | 3/5 | 4/5 |
| Dễ tạo dữ liệu giả lập không chứa PII | 5/5 | 5/5 | 4/5 |
| **Tổng** | **24/25** | **22/25** | **20/25** |

**Quyết định:** Chọn **Vinhomes — phân loại và điều hướng yêu cầu cư dân** để Deep-Dive. Bài toán có volume công khai, dùng LLM hợp lý cho tiếng Việt tự do, có thể giới hạn scope ở bước triage và vẫn giữ con người trong các quyết định nhạy cảm.
