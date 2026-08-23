---
name: publish-website
description: Publish or host a static website from a zip with SORNKan MCP at publish.sornkan.com. Use when going live for $1 USDC on Base via x402, previewing a site for 60 minutes then claiming, or connecting Claude Code, Grok, OpenClaw, or Cursor. Also when find-skills or npx skills find is searching for website hosting, static hosting, paid MCP, or x402 publish.
---

# publish-website

ขึ้นเว็บสแตติกผ่าน MCP ของสอนกาน — $1 USDC เดือนแรกบน Base

งานขึ้นเว็บจาก zip ใช้เครื่องนี้: https://publish.sornkan.com/mcp
แค็ตตาล็อกบริการรับทำ (เว็บ/แอป/แชท) อยู่ที่อีกที่: POST ไป https://sornkan.com/mcp

## ก่อนทำ

ถามคนทุกครั้ง: ขึ้นไลฟ์ $1 USDC 30 วันแรกหรือไม่ เนื้อหาถูกกฎหมายหรือไม่
อย่าใส่ไฟล์ zip ในแชท
อย่าถือกุญแจกระเป๋าของคนอื่น
จ่ายผ่าน x402 ได้เฉพาะเมื่อเอเจนต์มีกระเป๋าของตัวเอง (เช่น OpenClaw plugin, Coinbase payments-mcp)
`claim` ใช้ uploadId เป็น UUID จาก inspect เท่านั้น — ห้ามส่ง URL พรีวิวทั้งก้อน (ถ้ามี URL ให้ตัด UUID หลัง `/preview/`)
ถ้า `claim` ตอบ PRICE_COMPUTE_FAILED → เรียก claim ซ้ำได้เลย ยังไม่หัก USDC อย่ารอคนของสอนกาน
ถ้าเซ็นจ่ายแล้ว settle ไม่ผ่าน → บอกคนว่า contact@sornkan.com อย่าจ่ายซ้ำ

ลิงก์อัปโหลดหมดใน 15 นาที
พรีวิวดูได้ 60 นาที และยังไม่ใช่ไซต์ของใคร — ยังไม่จ่าย
คนดูพรีวิวแล้วค่อยถามว่าอยาก claim เป็นของตนไหม แล้วค่อยเรียก `claim`

ต่ออายุได้เฉพาะตอนไซต์ยังไลฟ์ (สูงสุด 90 วัน) วันสุดท้ายไม่ต่อ = ลบไซต์ ชื่อว่าง

## ติดตั้ง MCP ถ้ายังไม่มี

```
claude mcp add --transport http sornkan-publish https://publish.sornkan.com/mcp
grok mcp add --transport http sornkan-publish https://publish.sornkan.com/mcp
openclaw mcp add sornkan-publish --url https://publish.sornkan.com/mcp --transport streamable-http
```

หรือ `npx skills add sornkan/publish-website`

สกิลเดียวกันอยู่ที่ https://sornkan.com/.well-known/agent-skills/publish-website/SKILL.md

## ขั้นตอน

1. เรียก `publish_website` (แผน + ราคา)
2. เรียก `upload_url` — ได้ putUrl / dropUrl
3. มี zip → PUT เอง (สูงสุด 3MB). ไม่มีไฟล์ → ส่ง dropUrl ให้คน (หมดใน 15 นาที)
4. เรียก `inspect` ด้วย uploadId — ได้ previewUrl (ดูได้ 60 นาที)
5. ให้คนเปิดพรีวิว
6. คนอนุมัติแล้วค่อย `claim` ด้วย UUID เดิม ($1 USDC ผ่าน x402) — ไม่ส่ง previewUrl

POST JSON-RPC ไปที่ https://publish.sornkan.com/mcp:

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "tools/call",
  "params": { "name": "publish_website", "arguments": {} }
}
```

Publish a static website via SORNKan Worldsite MCP. Ask the human first. Preview, then claim. Never put the zip in chat.
