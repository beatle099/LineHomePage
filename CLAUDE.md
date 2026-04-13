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
├── images/                 # All PNG assets (see IMAGE_MAP in chat.html)
│   ├── HaruTokyodesignbanner.png  # Cover banner for index.html
│   ├── stylist_*.png       # 5 stylist photos
│   ├── menu_*.png          # 5 menu service photos
│   ├── coupon_bg.png       # Coupon card background
│   ├── gift_bg.png         # Gift card background
│   ├── salon_interior.png  # Salon intro card
│   ├── products_shelf.png  # Products card
│   ├── booking_hero.png    # Booking card hero
│   ├── bot_avatar.png      # Chat bot avatar (44x44)
│   └── rm_*.png            # Rich menu icons (unused — inline SVG used instead)
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

### chat.html — State Machine
Flow: INTRO → SURVEY_Q1 → Q2 → Q3 → SURVEY_DONE → COUPONS → (rich menu appears)
Then user-driven: showStylists() | showStyleMenu() | showCampaign() | showReservation() | showPoint() | showAccess()
Final: showReservationFinal() → showQR() → showFinalMsg()

**Critical functions:**
- `boot()` — starts the flow on page load (500ms delay)
- `handleLink(action)` — central router for all card/button taps (15 actions)
- `makeCaro(items)` — builds 2-column grid cards with images
- `makeImgCard(key, title, desc, links)` — builds single image card
- `av()` — creates bot avatar element (44px, image with SVG fallback)
- `addChips(chips)` — shows quick-reply button row
- `addNextChips()` — shows 6 navigation chips after each section
- `showCampaign()` — campaign carousel (2 cards)
- `rmAction(action)` — rich menu button router (7 actions)
- `timeStr()` — 午前/午後 dynamic timestamp

**IMAGE_MAP** (top of script): maps 20 art keys → `images/*.png` paths
**CARD_ARTS** (below IMAGE_MAP): 9 SVG fallback illustrations
**ICONS**: 12 inline SVG icons for rich menu and UI

**Image loading priority:** IMAGE_MAP real photo → CARD_ARTS SVG art → ICONS → color bg
**Card layout:** 2-column CSS grid (`grid-template-columns:1fr 1fr`), no horizontal scroll
**Bot avatar:** 44px circle, `images/bot_avatar.png` with SVG fallback

**KW auto-reply:** 10 keywords (予約/メニュー/スタイル/スタッフ/スタイリスト/クーポン/アクセス/場所/ポイント/キャンペーン)

### reservation.html — Booking + Admin
- Bot conversation → 4-step wizard (menu → calendar → info → confirm → save)
- **Confirm step:** User reviews booking, clicks "予約を確定する" to save (deferred save pattern)
- Calendar renders on step 2 transition (not page load — hidden container fix)
- localStorage DB key: `harutokyo_db_v3`
- DB schema: `{ reservations: [...], customers: [...] }`
- Customer upsert by phone number (visitCount, lastVisit tracking)
- Admin panel at top-right 管理 button → 3 tabs: 予約一覧 / 顧客管理 / 統計
- Staff memo per booking — saved to localStorage
- Visit type reads from `<select id="inp-visit">` (not radio buttons)
- Time-slot validation guard before step 3

