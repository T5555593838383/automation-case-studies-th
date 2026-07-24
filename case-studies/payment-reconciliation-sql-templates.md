# Payment Reconciliation SQL Template Library

**Domain:** Fintech reconciliation · Presto/Trino SQL, prompt engineering for LLM-assisted SQL generation

## ปัญหา
การ reconcile ธุรกรรมระหว่าง ledger ภายในกับระบบ settlement ภายนอก ต้องเขียน SQL ซับซ้อนใหม่ทุกครั้ง — join key ที่ถูกต้อง, timezone conversion, status-code filter, และ edge case (transaction ที่ void แล้ว, refund window) กระจัดกระจายอยู่ใน tribal knowledge และ query เก่าๆ ที่เขียนแบบ ad-hoc คนใหม่ในทีม (หรือ LLM assistant) ไม่มี single source of truth ให้ generate query ถูกต้องตั้งแต่ครั้งแรก

## แนวทาง
แทนที่จะเขียน query แบบ one-off สร้าง library เอกสาร markdown เล็กๆ ที่ LLM (หรือคน) อ่านก่อน generate SQL: schema cache (table, join key, ID type), business-context doc (ทำไม recon นี้ถึงมีอยู่ ใครใช้ output), glossary (คำศัพท์ระบบ A vs ระบบ B), recon-rules doc (matching logic, timezone handling, void rules), validation-checks doc (sanity check ที่ต้องรันหลัง query ใดๆ), และ known-issues doc (data-quality quirk ที่ต้องระวัง) มี prompt template ผูกทั้งหมดเข้าด้วยกัน เพื่อให้ request ใดๆ — "ขอ refund query ของวันนี้" — ดึงจาก reference set เดียวกันเสมอ แทนที่จะ hallucinate ชื่อ table หรือ filter

## Architecture
```
User/LLM request ("refund query for date X")
   │
   ▼
Recon prompt template → references:
   schema_cache.md · business_context.md · glossary.md
   recon_rules.md · validation_checks.md · known_issues.md
   │
   ▼
Generated SQL (Presto/Trino) + inline reasoning comments
   │
   ▼
Suggested validation checks + known-issue warnings appended automatically
```

## ผลลัพธ์
ลด error จาก SQL generation ที่มาจาก join-key type ผิดหรือลืม convert timezone จนแทบเป็นศูนย์ เพราะทั้งคนและ LLM assistant ตอนนี้ generate query จาก reference เดียวกันที่ maintain ไว้ แทนที่จะพึ่งความจำ เปลี่ยน tribal knowledge ของคนคนเดียวให้เป็น asset ที่ reuse ได้และมี version

**Stack:** Markdown documentation-as-prompt-context, Presto/Trino SQL, structured prompt template สำหรับ LLM-assisted query generation
