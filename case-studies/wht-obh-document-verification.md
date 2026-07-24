# Scanned-Document OCR Verification Workflow

**Domain:** Document processing · n8n, OCR/vision extraction, rule-based approval

## ปัญหา
กระบวนการยกเว้นภาษีเฉพาะทางหนึ่ง ต้อง verify เอกสาร authorization ที่ scan มาจากผู้สมัครที่เป็นนิติบุคคล — เช็คว่า required field ครบและอ่านออก manual review เอกสาร scan (ภาพ/PDF) ช้าและ inconsistent ระหว่างผู้ตรวจแต่ละคน

## แนวทาง
สร้าง n8n workflow ที่ extract binary file attachment จาก submission ที่เข้ามา, รัน OCR/vision-model extraction บนเอกสารที่ scan เพื่อดึง required field ออกมา แล้ว apply rule-based check ก่อนปล่อยให้ submission ไปต่อที่ขั้น approval ออกแบบให้รองรับ batch submission ใหม่รายวันที่สม่ำเสมอ และ case ที่ reject/resubmit จำนวนน้อยกว่า

## Architecture
```
Submission (scanned PDF/image attachment)
   │
   ▼
Extract binary attachment (n8n)
   │
   ▼
OCR / vision-model field extraction
   │
   ▼
Rule-based required-field check
   │
   ├─ Pass → forward to approval
   └─ Fail → reject with reason, route back for resubmission
```

## ผลลัพธ์
แทนที่การตรวจเอกสาร scan ด้วยสายตาแบบ manual ด้วยขั้นตอน field-extraction และ rule-check อัตโนมัติที่สม่ำเสมอ ออกแบบให้รองรับหลายสิบ case ต่อวันโดยไม่มีปัญหา reviewer fatigue ที่ทำให้ judgment ไม่คงเส้นคงวา

**Stack:** n8n (Binary data handling, HTTP Request ไปยัง vision/OCR model, Code node สำหรับ rule check)
