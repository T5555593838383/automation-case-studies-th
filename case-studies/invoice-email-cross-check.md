# Invoice ↔ Email Cross-Check Script

**Domain:** Billing verification · Python, email parsing

## ปัญหา
ก่อน invoice/billing note จะถือว่า process เสร็จสมบูรณ์ ต้องเทียบกับอีเมล confirmation ที่ counterparty ส่งมา — เป็นการเทียบตัวเลขและ reference แบบ manual คือดูสองเอกสารเทียบกันด้วยตา

## แนวทาง
เขียน Python script ที่ pull ข้อมูล invoice ที่เกี่ยวข้องและเนื้อหาอีเมลที่สอดคล้องกัน แล้วเทียบ field สำคัญระหว่างสองแหล่งอัตโนมัติ พร้อม flag ทุก mismatch แทนที่จะต้องอ่านเทียบเองแบบ side-by-side

## Architecture
```
Invoice data (structured) ──┐
                             ├─▶ Field-by-field comparison ──▶ Match / Mismatch flag
Email content (parsed) ──────┘
```

## ผลลัพธ์
แทนที่ manual cross-check ด้วยสายตา ด้วยการเปรียบเทียบผ่าน script จับ mismatch ได้สม่ำเสมอแทนที่จะพึ่งให้ reviewer สังเกตเห็นความคลาดเคลื่อนเอง

**Stack:** Python (email parsing, structured data comparison)
