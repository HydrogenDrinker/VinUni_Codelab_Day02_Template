# 02 — Problem Deep-Dive Report: Xanh SM Intelligent Battery Dispatcher

> **Khóa học:** AI Product Engineering & Scoping  
> **Đơn vị:** Vin Smart Future — Tập đoàn Vingroup  
> **Dự án:** Trợ lý Điều vận Thông minh Sự cố Sạc Pin Thực địa (Xanh SM Smart Battery Co-Pilot)  
> **Tài liệu nộp bài:** Deliverable 02 — Problem Deep-Dive & Evaluation Report  

---

## 🏛️ 1. Giới Thiệu & Bối Cảnh Dự Án

Là đơn vị công nghệ nòng cốt của Vingroup, **Vin Smart Future** hợp tác chặt chẽ cùng **Công ty Cổ phần Di chuyển Xanh và Thông minh GSM (Xanh SM)** nhằm số hóa và nâng cao năng lực vận hành của đội xe thuần điện lớn nhất Việt Nam.

Trong các hoạt động vận hành hằng ngày tại các đô thị lớn (Hà Nội, TP.HCM, Đà Nẵng), sự cố cạn kiệt pin hoặc trục trặc trong quá trình tìm kiếm trụ sạc trống là điểm nghẽn nghiêm trọng nhất. Việc điều phối viên phải thực hiện thủ công toàn bộ các bước định vị, đối chiếu loại cổng sạc và soạn văn bản hướng dẫn dẫn đến thời gian xe dừng đỗ chờ đợi kéo dài, tăng nguy cơ xe chết máy giữa đường và giảm trực tiếp chỉ số hài lòng của khách hàng cũng như tài xế.

Báo cáo này phân tích chi tiết quy trình hiện tại, thiết lập bài toán theo chuẩn 6 trường thông tin của Vin Smart Future, thiết kế quy trình tương lai tích hợp AI an toàn có cơ chế người duyệt (Human-in-the-loop) và đưa ra đánh giá sẵn sàng triển khai.

---

# 🏗️ Phase 3 — DEEP-DIVE: Phân Tích Chuyên Sâu

## 3.1. Current-State Workflow Mapping (Quy Trình Thủ Công Hiện Tại)

Quy trình xử lý sự cố sạc pin thực địa hiện tại trải qua 5 bước tuần tự hoàn toàn thủ công:

```text
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│ Bước 1          │     │ Bước 2          │     │ Bước 3          │     │ Bước 4          │
│ Tiếp nhận cuộc  │     │ Tra cứu định    │     │ Tra cứu trạm    │     │ Soạn thảo tin   │
│ gọi báo sự cố   │ ──> │ vị GPS xe       │ ──> │ sạc VinFast     │ ──> │ nhắn chỉ dẫn    │
│                 │     │                 │     │ còn trụ trống   │     │ gửi qua App     │
│ 👤 Dispatcher   │     │ 👤 Dispatcher   │     │ 👤 Dispatcher   │     │ 👤 Dispatcher   │
│ ⏱ 2 phút        │     │ ⏱ 2 phút        │     │ ⏱ 5 phút 🔴     │     │ ⏱ 5 phút 🔴     │
│ In: Cuộc gọi    │     │ In: Biển số xe  │     │ In: Toạ độ GPS  │     │ In: Tên trạm,   │
│ Out: Ticket     │     │ Out: Toạ độ     │     │ Out: Địa chỉ    │     │      đường đi   │
│ 🔄 Handoff 1    │     │                 │     │ 🔄 Handoff 2    │     │ Out: SMS/Chat   │
└─────────────────┘     └─────────────────┘     └─────────────────┘     └─────────────────┘
                                                                                 │
                                                                                 ▼
                                                                        ┌─────────────────┐
                                                                        │ Bước 5          │
                                                                        │ Điều phối xe    │
                                                                        │ cứu hộ sạc pin  │
                                                                        │ lưu động (nếu   │
                                                                        │ pin < 5%)       │
                                                                        │ 👤 Dispatcher   │
                                                                        │ ⏱ 1 phút        │
                                                                        │ 🔄 Handoff 3    │
                                                                        └─────────────────┘

🔴 = Bottlenecks (Điểm nghẽn cổ chai tốn thời gian nhất)
🔄 = Handoff (Điểm chuyển giao thông tin giữa người và hệ thống hoặc giữa các bộ phận)
⏱ Tổng thời gian xử lý trung bình mỗi sự cố: 15 phút.
```

