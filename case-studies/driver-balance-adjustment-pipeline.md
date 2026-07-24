# Driver-Balance Adjustment Pipeline

**Domain:** Gig-economy fintech · Python + Google Sheets + n8n

## ปัญหา
ทีม finance ops ปรับยอด balance ของ driver แต่ละคนด้วยมือจากคิว spreadsheet ที่ใช้ร่วมกัน — ช้า เกิด error ง่าย และตรวจสอบย้อนหลังแทบไม่ได้เมื่อ volume เกินสองสามร้อยแถวต่อวัน driver ID ซ้ำกันใน batch เดียวจะทำให้การ upload พังโดยไม่รู้ตัว เพราะไม่มีขั้นตอน dedup หรือ validation

## แนวทาง
สร้าง Python pipeline ที่อ่านคิว adjustment, validate แต่ละแถว (format ตัวเลข ต้อง ≤2 decimal ตามข้อจำกัดของระบบปลายทาง, ตรวจ duplicate driver-ID), แยก batch ที่มี driver ID ซ้ำออกเป็นหลาย sub-batch ที่สะอาด แล้ว push ผลลัพธ์เข้า Google Sheet สำหรับ track งาน คู่กับ n8n workflow ที่รัน BAU (business-as-usual) confirmation loop แบบ scheduled, poll หา submission ใหม่ แล้วแจ้งเตือนทีมเมื่อแต่ละ batch เสร็จ

## Architecture
```
Adjustment queue (Sheet)
   │
   ▼
Python validator — decimal check · duplicate-ID split · dry-run diff
   │
   ▼
Push to tracking sheet (batched, one clean batch per unique driver set)
   │
   ▼
n8n scheduled poller → BAU confirm loop → chat notification on completion
```

## ผลลัพธ์
ตัดขั้นตอนปรับยอดทีละแถวด้วยมือออกไปทั้งหมด บั๊ก duplicate-ID (ที่เคยทำให้ bulk upload บล็อกทั้ง batch) กลายเป็น pre-flight check แทนที่จะเป็น production incident ขนาด batch ขยายจากหลักสิบเป็นหลักร้อยแถวโดยไม่เพิ่มเวลา manual review

**Stack:** Python, gspread (Google Sheets API), n8n, Windows Task Scheduler สำหรับ trigger BAU cron
