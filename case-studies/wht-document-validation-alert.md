# Tax-Document Validation & Alert Workflow

**Domain:** Document processing automation · n8n, rule-based validation

## ปัญหา
เอกสารประกอบภาษีที่ submit เข้ามาสำหรับกระบวนการ withholding-tax ของ sub-brand หนึ่ง ต้องเช็ค required field และความถูกต้องก่อน accept — เดิมเป็น manual review step ที่จับ error ได้หลังเกิดเหตุแล้วเท่านั้น บางทีก็หลายวันถึงจะรู้ ต้อง back-and-forth ให้แก้เอกสารแล้ว submit ใหม่

## แนวทาง
สร้าง n8n workflow ที่ validate เอกสารที่ submit เข้ามาตาม required-field rule set ทันทีที่มาถึง แล้วยิง error/alert notification ทันทีถ้า validate ไม่ผ่าน — เปลี่ยนจาก manual catch ที่ล่าช้าให้เป็น automated catch แบบทันที

## Architecture
```
Document submission
   │
   ▼
n8n validation workflow — required-field rule check
   │
   ├─ Pass → proceed to processing
   └─ Fail → immediate error/alert notification to submitter
```

## ผลลัพธ์
ลด feedback loop ของ error เอกสารจากเป็นวันเหลือเกือบ real-time ลดจำนวนรอบ correction round-trip ต่อ submission ลงได้มาก

**Stack:** n8n (Webhook trigger, Code node สำหรับ rule validation), chat notification node
