# Lịch Việt PWA

> Lịch âm dương Việt Nam dạng Progressive Web App — chạy hoàn toàn trên trình duyệt, không cần backend, cài được như app trên Android & iOS.

**Live app:** https://bao-vn.github.io/lich-viet-pwa/

---

## Điểm khác biệt

- 🇻🇳 **Tính âm lịch theo múi giờ Việt Nam (UTC+7)** — không phải UTC+8 như nhiều lịch online khác. Kết quả khớp tuyệt đối với lịch blốc in ấn của Việt Nam.
- 📐 **Thuật toán thiên văn chuẩn**: Hồ Ngọc Đức / Jean Meeus (*Astronomical Algorithms*, 1998). Đã validate 1960–2099 (51,135 ngày, 0 lỗi).
- 📦 **Single-file HTML (~110KB)** — không build step, không framework, không dependencies. Clone về mở là chạy.
- 🔒 **100% client-side** — tất cả dữ liệu sự kiện lưu trong `localStorage` của trình duyệt, không gửi đi đâu cả.
- 📱 **Cài như app thật** — PWA installable trên Android (Add to Home screen) và iOS 16.4+ (Share → Add to Home Screen).

---

## Tính năng

### Lịch âm dương

- ✅ Hiển thị đồng thời dương lịch + âm lịch + can chi ngày + năm
- ✅ Hoàng Đạo / Hắc Đạo (chấm xanh/đỏ trên mỗi ngày)
- ✅ Ngày lễ Việt Nam: Tết Nguyên Đán, Rằm Giêng, Giỗ Tổ, Đoan Ngọ, Vu Lan, Trung Thu, Ông Táo, Giao Thừa…
- ✅ Ngày lễ dương: Tết Dương, 30/4, 1/5, 2/9, Giáng Sinh
- ✅ Tháng nhuận (leap month) đánh dấu rõ ràng với ký hiệu `⁺`

### View modes

- 📅 **Month view** — lưới 7 cột truyền thống, mỗi ngày show số âm + can chi + events
- 📆 **Week view** — editorial row-per-day layout tối ưu cho phone: số ngày Playfair Display lớn, ngày âm + can chi rõ nét, events dạng list có time
- 🔄 Toggle Tháng / Tuần instant trong header, lưu lựa chọn qua session

### Events management

- ➕ Tạo event với tên, giờ (optional), ghi chú
- 🔁 **Repeat events** với 3 loại:
  - **Hàng tuần** — dùng cho họp, lớp học, tập gym (default 52 lần)
  - **Hàng năm (dương lịch)** — sinh nhật, kỷ niệm (xử lý đúng case 29/2)
  - **Hàng năm (âm lịch)** — giỗ, Tết, rằm, Phật Đản (USP của app)
- ✏️ Edit/xoá: chọn "chỉ ngày này" hoặc "cả chuỗi lặp"
- 💡 **Quote Thứ Hai** — 50 danh ngôn xoay vòng deterministic mỗi tuần

### Notifications (local scheduled)

- 🔔 **Nhắc sớm khi mở app** — tổng hợp events ngày mai hoặc 3 ngày tới
- ⏰ **Nhắc khi gần tới giờ** — cấu hình 1h / 15m / 5m / 0m trước
- 📅 Nhắc sự kiện cả ngày — 20:00 hôm trước
- 🎉 Nhắc ngày lễ lớn (toggle riêng)
- 💤 Snooze 10 phút qua notification action

### Import / Export

- 📥 **Import file `.ics`** từ bất kỳ calendar app nào (Google Calendar, Apple Calendar, Outlook) — hỗ trợ RRULE expansion, timezone, dedup theo UID
- 📤 **Export file `.ics`** để import ngược vào calendar app khác — kèm ngày âm lịch trong description

### Tìm kiếm

- 🔍 Dương → âm: gõ `1/1/2025` → hiện ngày âm tương ứng
- 🔍 Âm → dương: gõ `15/7 âm 2025` → hiện ngày dương

### Responsive design (Material Design 3)

- **Compact (< 600px)**: phone portrait — header 2 rows, today-bar icon-only, row-per-day week view
- **Medium (≥ 600px)**: tablet / phone landscape — single-row header, generous spacing
- **Expanded (≥ 905px)**: tablet landscape / laptop — app center 1080px, sheets modal center

---

## Cài đặt

### Dùng online

Mở https://bao-vn.github.io/lich-viet-pwa/ trên bất kỳ trình duyệt modern nào.

### Cài như app (Android)

1. Mở link trên trong Chrome
2. Menu ⋮ → **Add to Home screen**
3. App chạy fullscreen như native app, hỗ trợ offline

### Cài như app (iPhone, iOS 16.4+)

