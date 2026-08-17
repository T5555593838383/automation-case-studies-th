# Confluence Knowledge Sync Pipeline

**Domain:** Internal knowledge base · Python ETL + LLM retrieval

## ปัญหา
AI assistant ภายในต้องตอบคำถามงาน finance ops โดยอ้างอิงเอกสารกระบวนการจริง แต่เอกสารเหล่านั้นกระจายอยู่ใน internal wiki ขนาดใหญ่ — หลายสิบ section รวมหลายหมื่นหน้า — ที่ต้องค้นหาด้วยมือทีละหน้า การ copy-paste เนื้อหาเข้า context ของ assistant ตรงๆ ทำไม่ได้ในขนาดนี้ และถ้า export ครั้งเดียวก็จะ stale ทันทีที่ wiki มีการแก้ไข

## แนวทาง
สร้าง Python ETL pipeline ที่ไล่อ่าน wiki ผ่าน REST API ทีละ section, paginate ทุกหน้าพร้อม rate-limit backoff, ตัดเนื้อหาแต่ละหน้าให้เหลือ clean text พร้อม metadata ของ section/page, dedup และ chunk ผลลัพธ์ แล้วเขียนลง structured knowledge base ในรูปแบบที่ retrieval layer ของ assistant ดึงมาใช้ตอบคำถามได้ทันที pipeline นี้รันซ้ำได้ ทำให้การ refresh knowledge base หลัง wiki เปลี่ยนเป็นแค่การรันใหม่ ไม่ต้องสร้างใหม่ทั้งหมด

## Architecture
```
Wiki space (14 sections)
   │
   ▼
Python crawler — paginated API walk · rate-limit backoff
   │
   ▼
Content extractor — HTML → clean text · section/page metadata tagging
   │
   ▼
Structured knowledge base (deduplicated, chunked)
   │
   ▼
LLM assistant retrieval layer — ดึงมาใช้ตอน query
```

## ผลลัพธ์
Ingest ได้ 14 sections / 58,739 หน้า — ขนาดที่ทำด้วยมือไม่มีทางเป็นไปได้ — กลายเป็น knowledge base ที่รองรับ chatbot production ทำให้คำตอบอ้างอิงเอกสารจริงแทนการเดาของโมเดล ความสามารถ rerun ได้ทำให้ knowledge base คงความ up-to-date โดยไม่ต้อง export มือซ้ำ

**Stack:** Python, wiki REST API, pagination/backoff handling, structured knowledge base format สำหรับ LLM retrieval
