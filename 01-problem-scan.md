# 01 — Problem Scan & Quick Assessment (Vin Smart Future)

> **Khóa học:** AI Product Engineering & Scoping  
> **Đơn vị:** Vin Smart Future — Tập đoàn Vingroup  
> **Người thực hiện:** Nhóm Kỹ sư AI Product (Vin Smart Future)  
> **Tài liệu nộp bài:** Deliverable 01 — Problem Scan & Quick Assessment  

---

## 🏛️ Bối cảnh Vận hành: Vin Smart Future (Vingroup)

Sau khi Vingroup hợp nhất các bộ phận công nghệ thành **Vin Smart Future**, nhiệm vụ trọng tâm là rà soát toàn bộ quy trình vận hành thực địa xuyên suốt các công ty thành viên (**VinFast, Xanh SM, Vinhomes, Vinmec, Vinpearl, VinWonders**) nhằm tìm kiếm những điểm nghẽn (bottlenecks) có thể giải quyết bằng giải pháp trí tuệ nhân tạo khả thi, an toàn và đem lại giá trị kinh tế trực tiếp.

---

# 🔍 Phase 1 — SCAN: Quét Tìm Cơ Hội (4 Lenses)

Sử dụng **4 Lenses** chuyên sâu để quét qua hoạt động vận hành của các công ty thành viên Vingroup:
1. **Lặp lại (Repetitive):** Tác vụ thao tác lặp đi lặp lại nhiều lần hằng ngày, quy tắc tương đối ổn định nhưng tốn nhân lực.
2. **Tốn thời gian (Time-consuming):** Tác vụ đòi hỏi tra cứu, tổng hợp dữ liệu hoặc viết soạn văn bản thủ công dài.
3. **AI có thể tốt hơn (AI-upgrade):** Dịch vụ hiện tại rập khuôn, thiếu cá nhân hóa hoặc thời gian phản hồi quá chậm so với kỳ vọng của khách hàng.
4. **Stakeholder Pain:** Điểm nghẽn gây ức chế, phàn nàn trực tiếp từ tài xế, cư dân, bệnh nhân hoặc nhân viên thực địa.

### 📝 Bảng Quét Cơ Hội Vận Hành (Opportunity Scan Table)

| # | Đơn vị thành viên | Phân loại Lens | Tên bài toán / Nghiệp vụ thủ công | Mô tả ngắn bài toán & Thực trạng vận hành |
|:---|:---|:---|:---|:---|
| 1 | **Xanh SM (GSM)** | **Tốn thời gian & Pain từ người khác** | Điều phối sự cố sạc pin / cạn pin khẩn cấp trên đường đón khách | Tài xế gọi về tổng đài khi xe báo pin khẩn cấp (< 10%). Điều phối viên phải tra GPS thủ công, mở dashboard trụ sạc VinFast tìm trạm trống, nhẩm tính khoảng cách và viết tin hướng dẫn thủ công (mất 12-15 phút/cuộc), gây nguy cơ xe cạn pin giữa đường. |
| 2 | **VinFast** | **Lặp lại** | Đối chiếu và so khớp hóa đơn sạc điện tại trạm đối tác bên thứ ba | Chuyên viên tài chính đối chiếu hàng ngàn tệp CDR (Charging Data Record) từ các trạm sạc nhượng quyền/đối tác ngoài với hệ thống thanh toán VinFast mỗi tuần; tốn 24 giờ lao động/tuần/nhân sự. |
| 3 | **Vinmec** | **Tốn thời gian** | Soạn thảo tóm tắt hồ sơ xuất viện (Discharge Summary) | Bác sĩ điều trị và điều dưỡng phải đọc lại toàn bộ kết quả xét nghiệm máu, chẩn đoán hình ảnh, đơn thuốc và nhật ký chăm sóc để viết bản tóm tắt xuất viện bằng tay; mất 25-30 phút/bệnh nhân, gây quá tải giờ cao điểm xuất viện. |
| 4 | **Vinhomes** | **AI-upgrade** | Phân loại và điều hướng ý kiến/khiếu nại cư dân trên Vinhomes Resident App | Cư dân gửi phản ánh bằng văn bản tự do kèm ảnh chụp (ví dụ: tiếng ồn thi công, hỏng bóng đèn hành lang, rò rỉ nước). CSKH đọc thủ công và phân loại sang Ban Quản lý từng tòa nhà; thời gian chuyển tiếp kéo dài 8-12 tiếng. |
| 5 | **Vinpearl** | **Pain từ người khác** | Trích xuất và phân tích ý kiến đánh giá đa kênh (OTA Review Intelligence) | Nhân viên quản lý chất lượng đọc thủ công hàng trăm bài đánh giá trên Agoda, Booking.com, Google Maps mỗi ngày; khó phát hiện kịp thời các cảnh báo khẩn cấp như ngộ độc thực phẩm, phòng hỏng điều hòa trong vòng 1 giờ. |
| 6 | **VinFast** | **AI-upgrade** | Chẩn đoán sơ bộ mã lỗi ô tô điện từ mô tả ngôn ngữ tự nhiên của khách hàng | Khách hàng lái VF8/VF9 mô tả hiện tượng xe qua tổng đài bằng tiếng Việt đời thường (ví dụ: *"xe bị giật khi nhả ga ở vận tốc 20km/h"*). Nhân viên tổng đài chưa có kiến thức kỹ thuật sâu nên mất 15 phút tra cứu sổ tay để mapping mã lỗi OBD-II. |