### Chi tiết các bước và điểm nghẽn:
1. **Bước 1: Tiếp nhận cuộc gọi (2 phút):** Tài xế hoảng loạn gọi điện về tổng đài báo xe sắp hết pin hoặc không cắm sạc được. Điều phối viên lắng nghe và mở ticket sự cố.
2. **Bước 2: Tra cứu định vị GPS (2 phút):** Điều phối viên copy biển số xe sang hệ thống Fleet Telematics của VinFast/GSM để lấy tọa độ kinh độ/vĩ độ hiện tại.
3. **Bước 3: Tra cứu trạm sạc VinFast phù hợp (5 phút - 🔴 Bottleneck 1):** Điều phối viên mở bản đồ trạm sạc VinFast, tìm các trạm trong bán kính lân cận, kiểm tra xem trụ sạc DC nhanh còn trống không và cổng sạc (CCS2) có tương thích với dòng xe của tài xế (VF5, VF e34, VF8, VF9) hay không.
4. **Bước 4: Soạn thảo tin nhắn hướng dẫn (5 phút - 🔴 Bottleneck 2):** Điều phối viên tự gõ văn bản tiếng Việt hướng dẫn đường đi ngắn nhất, dặn dò an toàn khi pin yếu, và gửi qua SMS/App tài xế. Do áp lực thời gian, câu chữ dễ nhầm lẫn hoặc thiếu thông tin trụ sạc.
5. **Bước 5: Điều phối xe sạc pin lưu động (1 phút):** Nếu tài xế báo pin dưới 5% và trạm sạc gần nhất quá xa, điều phối viên phải liên hệ riêng với đội xe sạc pin lưu động (Mobile Charging Vehicle) của VinFast.

---

## 3.2. Problem Statement (6-field) — Tiêu Chuẩn Vin Smart Future

Bảng đặc tả bài toán theo khung 6 trường tiêu chuẩn của Vin Smart Future:

| Trường thông tin (Field) | Mô tả chi tiết theo nghiệp vụ thực tế |
|:---|:---|
| **1. Actor / Operator (Người vận hành)** | **Điều phối viên (Dispatcher)** tại Trung tâm Điều vận Xanh SM Hà Nội & TP.HCM, phối hợp trực tiếp với **Tài xế taxi điện Xanh SM** đang hoạt động thực địa. |
| **2. Current Workflow (Quy trình hiện tại)** | Khi xe báo nguy cơ cạn pin, tài xế gọi điện thoại về tổng đài. Điều phối viên tra cứu thủ công vị trí trên hệ thống GPS, mở bản đồ trạm sạc VinFast tìm trụ trống phù hợp dòng xe, tính toán bán kính, soạn thảo tin nhắn hướng dẫn và gọi cứu hộ nếu pin nguy cấp. Toàn bộ qua 5 bước thủ công, mất trung bình **15 phút/lượt**. |
| **3. Bottleneck (Điểm nghẽn cổ chai)** | **Bước 3 và Bước 4 (chiếm 10/15 phút - 67% thời gian):** Tra cứu đối chiếu trạm sạc trống tương thích chuẩn kết nối theo thời gian thực và soạn thảo văn bản hướng dẫn bằng tiếng Việt chuẩn xác, rõ ràng dưới áp lực cuộc gọi dồn dập vào giờ cao điểm. |
| **4. Business Impact (Tác động kinh doanh)** | - Trung bình ~80 - 100 sự cố pin/ngày tại các thành phố lớn.<br>- Gây lãng phí **20 - 25 giờ công lao động/ngày** của đội ngũ điều phối.<br>- Thời gian xử lý chậm làm xe dừng hoạt động trung bình 45 - 60 phút (tính cả thời gian chờ và sạc), gây **thất thoát ~15% doanh thu cuốc xe trong ngày** của từng tài xế.<br>- Tăng nguy cơ xe chết máy trên cầu hoặc trục đường lớn gây ùn tắc giao thông và làm suy giảm uy tín thương hiệu taxi văn minh Xanh SM. |
| **5. Success Metric (Chỉ số thành công có số)** | 1. **Hiệu suất thời gian (Efficiency Metric):** Giảm thời gian điều phối viên xử lý một sự cố pin từ **15 phút xuống dưới 3 phút** (giảm 80%).<br>2. **Độ chính xác nghiệp vụ (Quality Metric):** Đạt tỉ lệ **>= 98%** hướng dẫn đúng trạm sạc còn trụ trống và tương thích cổng sạc.<br>3. **An toàn pin tuyệt đối (Safety Metric):** Đạt tỉ lệ **100%** các trường hợp pin báo nguy cấp (< 5%) được phát hiện và tự động kích hoạt điều xe cứu hộ sạc pin di động, không để tài xế mạo hiểm di chuyển trạm xa > 5km. |
| **6. Operational Boundary (Ranh giới vận hành)** | **Được phép làm:** Tự động đọc dữ liệu telemetry (vị trí GPS, mức pin SoC, mã dòng xe), gọi API kiểm tra trạng thái trụ sạc VinFast, tổng hợp phân tích khoảng cách và tự động soạn thảo dự thảo tin nhắn hướng dẫn.<br>**TUYỆT ĐỐI CẤM:** AI **không được tự ý gửi tin nhắn trực tiếp** cho tài xế hoặc kích hoạt lệnh cứu hộ mà chưa có nút click duyệt của Điều phối viên (Bắt buộc gắn thẻ `[DRAFT_ONLY]` ở đầu mọi phản hồi). Cấm đề xuất trạm sạc xa > 5km khi pin < 5%. |

