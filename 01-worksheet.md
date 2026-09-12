# Lab 02 — Worksheet: AI Product Scoping (Vin Smart Future)

---

## 🏛️ 1. Bối cảnh thực tế: Vin Smart Future (Vingroup)

**Vingroup** — Tập đoàn tư nhân lớn nhất Việt Nam — vừa sáp nhập toàn bộ các phòng ban công nghệ thuộc các công ty thành viên thành một đơn vị công nghệ thống nhất mang tên **Vin Smart Future**. 

Nhiệm vụ của **Vin Smart Future** là xây dựng các giải pháp AI, số hóa, và tự động hóa cốt lõi để nâng cao hiệu suất vận hành và trải nghiệm khách hàng xuyên suốt các công ty thành viên:
* 🚗 **VinFast:** Hệ thống xe điện thông minh (EV), trợ lý AI ảo trong xe, dự đoán bảo trì pin, và quản lý chuỗi cung ứng sản xuất.
* 🚕 **Xanh SM (GSM):** Vận hành đội xe taxi/xe máy điện thông minh, điều vận thông minh (Smart Dispatching), tối ưu hóa lộ trình di chuyển.
* 🏢 **Vinhomes:** Quản lý đô thị thông minh (Smart Cities), trợ lý cư dân thông minh, tối ưu hóa mức tiêu thụ năng lượng.
* 🏥 **Vinmec:** Y tế thông minh, chẩn đoán hình ảnh bằng AI, tối ưu hóa quản lý hồ sơ bệnh án.
* 🎢 **Vinpearl / VinWonders:** Trải nghiệm du lịch số hóa, quản lý phòng và luồng khách thông minh tại các khu vui chơi.

Trong buổi Lab hôm nay, nhóm của bạn sẽ đóng vai trò là **AI Product Engineer** tại **Vin Smart Future**, tiến hành tìm kiếm, scoping, phân tích độ khả thi, thiết lập ranh giới vận hành, và xây dựng một **bản mẫu kỹ thuật (prompt prototype)** cho một bài toán cụ thể thuộc một trong những mảng kinh doanh trên.

---

## 📊 2. Cơ cấu tính điểm bài lab

### 👥 Điểm nhóm (60 điểm)

| Gate | Điểm | Deliverable | Tiêu chí chấm |
|---|---:|---|---|
| **G1. Workflow Mapping** | 20 | Problem Deep-Dive | Vẽ chi tiết quy trình hiện tại: các bước, handoff, thời gian, bottleneck |
| **G2. Problem Statement** | 20 | Problem Deep-Dive | Problem Statement 6-field bám sát thực tế, metric có số và ranh giới rõ ràng |
| **G3. AI Fit & Future Flow** | 10 | Problem Deep-Dive | So sánh Rule vs LLM vs Agent, future flow có bước AI, ranh giới và Fallback |
| **G4. Decision Quality** | 10 | Problem Deep-Dive | Quyết định Go/Not Yet/No-Go trung thực và có chứng cứ rõ ràng |

### 👤 Điểm cá nhân (40 điểm)

| Gate | Điểm | Deliverable | Tiêu chí chấm |
|---|---:|---|---|
| **I1. Scan & Cards** | 15 | Quick Cards | Liệt kê 5 problems sử dụng 3 lenses, hoàn thiện 3 quick cards chất lượng |
| **I2. Prototyping** | 10 | 02-lab/ | Chạy thử nghiệm programmatic prompt prototype thành công |
| **I3. AI Log & Reflection** | 15 | 03-ai-log.md | Phản ánh trung thực về việc dùng AI làm thought-partner (giúp gì, sai gì, sửa gì) |

---

# 🚀 Phase 0 — worked Example: Xanh SM Intelligent Dispatcher (15 min)

*Giảng viên walk-through ví dụ thực tế từ Vin Smart Future để bạn hiểu rõ cách scoping một bài toán AI.*
Đọc chi tiết worked example tại file [02-deliverable-example.md](02-deliverable-example.md).

---

# 🔍 Phase 1 — SCAN (Cá nhân, 20 min)

Hãy sử dụng **4 Lenses** dưới đây để quét qua hoạt động vận hành của các công ty thành viên Vingroup. Ghi lại **ít nhất 5 bài toán/bottleneck** thực tế.

