# Merchant Commission Report — Chatbot Tool

**Domain:** Reporting via chatbot tool integration · Python, OAuth2, async data-service API

## ปัญหา
ตัวเลข commission ระดับ merchant อยู่หลัง internal data service ที่ต้อง query แบบ authenticated และ asynchronous — ไม่ใช่อะไรที่ทีมฝั่ง merchant-facing จะ self-serve ได้เองโดยไม่ผ่านคิว data/analytics request ทุกครั้ง

## แนวทาง
เพิ่ม "tool" node ใหม่เข้าไปใน chatbot/agent workflow ภายในที่มีอยู่แล้ว: integration ที่ config ด้วย Python ที่ authenticate ผ่าน OAuth2, ยิง async query ไปยัง data service, รอผลลัพธ์ แล้ว format เป็นรายงาน commission ของ merchant ที่ chatbot ส่งกลับได้เลยใน conversation — เปลี่ยนงานคิว data-request ให้เป็น self-serve chat query

## Architecture
```
Chat request: "commission report for merchant X"
   │
   ▼
Chatbot agent routes to the commission-report tool
   │
   ▼
OAuth2-authenticated async query → internal data service
   │
   ▼
Poll for async result → format as report
   │
   ▼
Reply inline in chat
```

## ผลลัพธ์
ตัด ticket คิว data-request ที่เกิดซ้ำๆ ออกไป ด้วยการทำให้ merchant commission lookup self-serve ผ่าน chatbot interface ที่มีอยู่แล้ว reuse infrastructure เดิมของทีมแทนที่จะสร้าง reporting surface ใหม่จากศูนย์

**Stack:** Python (OAuth2 client, async polling), integration เป็น tool node ใน chatbot/agent workflow บน n8n ที่มีอยู่แล้ว