---

## 3.3. Future-State Flow & Phân Tích Mức Độ AI Fit

### A. Phân tích AI-Fit Matrix (Lựa chọn công nghệ)

Nhóm đã so sánh 3 phương án kiến trúc kỹ thuật:
1. **Rule-based / State Machine thuần túy:** Chỉ giải quyết được việc lọc trạm sạc gần nhất theo bán kính toán học, nhưng không có khả năng thấu hiểu ngữ cảnh ngôn ngữ tự nhiên từ tin nhắn thoại của tài xế (ví dụ: *"đang kẹt xe ở ngã tư Sở, xe báo pin 4%"*) và không thể sinh câu văn hướng dẫn tiếng Việt linh hoạt, đồng cảm, dễ hiểu cho tài xế trong tình trạng căng thẳng.
2. **Autonomous Agentic Loop (Agent tự trị hoàn toàn):** Cho phép AI tự động nhận cuộc gọi, tự phân tích và tự dispatch cứu hộ mà không cần con người. **Bị loại bỏ** vì độ rủi ro quá cao: nếu mô hình gặp lỗi ảo giác (hallucination) hoặc API trụ sạc cập nhật trễ, việc điều xe cứu hộ sai gây lãng phí chi phí vận hành hàng triệu đồng/lần và có thể khiến xe khách chết máy trên đường cao tốc mà điều phối viên không hề hay biết.
3. **LLM Feature có ranh giới an toàn (Selected):** Đây là phương án tối ưu nhất. LLM đóng vai trò **Co-Pilot** tiếp nhận dữ liệu telemetry và mô tả tình huống, truy vấn tool trạm sạc, sau đó sinh dự thảo bản tin hướng dẫn kèm thẻ `[DRAFT_ONLY]` để Điều phối viên con người kiểm tra và bấm nút "Gửi" trong 5 giây.

### B. Quy trình tương lai (Future-State Flow Diagram)

```text
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│ Bước 1          │     │ Bước 2          │     │ Bước 3          │     │ Bước 4          │
│ Tiếp nhận thông │ ──> │ 🔵 AI Auto-pull │ ──> │ 🔵 AI sinh bản │ ──> │ 🟢 Human Review:│
│ tin sự cố       │     │ GPS & API trạm  │     │ dự thảo chỉ dẫn │     │ Dispatcher kiểm │
│ (Tổng đài/App)  │     │ sạc VinFast     │     │ [DRAFT_ONLY]    │     │ tra & click gửi │
└─────────────────┘     └─────────────────┘     └─────────────────┘     └─────────────────┘
                                                       │                         │
                                          Nếu Pin < 5% │                         │
                                                       ▼                         ▼
                                                ┌───────────────┐         ┌───────────────┐
                                                │ 🔵 AI Trigger │         │ Tin nhắn gửi  │
                                                │ đề xuất điều  │         │ tới tài xế/xe │
                                                │ xe sạc pin    │         │ cứu hộ chạy   │
                                                │ di động khẩn  │         └───────────────┘
                                                └───────────────┘
                                                       │
                                                       ▼
                                                ┌─────────────────────────────────────────┐
                                                │ ↩️ Fallback Mechanism:                   │
                                                │ Nếu LLM phản hồi chậm (>5s), lỗi API,   │
                                                │ hoặc vi phạm quy tắc an toàn ranh giới: │
                                                │ Hệ thống chuyển ngay sang chế độ        │
                                                │ điều phối thủ công truyền thống.        │
                                                └─────────────────────────────────────────┘

🔵 = Bước AI xử lý tự động (Data aggregation, Tool calling, Prompt drafting)
🟢 = Bước Con người kiểm duyệt (Human-in-the-loop - Bắt buộc)
↩️ = Kế hoạch dự phòng (Fallback Plan khi AI gặp lỗi)
```

