# Voucher Compensation — Multi-Stage Dispatch Pipeline

**Domain:** Customer compensation automation · n8n multi-workflow pipeline, bulk dispatch

## ปัญหา
การชดเชยลูกค้า/ร้านค้าด้วย voucher หลังเกิดปัญหาการบริการ เป็น manual process หลายขั้นตอน: หา case ที่เข้าเงื่อนไข, หา voucher type และจำนวนเงินที่ถูกต้อง, dispatch ออกไป, แล้วยืนยันว่าถึงจริง — แต่ละขั้นทำด้วยมือทีละ case ไม่มีวิธี batch-process backlog อย่างเป็นระบบ

## แนวทาง
ออกแบบ n8n workflow pipeline 5 stage (WF1 ถึง WF5) แต่ละตัวจัดการหนึ่ง stage ของ lifecycle: intake/eligibility check, voucher-type resolution, dispatch, next-day confirmation, และ exception handling deploy workflow ผ่าน Python import script แทนการ config ผ่าน UI ด้วยมือ ทำให้ pipeline ทั้งหมด redeploy หรือ update ได้ซ้ำอย่างสม่ำเสมอ สร้าง mock admin UI คู่กันไว้เพื่อ test bulk-dispatch logic อย่างปลอดภัยก่อนแตะของจริง

## Architecture
```
Backlog of eligible cases
   │
   ▼
WF1 Intake → WF2 Voucher resolution → WF3 Dispatch
   │
   ▼
WF4 T+1 Confirmation (did it land?)
   │
   ▼
WF5 Exception handling (bounced / failed dispatch)
```
แต่ละ stage เป็น n8n workflow อิสระ เชื่อมกันด้วย shared state (case ID) ดังนั้น fail ที่ stage หนึ่งไม่ต้อง re-run ทั้ง pipeline — rerun แค่ stage ที่ต้อง retry

## ผลลัพธ์
batch run จริงครั้งแรก process backlog สำเร็จ auto-dispatch ส่วนใหญ่ เหลือแค่ส่วนน้อยที่ต้อง manual follow-up — จากเดิม manual 100% การออกแบบทีละ stage ทำให้แต่ละ workflow ใหม่ build/test/verify ได้อิสระก่อนเพิ่ม stage ถัดไป แทนที่จะ ship ทั้ง pipeline โดยไม่ทดสอบทีเดียว

**Stack:** n8n (multi-workflow chaining), Python (deployment/import automation), mock HTML admin UI สำหรับ test ก่อนขึ้น production
