# Reimbursement Edit-Notification Loop

**Domain:** Workflow automation · n8n, scheduled folder-watch, bounce handling

## ปัญหา
เมื่อ submitter ต้อง correct record reimbursement ที่ submit ไปก่อนหน้า ไม่มีวิธี automated ที่จะแจ้งเตือนหรือ track ว่า correction กำลังดำเนินอยู่ — ต้องพึ่งให้คนจำได้ว่าต้อง follow up เอง และอีเมล notification ที่ bounce กลับมาก็ไม่มีใครสังเกต

## แนวทาง
สร้าง n8n workflow ที่ watch shared source (spreadsheet/folder) แบบ scheduled, ตรวจจับว่า record ไหนต้อง correction แล้ว auto notify submitter ที่เกี่ยวข้องทางอีเมล — loop จนกว่า correction จะถูก confirm มี script คู่กันที่ fetch อีเมล bounced-delivery เพื่อไม่ให้ notification ที่ fail หายไปเงียบๆ แต่จะถูก surface ให้ตาม manual แทน

## Architecture
```
Scheduled trigger (n8n)
   │
   ▼
Scan source sheet/folder for "needs correction" records
   │
   ▼
Send edit-notification email to submitter
   │
   ├─ Delivered → mark as notified, continue watching for resolution
   └─ Bounced → separate script fetches bounce, flags for manual follow-up
```

## ผลลัพธ์
ย้าย follow-up step ที่เดิมเป็น manual ล้วนๆ และลืมง่าย ให้เป็น scheduled loop ที่มี bounce-handling safety net ชัดเจน ทำให้ notification ที่ fail ถูกจับได้แทนที่จะหายไปเงียบๆ

**Stack:** n8n (Schedule Trigger, Google Sheets/Drive node, Email node), script เล็กๆ สำหรับดึง bounce email, Google Apps Script สำหรับ export ที่ใช้ประกอบ
