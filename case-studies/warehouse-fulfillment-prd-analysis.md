# Warehouse-Fulfillment Model — Requirements Analysis

**Domain:** Product/requirements analysis · logistics operations, settlement design

## ปัญหา
Fulfillment model ใหม่ (assign และ complete order จาก warehouse แทนที่ point-to-point model เดิม) ต้องออกแบบ settlement logic ทางการเงินก่อนที่ engineering จะ build ได้ — โดยเฉพาะ fee ควร trigger ตอนไหน และควรคำนวณ partial/prorated fee ยังไงเมื่อ job ถูก split ข้าม fulfillment chain

## แนวทาง
เขียน requirements analysis ครอบคลุม: free-assignment model สำหรับวิธี allocate job ใน context ของ warehouse, จุด trigger ของ settlement ที่ชัดเจน (ผูกกับ group/batch completion แทนที่ order completion รายตัว), model คำนวณ prorated-fee สำหรับ job ที่ split ข้ามหลาย handler, และ definition ของ transaction type ใหม่หลายตัวที่ต้องมีเพื่อแทน model นี้ใน settlement ledger ส่งมอบเป็น slide deck ที่มีโครงสร้างชัดเจนพร้อม reviewer comment ที่รวมเข้าไว้แล้ว ใช้เป็น reference ร่วมระหว่าง product, ops, และ engineering

## Key design decisions
- **Settlement trigger:** ย้ายจาก per-order เป็น per-group completion เพราะ warehouse model batch หลาย order เข้าด้วยกัน — ทำให้จังหวะ recognize revenue/cost เปลี่ยนไป
- **Prorated fee:** จำเป็นเพราะ job เดียวตอนนี้ split ข้ามมากกว่าหนึ่ง handler ได้ fee แบบ flat ไม่สะท้อน cost split จริงอีกต่อไป
- **New transaction types:** taxonomy ของ transaction ใน settlement ledger เดิมไม่มี category สำหรับ warehouse-batch หรือ prorated-split transaction เลยต้อง define type ใหม่หลายตัวเพื่อแทนสิ่งเหล่านี้โดยไม่ overload category ที่มีอยู่แล้ว

## ผลลัพธ์
ผลิต reference document ที่ทั้ง engineering และ finance ops align ก่อน build settlement logic ของ fulfillment model ใหม่ — เป็นงาน requirements ประเภทที่ป้องกัน mismatch ระหว่างสิ่งที่ finance คาดหวังจะ reconcile กับสิ่งที่ระบบ generate ออกมาจริง

**Deliverable format:** Structured slide deck (PRD-style) พร้อม definition ของ trigger/timing ที่ชัดเจนและตัวอย่างประกอบสำหรับ transaction type ใหม่
