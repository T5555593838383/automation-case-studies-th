# Revenue & Billing Automation

**Domain:** Revenue operations · n8n + PDF generation

## ปัญหา
ทีม revenue สร้าง billing-note PDF ด้วยมือทุกรอบ invoice และ track สถานะใบเสนอราคาที่รอออก invoice ในอีก sheet แยกกัน สองระบบนี้ desync กันได้แบบไม่รู้ตัว — เมื่อใบเสนอราคาถูก cancel หลังจากถูก mark ว่า "awaiting invoicing" ไปแล้ว ไม่มีอะไร sync กลับไปบอกว่า cancel แล้ว ทำให้เหลือรายการค้างที่ดูเหมือนงานยังไม่เสร็จ

## แนวทาง
สร้าง n8n workflow ที่กรอก template แล้ว generate billing-note PDF จากข้อมูลต้นทางอัตโนมัติ พร้อม workflow คู่กันที่ sync สถานะใบเสนอราคาเข้า tracking sheet ให้ตรงกับสถานะ invoicing เพิ่ม cancel-event listener ที่เขียนสถานะ cancel กลับเข้า tracking sheet ปิดช่องโหว่ที่ทำให้แถว "awaiting invoicing" ค้างเก่าสะสม

## Architecture
```
Source system (quotations / billing data)
   │
   ▼
n8n workflow — template fill → billing-note PDF generation
   │
   ▼
Quotation status sync — awaiting-invoicing tracking sheet
   │
   ▼
Cancel-event listener → sync สถานะ cancel กลับเข้า source status
```

## ผลลัพธ์
ตัดขั้นตอน generate PDF ด้วยมือต่อ invoice ออกไปทั้งหมด และแก้บัค production จริงที่ใบเสนอราคา cancel ไปแล้วยังโชว์เป็น pending invoicing อยู่ — tracking sheet สะท้อนสถานะจริงโดยไม่ต้องมา reconcile มือ

**Stack:** n8n, Google Sheets, PDF generation node, webhook-driven status sync
