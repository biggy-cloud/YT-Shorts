# YT Shorts Content Research System

ระบบวิจัยและสร้างคอนเทนต์สำหรับช่อง YouTube Shorts แนว **ผี/เรื่องลี้ลับ/เรื่องหลอน** (ภาษาไทย โทนเป็นกันเอง/ปากๆ)
ขับเคลื่อนด้วย 4 subagent ที่แยกหน้าที่กันชัดเจน เรียกทีละตัวผ่าน slash command

## Pipeline

```
/research <youtube-link> [เนื้อเรื่องคร่าวๆ]   → research-content agent
        ↓ บันทึกลง output/content_ideas.xlsx (สร้าง ID ใหม่)
/hook <ID>                                      → hook-writer agent
        ↓ เสนอ hook ใหม่ 3-5 แบบ ให้เลือก บันทึก Chosen Hook
/script <ID>                                    → script-writer agent
        ↓ เขียนสคริปต์เต็ม 45-60 วิ + CTA
/qa <ID>                                        → qa-checker agent
        ↓ ตรวจ hook (แรงพอใน 3 วิแรก) + CTA (ชัดเจน/engage) → Approved / Needs Revision
```

ทุกขั้นตอนเก็บสถานะไว้ในไฟล์เดียว: [output/content_ideas.xlsx](output/content_ideas.xlsx) — เปิดดูรวมทุกไอเดียได้ตลอด

## ไฟล์สำคัญ

- `.claude/agents/*.md` — นิยาม subagent ทั้ง 4 ตัว (research-content, hook-writer, script-writer, qa-checker)
- `.claude/commands/*.md` — slash command สำหรับเรียกแต่ละ agent (`/research`, `/hook`, `/script`, `/qa`)
- `scripts/sheet_utils.py` — CLI เดียวที่ทุก agent ใช้อ่าน/เขียน `output/content_ideas.xlsx` (กัน agent แก้ไฟล์ตรงๆ จนพัง)
- `output/content_ideas.xlsx` — สเปรดชีตกลาง เก็บทุกไอเดีย ตั้งแต่ research จนถึง QA

## กติกาของระบบ (อย่าแก้โดยไม่คุยกับเจ้าของช่องก่อน)

- **CTA ต้องมีเสมอ 2 อย่าง**: ชวนติดตาม/ซับสไครบ์ + ชวนคอมเมนต์/ทายผล
- **ความยาวสคริปต์**: 45-60 วินาที (~120-160 คำ)
- **เกณฑ์ QA แบบ hard requirement**: hook ต้องแรงใน 3 วิแรก และ CTA ต้องชัดเจน — ถ้าไม่ผ่านข้อใดข้อหนึ่ง ผลรวมต้องเป็น "Needs Revision" เสมอ
- **ห้ามลอกคำต่อคำ** จากคลิปต้นทาง ทุก agent ต้อง "พัฒนาให้ดีขึ้น" ไม่ใช่แปล/copy

## หมายเหตุเรื่อง output

ตอนนี้ผลลัพธ์เก็บเป็นไฟล์ `.xlsx` ในเครื่อง เพราะเซสชันนี้ยังไม่ได้เชื่อมต่อ Google Sheets/Drive connector
ถ้าต้องการให้เขียนลง Google Sheet จริงบน Drive: ไปที่ Settings → Connectors แล้วเชื่อมต่อ Google Sheets จากนั้นแจ้งให้ปรับ `scripts/sheet_utils.py` ให้ sync ขึ้น Drive แทน/เพิ่มเติมจากไฟล์ local
