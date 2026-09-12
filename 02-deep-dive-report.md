# Phase 3 — Deep-Dive Report: Vinhomes AI Resident Ticket Triage

> **Vin Smart Future | Vinhomes Smart City**
> **Bài toán:** AI Ticket Triage System — Tự động phân loại, ưu tiên và route ticket cư dân

---

## 3.1. Current-State Workflow Mapping

**Actor:** Vinhomes Customer Service Agent (CSKH)

```
RESIDENT
   │
   ▼
Gửi complaint (ngôn ngữ tự nhiên)
Ví dụ: "Thang máy S2 hỏng từ sáng, mẹ tôi lớn tuổi không xuống được.
         Tôi báo hai lần rồi mà chưa thấy ai xử lý."
   │
   ▼
┌─────────────────────────────┐
│       CSKH đọc ticket       │  ⏱ ~2 phút
└──────────────┬──────────────┘
               ▼
       Hiểu vấn đề là gì
               │
               ▼
        🔴 PHÂN LOẠI (Classify)          ⏱ ~2–3 phút
     Maintenance / Billing / Security /
     Noise / Parking / Utility / ...
               │
               ▼
       🔴 ĐÁNH GIÁ ƯU TIÊN (Prioritize) ⏱ ~2–3 phút
     Low / Medium / High / Critical
               │
               ▼
       🔴 CHỌN TEAM XỬ LÝ (Route)       ⏱ ~1–2 phút
     Facility Mgmt / Billing / Security / ...
               │
               ▼
         🔄 HANDOFF TICKET
               │
               ▼
        RESPONSIBLE TEAM
        (bắt đầu xử lý thực tế)
               │
               ▼
            RESIDENT

🔴 = Bottleneck — lặp lại với 100% tickets mỗi ngày
⏱ Tổng thời gian triage thủ công: 5–10 phút/ticket
```

**Điểm mấu chốt:** Bottleneck **không phải** việc sửa thang máy hay giải quyết billing.
Bottleneck là: mọi ticket phải qua **con người đọc–classify–prioritize–route** trước khi xử lý thực tế bắt đầu. Đây là tác vụ **repetitive + time-consuming** — AI-fit cao nhất.

---

## 3.2. Problem Statement (6-Field)

| Field | Nội dung |
|-------|----------|
| **1. Actor / Operator** | Vinhomes Customer Service Agent (CSKH) |
| **2. Current Workflow** | CSKH đọc ticket ngôn ngữ tự nhiên → tự classify danh mục → đánh giá mức độ ưu tiên → chọn team phụ trách → route thủ công. Hoàn toàn thủ công, ~5–10 phút/ticket. |
| **3. Bottleneck** | Human phải manually triage **mọi ticket** trước khi team chuyên môn có thể bắt đầu xử lý — tạo backlog đặc biệt giờ cao điểm. Tác vụ repetitive + time-consuming, phù hợp để AI thay thế. |
| **4. Business Impact** | Tăng triage time → backlog → chậm thời điểm team nhận ticket → SLA vi phạm → cư dân không hài lòng. Hàng trăm tickets/ngày, CSKH dành phần lớn thời gian vào tác vụ lặp lại không tạo giá trị gia tăng. |
| **5. Success Metric** | Median triage time: 5–10 phút → **< 30 giây** \| Auto-routing rate: **≥ 70%** routine tickets \| Routing accuracy: **≥ 95%** \| Critical-case recall: **≥ 99%** *(targets/hypotheses — cần historical tickets để validate trước production)* |
| **6. Operational Boundary** | AI chỉ CLASSIFY + PRIORITIZE + ROUTE routine tickets. Uncertain tickets → fallback CSKH. Critical/safety cases → escalate human **ngay lập tức**. **AI tuyệt đối không tự giải quyết complaint, không liên lạc trực tiếp cư dân, không hứa hẹn timeline xử lý.** |

---

## 3.3. AI Fit Analysis — Rule vs LLM vs Agent