1. Mở link trên trong Safari
2. Tap nút Share → **Add to Home Screen**
3. Mở app từ home screen (cần bước này để notifications hoạt động)

### Chạy local / Self-host

```bash
# Clone (không có dependencies!)
git clone https://github.com/bao-vn/lich-viet-pwa.git
cd lich-viet-pwa

# Mở trực tiếp trong browser
open index.html
# hoặc serve qua HTTP đơn giản
python3 -m http.server 8000
```

Deploy lên GitHub Pages / Netlify / Cloudflare Pages: chỉ cần upload `index.html` là xong.

---

## Tech stack

Không dùng build step hay package manager. 100% vanilla:

- HTML5 + CSS3 + Vanilla JavaScript (ES2020+)
- Service Worker cho offline cache (inline blob, không cần file riêng)
- Web Manifest inline qua blob URL
- `localStorage` cho data persistence
- Notification API cho reminders
- Google Fonts: [Playfair Display](https://fonts.google.com/specimen/Playfair+Display) + [Quicksand](https://fonts.google.com/specimen/Quicksand)

**File size:** ~110KB (chưa minify). Khi PWA cache xong, app chạy offline hoàn toàn.

---

## Kiến trúc

Single file `index.html` chia thành 10 JS modules logic:

| Module | Chức năng |
|---|---|
| MODULE 1 | Thuật toán Hồ Ngọc Đức (lunar algorithm) |
| MODULE 2 | Can Chi & Hoàng Đạo |
| MODULE 3 | Ngày lễ (`LE_AM`, `LE_DUONG`) |
| MODULE 4 | Event store với recurring rules + migration v1→v2 |
| MODULE 5 | Settings |
| MODULE 5b | Notifications scheduler |
| MODULE 6 | State & Render (Month + Week views) |
| MODULE 7 | 5 bottom sheets (daySheet, searchSheet, pickerSheet, settingsSheet, importSheet) |
| MODULE 8 / 8b | Export / Import ICS |
| MODULE 10 | Service Worker + notification click handler |

Chi tiết architecture, data schema, và edge cases xem [`lich-viet-project-summary.md`](./lich-viet-project-summary.md).

### localStorage keys

| Key | Nội dung |
|---|---|
| `lichviet_events_v2` | `{ events: {"YYYY-MM-DD": [event]}, recurringRules: [rule] }` |
| `lichviet_settings_v1` | Display toggles + notification config + `viewMode` |

Data migration tự động từ `_v1` → `_v2` lần đầu app load sau upgrade.

---

## Quyền riêng tư

- **Không có tracking**, không analytics, không cookies.
- **Không gửi data ra ngoài** — mọi event, setting lưu trong `localStorage` trình duyệt của bạn.
- **Không có backend** — code chạy 100% trên browser.
- **Notifications chạy local** — dùng `setTimeout` + Service Worker `showNotification()`, không qua push server.

---

## Giới hạn đã biết

- **Phạm vi năm:** 1960–2099 (đã validate). Ngoài range có thể sai.
- **Notifications chỉ fire khi app đang mở** (hoặc vừa mở gần đây). Khi app đóng lâu, app sẽ "catch up" notifications bị miss khi bạn mở lại.
- **iOS Notifications** yêu cầu PWA đã add-to-home-screen (iOS 16.4+).

---

## Roadmap

- [x] Month view
- [x] Week view (editorial layout)
- [x] Import / Export ICS
- [x] Repeat events (Weekly / Yearly âm / Yearly dương)
- [x] Local notifications (6 options, 2-tier: open-trigger + in-session)
- [x] Responsive design Material Design 3 (Compact / Medium / Expanded)
- [ ] Dark mode
- [ ] Tiết khí (24 solar terms)
- [ ] Giờ Hoàng Đạo (12 giờ trong ngày)
- [ ] Chia sẻ lịch multi-device (cần backend — phase 2)
- [ ] Đóng gói APK qua Capacitor + Play Store

---

## Đóng góp

Project là **single-file HTML**, không cần setup môi trường gì. Fork repo, edit `index.html`, open trong browser để test.

Issues & pull requests welcome tại https://github.com/bao-vn/lich-viet-pwa

---

## Credits

- **Thuật toán âm lịch:** [Hồ Ngọc Đức](https://honguyenviet.com/amlich/calrules.html) — port từ code JavaScript gốc
- **Nền tảng khoa học:** Jean Meeus, *Astronomical Algorithms*, Willmann-Bell, 1998
- **Phát triển:** [Bao Vo](https://github.com/bao-vn)

---

## License

MIT (nếu chưa quyết định, bạn update sau).

---

*Lịch Việt PWA — một nỗ lực giữ gìn lịch truyền thống Việt Nam trong thời đại số. 🇻🇳*
