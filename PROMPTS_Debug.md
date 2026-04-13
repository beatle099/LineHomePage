# Haru Tokyo — AI Debug Prompts
# Copy-paste these prompts directly into Claude Code or Codex

---

## HOW TO USE

### Claude Code (VS Code extension)
1. Open VS Code in the LineHomePage folder
2. Press `Ctrl+Shift+P` → "Claude: Open Chat"
3. Copy-paste any prompt below

### Codex (OpenAI VS Code extension)
1. Open VS Code in the LineHomePage folder
2. Press `Ctrl+Shift+P` → "Codex: Open"
3. Copy-paste any prompt below

---
---

## PROMPT 1 — PROJECT ONBOARDING
*Use this first so the AI understands the full project*

```
Read CLAUDE.md and AGENTS.md in this repo, then read all 3 HTML files
(index.html, chat.html, reservation.html).

After reading, give me a summary of:
1. The current state of each file (size, purpose, key functions)
2. Any obvious bugs or issues you can see
3. Which images are referenced and whether they exist in the images/ folder
4. The full chat bot conversation flow from boot() to showFinalMsg()

Do not make any changes yet. Just report what you find.
```

---

## PROMPT 2 — FULL DEBUG SCAN
*Run this to find all bugs automatically*

```
You are debugging a vanilla JS LINE chatbot demo with 3 HTML files.
Read CLAUDE.md for project context, then perform a full debug scan:

CHECK THESE SPECIFIC THINGS:

1. JAVASCRIPT SYNTAX
   - Run syntax check on all <script> blocks in all 3 HTML files
   - Report any uncaught errors, undefined variables, or broken functions

2. IMAGE LOADING
   - Find the IMAGE_MAP object in chat.html
   - List every key → value pair
   - Check if the file exists in images/ for each value
   - Report any mismatches between key names and actual art/icon keys in botCaro() calls

3. HANDLELINK ROUTING
   - Find every handleLink('xxx') call in chat.html
   - Find every if(a==='xxx') handler in the handleLink() function
   - Report any actions that are called but NOT handled

4. STATE MACHINE COMPLETENESS
   - Trace the full conversation flow from boot()
   - Check every state transition is reachable
   - Check addNextChips() is called after every section so user can navigate

5. LOCALSTORAGE DATABASE
   - Check saveReservation() saves all required fields
   - Check renderAdmin() reads from correct DB key (harutokyo_db_v3)
   - Check saveMemo() updates correctly

6. NAVIGATION
   - Check all href and window.location.href links point to valid files
   - Check back buttons return to correct pages

7. PHONE NUMBER
   - Check tel:0312345678 is consistent across all 3 files

Report every issue found with exact file name, line number, and suggested fix.
Do NOT make changes yet — report only.
```

---

## PROMPT 3 — FIX IMAGES NOT SHOWING
*Use when images show as SVG art instead of real photos*

```
Read CLAUDE.md for context.

The images in chat.html are not loading — showing SVG fallback illustrations
instead of real photos from the images/ folder.

Debug and fix this issue:

1. Open chat.html and find the IMAGE_MAP constant
2. Find the makeCaro() function — check how it resolves:
   const imgKey = it.art || it.icon || it.img || ''
   const imgSrc = IMAGE_MAP[imgKey]
3. For each botCaro() call in the file, trace what art/icon/img values
   are passed and verify IMAGE_MAP has a matching key
4. Check the <img> tag is being created correctly with the right src
5. Check the onerror fallback is not hiding a path issue

List every carousel call with its resolved imgKey and imgSrc.
Fix any mismatches between the keys passed in botCaro() and the IMAGE_MAP keys.
Show the exact lines changed.
```

---

## PROMPT 4 — FIX CHAT FLOW STOPPING
*Use when the chatbot stops and doesn't continue*

```
Read CLAUDE.md for context.

The chat.html state machine stops at a certain point and doesn't continue.
Debug the conversation flow:

1. Trace the full async chain:
   boot() → startSurvey() → ansQ1() → ansQ2() → ansQ3() →
   showGift() → showMainNav() → [user picks] → section functions

2. Check every async function:
   - Does it properly await botMsg()?
   - Does it call addNextChips() or addChips() at the end?
   - Could it throw an error that breaks the chain?

3. Check the rich menu visibility:
   - Is document.getElementById('rich-menu') found?
   - Is showRichMenuOpen() being called?

4. Check handleLink():
   - Every action string called in HTML must have a matching case
   - Look for typos in action names (e.g. 'reservation' vs 'reservation_bot')

5. Check addNextChips() — does it correctly pass action functions?

Fix any broken async chains, missing handlers, or missing chip calls.
Show all changes with before/after.
```

---

## PROMPT 5 — FIX RESERVATION BOOKING
*Use when reservation.html has issues*

```
Read CLAUDE.md and AGENTS.md for context.

Debug and fix reservation.html:

1. WIZARD FLOW
   - Trace goToStep(1) → goToStep(2) → goToStep(3) → goToStep(4)
   - Check each step shows correct screen and hides others
   - Check the step indicator dots update correctly

2. CALENDAR
   - Check renderCalendar() generates correct dates for current month
   - Check selectDate() enables the Next button
   - Check showTimeSlots() renders time slots correctly

3. FORM VALIDATION
   - Check validateField('name') and validateField('phone') work
   - Check error messages show/hide correctly

4. DATABASE SAVE
   - Check saveReservation() is called on step 4
   - Check all fields (gender, ageRange, concerns from bot survey) are included
   - Verify data appears in admin panel after booking

5. ADMIN PANEL
   - Check renderAdmin('reservations') reads from localStorage key harutokyo_db_v3
   - Check saveMemo() saves the memo correctly
   - Check stats tab shows correct counts

Fix all issues found. Show exact changes.
```

