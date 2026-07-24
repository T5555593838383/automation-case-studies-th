# Scheduled Incentive Report Workflow

**Domain:** Reporting automation · n8n, Python workflow-generation, dedup logic

## ปัญหา
รายงาน promo/incentive แบบ recurring ถูกส่งด้วยมือตามกำหนดเวลา — ต้องมีคน pull ข้อมูลต้นทาง, format, แล้วส่งอีเมลไปยัง distribution list ไม่มีการป้องกันไม่ให้ส่งรายงานของ period เดียวกันซ้ำโดยไม่ตั้งใจถ้า process ถูก trigger ซ้ำ

## แนวทาง
เขียน Python generator script ที่ generate workflow JSON ของ n8n แล้ว push เข้าไปตรงๆ ผ่าน n8n API แทนการสร้างด้วยมือผ่าน UI — ทำให้ workflow reproducible และมี version เป็น code workflow มี dedup check 3 เงื่อนไข (period + source file + identifying key) เพื่อไม่ให้ retrigger หรือ retry ทำให้ส่งซ้ำ พร้อม error-alert workflow คู่กันที่ยิงเมื่อ scheduled run fail

## Architecture
```
Python generator script → workflow JSON → pushed via n8n API
   │
   ▼
Scheduled trigger (n8n) → pull source data → format report
   │
   ▼
Dedup check (period + source file + key) — skip if already sent
   │
   ▼
Email report to distribution list
   │
   └─ On failure → paired error-alert workflow notifies the team
```

## ผลลัพธ์
เปลี่ยนรายงานที่ trigger ด้วยมือให้เป็น scheduled send แบบไม่ต้องดูแล dedup check ตัดความเสี่ยง double-send ที่เกิดขึ้นได้เมื่อทั้ง schedule และ manual retry ทำงานพร้อมกัน

**Stack:** n8n (Schedule Trigger, Email node), Python (workflow generation + push ผ่าน n8n REST API), HTML guide สำหรับเอกสาร handover
