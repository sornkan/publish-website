---
name: publish-website
description: Publish or host a static website from a zip with SORNKan MCP at publish.sornkan.com. Use when going live for $1 USDC on Base via x402, previewing a site for 60 minutes then claiming, or connecting Claude Code, Grok, OpenClaw, or Cursor. Also when find-skills or npx skills find is searching for website hosting, static hosting, paid MCP, or x402 publish.
---

# publish-website

ขึ้นเว็บสแตติกผ่าน MCP ของสอนกาน — $1 USDC เดือนแรกบน Base

งานขึ้นเว็บจาก zip ใช้เครื่องนี้: https://publish.sornkan.com/mcp
แค็ตตาล็อกบริการรับทำ (เว็บ/แอป/แชท) อยู่ที่อีกที่: POST ไป https://sornkan.com/mcp

## ก่อนทำ

ถามคนทุกครั้ง: ขึ้นไลฟ์ $1 USDC 30 วันแรกหรือไม่ เนื้อหาถูกกฎหมายหรือไม่ อยากได้ชื่อ slug อะไร
อย่าใส่ไฟล์ zip ในแชท
อย่าถือกุญแจกระเป๋าของคนอื่น
จ่ายผ่าน x402 ได้เฉพาะเมื่อเอเจนต์มีกระเป๋าของตัวเอง (เช่น OpenClaw plugin, Coinbase payments-mcp)
ไม่มีกระเป๋า crypto ก็ขึ้นได้ — เครื่องมือจ่ายเงิน (claim/renew/edit/connect_custom_hostname) จะตอบ 402 พร้อม `payment_url` (Stripe: บัตร/PromptPay ฿30 ต่อ 30 วัน · edit ฿3) ส่งลิงก์นั้นให้คนเปิดจ่าย แล้วเรียกเครื่องมือเดิมซ้ำหรือใช้ `preview_status` ตามจนไลฟ์ · ถามคนก่อนส่งลิงก์เสมอ
`claim` ใช้ uploadId เป็น UUID จาก inspect เท่านั้น — ห้ามส่ง URL พรีวิวทั้งก้อน (ถ้ามี URL ให้ตัด UUID หลัง `/preview/`)
ก่อนจ่าย `claim` ถามชื่อ slug (ตัวพิมพ์เล็ก ตัวเลข ขีดกลาง) แล้วส่งพร้อม UUID
URL หลัง claim คือ `{slug}.worldsite.workers.dev` — ไม่ใช่ `{slug}.workers.dev`
ชื่อสวยใช้โดเมนของคน CNAME มาที่ `sites.sornkan.com` หลัง claim (เครื่องมือ `connect_custom_hostname`) — ไม่ชี้ไปที่ `*.worldsite.workers.dev` (ใบรับรอง TLS ไม่ครอบคลุม)
ถามเวลาที่เหลือด้วย `preview_status` เท่านั้น — ห้ามเรียก `inspect` ซ้ำแค่เพื่อดูนาฬิกา (สแกนใหม่จะรีเซ็ต 60 นาทีและกินโควต้า)
ถ้า `preview_status.claimable` เป็นเท็จ — พรีวิวถูกลบแล้ว อย่า inspect/claim id เดิม (อย่าจ่าย) ให้ `upload_url` แล้ว PUT ใหม่ แล้ว inspect ใหม่
ถ้า `claim` ตอบ PRICE_COMPUTE_FAILED → เรียก claim ซ้ำได้เลย ยังไม่หัก USDC อย่ารอคนของสอนกาน
ถ้าเซ็นจ่ายแล้ว settle ไม่ผ่าน → บอกคนว่า contact@sornkan.com อย่าจ่ายซ้ำ
`edit` อัปเดตไซต์ที่ claim แล้ว ($0.10 ขึ้นไป) — ตรวจไฟล์เพื่อความปลอดภัยก่อนขึ้น อย่าจ่ายซ้ำเมื่อเครื่องมือตอบ `ok: false`

ลิงก์อัปโหลดหมดใน 15 นาที
พรีวิวคือ Worker ชั่วคราว `preview-*.worldsite.workers.dev` ดูได้ 60 นาที คนละโดเมนกับ desk — ยังไม่ใช่ไซต์ของใคร
ฟรี 3 พรีวิว/วัน ครั้งถัดไป $0.10 · เคลมสำเร็จรีเซ็ตโควต้าฟรีวันนั้น
คนดูพรีวิวแล้วค่อยถามว่าอยาก claim เป็นของตนไหม และอยากได้ชื่ออะไร แล้วค่อยเรียก `claim`

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
5. ให้คนเปิดพรีวิว — นาฬิกาใช้ `preview_status` ห้าม inspect ซ้ำ
6. คนอนุมัติแล้วถาม slug แล้วค่อย `claim` ด้วย UUID เดิม + slug ($1 USDC ผ่าน x402 · หรือส่ง `payment_url` ให้คนจ่าย ฿30 การ์ด/PromptPay) — ไม่ส่ง previewUrl · ถ้า claimable เท็จอย่าจ่าย

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