---

# 🃏 Phase 2 — QUICK-ASSESS: 3 Thẻ Bài Toán Tiềm Năng (Quick Problem Cards)

Từ 6 bài toán trên, nhóm tiến hành chọn lọc **Top 3 bài toán** có giá trị kinh doanh và tính cấp bách cao nhất để lập thẻ đánh giá nhanh:

---

## 📌 QUICK PROBLEM CARD #1: Xanh SM — Điều Phối Sự Cố Sạc Pin Thực Địa

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                                   │
│                                                                         │
│ Bài toán: Hỗ trợ Điều phối viên Xanh SM tra cứu trạm sạc VinFast còn    │
│ trống và tự động soạn thảo chỉ dẫn khẩn cấp / điều xe sạc lưu động.    │
│ Công ty thành viên: [x] Xanh SM (GSM)  [ ] VinFast  [ ] Vinhomes        │
│                     [ ] Vinmec         [ ] Vinpearl                     │
│                                                                         │
│ Ai đang đau (Actor)?                                                    │
│ - Điều phối viên (Dispatcher): Quá tải cuộc gọi giờ cao điểm.           │
│ - Tài xế Xanh SM: Lo sợ xe cạn pin, bị phạt hủy chuyến, stress cao độ.   │
│                                                                         │
│ Workflow thủ công hiện tại (5 bước):                                    │
│   1. Nhận cuộc gọi khẩn từ tài xế ──> 2. Tra cứu toạ độ GPS xe          │
│   ──> 3. Tra cứu dashboard trạm sạc VinFast tìm trụ trống phù hợp cổng  │
│   ──> 4. Soạn thảo SMS chỉ dẫn đường đi gửi cho tài xế                  │
│   ──> 5. Gọi đội xe cứu hộ sạc pin lưu động (nếu pin báo < 5%)          │
│                                                                         │
│ Bước tốn thời gian/lỗi nhất: Bước 3 & 4 (⏱ 10 - 12 phút/lượt xử lý).    │
│ AI có thể can thiệp ở đâu: Tự động trích xuất ngữ cảnh, kiểm tra        │
│ trạm sạc gần nhất theo bán kính an toàn, soạn thảo bản nháp chỉ đường    │
│ kèm ranh giới an toàn [DRAFT_ONLY].                                     │
│                                                                         │
│ Đo lường thành công (Metric có số cụ thể):                              │
│ - Giảm thời gian xử lý sự cố từ 15 phút xuống dưới 3 phút/lượt.         │
│ - Tỉ lệ chỉ dẫn đúng trụ sạc còn trống và phù hợp chuẩn sạc đạt >= 98%. │
│ - 100% trường hợp pin < 5% được cảnh báo điều xe cứu hộ sạc lưu động.   │
│                                                                         │
│ Phân loại kiến trúc: [ ] No AI  [ ] Rule  [x] LLM Feature  [ ] Agent    │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## 📌 QUICK PROBLEM CARD #2: Vinmec — Tóm Tắt Hồ Sơ Xuất Viện (Discharge Summary)

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                                   │
│                                                                         │
│ Bài toán: Trích xuất các chỉ số xét nghiệm, chẩn đoán xác định và đơn   │
│ thuốc từ bệnh án điện tử để soạn thảo dự thảo tóm tắt xuất viện.       │
│ Công ty thành viên: [ ] Xanh SM  [ ] VinFast  [ ] Vinhomes              │
│                     [x] Vinmec   [ ] Vinpearl                           │
│                                                                         │
│ Ai đang đau (Actor)?                                                    │
│ - Bác sĩ điều trị & Điều dưỡng khoa nội/ngoại: Tốn thời gian làm hồ sơ. │
│ - Bệnh nhân & Người nhà: Chờ đợi 2-3 tiếng trong ngày làm thủ tục về.   │
│                                                                         │
│ Workflow thủ công hiện tại (4 bước):                                    │
│   1. Mở phần mềm HIS/EMR tra cứu lịch sử khám bệnh của bệnh nhân        │
│   ──> 2. Đọc lại 10-15 trang kết quả xét nghiệm máu, chẩn đoán hình ảnh │
│   ──> 3. Gõ tay bản tóm tắt tình trạng lúc vào, diễn biến, hướng điều trị│
│   ──> 4. Trưởng khoa ký duyệt và in phiếu tóm tắt xuất viện             │
│                                                                         │
│ Bước tốn thời gian/lỗi nhất: Bước 2 & 3 (⏱ 25 - 30 phút/hồ sơ).         │
│ AI có thể can thiệp ở đâu: Đọc dữ liệu phi cấu trúc từ EMR, trích xuất  │
│ mốc sự kiện quan trọng, tự động điền form dự thảo tóm tắt bệnh án.      │
│                                                                         │
│ Đo lường thành công (Metric có số cụ thể):                              │
│ - Rút ngắn thời gian soạn hồ sơ từ 30 phút xuống dưới 5 phút.           │
│ - Tỉ lệ chính xác về thông số liều lượng thuốc và chống chỉ định 100%.  │
│                                                                         │
│ Phân loại kiến trúc: [ ] No AI  [ ] Rule  [x] LLM Feature  [ ] Agent    │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## 📌 QUICK PROBLEM CARD #3: Vinhomes — Phân Loại Khiếu Nại Cư Dân Trên Resident App

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                                   │
│                                                                         │
│ Bài toán: Tự động phân loại nội dung phản ánh của cư dân và định tuyến  │
│ ticket đến đúng bộ phận kỹ thuật / vệ sinh / bảo vệ của từng tòa nhà.  │
│ Công ty thành viên: [ ] Xanh SM  [ ] VinFast  [x] Vinhomes              │
│                     [ ] Vinmec   [ ] Vinpearl                           │
│                                                                         │
│ Ai đang đau (Actor)?                                                    │
│ - Cư dân khu đô thị Vinhomes (Times City, Ocean Park, Grand Park).      │
│ - Nhân viên CSKH tổng đài Vinhomes (tiếp nhận hàng ngàn ticket/ngày).   │
│                                                                         │
│ Workflow thủ công hiện tại (4 bước):                                    │
│   1. Tiếp nhận ticket phản ánh bằng chữ và hình ảnh trên hệ thống CRM   │
│   ──> 2. Nhân viên CSKH đọc nội dung, xác định tòa nhà và loại sự cố    │
│   ──> 3. Chọn thủ công bộ phận tiếp nhận (Kỹ thuật điện/nước/an ninh)   │
│   ──> 4. Nhắn tin xác nhận tiếp nhận tới cư dân                         │
│                                                                         │
│ Bước tốn thời gian/lỗi nhất: Bước 2 & 3 (⏱ 15 phút - 2 giờ chờ đợi).    │
│ AI có thể can thiệp ở đâu: Nhận diện ý định (Intent Classification) từ  │
│ văn bản cư dân và tự động gán nhãn phòng ban xử lý.                     │
│                                                                         │
│ Đo lường thành công (Metric có số cụ thể):                              │
│ - 90% ticket được phân loại và chuyển tiếp trong vòng dưới 10 giây.      │
│ - Độ chính xác định tuyến bộ phận đạt trên 95%.                         │
│                                                                         │
│ Phân loại kiến trúc: [ ] No AI  [x] Rule/ML Classifier  [ ] Agent       │
└─────────────────────────────────────────────────────────────────────────┘
```

---

# 🗳️ Quyết Định Lựa Chọn Bài Toán Cho Nhóm (Selection Rationale)

Sau khi đánh giá so sánh giữa 3 thẻ bài toán theo các tiêu chí: **Tính cấp bách vận hành (Urgency)**, **Mức độ sẵn sàng dữ liệu (Data Readiness)**, **Độ an toàn rủi ro (Risk Profile)** và **Khả năng giải quyết bằng AI so với Rule thông thường (AI Feasibility)**:

Nhóm thống nhất lựa chọn **CARD #1: Xanh SM — Điều Phối Sự Cố Sạc Pin Thực Địa** để thực hiện phân tích sâu (Deep-Dive).

### Phân tích lý do lựa chọn Card #1 và loại trừ các Card khác:

1. **Vì sao chọn Card #1 (Xanh SM Sự cố sạc pin):**
   - **Tác động trực tiếp tới doanh thu và an toàn thực tế:** Taxi điện là ngành kinh doanh vận tải thời gian thực. Mỗi phút xe nằm chờ hết pin là rò rỉ doanh thu của Xanh SM và gây ức chế cực lớn cho tài xế.
   - **Ranh giới an toàn (Operational Boundary) rõ ràng và đo lường được:** Quy tắc kỹ thuật về pin (mức pin < 5% tuyệt đối không được đi xa quá 5km) cho phép kiểm thử tính bảo vệ ranh giới của LLM một cách minh bạch, lập trình hóa được (programmatic assertion).
   - **Mô hình hợp tác người - máy lý tưởng (Co-pilot / HITL):** AI không thay thế điều phối viên mà đóng vai trò trợ lý tổng hợp thông tin và soạn thảo văn bản nháp `[DRAFT_ONLY]`, giữ điều phối viên làm chốt chặn an toàn cuối cùng.

2. **Vì sao không chọn Card #2 (Vinmec Bệnh án):**
   - Mặc dù giá trị tiết kiệm thời gian cho y bác sĩ là rất cao, nhưng rủi ro pháp lý và an toàn người bệnh trong y tế là mức tối thượng. Một sai sót nhỏ về đơn thuốc hay tiền sử dị ứng có thể đe dọa tính mạng bệnh nhân. Bài toán này cần quy trình kiểm định lâm sàng khắt khe, kết nối trực tiếp với cổng bảo mật dữ liệu y tế chuẩn HL7/FHIR mà khuôn khổ codelab ngắn hạn chưa thể đáp ứng đầy đủ.

3. **Vì sao không chọn Card #3 (Vinhomes Định tuyến ticket):**
   - Bản chất bài toán phân loại ticket khiếu nại của cư dân có thể được giải quyết phần lớn bằng các thuật toán máy học truyền thống (Text Classification như TF-IDF + Logistic Regression, SVM) hoặc Rule-based regex đơn giản kết hợp menu chọn lọc trên App. Việc áp dụng LLM lớn vào bước này là "dùng dao mổ trâu giết gà", chi phí token cao mà hiệu quả không vượt trội hơn giải pháp cổ điển.
