# Scheduled Split-Fee/Subsidy Report Workflow

**Domain:** Reporting automation · n8n, Python workflow-generation

## ปัญหา
รายงาน split ค่าส่ง/subsidy แบบ recurring (ส่วนหนึ่งของโปรแกรม cost-sharing เชิงโปรโมชั่น) ต้องส่งทางอีเมลตามกำหนดเวลา — เป็นอีก case ของ manual pull-format-send cycle ที่ทำซ้ำทุก period บวก error handling แยกและ "waiter" notification variant สำหรับอีก audience หนึ่ง

## แนวทาง
Reuse pattern เดิม (Python generator → n8n API) ที่สร้างไว้กับ report workflow ก่อนหน้า: generate workflow JSON เป็น code, push ผ่าน n8n API, คู่กับ error-alert workflow เฉพาะตัว เพิ่ม workflow variant ที่ 3 สำหรับ notification audience อีกกลุ่ม (waiter notification) โดย reuse core logic การ generate report เดิม แต่ branch เป้าหมายการส่งต่างออกไป

## Architecture
```
Python generator script → workflow JSON → pushed via n8n API
   │
   ▼
Scheduled trigger (n8n) → pull source data → format split-fee report
   │
   ├─ Email WF → main distribution list
   └─ Waiter WF → secondary audience notification
   │
   └─ Error WF → fires on failure, alerts the team
```

## ผลลัพธ์
เพราะ pattern พื้นฐาน (generator script → API push → dedicated error WF) พิสูจน์แล้วจาก report ก่อนหน้า report workflow ตัวที่สองนี้ใช้เวลา build แค่เศษเสี้ยว — generator script ถูกปรับใช้แทนที่จะเขียนใหม่ทั้งหมด

**Stack:** n8n (Schedule Trigger, Email node, workflow หลาย variant), Python (workflow generation + push ผ่าน n8n REST API)