### index.html — Homepage
- 6 scrollable tabs. Tab switching: `switchTab(name)`
- Cover banner: `images/HaruTokyodesignbanner.png` with gradient fallback
- Back button: `onclick → chat.html`
- FAQ accordion built dynamically from `FAQS` array (8 questions)
- Rich menu: 6 buttons with `onclick` handlers (予約/メニュー/アクセス/クーポン/スタイル/FAQ)
- Style tab: 4 gallery cards with real images (menu_color/cut/perm/treatment.png)
- Coupon tab: `useCoupon(btn)` — persists used state to localStorage (`harutokyo_coupons` key)

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
- The LINE green color (#06C755) — brand critical
- The tel: link (tel:0312345678) on all reservation steps — failsafe requirement
- The 6-tab structure of index.html — matches LINE Official Account spec
- Salon name: Haru Tokyo / ハル東京
- DB key: `harutokyo_db_v3` — admin panel depends on it

---

## Known Constraints
- No backend — all state is in-memory or localStorage
- Images must be `.png` (ChatGPT DALL-E output, compressed via Pillow to ~8MB total)
- Phone number hardcoded: `tel:0312345678` (03-1234-5678) — placeholder
- Address placeholder: `神宮前X-XX-XX` / `〇〇ビル` — needs real address
- Reservation goes to `reservation.html` — no actual LINE webhook
- QR code is canvas-drawn pattern pointing to GitHub Pages URL
- Font loaded non-blocking: `media="print" onload="this.media='all'"` trick

---

## Common Debug Scenarios

### Images not showing
1. Check `IMAGE_MAP` keys match `it.art || it.icon || it.img` in `makeCaro()`
2. Check file exists in `images/` folder with correct `.png` extension
3. Check browser console for 404 on image path
4. onerror handler uses `this.style.display='none'` — background gradient shows through

### Chat flow stops (no chips appear)
1. Check browser console for JS errors
2. Check `handleLink()` has a case for the action string AND has `return;`
3. Check async/await — `await botMsg()` blocks until typing animation completes
4. Check `addChips()` / `addNextChips()` are called after EVERY section and handleLink branch
5. All 15 handleLink branches must end with either `addNextChips()`, navigation, or delegate to a section function

### Calendar dates not showing
1. `renderCalendar()` must be called AFTER screen2 is visible (deferred in `goToStep(2)`)
2. `aspect-ratio:1` on `.cal-cell` produces 0px height in hidden containers
3. Check `isPast()` and Tuesday closed logic

### Reservation admin not saving
1. Check localStorage key is `harutokyo_db_v3`
2. `saveReservation()` is called from `confirmBooking()` (NOT from `goToStep`)
3. Check browser isn't in private/incognito mode (localStorage disabled)
4. Visit field reads from `<select id="inp-visit">`, not radio buttons

### Rich menu not appearing
1. Rich menu shows after `showGift()` is called (post-survey)
2. `document.getElementById('rich-menu').style.display='block'`
3. `showRichMenuOpen()` opens the panel on first show

---

## Performance Optimizations Applied
- `loading="lazy"` on all 6 image creation points
- Non-blocking Google Fonts (`media="print" onload` trick)
- Scroll debounce (clearTimeout + 120ms re-arm)
- Images compressed: 63MB → 8MB (Pillow resize + optimize)

---

## Testing Checklist
- [ ] Open chat.html → welcome message appears
- [ ] Complete survey (3 questions) → gift card appears
- [ ] Tap 初回特典 → coupon carousel + rich menu appear
- [ ] Tap each rich menu button → correct section loads
- [ ] Tap スタイリスト → 2-column grid with 5 stylist cards
- [ ] Tap キャンペーン → 2 campaign cards fully visible
- [ ] Tap ポイントを貯める → bot responds → navigation chips appear (not dead end)
- [ ] Tap 予約する → booking card with phone + LINE buttons
- [ ] Tap LINEで予約する → goes to reservation.html
- [ ] Select menu → step 2 → calendar dates appear and are clickable
- [ ] Select date + time → step 3 → fill form → step 4 shows review
- [ ] Click 予約を確定する → success message + data in localStorage
- [ ] Click 管理 → admin shows reservation in 予約一覧 + memo editable
- [ ] Open index.html → cover banner image visible + "Haru Tokyo" text
- [ ] Back button (トーク) → navigates to chat.html
- [ ] All 6 tabs switch correctly (home/menu/coupon/info/style/faq)
- [ ] Style tab → 4 cards with real images
- [ ] FAQ tab → 8 questions with accordion toggle
- [ ] Use coupon → refresh → stays "使用済み"
- [ ] Type keyword (予約/キャンペーン) in chat → bot responds
- [ ] Type unknown message → auto-reply fires
- [ ] No console errors in any file (F12 DevTools)

---

## Deploy
```bash
git add .
git commit -m "fix: description"
git push origin main
# GitHub Pages auto-deploys in ~60 seconds
```
