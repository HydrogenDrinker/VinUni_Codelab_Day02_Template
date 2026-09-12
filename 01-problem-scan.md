# Phase 1 & 2 — Problem Scan & Quick Cards (Cá nhân)

> **AI Engineer:** Vin Smart Future | **Mảng:** Vinhomes Smart City

---

## 🔍 Phase 1 — SCAN: 5 Bài Toán Tìm Được

Sử dụng **4 Lenses** để quét qua vận hành của các công ty thành viên Vingroup:

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | **Vinhomes** | Lặp lại (Repetitive) | CSKH phải đọc–classify–prioritize–route thủ công **100% ticket** cư dân mỗi ngày trước khi team chuyên môn bắt đầu xử lý. |
| 2 | **Vinhomes** | Tốn thời gian (Time-consuming) | Mỗi ticket mất 5–10 phút CSKH xử lý → tạo backlog giờ cao điểm → SLA vi phạm → cư dân không hài lòng. |
| 3 | **Xanh SM** | AI-upgrade | Hệ thống gợi ý điểm đón khách chưa tính traffic real-time; tài xế phàn nàn về điểm đón không chính xác. |
| 4 | **Vinmec** | Tốn thời gian (Time-consuming) | Bác sĩ mất 20–30 phút viết tóm tắt hồ sơ xuất viện thủ công mỗi bệnh nhân — bác sĩ phàn nàn quá tải. |
| 5 | **VinFast** | Lặp lại (Repetitive) | So khớp hóa đơn sạc điện và đối chiếu số liệu trạm sạc đối tác hằng tuần — hoàn toàn thủ công. |

**Bài toán được chọn để Deep-Dive:** **#1 + #2 — Vinhomes AI Resident Ticket Triage**

**Lý do chọn:**
- Lặp lại hằng ngày + tốn thời gian → AI-fit cao nhất trong danh sách
- Scope rõ ràng: chỉ tự động hóa bước triage, không đụng vào việc xử lý thực tế
- HITL kiểm soát được rủi ro (uncertain → CSKH, critical → escalate)
- Defend được trước giảng viên mà không cần giải thích calibration/confidence score phức tạp

---

## 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

### Card #1 — AI Auto-Triage Routine Tickets

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán: AI tự động classify, prioritize và route ticket   │
│           cư dân Vinhomes — thay bước triage thủ công       │
│ Công ty thành viên: [x] Vinhomes                            │
│                                                             │
│ Ai đang đau (Actor)? CSKH Vinhomes (triage mọi ticket)      │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Cư dân gửi ticket (ngôn ngữ tự nhiên)                 │
│   → 2. CSKH đọc và hiểu vấn đề                             │
│   → 3. CSKH phân loại danh mục (Maintenance/Billing/...)   │
│   → 4. CSKH đánh giá mức ưu tiên (Low/Med/High/Critical)   │
│   → 5. CSKH chọn team và route ticket                      │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3–5 (⏱ 5–10 phút)   │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3, 4, 5         │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   Median triage time: 5–10 min ──> < 30 giây               │
│   Auto-routing rate ≥ 70% routine tickets                   │
│   Routing accuracy ≥ 95%                                    │
│                                                             │
│ Quick Architecture: [x] LLM Feature                        │
└─────────────────────────────────────────────────────────────┘
```

### Card #2 — Safety Signal Detection (Anti-Downplay)

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán: AI escalate tự động ticket an toàn/khẩn cấp       │
│           dù cư dân cố tình downplay mức độ nghiêm trọng    │
│ Công ty thành viên: [x] Vinhomes                            │
│                                                             │
│ Ai đang đau (Actor)? CSKH + Ban quản lý tòa nhà             │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Cư dân gửi ticket mô tả mơ hồ ("hơi có mùi lạ")       │
│   → 2. CSKH đọc và tự đánh giá mức nghiêm trọng            │
│   → 3. Thấy mô tả bình thường → route về maintenance        │
│   → 4. Nếu thực ra là sự cố → phát hiện muộn, nguy hiểm    │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 (⏱ 3–5 phút)       │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2: flag safety  │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   Critical-case recall ≥ 99% (không miss safety incident)   │
│                                                             │
│ Quick Architecture: [x] LLM Feature                        │
└─────────────────────────────────────────────────────────────┘
```

### Card #3 — Intelligent Fallback for Ambiguous Tickets

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán: Detect sớm ticket ambiguous (spanning 2+ domains) │
│           → fallback CSKH thay vì auto-route sai team       │
│ Công ty thành viên: [x] Vinhomes                            │
│                                                             │
│ Ai đang đau (Actor)? CSKH nhận ticket bị route sai          │
│                                                             │
│ Workflow thủ công hiện tại (3 bước):                        │
│   1. CSKH nhận ticket từ team sai (wrong routing)           │
│   → 2. CSKH phải đọc lại và re-classify                    │
│   → 3. Route lại đúng team → mất thêm 5–10 phút            │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Toàn bộ (⏱ 5–10 phút)    │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Detect ambiguity sớm │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   Routing accuracy ≥ 95% | Wrong-routing rate < 5%          │
│                                                             │
│ Quick Architecture: [x] LLM Feature                        │
└─────────────────────────────────────────────────────────────┘
```
