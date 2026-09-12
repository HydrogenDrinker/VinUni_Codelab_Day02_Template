# Lab 02 — Deep-Dive Report

## Vinhomes Resident Request Triage Assistant

**Trạng thái:** Bản scoping cho prototype/offline pilot — chưa được phép triển khai production  
**Bài toán được chọn:** Phân loại, đánh giá mức khẩn cấp và điều hướng yêu cầu cư dân Vinhomes

> Vinhomes công bố rằng Ban Quản lý tại các đại đô thị tiếp nhận hàng nghìn yêu cầu mỗi ngày, gồm yêu cầu thông tin, tiện ích và trường hợp khẩn cấp về sinh hoạt/y tế. Đây là căn cứ về quy mô và sự đa dạng của đầu vào, không phải bằng chứng rằng quy trình hiện tại đang thất bại. [Nguồn Vinhomes](https://vinhomes.vn/vi/nhung-la-thu-cam-on-tu-cu-dan-va-dich-vu-tu-trai-tim-vinhomes)

## 1. Scope và giả định

### In scope

- Tiếp nhận văn bản tiếng Việt từ app/tổng đài đã được nhân viên nhập lại.
- Trích xuất thông tin cần thiết cho ticket.
- Phân loại chủ đề, mức ưu tiên và bộ phận nhận.
- Tạo kết quả dạng JSON và lời giải thích ngắn để nhân viên review.

### Out of scope

- Trực tiếp giải quyết sự cố kỹ thuật tại căn hộ.
- Tự đóng yêu cầu hoặc xác nhận cư dân đã hài lòng.
- Tự quyết định bồi thường, phí, tranh chấp hoặc trách nhiệm pháp lý.
- Đưa ra tư vấn y tế, mệnh lệnh an ninh hoặc thay thế tổng đài khẩn cấp.
- Tự gửi dữ liệu cá nhân ra ngoài hệ thống Vinhomes.

### Giả định phải được xác minh

1. Hệ thống ticket có timestamp nhận, phân loại, chuyển bộ phận và đóng yêu cầu.
2. Có taxonomy thống nhất cho dự án/tòa nhà/bộ phận/chủ đề/mức ưu tiên.
3. Có thể lấy một tập ticket đã ẩn danh và nhãn xử lý cuối cùng để đánh giá offline.
4. **Giả định pilot** về thời gian triage thủ công là 5 phút/yêu cầu; con số thật phải được đo trước khi công bố business case.

## 2. Current-State Workflow Mapping

### Quy trình hiện tại cần xác minh với Ban Quản lý

| Bước | Actor | Hoạt động | Input | Output | Thời gian giả định | Dấu hiệu |
|---:|---|---|---|---|---:|---|
| 1 | Cư dân / Tổng đài | Gửi hoặc ghi nhận yêu cầu | Văn bản, cuộc gọi, ảnh | Yêu cầu thô | 0,5 phút | 🔄 Handoff cư dân → Vinhomes |
| 2 | Nhân viên trực | Đọc, chuẩn hóa và xác định yêu cầu chính | Yêu cầu thô | Nội dung đã hiểu | 1,0 phút | 🔴 Nội dung tự do/sai chính tả |
| 3 | Nhân viên trực | Kiểm tra dự án, tòa, căn và hỏi thông tin còn thiếu | Nội dung + hồ sơ cư dân | Thông tin đủ để tạo ticket | 1,5 phút | 🔴 Có thể phải liên hệ lại |
| 4 | Nhân viên trực | Phân loại chủ đề và mức khẩn cấp | Nội dung đã chuẩn hóa | Nhãn + priority | 1,0 phút | 🔴 Rủi ro bỏ sót khẩn cấp |
| 5 | Nhân viên trực | Tra cứu đơn vị chịu trách nhiệm | Nhãn + vị trí | Bộ phận nhận | 0,5 phút | 🔄 Handoff giữa bộ phận |
| 6 | Nhân viên trực | Tạo và chuyển ticket | Toàn bộ thông tin | Ticket được giao | 0,5 phút | 🔄 Handoff hệ thống → đội xử lý |

**Tổng thời gian triage giả định:** 5 phút/yêu cầu.  
**Bottleneck chính:** bước 2–4. Phạm vi dự án chỉ tối ưu triage; thời gian xử lý thực địa sau khi giao ticket không nằm trong metric chính.

```text
Cư dân/App/Tổng đài
        │
        │ 🔄 yêu cầu thô
        ▼
[Đọc & chuẩn hóa] 🔴
        │
        ▼
[Hỏi thông tin thiếu] 🔴
        │
        ▼
[Phân loại + đánh giá khẩn cấp] 🔴
        │
        ▼
[Tra bộ phận phụ trách]
        │ 🔄 ticket
        ▼
[Đội vận hành xử lý]
```

## 3. Problem Statement — 6 fields

| Field | Nội dung |
|---|---|
| **1. Actor / Operator** | Nhân viên tổng đài và nhân viên trực Ban Quản lý chịu trách nhiệm tiếp nhận, chuẩn hóa, phân loại và chuyển yêu cầu cư dân. |
| **2. Current Workflow** | Yêu cầu đến từ app hoặc tổng đài. Nhân viên đọc nội dung, kiểm tra thông tin căn hộ, hỏi bổ sung khi thiếu, xác định chủ đề/mức khẩn cấp, tra cứu bộ phận phụ trách rồi tạo và chuyển ticket. |
| **3. Bottleneck** | Nội dung tiếng Việt tự do có thể dài, sai chính tả, thiếu vị trí hoặc chứa nhiều vấn đề; nhân viên phải diễn giải rồi gắn nhãn thủ công. Sai ở bước này có thể làm ticket bị chuyển vòng hoặc bỏ sót yêu cầu khẩn cấp. |
| **4. Business Impact** | Nguồn công khai cho biết các Ban Quản lý đại đô thị tiếp nhận hàng nghìn yêu cầu/ngày. Trong kịch bản thận trọng 1.000 yêu cầu/ngày và 5 phút triage/yêu cầu, workload tương đương khoảng 83 giờ công/ngày. Đây là phép tính kịch bản, không phải số liệu nội bộ đã xác nhận. |
| **5. Success Metric** | (a) Routing accuracy ≥ 92%; (b) recall yêu cầu khẩn cấp ≥ 99%; (c) p95 thời gian sinh draft ≤ 10 giây; (d) thời gian có người tham gia triage trung bình < 1 phút; (e) 0 ticket được AI tự đóng hoặc tự hứa bồi thường. |
| **6. Operational Boundary** | AI chỉ tạo đề xuất/draft. Rule bắt buộc chuyển người nếu có tín hiệu khẩn cấp, confidence thấp, dữ liệu thiếu hoặc nội dung nhạy cảm. Con người quyết định bộ phận nhận trong các trường hợp này và chịu trách nhiệm cho mọi hành động thực địa/pháp lý/tài chính. |

## 4. AI Fit Analysis

| Phương án | Điểm mạnh | Điểm yếu | Quyết định |
|---|---|---|---|
| **Rule / State Machine** | Dễ kiểm toán; tốt cho mapping dự án/tòa, trường bắt buộc và từ khóa khẩn cấp. | Khó bao phủ ngôn ngữ tự do, sai chính tả, đồng nghĩa và yêu cầu có nhiều ý. | Dùng làm validation và safety gate. |
| **LLM Feature** | Hiểu tiếng Việt tự do, trích xuất nhiều trường, phân loại đa nhãn và giải thích ngắn. | Có thể hallucinate hoặc tự tin sai; cần schema, confidence gate và test set. | **Chọn làm bộ máy triage chính.** |
| **Agentic Loop** | Có thể tự gọi nhiều hệ thống và theo dõi ticket. | Tăng quyền tự trị, độ phức tạp và rủi ro sai hành động; chưa cần thiết cho scope triage. | Không chọn trong phiên bản đầu. |

**Kết luận AI Fit:** Kiến trúc phù hợp là **rules + một LLM feature + Human-in-the-loop**, không phải agent tự trị.

## 5. Future-State Flow

```text
Cư dân/App/Tổng đài
        │
        ▼
[Rule: bỏ PII không cần thiết + kiểm tra trường bắt buộc]
        │
        ▼
[🔵 LLM: extract + multi-label + priority + confidence]
        │
        ▼
┌─────────────────────────────────────────────────────────┐
│ Safety Gate                                             │
│ urgent=true OR confidence<0.85 OR thiếu dữ liệu?        │
└─────────────────────────────────────────────────────────┘
        │ Có                                  │ Không
        ▼                                      ▼
[🟢 Người trực review]                 [Tạo ticket nháp]
        │                                      │
        └──────────────────┬───────────────────┘
                           ▼
              [Rule map sang bộ phận nhận]
                           │ 🔄
                           ▼
                 [Đội vận hành xử lý]

↩️ Fallback: nếu LLM timeout, JSON sai schema hoặc dịch vụ không khả dụng,
hệ thống giữ nguyên yêu cầu thô và đưa về hàng đợi triage thủ công hiện tại.
```

### Human-in-the-loop

- Bắt buộc review 100% yêu cầu khẩn cấp, confidence dưới `0.85`, thiếu vị trí, có tranh chấp/phí/bồi thường hoặc chứa nội dung y tế/an ninh.
- Trong pilot, review 100% mọi kết quả. Chỉ sau khi đạt metric mới xem xét auto-route nhóm rủi ro thấp; AI vẫn không được auto-close.
- Nhân viên có thể sửa nhãn, mức ưu tiên và bộ phận. Các sửa đổi được ghi log làm dữ liệu đánh giá, không tự động dùng để huấn luyện khi chưa được duyệt.

## 6. Proposed Output Contract

```json
{
  "request_id": "string",
  "summary": "string",
  "project": "string_or_unknown",
  "building": "string_or_unknown",
  "apartment": "string_or_unknown",
  "categories": ["maintenance"],
  "priority": "low|normal|high|critical",
  "urgent": false,
  "missing_fields": [],
  "suggested_team": "string_or_manual_review",
  "confidence": 0.0,
  "requires_human_review": true,
  "reason": "string",
  "action": "create_draft|manual_review|escalate_emergency",
  "prohibited_action": "DRAFT_ONLY"
}
```

### Safety invariants

1. `prohibited_action` luôn phải là `DRAFT_ONLY`.
2. `critical` hoặc `urgent=true` luôn kéo theo `requires_human_review=true`.
3. AI không được tạo dữ liệu vị trí/căn hộ còn thiếu; phải trả về `unknown` và liệt kê trong `missing_fields`.
4. AI không được hứa thời gian xử lý, bồi thường hoặc kết luận trách nhiệm.
5. Output sai schema bị loại và chuyển fallback thủ công.

## 7. Evaluation Plan

### Dataset tối thiểu trước pilot

- 500–1.000 ticket lịch sử đã ẩn danh, phân bố theo dự án, chủ đề và mức ưu tiên.
- Nhãn chuẩn được hai nhân viên vận hành thống nhất; trường hợp bất đồng do quản lý phân xử.
- Tập test khóa riêng, có ít nhất 100 tình huống khẩn cấp/nhạy cảm hoặc dữ liệu tổng hợp được chuyên gia xác nhận.
- Không dùng họ tên, số điện thoại, số căn đầy đủ hoặc dữ liệu định danh trong môi trường phát triển.

### Các phép đo

- Macro-F1 theo category và routing accuracy theo bộ phận.
- Recall/false-negative rate riêng cho lớp `critical`.
- Schema validity, hallucinated-field rate và tỷ lệ fallback.
- Thời gian triage trước/sau, tỷ lệ nhân viên sửa nhãn và mức chấp nhận của người dùng nội bộ.

## 8. Readiness Checklist

| Câu hỏi | Trạng thái | Bằng chứng / việc cần làm |
|---|---|---|
| Bài toán có volume và actor rõ? | ✅ Có ở mức scoping | Nguồn Vinhomes nêu hàng nghìn yêu cầu/ngày; cần xác nhận theo từng dự án. |
| Có baseline thời gian và routing accuracy? | ❌ Chưa | Đo 1 tuần bằng timestamp và review mẫu ticket. |
| Có dữ liệu đã gắn nhãn, được phép sử dụng? | ⚠️ Chưa xác nhận | Làm việc với vận hành, pháp chế và bảo mật dữ liệu. |
| Có Human-in-the-loop? | ✅ Có trong thiết kế | Review toàn bộ ở pilot; bắt buộc review nhóm nhạy cảm. |
| Có fallback? | ✅ Có trong thiết kế | Quay lại hàng đợi thủ công khi lỗi/timeout/schema invalid. |
| Boundary có thể kiểm thử? | ✅ Có | Dùng schema, invariants và adversarial test. |
| Tích hợp với taxonomy/ticket system khả thi? | ⚠️ Chưa xác nhận | Cần API/schema và owner hệ thống. |

## 9. Quyết định GO / NOT YET / NO-GO

### ✅ GO — nhưng chỉ cho discovery và offline pilot có kiểm soát

Không phê duyệt triển khai production hoặc auto-route ở thời điểm hiện tại.

**Justification:**

- Bài toán có volume lớn và phù hợp với khả năng hiểu ngôn ngữ tự do của LLM.
- Có thể thu hẹp scope ở bước tạo ticket nháp, giữ nguyên trách nhiệm của con người và cơ chế fallback hiện tại.
- Rule-based vẫn được dùng cho các ràng buộc xác định và safety gate; không dùng LLM cho mọi phần của quy trình.
- Các khoảng trống lớn nhất là baseline, taxonomy chuẩn, quyền sử dụng dữ liệu và khả năng tích hợp. Vì vậy, GO chỉ có nghĩa là được phép đo baseline, tạo dữ liệu ẩn danh, xây prompt prototype và đánh giá offline.
- Chỉ xem xét production pilot khi routing accuracy ≥ 92%, critical recall ≥ 99%, không có vi phạm safety invariant và được vận hành/bảo mật/pháp chế phê duyệt.

## 10. Nguồn tham khảo

1. [Vinhomes — Những lá thư cảm ơn từ cư dân và dịch vụ từ trái tim](https://vinhomes.vn/vi/nhung-la-thu-cam-on-tu-cu-dan-va-dich-vu-tu-trai-tim-vinhomes)
2. [Vinhomes — Quy định xử lý khiếu nại/yêu cầu của khách hàng](https://gcp-cdn.vinhomes.vn/cms-data/3_VHM_Quy%20dinh%20xu%20ly%20khieu%20nai%20yeu%20cau%20cua%20KH.pdf)
3. [Vinhomes Resident trên App Store — mô tả các chức năng hỗ trợ và phản ánh](https://apps.apple.com/vn/app/vinhomes-resident/id6450522818)
