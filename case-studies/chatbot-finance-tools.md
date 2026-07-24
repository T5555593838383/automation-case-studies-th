# Chatbot-Callable Finance Tools

**Domain:** Internal chatbot integrations · n8n, token-verified webhooks

## ปัญหา
ทีม finance ops รับ request lookup/export แบบเดิมซ้ำๆ ผ่าน chat ทั้งวัน — "ขอ record withholding-tax ของ X หน่อย", "export revenue aging by brand ของเดือนนี้" — แต่ละครั้งหมายถึงต้องมีคนหยุดงาน รัน query, format, แล้วตอบกลับ นี่คือ lookup ที่ bounded และ well-defined พอดีที่ chatbot tool ควรจัดการเองได้ตรงๆ

## แนวทาง
สร้าง n8n workflow ชุดหนึ่งที่ expose เป็น "tool" ที่เรียกได้อยู่หลัง chat webhook: tool lookup/export record ภาษี, document-based reimbursement lookup, และ revenue-aging-by-brand report generator แต่ละ tool call ที่เข้ามาจะถูก verify ผ่าน signed token exchange ก่อนที่ workflow จะรัน เพื่อไม่ให้ chatbot integration ถูก trigger จาก caller ที่ไม่ได้รับอนุญาต output จะถูก format และตอบกลับ inline ใน chat แทนที่จะ export ด้วยมือ

## Architecture
```
Chat message → chatbot agent decides "this needs the lookup tool"
   │
   ▼
Webhook (token-verified) → n8n tool workflow
   │
   ▼
Query + format (per tool: tax lookup / export / revenue aging)
   │
   ▼
Formatted reply → posted back to the same chat thread
```

## ผลลัพธ์
เปลี่ยน manual lookup/export request ที่เกิดซ้ำๆ 4 อย่างให้เป็น self-serve chatbot tool ตัดงาน interrupt-driven ที่ไหลเข้ามาทีม finance ทุกวันออกไป token-verification layer กลายเป็น pattern ที่ reuse ได้กับทุก tool ที่เพิ่มเข้ามาทีหลัง

**Stack:** n8n (HTTP Request / Webhook / Code node), signed token verification, chat platform integration