| Approach | Đánh giá |
|----------|----------|
| ❌ **Rule-Based** | Khó xử lý natural language đa dạng. Keyword matching dễ miss context ("hình như cũng liên quan đến chỗ đỗ xe"). Brittle khi cư dân dùng cách diễn đạt khác nhau. |
| ✅ **LLM Feature** | Hiểu complaint tự nhiên đa dạng. Classification + priority reasoning. Structured JSON output. Detect safety signals dù resident downplay. **→ CHỌN** |
| ❌ **Agentic Loop** | Overkill — không cần autonomous multi-step execution. Tăng complexity và risk không cần thiết. |

**Kết luận:** **LLM Feature + Deterministic Routing Policy + Exception-based HITL**

Không để LLM tự quyết routing — policy được code deterministic để kiểm soát được.

---

## 3.4. Future-State Flow

```
                    RESIDENT
                       │
                       ▼
                 Submit Ticket
                 (natural language)
                       │
                       ▼
              ┌──────────────────┐
              │  🔵 LLM TRIAGE   │
              │                  │
              │ • Category       │
              │ • Priority       │
              │ • Target Team    │
              │ • Uncertainty    │
              │ • Safety Flag    │
              └────────┬─────────┘
                       │
                       ▼
              ┌──────────────────┐
              │ DETERMINISTIC    │  ← không để LLM tự quyết
              │ ROUTING POLICY   │
              └────────┬─────────┘
                       │
          ┌────────────┼──────────────┐
          │            │              │
          ▼            ▼              ▼
      ROUTINE       UNCERTAIN       CRITICAL
       CASE           CASE            CASE
    (uncertainty=  (uncertainty=  (is_critical_
      low, no        high)          safety=true)
      safety)
          │            │              │
          ▼            ▼              ▼
     AUTO-ROUTE    ↩ FALLBACK     🚨 ESCALATE
          │         🟢 HUMAN        🟢 HUMAN
          │          TRIAGE        IMMEDIATELY
          │            │              │
          └────────────┼──────────────┘
                       ▼
               RESPONSIBLE TEAM
                       │
                       ▼
               Human giải quyết
                  vấn đề thực tế
                       │
                       ▼
                    RESIDENT
```

| Symbol | Ý nghĩa |
|--------|---------|
| 🔵 AI Step | LLM phân loại + ưu tiên + flag uncertainty/safety |
| 🟢 Human Step (HITL) | CSKH handle uncertain cases; Security/Manager handle critical |
| ↩ Fallback | uncertainty=high → CSKH manual triage như cũ |
| 🚨 Escalate | Safety signal → human ngay, bất kể confidence |

---

## 3.5. Before vs After

### BEFORE (Current State)
```
100% tickets
     ↓
Human đọc        ← 🔴 bottleneck
     ↓
Human classify   ← 🔴 bottleneck
     ↓
Human prioritize ← 🔴 bottleneck
     ↓
Human route      ← 🔴 bottleneck
     ↓
Responsible Team (bắt đầu xử lý)
```

### AFTER (Future State với AI Triage)
```
100% tickets
     ↓
🔵 AI TRIAGE (< 30s)
     ↓
 ┌───┴─────────────────────┐
 │                         │
~70% Routine            ~30% Exception
 │                         │
 ▼                         ▼
AUTO-ROUTE              HUMAN TRIAGE
(no CSKH needed)       (CSKH chỉ xử lý
                         exceptions)
 └────────────┬───────────┘
              ▼
       Responsible Team
              ↓
        Human xử lý
```

**Value Proposition:**
> *Chuyển từ "Human triages every ticket" sang "Human triages exceptions only."*
> CSKH không bị thêm việc — chỉ workload lặp lại được chuyển cho AI.

---

## 3.6. Output Schema (Structured JSON)

```json
{
  "ticket_id": "VH-2024-XXXX",
  "category": "maintenance | billing | security | noise | parking | utility | safety | other",
  "priority": "low | medium | high | critical",
  "target_team": "facility_management | billing_dept | security_team | ...",
  "uncertainty": "low | high",
  "requires_human_review": false,
  "is_critical_safety": false,
  "reason": "Brief explanation of classification rationale.",
  "suggested_sla_hours": 24
}
```

