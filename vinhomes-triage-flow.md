# Vinhomes AI Resident Ticket Triage — Design Document

> **Bối cảnh:** Vin Smart Future — AI Product Scoping Lab (Day 02)
> **Scope:** Vinhomes Smart City — Customer Service Operations
> **Architecture:** LLM Feature + Deterministic Routing Policy + Exception-based HITL

---

## 1. Bài Toán Hiện Tại (Current State)

Cư dân Vinhomes gửi ticket dưới dạng ngôn ngữ tự nhiên, ví dụ:

> *"Thang máy S2 hỏng từ sáng, mẹ tôi lớn tuổi không xuống được. Tôi báo hai lần rồi mà chưa thấy ai xử lý."*

### Current-State Workflow

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
         🔄 HANDOFF TICKET
               │
               ▼
┌─────────────────────────────┐
│ Team chuyên môn xử lý       │
│ vấn đề thực tế              │
└─────────────────────────────┘
               │
               ▼
            RESIDENT
```

### Bottleneck Xác Định

**Không phải** việc sửa thang máy / giải quyết billing / xử lý security.
**Bottleneck chính:** Mọi ticket đều phải qua con người đọc–classify–prioritize–route **trước khi** team chuyên môn có thể bắt đầu xử lý.

Đây là phần **repetitive + time-consuming** phù hợp với các lenses của worksheet.

---

## 2. Problem Statement (6-Field)

| Field | Nội dung |
|-------|----------|
| **1. Actor / Operator** | Vinhomes Customer Service Agent (CSKH) |
| **2. Current Workflow** | CSKH đọc ticket → classify danh mục → đánh giá mức độ ưu tiên → chọn team phụ trách → route thủ công |
| **3. Bottleneck** | Human phải manually triage **mọi ticket** trước khi team chuyên môn có thể bắt đầu xử lý — tạo backlog, đặc biệt giờ cao điểm |
| **4. Business Impact** | Tăng triage time → backlog → chậm thời điểm team nhận ticket → SLA vi phạm → cư dân không hài lòng |
| **5. Success Metric** | Giảm median triage time; tăng % tickets auto-routed; duy trì routing accuracy ≥ 95%; critical-case recall ≥ 99% |
| **6. Operational Boundary** | AI chỉ auto-route routine tickets; uncertain → fallback CSKH; critical/safety → escalate human ngay lập tức; AI **không tự giải quyết** complaint |

### Target Metrics (Prototype Hypotheses)

| Metric | Baseline (Current) | Target |
|--------|-------------------|--------|
| Median triage time | ~5–10 phút/ticket | < 30 giây |
| Auto-routing rate | 0% | ≥ 70% tickets |
| Routing accuracy | Manual (baseline cần đo) | ≥ 95% |
| Critical-case recall | Manual | ≥ 99% |

> ⚠️ **Lưu ý:** Những con số trên là **prototype targets / hypotheses**. Production cần historical labeled tickets để validate trước khi cho phép auto-routing thực tế.

---

## 3. AI Fit Analysis

### Rule vs LLM vs Agent

| Approach | Nhận xét |
|----------|----------|
| ❌ **Rule-Based** | Khó xử lý natural language đa dạng; keyword matching dễ miss context ("hình như cũng liên quan đến chỗ đỗ xe") |
| ✅ **LLM Feature** | Hiểu complaint tự nhiên; classification + priority reasoning; structured JSON output |
| ❌ **Agentic Loop** | Overkill — không cần autonomous multi-step execution; tăng complexity và risk không cần thiết |

**Kết luận:** **LLM Feature** + deterministic routing rules + exception-based HITL.

---

## 4. AI Chỉ Làm 3+1 Việc

```
AI TICKET TRIAGE SYSTEM — Nhiệm vụ:

  1. CLASSIFY   → Ticket thuộc vấn đề gì?
  2. PRIORITIZE → Mức độ ưu tiên thế nào?
  3. ROUTE      → Chuyển tới team nào?

  + SAFETY:
  4. ESCALATE   → Nếu không chắc / case nguy hiểm → human
```

**AI không trực tiếp giải quyết complaint.**

---

## 5. Future-State Flow (Tối Ưu)

```
                    RESIDENT
                       │
                       ▼
                 Submit Ticket
                       │
                       ▼
              ┌─────────────────┐
              │  🔵 LLM TRIAGE  │
              │                 │
              │ • Classify      │
              │ • Prioritize    │
              │ • Route         │
              │ • Uncertainty?  │
              └────────┬────────┘
                       │
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
          │            │             │
          │         🟢 HUMAN       🟢 HUMAN
          │          TRIAGE       IMMEDIATELY
          │            │             │
          └────────────┼─────────────┘
                       ▼
               RESPONSIBLE TEAM
                       │
                       ▼
               HUMAN RESOLVES
                  THE ISSUE
                       │
                       ▼
                    RESIDENT
```

### Luồng Quyết Định (Routing Policy)

```
IF   critical keywords detected (fire, gas leak, security threat, ...)
     → IMMEDIATE HUMAN ESCALATION  (không phụ thuộc uncertainty)

ELSE IF uncertainty == "high"
     → FALLBACK: CSKH manual triage

ELSE
     → AUTO-ROUTE to responsible team
