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
├── index.html          # LINE homepage (6 tabs: home/menu/coupon/info/style/faq)
├── chat.html           # LINE chatbot state machine (full conversation flow)
├── reservation.html    # 4-step booking wizard + customer DB + admin panel
└── images/             # All PNG assets (see IMAGE_MAP in chat.html)
```

---

## Key Architecture

### chat.html — State Machine
Flow: INTRO → SURVEY_Q1 → Q2 → Q3 → SURVEY_DONE → COUPONS → (rich menu appears)
Then user-driven: showStylists() | showStyleMenu() | showReservation() | showPoint() | showAccess()
Final: showReservationFinal() → showQR() → showFinalMsg()

**Critical functions:**
- `boot()` — starts the flow on page load
- `handleLink(action)` — central router for all card/button taps
- `makeCaro(items)` — builds horizontal carousel cards with images
- `makeImgCard(key, title, desc, links)` — builds single image card
- `av()` — creates bot avatar element with image fallback
- `addChips(chips)` — shows quick-reply button row
- `addNextChips()` — shows the 5 navigation chips after each section

**IMAGE_MAP** (top of script): maps art keys → `images/*.png` paths
**CARD_ARTS** (below IMAGE_MAP): SVG fallback illustrations if image fails
**ICONS**: small SVG icon set for rich menu and UI elements

**Image loading priority:** IMAGE_MAP real photo → CARD_ARTS SVG art → color bg

### reservation.html — Booking + Admin
- Bot conversation → 4-step wizard (menu → calendar → info → confirm)
- localStorage DB key: `harutokyo_db_v3`
- DB schema: `{ reservations: [...], customers: [...] }`
- Admin panel at top-right 管理 button → 3 tabs: 予約一覧 / 顧客管理 / 統計
- Staff memo per booking — saved to localStorage

### index.html — Homepage
- 6 scrollable tabs. Tab switching: `switchTab(name)`
- FAQ accordion built dynamically from `FAQS` array
- Rich menu: `rmAction(action)` → routes to chat.html or reservation.html
- Style tab: 4 gallery cards
- Coupon tab: `useCoupon(btn)` — marks as used

---

## Brand Colors
```
--rose:    #c8846a   (primary)
--rose-lt: #fdf3ef   (light bg)
--rose-dk: #a0604a   (dark)
--green:   #06C755   (LINE green)
--gold:    #b8860b   (point/coupon)
--blue:    #1a6bb5   (links)
--bg:      #ddd5c8   (chat bg)
```

---

## Known Constraints
- No backend — all state is in-memory or localStorage
- Images must be `.png` (ChatGPT DALL-E output format)
- Phone number hardcoded: `tel:0312345678` (03-1234-5678)
- Reservation goes to `reservation.html` — no actual LINE webhook
- QR code is canvas-drawn pattern pointing to GitHub Pages URL

---

## Common Debug Scenarios

### Images not showing
1. Check `IMAGE_MAP` keys match `it.art || it.icon || it.img` in `makeCaro()`
2. Check file exists in `images/` folder with correct `.png` extension
3. Check browser console for 404 on image path
4. If fallback SVG shows instead of photo → image path is wrong

### Chat flow stops
1. Check browser console for JS errors
2. Check `handleLink()` has a case for the action string
3. Check async/await — `await botMsg()` blocks until typing animation completes
4. Check `addChips()` / `addNextChips()` are called after each section

### Reservation admin not saving
1. Check localStorage key is `harutokyo_db_v3`
2. Check `saveReservation()` is called on wizard step 4
3. Check browser isn't in private/incognito mode (localStorage disabled)

### Rich menu not appearing
1. Rich menu shows after `showGift()` is called (post-survey)
2. `document.getElementById('rich-menu').style.display='block'`
3. `showRichMenuOpen()` opens the panel on first show

---

## Testing Checklist
- [ ] Open chat.html → welcome message appears
- [ ] Complete survey (3 questions) → gift card appears
- [ ] Tap 初回特典 → coupon carousel + rich menu appear
- [ ] Tap each rich menu button → correct section loads
- [ ] Tap スタイリスト → 5-card carousel with images
- [ ] Tap スタイルメニュー → wide cards + 5 menu cards
- [ ] Tap 予約する → booking card with 2 buttons, no auto-navigate
- [ ] Tap LINEで予約する → goes to reservation.html
- [ ] Complete reservation → data saved to localStorage
- [ ] Open 管理 panel → reservation appears in 予約一覧
- [ ] Type keyword (予約/メニュー/アクセス) → bot responds
- [ ] Type unknown message → auto-reply fires

---

## Deploy
```bash
git add .
git commit -m "fix: description"
git push origin main
# GitHub Pages auto-deploys in ~60 seconds
```