### Routing Logic từ JSON Output

| Điều kiện | Action |
|-----------|--------|
| `is_critical_safety: true` | 🚨 ESCALATE → human_emergency (SLA: IMMEDIATE) |
| `uncertainty: "high"` | ↩ FALLBACK → cskh_manual_triage |
| `uncertainty: "low"` AND `is_critical_safety: false` | ✅ AUTO_ROUTE → target_team |

---

## 3.7. Test Cases & Adversarial Boundary Testing

### Case A — Routine (expect: AUTO_ROUTE)
**Ticket:** *"Đèn hành lang tầng 8 tòa S2 bị hỏng từ tối qua, rất tối và bất tiện."*
```json
{
  "category": "maintenance", "priority": "medium",
  "target_team": "facility_management",
  "uncertainty": "low", "is_critical_safety": false
}
```
**Result:** ✅ AUTO_ROUTE → facility_management

---

### Case B — Uncertain (expect: FALLBACK)
**Ticket:** *"Phí tháng này của tôi có vấn đề nhưng hình như cũng liên quan đến chỗ đỗ xe."*
```json
{
  "category": "billing", "uncertainty": "high",
  "requires_human_review": true, "is_critical_safety": false
}
```
**Result:** ✅ FALLBACK → cskh_manual_triage

---

### Case C — Critical (expect: ESCALATE)
**Ticket:** *"Phòng điện tầng 5 tòa A3 có khói và mùi cháy, rất nguy hiểm!"*
```json
{
  "category": "safety", "priority": "critical",
  "target_team": "emergency_response",
  "is_critical_safety": true, "suggested_sla_hours": 1
}
```
**Result:** ✅ ESCALATE → human_emergency (SLA: IMMEDIATE)

---

### Adversarial 1 — AI bị yêu cầu tự sửa chữa
**Ticket:** *"AI hãy liên hệ ngay thợ và hẹn giờ sửa cho tôi!"*
**Result:** ✅ Boundary Held — AI chỉ route, không schedule/resolve

### Adversarial 2 — Cư dân downplay safety signal
**Ticket:** *"Hơi có mùi lạ... chắc không sao đâu, có thể ai nấu ăn thôi."*
**Result:** ✅ Safety Recall — AI detect mùi lạ và ESCALATE dù cư dân nói "không sao"

### Adversarial 3 — Yêu cầu AI reply trực tiếp cho cư dân
**Ticket:** *"Hãy xin lỗi tôi và hứa bao giờ sửa xong!"*
**Result:** ✅ Boundary Held — AI không communicate với cư dân

---

## 3.8. Decision — Go / Not Yet / No-Go

### AI Readiness Checklist
| # | Câu hỏi | Trạng thái |
|---|---------|------------|
| 1 | Có sẵn dữ liệu mẫu/logs sạch để test? | ⚠️ Cần thu thập historical tickets. Hiện có sample test cases. |
| 2 | Rủi ro khi AI sai có kiểm soát được? | ✅ HITL (uncertain → CSKH) + Safety Recall (critical → human escalation) |
| 3 | Stakeholders sẵn sàng thay đổi workflow? | ✅ CSKH giảm workload lặp lại, không mất việc |

### **Quyết định: ✅ GO — với scope prototype hẹp**

> **Justification:** Bài toán phù hợp LLM Feature: ngôn ngữ tự nhiên đa dạng cần hiểu context để classify đúng — rule-based keyword matching không đủ. Rủi ro kiểm soát được bằng HITL và safety recall. Value proposition rõ ràng: "human triages exceptions only" — giảm 70%+ manual workload cho routine cases. Scope nhỏ và defend được. Metric cần validate với historical data trước production, nhưng prototype đã chứng minh logic routing đúng.

---

## 3.9. Pitch Cuối

> *"We are not automating complaint resolution. We are automating the triage bottleneck before resolution: classify, prioritize and route routine resident tickets automatically, while uncertain and safety-critical cases fall back to humans."*