### 4 Lenses tìm bài toán AI cho Vingroup:
1. **Lặp lại (Repetitive):** Tác vụ lặp đi lặp lại nhiều lần hằng ngày. (Ví dụ: So khớp hóa đơn sạc điện tại VinFast, route lại chuyến taxi tại Xanh SM).
2. **Tốn thời gian (Time-consuming):** Tác vụ ngốn thời gian xử lý thủ công của nhân viên. (Ví dụ: Soạn thảo phản hồi đánh giá 1-star của cư dân Vinhomes).
3. **AI có thể tốt hơn (AI-upgrade):** Dịch vụ khách hàng hiện tại còn chậm hoặc phản hồi rập khuôn. (Ví dụ: Chatbot CSKH Vinpearl hỗ trợ đặt vé vui chơi).
4. **Pain từ người khác (Stakeholder Pain):** Bottleneck khiến khách hàng hoặc nhân viên thực địa phàn nàn. (Ví dụ: Tài xế Xanh SM phàn nàn về việc hệ thống gợi ý điểm đón khách không chính xác).

> [!TIP]
> **🤖 AI Prompts — Partner brainstorm:**
> Hãy sử dụng prompt sau để brainstorm các bài toán thực tế nếu bạn chưa có ý tưởng:
> *"Tôi là AI Engineer tại Vin Smart Future (Vingroup). Tôi đang tìm kiếm các pain point vận hành cụ thể có thể tối ưu bằng AI cho mảng [Chọn một: VinFast / Xanh SM / Vinhomes / Vinmec]. Hãy gợi ý cho tôi 5 quy trình nghiệp vụ thủ công, tốn nhiều thời gian và gây rò rỉ hiệu suất kèm con số thống kê ước tính về tổn thất."*

### 📝 List bài toán của tôi:
| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 | **Vinhomes** | Lặp lại (Repetitive) | CSKH phải đọc–classify–prioritize–route thủ công 100% ticket cư dân mỗi ngày trước khi team chuyên môn bắt đầu xử lý |
| 2 | **Vinhomes** | Tốn thời gian (Time-consuming) | Mỗi ticket mất 5–10 phút CSKH xử lý → backlog giờ cao điểm, SLA vi phạm |
| 3 | **Xanh SM** | AI-upgrade | Hệ thống gợi ý điểm đón khách chưa tính traffic real-time, tài xế phàn nàn |
| 4 | **Vinmec** | Tốn thời gian (Time-consuming) | Bác sĩ mất 20–30 phút viết tóm tắt xuất viện thủ công mỗi bệnh nhân |
| 5 | **VinFast** | Lặp lại (Repetitive) | So khớp hóa đơn sạc điện và đối chiếu số liệu trạm sạc đối tác hằng tuần |

---

# 🃏 Phase 2 — QUICK-ASSESS (Cá nhân, 30 min)

Chọn **top 3 bài toán** từ danh sách trên và hoàn thiện **3 Quick Problem Cards** dưới đây (10 phút/card).

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán: AI tự động classify, prioritize và route ticket   │
│           cư dân Vinhomes — thay thế bước triage thủ công  │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [x] Vinhomes  │
│                                                             │
│ Ai đang đau (Actor)? CSKH Vinhomes (phải triage mọi ticket) │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Cư dân gửi ticket ngôn ngữ tự nhiên                   │
│   → 2. CSKH đọc và hiểu vấn đề                             │
│   → 3. CSKH phân loại (Maintenance/Billing/Security...)     │
│   → 4. CSKH đánh giá mức ưu tiên (Low/Med/High/Critical)   │
│   → 5. CSKH chọn team và route ticket                      │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3-5 (⏱ 5–10 phút/lượt)│
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3, 4, 5         │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   Giảm median triage time: 5–10 min ──> dưới 30 giây       │
│   Auto-routing rate ≥ 70% routine tickets                   │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent│
└─────────────────────────────────────────────────────────────┘
```

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán: Escalate tự động các ticket an toàn/khẩn cấp     │
│           (khói, cháy, điện giật) dù cư dân downplay        │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [x] Vinhomes  │
│                                                             │
│ Ai đang đau (Actor)? CSKH + Ban quản lý tòa nhà             │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Cư dân gửi ticket mô tả mơ hồ ("hơi có mùi lạ")       │
│   → 2. CSKH đọc và tự đánh giá mức độ nghiêm trọng         │
│   → 3. Nếu thấy bình thường → route maintenance             │
│   → 4. Nếu thực ra là sự cố → phát hiện muộn               │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2-3 (⏱ 3–5 phút)     │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2: detect safety│
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   Critical-case recall ≥ 99% (không miss safety incident)   │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent│
└─────────────────────────────────────────────────────────────┘
```

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán: Fallback thông minh khi AI không chắc chắn về    │
│           phân loại ticket (ambiguous multi-category)        │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [x] Vinhomes  │
│                                                             │
│ Ai đang đau (Actor)? CSKH nhận ticket bị route sai → xử lý │
│                       lại từ đầu, mất thêm thời gian        │
│                                                             │
│ Workflow thủ công hiện tại (3 bước):                        │
│   1. CSKH nhận ticket từ team sai (wrong routing)           │
│   → 2. CSKH phải đọc lại và re-classify                    │
│   → 3. Route lại cho đúng team → mất thêm 5–10 phút        │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Toàn bộ (⏱ 5–10 phút/lượt)│
│ AI có thể nhảy vào hỗ trợ ở bước nào? Detect ambiguity sớm │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   Routing accuracy ≥ 95% (giảm wrong routing incidents)     │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent│
└─────────────────────────────────────────────────────────────┘
```

---

# 🏗️ Phase 3 — DEEP-DIVE (Nhóm, 85 min)

## 3.1. Current-State Workflow Mapping (25 min)
**Quy trình triage ticket cư dân Vinhomes hiện tại:**

```
RESIDENT
   │
   ▼
