# Haru Tokyo — LINE Demo · Codex / OpenAI Agents Context

## Project Overview
Static HTML LINE Official Account demo. No framework, no bundler, no backend.
Three HTML files + PNG images in `/images/` folder.

**Live:** https://beatle099.github.io/LineHomePage/
**Stack:** Vanilla JS, CSS custom properties, localStorage

---

## File Map
| File | Purpose | Key Functions |
|------|---------|---------------|
| `chat.html` | LINE chatbot UI + state machine | `boot()`, `handleLink()`, `makeCaro()`, `makeImgCard()` |
| `index.html` | LINE homepage tabs | `switchTab()`, `rmAction()`, `toggleFaq()` |
| `reservation.html` | Booking wizard + admin DB | `goToStep()`, `saveReservation()`, `renderAdmin()` |

---

## State Machine (chat.html)

### Conversation Flow
```
boot()
  └─ INTRO message + survey card
       └─ [tap] startSurvey()
            └─ surveyQ1() → ansQ1() → surveyQ2() → ansQ2() → surveyQ3() → ansQ3()
                 └─ gift card appears
                      └─ [tap gift_unlock] showGift()
                           └─ coupon carousel + rich menu visible
                                └─ addNextChips() → user picks section
                                     ├─ showStylists()
                                     ├─ showStyleMenu()
                                     ├─ showReservation()
                                     ├─ showPoint()
                                     └─ showAccess()
                                          └─ showReservationFinal() → showQR() → showFinalMsg()
```

### handleLink() Action Map
| Action | Handler |
|--------|---------|
| `start_survey` | starts Q1 survey |
| `gift_unlock` | showGift() |
| `reservation` / `reservation_bot` | navigate to reservation.html |
| `stylist` | showStylists() |
| `style_menu` / `style_menu_detail` | showStyleMenu() |
| `access` | showAccess() |
| `coupon_20` | show coupon confirm message |
| `coupon_spa` | show spa coupon message |
| `coupon_lottery` | show lottery result |
| `open_map` | window.open Google Maps |
| `point_stamp` | show point added message |
| `point_use` | show point balance |
| `hobby` | show coming soon message |
| `contact` | show contact message |

---

## Image System (chat.html)

### Loading Priority
1. `IMAGE_MAP[key]` → real photo from `images/*.png`
2. `CARD_ARTS[key]` → SVG illustration fallback
3. Color gradient background (last resort)

### Key Mapping
```js
IMAGE_MAP = {
  'koshigaya'    → 'images/stylist_koshigaya.png',
  'okuyama'      → 'images/stylist_okuyama.png',
  'sato'         → 'images/stylist_sato.png',
  'manabe'       → 'images/stylist_manabe.png',
  'any_stylist'  → 'images/stylist_all.png',
  'cut_large'    → 'images/menu_cut.png',
  'color_large'  → 'images/menu_color.png',
  'perm'         → 'images/menu_perm.png',
  'treatment'    → 'images/menu_treatment.png',
  'stylist'      → 'images/menu_consult.png',
  'coupon'       → 'images/coupon_bg.png',
  'spa'          → 'images/coupon_bg.png',
  'gift_unlock'  → 'images/gift_bg.png',
  'welcome'      → 'images/gift_bg.png',
  'salon_intro'  → 'images/gift_bg.png',
  'salon_interior'→ 'images/salon_interior.png',
  'products'     → 'images/products_shelf.png',
  'booking'      → 'images/booking_hero.png',
  'point'        → 'images/rm_point.png',
  'access'       → 'images/rm_access.png',
}
```

### Carousel Item Schema
```js
{
  img: 'c1',           // fallback CSS class (c1-c5 = color gradients)
  icon: 'cut',         // ICONS map key
  art: 'cut_large',    // CARD_ARTS map key (SVG illustration)
  title: 'デザインカット',
  desc: '説明文',
  links: [
    { t: 'ご予約はこちら', a: 'reservation' }
  ]
}
```

---

## Database (reservation.html)

### localStorage Key: `harutokyo_db_v3`

### Schema
```js
{
  reservations: [{
    id: 'R' + Date.now(),
    createdAt: ISO string,
    memo: '',              // staff memo, editable in admin
    menu, duration, price,
    date: 'YYYY-MM-DD',
    time: 'HH:MM',
    name, phone, email,
    visit: 'first' | 'repeat',
    note,
    gender, ageRange,
    concerns: string[]
  }],
  customers: [{
    id: 'C' + Date.now(),
    name, phone, email,
    gender, ageRange,
    concerns: string[],
    visitCount: number,
    firstVisit: ISO string,
    lastVisit: ISO string,
    reservationIds: string[]
  }]
}
```

### DB Functions
- `loadDB()` — reads from localStorage, returns default if empty
- `saveDB(db)` — writes to localStorage
- `saveReservation(data)` — adds reservation + upserts customer record

---

## Keyword Auto-Reply (chat.html)
```js
KW = {
  '予約'       → showReservation(),
  'メニュー'   → showStyleMenu(),
  'スタイル'   → showStyleMenu(),
  'スタッフ'   → showStylists(),
  'スタイリスト'→ showStylists(),
  'クーポン'   → handleLink('coupon_20'),
  'アクセス'   → showAccess(),
  '場所'       → showAccess(),
  'ポイント'   → showPoint(),
}
// Unknown message → AUTO_REPLY (standard deflection message)
```

---

## Admin Panel (reservation.html)
- Toggle: top-right 管理 button → `openAdmin()` / `closeAdmin()`
- Tabs: 予約一覧 / 顧客管理 / 統計
- `renderAdmin(tab)` — renders selected tab from localStorage data
- `saveMemo(id)` — saves staff memo for a reservation
- `clearDB()` — deletes all data (with confirm)

---

## Debugging Guide

### Problem: Images show as SVG illustrations, not photos
- Check: `images/` folder exists next to HTML files
- Check: filename is lowercase `.png` exactly as in IMAGE_MAP
- Check: browser network tab for 404 on image URL

### Problem: Chat stops at a certain step
- Open DevTools Console — look for uncaught errors
- Check all `await botMsg()` have proper async function parents
- Check `handleLink()` has a case for the tapped action

### Problem: Survey repeats or skips
- State functions are not gated — they just append to DOM
- If called twice, messages double up
- Fix: add a `busy` flag guard before each section function

### Problem: Reservation data not in admin
- Check `saveReservation()` was called on step 4 confirmation
- Check localStorage in DevTools: Application → Local Storage → harutokyo_db_v3
- Check if browser is incognito (localStorage disabled)

### Problem: Rich menu doesn't appear
- `document.getElementById('rich-menu').style.display` must be `'block'`
- This is set inside `showGift()` → setTimeout 800ms
- If survey was skipped, rich menu stays hidden

---

## Constraints
- No npm, no build, no TypeScript — pure vanilla JS
- All state is in-memory (chat) or localStorage (reservations)
- Images must be `.png` — not `.jpg` or `.jpeg`
- Max image file size: ~500KB per file for fast loading
- Phone: `tel:0312345678` — hardcoded across all 3 files
- GitHub Pages serves from repo root — paths are relative