---

# 🏁 Phase 5 — EVALUATE: Đánh Giá Sẵn Sàng & Quyết Định Đầu Tư

### 1. Bảng Đánh Giá Mức Độ Sẵn Sàng (AI Readiness Checklist)

| Tiêu chí sẵn sàng | Đánh giá thực tế tại Vin Smart Future & Xanh SM | Trạng thái |
|:---|:---|:---:|
| **1. Dữ liệu mẫu & Telematics logs** | Đã có sẵn dữ liệu định vị xe GPS từ hệ thống Fleet Management của VinFast và API thời gian thực của trạm sạc V-GREEN / VinFast (vị trí, công suất trụ, trạng thái trống/bận). | ✅ **ĐẠT** |
| **2. Kiểm soát rủi ro & Fallback** | Rủi ro được khoanh vùng triệt để thông qua cơ chế Human-in-the-loop (chỉ sinh `[DRAFT_ONLY]`) và kiểm tra logic pin < 5% bằng rule cứng kết hợp Prompt Boundary. Có cơ chế fallback về quy trình thủ công ngay lập tức nếu API timeout. | ✅ **ĐẠT** |
| **3. Mức độ sẵn sàng của Stakeholders** | Đội ngũ Điều phối viên Xanh SM rất hoan nghênh giải pháp vì đang chịu áp lực quá tải nghiêm trọng trong giờ cao điểm; giao diện Co-pilot được tích hợp ngay trên màn hình Dispatch hiện tại mà không làm xáo trộn thói quen làm việc. | ✅ **ĐẠT** |

---

### 2. Quyết Định Cuối Cùng Của Ban Giám Đốc Vin Smart Future

```text
[ x ] GO (Bắt đầu xây dựng Prototype kỹ thuật & Triển khai thử nghiệm Pilot)
[   ] NOT YET (Cần tích lũy thêm dữ liệu / xác lập baseline)
[   ] NO-GO (Không khả thi / Giải pháp Rule-based thông thường tốt hơn)
```

### 3. Lý Giải Quyết Định Đầu Tư (Justification & ROI Analysis):

1. **Hiệu quả kinh tế (ROI) rõ ràng:**
   - Dự án giúp cắt giảm **80% thời gian xử lý sự cố** của điều phối viên (từ 15 phút xuống dưới 3 phút/lượt), tương đương tiết kiệm hơn 600 giờ lao động mỗi tháng cho trung tâm điều vận.
   - Giảm tỉ lệ xe taxi điện phải nằm chờ chết máy, giúp đưa xe quay lại đón khách nhanh hơn, ước tính thu hồi thêm **~1.2 tỷ VNĐ doanh thu vận tải mỗi tháng** trên toàn bộ đội xe Xanh SM tại Hà Nội và TP.HCM.
2. **Tính khả thi kỹ thuật vượt trội:**
   - Việc ứng dụng mô hình ngôn ngữ lớn **Gemini 3.6 Flash** thế hệ mới cung cấp tốc độ phản hồi cực nhanh (< 1.5 giây), chi phí API cực thấp và hỗ trợ chỉ thị hệ thống (system instructions) nghiêm ngặt giúp bảo vệ ranh giới an toàn tuyệt đối.
3. **Quản trị rủi ro toàn diện:**
   - Dự án không trao quyền tự trị cho AI mà tuân thủ tuyệt đối triết lý **AI Co-Pilot**. Mọi hành động gửi tin nhắn hay điều xe cứu hộ đều giữ con người làm trung tâm kiểm soát, loại trừ hoàn toàn nguy cơ rủi ro pháp lý và an toàn giao thông.
