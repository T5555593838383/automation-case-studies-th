# Multimodal Chat Agent with Abuse Rate-Limiting

**Domain:** Conversational AI agent · n8n, LLM + memory, vision/voice input, abuse handling

## ปัญหา
มี chatbot agent theme finance อยู่แล้ว ความต้องการคือ persona variant ที่เบากว่าสำหรับ audience อื่นที่ไม่ใช่ finance — ตัวที่รองรับข้อความรูปภาพและเสียงได้ด้วย ไม่ใช่แค่ text และต้องป้องกัน user บางส่วนที่ spam หรือ abuse bot

## แนวทาง
Clone architecture ของ chatbot agent ที่มีอยู่แล้ว (LLM + conversation memory + web-search tool) แล้วตัด tool เฉพาะ finance ออก แทนที่ด้วย persona อื่น เพิ่ม vision และ voice-transcription node เพื่อแปลงข้อความรูปภาพและเสียงเป็น text ให้ LLM reason ได้ก่อนตอบ เพิ่ม rate-limiter ที่ key ตาม sender เพื่อให้ user ที่ส่งข้อความปริมาณผิดปกติโดน throttle แทนที่จะปล่อยให้ spam agent (และ budget ของ LLM API) ได้ไม่จำกัด

## Architecture
```
Incoming chat message (text / image / voice)
   │
   ├─ image → vision node → text description
   ├─ voice → transcription node → text
   └─ text → passthrough
   │
   ▼
Rate-limiter check (per-sender) — throttle if abusive volume detected
   │
   ▼
LLM agent (persona-specific system prompt) + conversation memory + web-search tool
   │
   ▼
Reply in chat
```

## ผลลัพธ์
ส่ง chatbot persona ตัวที่สองสำหรับ audience ใหม่ได้ ด้วยการ reuse architecture ของ agent เดิมประมาณ 80% แทนที่จะ build จากศูนย์ พร้อมปิดช่องโหว่ abuse (volume ข้อความไม่จำกัดจาก sender เดียว) ที่ agent ตัวเดิมไม่เคยต้องรับมือ

**Stack:** n8n (LLM node with memory, HTTP Request สำหรับ vision/transcription API, Code node สำหรับ rate-limiting logic), chat platform integration
