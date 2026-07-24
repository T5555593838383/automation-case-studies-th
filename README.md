# Automation & Reconciliation Case Studies (ภาษาไทย)

รวม case study จากการสร้างระบบ automation งาน finance ops: reconciliation SQL, n8n workflow pipeline, chatbot tool integration, และ reporting automation แต่ละอันครอบคลุม ปัญหา → แนวทาง → architecture → ผลลัพธ์ — รายละเอียดเฉพาะบริษัท ID จริง และตัวเลขธุรกิจ ตัดออกโดยตั้งใจ สิ่งที่มีคุณค่าตรงนี้คือ engineering pattern ไม่ใช่ business data เบื้องหลัง

(English version: [automation-case-studies](https://github.com/T5555593838383/automation-case-studies))

## Case studies

| Case study | Domain |
|---|---|
| [Driver-Balance Adjustment Pipeline](case-studies/driver-balance-adjustment-pipeline.md) | Python + Google Sheets + n8n batch validation |
| [Payment Reconciliation SQL Template Library](case-studies/payment-reconciliation-sql-templates.md) | LLM-assisted SQL generation, documentation-as-context |
| [Chatbot-Callable Finance Tools](case-studies/chatbot-finance-tools.md) | n8n tool workflows หลัง token-verified webhook |
| [Voucher Compensation — Multi-Stage Dispatch Pipeline](case-studies/voucher-compensation-bulk-dispatch.md) | 5-stage n8n pipeline, reproducible deployment |
| [Tax-Document Validation & Alert Workflow](case-studies/wht-document-validation-alert.md) | n8n rule-based document validation |
| [Reimbursement Edit-Notification Loop](case-studies/wht-edit-notification-loop.md) | Scheduled folder-watch + bounce handling |
| [Scanned-Document OCR Verification Workflow](case-studies/wht-obh-document-verification.md) | OCR/vision extraction + rule-based approval |
| [Monthly Adjustment Report Builder](case-studies/monthly-driver-adjust-report.md) | Email parsing + spreadsheet diffing automation |
| [Scheduled Incentive Report Workflow](case-studies/incentive-report-email-wf.md) | n8n workflow generate & deploy ผ่าน Python |
| [Scheduled Split-Fee/Subsidy Report Workflow](case-studies/split-fee-report-workflow.md) | Reuse pattern เดิม, build รอบสองเร็วขึ้น |
| [Invoice ↔ Email Cross-Check Script](case-studies/invoice-email-cross-check.md) | Automated billing verification |
| [Merchant Commission Report — Chatbot Tool](case-studies/merchant-commission-report-tool.md) | OAuth2 + async API integration เป็น chat tool |
| [Multimodal Chat Agent with Abuse Rate-Limiting](case-studies/multimodal-chatbot-agent.md) | Vision/voice input, LLM agent, abuse throttling |
| [Warehouse-Fulfillment Model — Requirements Analysis](case-studies/warehouse-fulfillment-prd-analysis.md) | Settlement/PRD design work (non-code) |

## ที่เกี่ยวข้อง

ดู [ai-agent-orchestrator](https://github.com/T5555593838383/ai-agent-orchestrator) ด้วย — multi-agent orchestration system (plan → delegate → review) ที่ build บน Claude API ใช้เป็น coordination layer สำหรับงาน automation ที่กล่าวถึงในนี้

## หมายเหตุเรื่อง scope

นี่คือ internal engineering case study ชื่อบริษัท ชื่อ internal system/tool, ID จริง, และตัวเลขธุรกิจ ตัดออกโดยตั้งใจ สิ่งที่แสดงคือรูปแบบปัญหา, architecture, และผลลัพธ์ ซึ่งเป็นสิ่งที่ transferable และควรค่าแก่การโชว์ใน portfolio จริงๆ