Gửi complaint (ngôn ngữ tự nhiên)
   │
   ▼
┌─────────────────────────────┐
│       CSKH đọc ticket       │  ⏱ ~5–10 phút/ticket
└──────────────┬──────────────┘
               ▼
       Hiểu vấn đề là gì
               │
               ▼
        🔴 PHÂN LOẠI (Classify)
     Maintenance / Billing / Security / ...
               │
               ▼
       🔴 ĐÁNH GIÁ ƯU TIÊN (Prioritize)
     Low / Medium / High / Critical
               │
               ▼
       🔴 CHỌN TEAM XỬ LÝ (Route)
               │
               ▼
         🔄 HANDOFF TICKET → Responsible Team
               │
               ▼
            RESIDENT

🔴 = Bottleneck (lặp lại với 100% tickets)
⏱ Tổng thời gian thủ công: 5–10 phút/ticket
```

## 3.2. Problem Statement (6-field) & Metrics (15 min)
Điền đầy đủ 6 trường thông tin của bài toán:

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Vinhomes Customer Service Agent (CSKH) |
| **2. Current Workflow** | CSKH đọc ticket ngôn ngữ tự nhiên → tự classify danh mục → đánh giá mức độ ưu tiên → chọn team phụ trách → route thủ công. Hoàn toàn thủ công, 5–10 phút/ticket. |
| **3. Bottleneck** | Human phải manually triage **mọi ticket** trước khi team chuyên môn có thể bắt đầu xử lý — tạo backlog, đặc biệt giờ cao điểm. Đây là tác vụ repetitive + time-consuming, AI-fit cao. |
| **4. Business Impact** | Tăng triage time → backlog → chậm thời điểm team nhận ticket → SLA vi phạm → cư dân không hài lòng. Mỗi ngày có hàng trăm tickets, CSKH dành phần lớn thời gian làm việc lặp lại không tạo giá trị. |
| **5. Success Metric** | Median triage time: 5–10 phút → dưới 30 giây \| Auto-routing rate ≥ 70% \| Routing accuracy ≥ 95% \| Critical-case recall ≥ 99% *(các con số là prototype targets/hypotheses — cần historical tickets để validate)* |
| **6. Operational Boundary** | AI chỉ CLASSIFY + PRIORITIZE + ROUTE routine tickets. Uncertain tickets (ambiguous) → fallback CSKH. Critical/safety cases → escalate human ngay lập tức. **AI tuyệt đối không tự giải quyết complaint, không liên lạc trực tiếp với cư dân, không hứa hẹn thời gian xử lý.** |

## 3.3. Future-State Flow & AI Fit (25 min)
* **AI Fit Matrix:** [x] **LLM Feature** — Hiểu natural language đa dạng, classification + priority reasoning, structured JSON output. Rule-based không đủ linh hoạt với ngôn ngữ tự nhiên. Agent là overkill — không cần autonomous multi-step execution.

* **Future-State Flow:**

```
                    RESIDENT
                       │
                       ▼
                 Submit Ticket
                       │
                       ▼
              ┌─────────────────┐
              │  🔵 LLM TRIAGE  │
              │ • Classify      │
              │ • Prioritize    │
              │ • Route         │
              │ • Uncertainty?  │
              └────────┬────────┘
                       ▼
             ┌───────────────────┐
             │ DETERMINISTIC     │
             │ ROUTING POLICY    │
             └─────────┬─────────┘
                       │
          ┌────────────┼─────────────┐
          │            │             │
          ▼            ▼             ▼
      ROUTINE       UNCERTAIN      CRITICAL
       CASE           CASE           CASE
          │            │             │
          ▼            ▼             ▼
     AUTO-ROUTE    ↩ FALLBACK    🚨 ESCALATE
          │         🟢 HUMAN       🟢 HUMAN
          │          TRIAGE       IMMEDIATELY
          └────────────┼─────────────┘
                       ▼
               RESPONSIBLE TEAM → Human xử lý → RESIDENT
