# Monthly Adjustment Report Builder

**Domain:** Reporting automation · Python, email parsing, spreadsheet diffing

## ปัญหา
รายงาน reconciliation รายเดือนต้อง pull อีเมล approval พร้อม attachment spreadsheet, เทียบกับ record manual-adjustment ของเดือนก่อน แล้วประกอบเป็นรายงานรวม — ทำด้วยมือทุกเดือน หมายความว่าต้องทำ lookup/compare/assemble sequence เดิมซ้ำทุก cycle

## แนวทาง
สร้าง Python pipeline ที่ fetch อีเมล approval พร้อม download attachment อัตโนมัติ, เทียบ spreadsheet manual-adjustment ของแต่ละเดือนกับ reference structure เพื่อจับ mismatch หรือ category ที่หายไป แล้วประกอบรายงานรายเดือนรวมจากผลลัพธ์ — match กับ category/sheet structure ของ reference report เดิมเป๊ะ ทำให้การเปลี่ยนมาใช้ automation มองไม่เห็นจากฝั่ง downstream

## Architecture
```
Mailbox (approval emails with attachments)
   │
   ▼
Download + unzip attachments (Python)
   │
   ▼
Compare against prior-month reference structure (category/sheet diff)
   │
   ▼
Assemble consolidated monthly report (matches existing reference format)
```

## ผลลัพธ์
ลดงาน manual รายเดือนที่ใช้เวลาทั้งวันเหลือแค่รัน script เดียว output ถูก verify ว่าตรงกับ structure ของ reference report เดิมเป๊ะ (category เดียวกัน, sheet layout เดียวกัน) ทำให้ downstream process ไม่ต้องเปลี่ยนอะไรเลย

**Stack:** Python (email/IMAP handling, zip extraction, spreadsheet diffing และ generation)
