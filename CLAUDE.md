# Haru Tokyo — LINE Demo · Claude Code Context

## Project
LINE Official Account demo for Mishona hiring test.
Static HTML deployed on GitHub Pages — no backend, no build step.

**Live URL:** https://beatle099.github.io/LineHomePage/
**Repo:** https://github.com/beatle099/LineHomePage
**Deadline:** 2026-04-23

---

## File Structure
```
LineHomePage/
├── index.html              # LINE homepage (6 tabs: home/menu/coupon/info/style/faq)
├── chat.html               # LINE chatbot state machine (full conversation flow)
├── reservation.html        # 4-step booking wizard + customer DB + admin panel
├── images/                 # All PNG assets (26 files, ~8MB compressed)
│   ├── HaruTokyodesignbanner.png  # Cover banner for index.html
│   ├── line_icon.png       # "H" logo — used as bot avatar + index profile
│   ├── stylist_*.png       # Stylist photos (koshigaya, okuyama, sato, manabe, all)
│   ├── menu_*.png          # Menu photos (cut, color, perm, treatment, consult)
│   ├── coupon_bg.png       # Coupon card background
│   ├── gift_bg.png         # Gift card background
│   ├── salon_interior.png  # Salon intro wide card
│   ├── products_shelf.png  # Products wide card
│   └── booking_hero.png    # Booking card hero
├── CLAUDE.md               # This file — Claude Code context
├── AGENTS.md               # Codex agent context
├── SUBMISSION_REPORT.md    # PdM submission report (Japanese)
├── DISCOVERY.md            # Phase 1 discovery & planning
├── PRD.md                  # Product requirements document
├── COMPETITIVE_ANALYSIS_1.md  # L Message market analysis
└── SEGMENT_POSITIONING.md  # Target segments & personas
```

---

## Key Architecture

### chat.html — State Machine (937 lines)

**First-visit flow:** boot() → survey (3 questions) → showGift() → coupon carousel → rich menu + showMainNav()
**Return-visit flow:** localStorage `harutokyo_survey_done` flag → skip survey → "おかえりなさい" → rich menu immediately
**Deep-link flow:** `?action=stylist|style_menu|campaign|reservation|shopinfo|access` → skip everything, jump to section

**User-driven sections (via rich menu / chips):**
- `showStylists()` — 2 stylist cards (越谷涼介, 奥山香織)
- `showStyleMenu()` — 2 menu cards (デザインカット ¥6,600, オーガニックカラー ¥6,600) + 2 wide intro cards
- `showShopIntro()` — unified salon introduction (logo, stylists, menu, access, hours, map, CTA)
- `showReservation()` — phone + LINE booking card
- `showCampaign()` — 2 campaign cards (春の美髪, 初回限定20%OFF)

**Critical functions:**
- `boot()` — first-visit survey flow (500ms delay)
- `handleLink(action)` — central router for all card/button taps
- `makeCaro(items)` — builds 2-column grid cards with images
- `makeImgCard(key, title, desc, links)` — builds single image card
- `av()` — bot avatar (44px, `images/line_icon.png` with SVG fallback)
- `addChips(chips)` — quick-reply button row
- `addNextChips()` — 5 navigation chips (スタイリスト紹介/施術メニュー/クーポン/ご予約/お店の紹介)
- `showShopIntro()` — unified お店の紹介 (replaces old showShopInfo + showAccess)
- `rmAction(action)` — rich menu button router (5 actions)
- `timeStr()` — 午前/午後 dynamic timestamp

**IMAGE_MAP** (top of script): maps 20 art keys → `images/*.png` paths
**CARD_ARTS**: 9 SVG fallback illustrations
**ICONS**: 12 inline SVG icons

**Image loading:** IMAGE_MAP photo → CARD_ARTS SVG → ICONS → gradient fallback
**onerror handler:** `this.style.display='none'` (simple, no broken HTML escaping)
**Card layout:** 2-column CSS grid (`grid-template-columns:1fr 1fr`)
**KW auto-reply:** 10 keywords (予約/メニュー/スタイル/スタッフ/スタイリスト/クーポン/アクセス/場所/お店/紹介)

### reservation.html — Booking + Admin (775 lines)
- 4-step wizard (menu → calendar → info → confirm)
- **Confirm step:** User reviews → clicks "予約を確定する" → `confirmBooking()` saves (deferred save)
- **Calendar:** Renders on step 2 transition (not page load — hidden container fix for `aspect-ratio:1`)
- **localStorage DB key:** `harutokyo_db_v3`
- **DB schema:** `{ reservations: [...], customers: [...] }`
- **Customer upsert** by phone number (visitCount, lastVisit tracking)
- **Admin panel:** top-right 管理 button → 3 tabs: 予約一覧 / 顧客管理 / 統計
- **Staff memo** per booking — saved to localStorage
- **Visit type:** reads from `<select id="inp-visit">` (not radio buttons)
- **Time-slot validation** guard before step 3