---

## PROMPT 6 — PERFORMANCE OPTIMIZATION
*Use to make the app load faster*

```
Read CLAUDE.md for context.

Optimize chat.html for faster loading on mobile:

1. IMAGES
   - The onerror fallback on each <img> loads a full SVG CARD_ARTS string
   - Consider lazy-loading images with loading="lazy" attribute
   - Add explicit width/height to all <img> tags to prevent layout shift

2. SVG ASSETS
   - CARD_ARTS and ICONS are large inline SVG strings in the JS
   - Suggest whether to keep inline or move to separate files

3. CHAT RENDERING
   - Check if botMsg() typing animation delay (650ms) is appropriate
   - Check if multiple rapid botMsg() calls could cause race conditions

4. LOCALSTORAGE
   - Check loadDB() is not called too frequently
   - Check if DB reads/writes could be batched

5. DOM OPERATIONS
   - Check if area.scrollTo() is called too frequently
   - Check if scroll() setTimeout(80ms) is optimal

Report specific optimizations with estimated impact.
Implement the top 3 highest-impact changes.
```

---

## PROMPT 7 — ADD NEW FEATURE
*Use to add a new section to the chatbot*

```
Read CLAUDE.md and AGENTS.md for context.

Add a new "キャンペーン" (Campaign) section to chat.html:

Requirements:
1. Add 'campaign' button to the rich menu (replace one existing button or add logic)
2. Add キャンペーン chip to addNextChips() list
3. Create showCampaign() function that:
   - Shows a bot message: "現在開催中のキャンペーンをご紹介します 🌸"
   - Shows a carousel of 2 campaign cards using botCaro():
     Card 1: title="春の美髪キャンペーン", desc="カラー＋トリートメント ¥8,800（税込）", art='coupon', link action='coupon_20'
     Card 2: title="初回限定20%OFF", desc="はじめてのご来店に", art='gift_unlock', link action='gift_unlock'
   - Calls addNextChips() after showing cards
4. Add 'campaign' case to handleLink() routing to showCampaign()
5. Add キャンペーン to the KW keyword auto-reply map

Show the complete implementation with all changes across the file.
```

---

## PROMPT 8 — PREPARE FOR MISHONA SUBMISSION
*Run this before final submission*

```
Read CLAUDE.md for context. This project is a hiring test submission for Mishona.
Deadline: 2026-04-23. Live URL: https://beatle099.github.io/LineHomePage/

Perform final pre-submission checks:

1. FUNCTIONALITY CHECK
   - Verify all 3 HTML files open without console errors
   - Verify chat flow completes end-to-end
   - Verify reservation can be completed and shows in admin
   - Verify all images load (check IMAGE_MAP vs actual files)
   - Verify phone number 03-1234-5678 appears on all booking screens

2. CONTENT CHECK
   - Salon name shows as "Haru Tokyo" everywhere (not "Hair Alice")
   - LINE ID shows as @harutokyo where relevant
   - All Japanese text is grammatically correct
   - No placeholder text like "XXX" or "TODO" remains

3. MOBILE CHECK
   - max-width:390px (index/reservation) or 430px (chat) on body
   - No horizontal overflow
   - Touch targets are at least 44px tall
   - Fonts are readable at mobile size (min 11px)

4. GITHUB PAGES CHECK
   - All image paths are relative (images/xxx.png, not /images/xxx.png)
   - No absolute localhost URLs
   - QR code URL points to beatle099.github.io (not taha300059)

5. CODE QUALITY
   - No console.log() calls left in production code
   - No commented-out debug code
   - No unused variables or functions

Report every issue found with priority (HIGH/MEDIUM/LOW).
Fix all HIGH priority issues immediately.
```

---

## PROMPT 9 — GENERATE SUBMISSION REPORT
*Create a PdM-style report for Mishona*

```
Read all files in this repo: CLAUDE.md, AGENTS.md, DISCOVERY.md, PRD.md,
COMPETITIVE_ANALYSIS.md, SEGMENT_POSITIONING.md, and all 3 HTML files.

Create a professional Product Manager submission report for Mishona in Japanese.
The report should cover:

1. プロジェクト概要 (Project Overview)
   - What was built and why
   - Live demo URL

2. 機能一覧 (Feature List)
   - List every feature implemented across all 3 pages
   - Mark each as ✅ Completed

3. 技術仕様 (Technical Spec)
   - Architecture decisions
   - State machine design
   - Data persistence approach

4. デモシナリオ (Demo Scenario)
   - Step-by-step guide for evaluator to test the app
   - Expected behavior at each step

5. 今後の改善提案 (Future Improvements)
   - 3-5 things that would be added with more time

Format as a clean markdown document ready to paste into a LINE message or email.
Keep it concise — max 500 words total.
```

---

## QUICK REFERENCE

### Most common fixes

**Images not showing:**
```
In chat.html IMAGE_MAP, verify the key matches it.art in botCaro() calls
```

**Chat stops after survey:**
```
Check showGift() calls showMainNav() and addNextChips() at the end
```

**Reservation not saving:**
```
Check localStorage key is exactly 'harutokyo_db_v3'
```

**Push fails:**
```
git remote set-url origin https://github.com/beatle099/LineHomePage.git
git push -u origin main
```

**GitHub Pages not updating:**
```
Wait 60 seconds after push — or check Actions tab for deploy status
```
