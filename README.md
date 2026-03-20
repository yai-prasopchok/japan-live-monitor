# 🇯🇵 Japan Live Monitor

หน้าจอรวม live camera จากสถานที่ต่างๆ ในประเทศญี่ปุ่น สำหรับดูสภาพอากาศ ความหนาแน่นของผู้คน และบรรยากาศก่อนเดินทาง

🔗 **[เปิดใช้งาน](https://yai-prasopchok.github.io/japan-live-monitor/)**

![Japan Live Monitor Screenshot](screenshot.png)

---

## Features

- 📍 จัดกล้องเป็นกลุ่มตามเมือง — โตเกียว, โอซาก้า, เกียวโต, ฮอกไกโด, ฟุกุโอกะ, นารา
- 🌤 Weather overlay แสดงอุณหภูมิและสภาพอากาศแบบ real-time แต่ละเมือง
- 🗺 Map view ดูตำแหน่งกล้องบนแผนที่
- 🕐 นาฬิกา Japan Standard Time (JST) แบบ real-time
- 🔍 ค้นหากล้องและเรียงลำดับ (A-Z / Z-A)
- 🔗 ดึงรายการกล้องจาก Google Sheets ผ่าน Apps Script
- ↻ Auto refresh เมื่อกลับมาที่ tab (threshold 5 นาที)
- ⛶ คลิกขยายกล้องเต็มจอ พร้อม Picture-in-Picture
- ⏸ Auto pause iframe เมื่อ scroll พ้นจอ ประหยัด CPU
- 💾 จำค่า city filter, คอลัมน์ และการเรียงลำดับ
- 📱 รองรับมือถือ, แท็บเล็ต และติดหน้าจอได้ (PWA)

---

## วิธีใช้งาน

### 1. สร้าง Google Sheet

เปิด [Google Sheets](https://sheets.google.com) แล้วสร้างไฟล์ใหม่ชื่อ **Japan Live Cameras**

จัดคอลัมน์ดังนี้:

| A (id) | B (title) | C (city) |
|--------|-----------|----------|
| 3Q5wZeTuttw | Shibuya Crossing | tokyo |
| bzn2QWfOLFY | Dotonbori Canal | osaka |
| Y1XxYLwpJy4 | Higashi Honganji | kyoto |

**ค่า city ที่รองรับ:** `tokyo` · `osaka` · `kyoto` · `hokkaido` · `fukuoka` · `nara` · `other`

> รองรับทั้ง `tokyo` และ `Tokyo` — ไม่ต้องสนใจตัวพิมพ์เล็ก/ใหญ่

### 2. สร้าง Apps Script

ใน Google Sheet ไปที่ **Extensions → Apps Script** แล้ววางโค้ดนี้:

```javascript
function doGet(e) {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  const rows = sheet.getDataRange().getValues();

  const cameras = rows.slice(1)
    .filter(r => r[0])
    .map(r => ({
      id:    String(r[0]).trim(),
      title: String(r[1]).trim() || 'Live Camera',
      city:  String(r[2]).trim() || 'other'
    }));

  return ContentService
    .createTextOutput(JSON.stringify({ cameras }))
    .setMimeType(ContentService.MimeType.JSON);
}
```

กด **Save** แล้วตั้งชื่อ project ว่า **JapanLiveAPI**

### 3. Deploy เป็น Web App

1. กด **Deploy → New deployment**
2. เลือก Type: **Web app**
3. Execute as: **Me**
4. Who has access: **Anyone**
5. กด **Deploy** แล้ว copy URL ที่ได้

### 4. เปิดใช้งาน

เปิด [https://yai-prasopchok.github.io/japan-live-monitor/](https://yai-prasopchok.github.io/japan-live-monitor/) ได้เลย

---

## การเพิ่มกล้องใหม่

เพิ่มแถวใน Google Sheet ได้เลย แล้วกดปุ่ม **Refresh** ในหน้าจอ

**วิธีหา YouTube Video ID:**
```
https://www.youtube.com/watch?v=3Q5wZeTuttw
                                 ^^^^^^^^^^^
                                 นี่คือ Video ID
```

---

## ติดตั้งบนมือถือ (PWA)

**Android** — เปิด Chrome → กด ⋮ → Add to Home screen

**iOS** — เปิด Safari → กด Share → Add to Home Screen

---

## โครงสร้างไฟล์

```
japan-live-monitor/
├── index.html        # ไฟล์หลัก
├── manifest.json     # PWA manifest
├── sw.js             # Service Worker
├── icon-192.png      # App icon
├── icon-512.png      # App icon (large)
└── README.md
```

---

## Tech Stack

- **Frontend:** HTML, CSS, JavaScript (vanilla — ไม่มี dependencies)
- **Data source:** Google Sheets + Google Apps Script
- **Video:** YouTube Embed API
- **Weather:** [Open-Meteo](https://open-meteo.com/) (ฟรี ไม่ต้อง API key)
- **Map:** OpenStreetMap (ฟรี ไม่ต้อง API key)
- **Font:** Bebas Neue, Sarabun (Google Fonts)

---

## ข้อจำกัด

- กล้องที่แสดงต้องเป็น YouTube live stream เท่านั้น
- Apps Script มี quota ฟรี 20,000 requests/วัน (เกินพอสำหรับใช้ส่วนตัว)
- PWA ต้องเสิร์ฟผ่าน HTTPS (GitHub Pages รองรับอยู่แล้ว)
- Picture-in-Picture อาจไม่รองรับบางเบราว์เซอร์