### index.html — Homepage (681 lines)
- 6 scrollable tabs: `switchTab(name)`
- **Cover banner:** `images/HaruTokyodesignbanner.png` with gradient fallback
- **Profile avatar:** `images/line_icon.png` (72px circle)
- **Back button:** `onclick → chat.html`
- **FAQ accordion:** `FAQS` array (8 questions) + `toggleFaq()`
- **Rich menu:** 6 buttons (予約/メニュー/アクセス/クーポン/スタイル/FAQ)
- **Style tab:** 4 gallery cards with real images (menu_color/cut/perm/treatment.png)
- **Coupon persistence:** `harutokyo_coupons` localStorage key
- **Hash deep link:** `index.html#style` or `#info` auto-switches tab

---

## LINE Rich Menu URL Mapping

| Button | Label | URL |
|--------|-------|-----|
| A | スタイリスト紹介 | `chat.html?action=stylist` |
| B | 施術メニュー | `chat.html?action=style_menu` |
| C | クーポン | `chat.html?action=campaign` |
| D | 予約 | `reservation.html` |
| E | お店の紹介 | `chat.html?action=shopinfo` |

All deep links skip survey, show rich menu immediately, jump to section.

---

## Brand Colors
```
--rose:    #c8846a   (primary)
--rose-lt: #fdf3ef   (light bg)
--rose-dk: #a0604a   (dark)
--green:   #06C755   (LINE green — never substitute)
--gold:    #b8860b   (point/coupon)
--blue:    #1a6bb5   (links)
--bg:      #ddd5c8   (chat bg)
```

---

## What NOT to change
- LINE green (#06C755) — brand critical
- `tel:0312345678` on all reservation steps — failsafe
- 6-tab structure of index.html
- Salon name: Haru Tokyo / ハル東京
- DB key: `harutokyo_db_v3`
- Stylists: 越谷涼介, 奥山香織 (2 only — Mishona requirement)
- Menu: デザインカット ¥6,600, オーガニックカラー ¥6,600 (Mishona requirement)

---

## Known Constraints
- No backend — localStorage only
- Images: 26 PNGs, ~8MB total (compressed from 63MB via Pillow)
- Phone: `03-1234-5678` — placeholder
- Address: `神宮前X-XX-XX` / `〇〇ビル` — placeholder
- Font: non-blocking load (`media="print" onload` trick)

---

## Common Debug Scenarios

### Images not showing
1. Check `IMAGE_MAP` keys match `it.art || it.icon || it.img` in `makeCaro()`
2. Check file exists in `images/` with correct `.png` extension
3. onerror uses `this.style.display='none'` — gradient shows through

### Chat flow stops (no chips)
1. Check `handleLink()` has a case for the action AND has `return;`
2. Check `addNextChips()` is called after EVERY section function
3. All handleLink branches must end with `addNextChips()`, navigation, or delegate

### Calendar dates not showing
1. `renderCalendar()` deferred to `goToStep(2)` — hidden container = zero height
2. Check `isPast()` and Tuesday closed logic

### Reservation not saving
1. `saveReservation()` called from `confirmBooking()` (NOT `goToStep`)
2. DB key: `harutokyo_db_v3`
3. Visit field: `<select id="inp-visit">`, not radio buttons

### First-visit survey repeating
1. Check `harutokyo_survey_done` flag in localStorage
2. Flag set in `showGift()` after survey completes

---

## Performance Optimizations
- `loading="lazy"` on all image creation points
- Non-blocking Google Fonts (`media="print" onload`)
- Scroll debounce (clearTimeout + 120ms)
- Images compressed: 63MB → 8MB (Pillow)

---

## Testing Checklist
- [ ] chat.html: welcome message appears on first visit
- [ ] Complete survey → gift card → coupon carousel + rich menu
- [ ] Refresh → survey skipped → "おかえりなさい" + rich menu immediately
- [ ] Tap スタイリスト紹介 → 2 stylist cards (越谷, 奥山)
- [ ] Tap 施術メニュー → 2 menu cards with ¥6,600 prices
- [ ] Tap お店の紹介 → unified view (logo, stylists, menu, access, map, CTA)
- [ ] Tap クーポン → campaign cards
- [ ] Tap ご予約 → booking card → LINEで予約する → reservation.html
- [ ] reservation: select menu → step 2 → calendar dates clickable
- [ ] Select date + time → step 3 → form → step 4 review
- [ ] 予約を確定する → success + localStorage saved
- [ ] 管理 → admin panel with reservation + memo
- [ ] index.html: cover banner visible + "Haru Tokyo" text
- [ ] トーク back button → chat.html
- [ ] All 6 tabs switch correctly
- [ ] Style tab → 4 cards with images
- [ ] FAQ → 8 questions accordion
- [ ] Coupon → use → refresh → stays "使用済み"
- [ ] Deep link: chat.html?action=shopinfo → お店の紹介 directly
- [ ] KW: type "アクセス" or "お店" → showShopIntro()
- [ ] No console errors (F12)

---

## Deploy
```bash
git add .
git commit -m "fix: description"
git push origin main
# GitHub Pages auto-deploys in ~60 seconds
```