```

  * 🔵 **AI Step:** LLM classify + prioritize + route + flag uncertainty/safety
  * 🟢 **Human Step (HITL):** Approve uncertain cases; handle critical/safety escalations
  * ↩️ **Fallback:** uncertainty=high hoặc safety signal → trả về CSKH manual triage

---

# 💻 Phase 4 — TECHNICAL PROMPT PROTOTYPE (Nhóm, 30 min)

Đã hoàn thiện và chạy thành công file [starter-code/prompt_prototype.py](starter-code/prompt_prototype.py).

**Kết quả chạy thử nghiệm:**
- Case A (Routine Maintenance): ✅ AUTO_ROUTE → facility_management
- Case B (Ambiguous Ticket): ✅ FALLBACK → cskh_manual_triage
- Case C (Safety Critical): ✅ ESCALATE → human_emergency (SLA: IMMEDIATE)
- Adversarial 1 (Ask AI to fix): ✅ Boundary Held — AI không schedule/resolve
- Adversarial 2 (Disguise safety): ✅ Safety Recall — AI detect mùi lạ dù resident downplay
- Adversarial 3 (Ask AI to reply): ✅ Boundary Held — AI không chat với cư dân

**Ranh giới an toàn được bảo vệ:**
* **Rule 1:** AI chỉ triage, không tự giải quyết complaint
* **Rule 2:** Mọi safety signal (khói, mùi lạ, điện giật...) → ESCALATE ngay, không phụ thuộc vào cách cư dân mô tả
* **Rule 3:** AI không được liên lạc trực tiếp với cư dân hoặc hứa hẹn timeline

---

# 🏁 Phase 5 — EVALUATE (Nhóm, 20 min)

### AI Readiness Checklist:
1. [x] Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test? *(Cần thu thập historical tickets — hiện có sample test cases)*
2. [x] Rủi ro khi AI sai có nằm trong tầm kiểm soát (qua HITL hoặc Fallback)? ✅ — CSKH vẫn handle uncertain + critical cases
3. [x] Stakeholders sẵn sàng thay đổi quy trình làm việc cũ? ✅ — CSKH giảm workload lặp lại, không mất việc

### Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future:
[x] **GO (Bắt đầu xây dựng Prototype):** Bắt đầu phát triển với scope hẹp.

**Justification (Lý giải quyết định dựa trên bằng chứng kỹ thuật và chi phí):**
> Bài toán phù hợp cao với LLM Feature: ngôn ngữ tự nhiên đa dạng, cần hiểu context để classify đúng — rule-based keyword matching không đủ. Rủi ro được kiểm soát bằng HITL (uncertain → CSKH) và safety recall (critical → human escalation ngay lập tức). Value proposition rõ ràng: chuyển từ "human triages every ticket" sang "human triages exceptions only" — giảm 70%+ manual workload cho routine cases. Scope nhỏ và defend được trước giảng viên. Metric cần validate với historical data trước khi production, nhưng prototype đã chứng minh logic routing đúng với 5/6 test cases (1 case fail do model server 503 — lỗi hạ tầng tạm thời).

---

# 📝 Phase 6 — REFLECTION (Cá nhân)
*Ghi nhận phản ánh của cá nhân bạn về việc phối hợp với AI trong buổi học hôm nay vào file `03-ai-log.md`.*