```

---

## 6. AI Output Schema (Structured JSON)

```json
{
  "ticket_id": "VH-2024-XXXX",
  "category": "maintenance | billing | security | noise | parking | utility | other",
  "priority": "low | medium | high | critical",
  "target_team": "facility_management | billing_dept | security_team | ...",
  "uncertainty": "low | high",
  "requires_human_review": false,
  "is_critical_safety": false,
  "reason": "Brief explanation of classification rationale",
  "suggested_sla_hours": 24
}
```

### Routing Decision từ Output

| Điều kiện | Hành động |
|-----------|-----------|
| `is_critical_safety: true` | 🚨 Immediate human escalation |
| `uncertainty: "high"` | ↩ Fallback → CSKH manual triage |
| `uncertainty: "low"` AND `is_critical_safety: false` | ✅ AUTO-ROUTE to `target_team` |

> **Honest Note:** `uncertainty: "low"` hiện tại vẫn do LLM tự đánh giá — chưa phải measurement đáng tin cậy.
> **Production requirement:** Cần historical labeled tickets để xây dựng và validate confidence/risk mechanism trước khi cho phép full auto-routing.

---

## 7. Ví Dụ Chạy Qua Flow

### Case A — Routine (Auto-Route)

**Ticket:** *"Đèn hành lang tầng 8 tòa S2 bị hỏng."*

```json
{
  "category": "maintenance",
  "priority": "low",
  "target_team": "facility_management",
  "uncertainty": "low",
  "requires_human_review": false,
  "is_critical_safety": false,
  "reason": "Resident reports a corridor light malfunction on floor 8, Tower S2."
}
```

```
Ticket → AI → maintenance/low → AUTO-ROUTE → Facility Management → Human sửa đèn
```
✅ Không cần CSKH ngồi approve classification → **tiết kiệm thời gian**.

---

### Case B — Uncertain (Fallback to Human)

**Ticket:** *"Phí tháng này của tôi có vấn đề nhưng hình như cũng liên quan đến chỗ đỗ xe."*

```json
{
  "category": "billing",
  "priority": "medium",
  "target_team": "billing_dept",
  "uncertainty": "high",
  "requires_human_review": true,
  "is_critical_safety": false,
  "reason": "Ticket overlaps billing and parking domains. Classification ambiguous."
}
```

```
Ticket → AI → uncertainty=HIGH → ↩ FALLBACK → CSKH manual triage
```
✅ AI không chắc → **trả lại quy trình cũ**, không route sai.

---

### Case C — Critical (Immediate Escalation)

**Ticket:** *"Phòng điện tầng 5 có khói và mùi cháy."*

```json
{
  "category": "safety",
  "priority": "critical",
  "target_team": "security_team",
  "uncertainty": "low",
  "requires_human_review": true,
  "is_critical_safety": true,
  "reason": "Potential fire/safety incident detected. Immediate human escalation required."
}
```

```
Ticket → AI → is_critical_safety=TRUE → 🚨 IMMEDIATE ESCALATION → HUMAN (Security + Building Manager)
```
✅ Không quan tâm model có tự tin thế nào — **safety luôn trump uncertainty**.

---

## 8. Before vs After Comparison

### BEFORE (Current State)

```
100% tickets
     ↓
Human đọc
     ↓
Human classify  ← 🔴 bottleneck
     ↓
Human prioritize
     ↓
Human route
     ↓
Responsible Team (bắt đầu xử lý thực tế)
```

### AFTER (Future State với AI Triage)

```
100% tickets
     ↓
  🔵 AI TRIAGE
     ↓
 ┌───┴───────────────────────┐
 │                           │
Routine (~70%)           Exception (~30%)
 │                           │
 ▼                           ▼
AUTO-ROUTE               HUMAN TRIAGE
 │                        (CSKH chỉ xử lý
 │                         exception cases)
 └───────────┬─────────────┘
             ▼
      Responsible Team
             ↓
       Human giải quyết vấn đề thực tế
```

**Value Proposition:**
> *Chuyển từ "Human triages every ticket" sang "Human triages exceptions only."*

---

## 9. Architecture Summary

```
┌────────────┐
│  RESIDENT  │
└─────┬──────┘
      │ complaint (natural language)
      ▼
┌──────────────────────┐
│    🔵 LLM TRIAGE     │
│                      │
│ • Category           │
│ • Priority           │
│ • Target Team        │
│ • Uncertainty Flag   │
│ • Safety Flag        │
└──────────┬───────────┘
           ▼
┌──────────────────────┐
│ DETERMINISTIC POLICY │  ← không để LLM tự quyết routing
└──────────┬───────────┘
           │
     ┌─────┼───────────┐
     │     │           │
     ▼     ▼           ▼
 Routine Uncertain   Critical
     │     │           │
     │     ▼           ▼
     │   🟢 HUMAN    🟢 HUMAN
     │   TRIAGE     ESCALATION
     │     │           │
     └─────┴─────┬─────┘
                 ▼
        ┌─────────────────┐
        │ RESPONSIBLE TEAM│
        └────────┬────────┘
                 ▼
           Human resolves the issue
                 ▼
              RESIDENT
```

---

## 10. Pitch Cuối (One-Sentence)

> *"We are not automating complaint resolution. We are automating the triage bottleneck before resolution: classify, prioritize and route routine resident tickets automatically, while uncertain and safety-critical cases fall back to humans."*

---

## 11. Go / Not Yet / No-Go Decision

| Checklist | Status |
|-----------|--------|
| Dữ liệu mẫu / logs sạch để test? | ⚠️ Cần thu thập historical tickets |
| Rủi ro khi AI sai có kiểm soát được? | ✅ HITL + Fallback + Critical Escalation |
| Stakeholders sẵn sàng thay đổi workflow? | ✅ CSKH workload giảm, không mất việc |

**Quyết định: ✅ GO — với scope prototype hẹp**

> **Justification:** Bài toán phù hợp LLM (natural language understanding + structured output), HITL kiểm soát được rủi ro, value proposition rõ ràng (giảm manual triage workload), scope nhỏ và defend được. Bắt đầu với prototype trên sample tickets trước khi deploy production.
