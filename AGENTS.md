# Haru Tokyo — Codex Agent Instructions

## Project

Beauty salon LINE Official Account demo page for Mishona hiring test.
Deadline: 2026年4月23日

## Objective

* Create a demo page that closely recreates the LINE Official Account UI/UX
* Implement an interactive reservation bot in reservation.html
* Keep the feature set within the equivalent free-plan scope
* Show PdM-candidate judgment through product design, implementation, and UX thinking

## Repo location (Windows)

C:\\Users\\taha300059\\AppData\\LineHomePage

## Files to work on

* index.html — main LINE homepage (4-tab UI)
* reservation.html — interactive reservation bot

## Stack

* Pure HTML + CSS + JS, zero dependencies
* Single file per page — all CSS and JS inline
* No npm, no build step, no framework
* Google Fonts via CDN only

## Hard rules — never violate

1. CSS variables only for colors — never hardcode hex values
2. Max body width: 390px (mobile)
3. LINE green stays #06C755 everywhere
4. tel:0312345678 failsafe link must exist on every reservation screen
5. No external scripts except Google Fonts
6. No console errors
7. Noto Sans JP for all Japanese text
8. Salon name is always "Haru Tokyo" / "ハル東京" — never change

## Current feature status

### index.html ✅

* LINE-style topbar with back button
* Cover photo area with brand text
* Profile block: avatar, friend count, bio, add-friend button
* 4 tabs: ホーム / メニュー / クーポン / 店舗情報
* Home: 6-button rich menu, campaign card, news list
* Menu: categorized price list
* Coupon: 2 coupons with つかう button
* Info: address, hours, map placeholder

### reservation.html ✅

* 4-step wizard with progress indicator
* Step 1: menu selection (5 options with icons)
* Step 2: interactive calendar + time slots (available/full)
* Step 3: customer info form with validation
* Step 4: booking confirmation summary
* Phone fallback button on every step

## What Codex should help with

* Small UI fixes (spacing, alignment, font sizes)
* Adding CSS transitions or animations
* Fixing bugs found during QA
* Adding new time slots or menu items
* Improving mobile tap targets (min 44px height)
* Adding smooth accordion animations
* Form UX improvements

## What Codex should NOT do

* Change the file structure (keep single-file)
* Add npm packages or external libraries
* Change the salon name or brand colors
* Remove the phone failsafe buttons
* Break the 4-tab navigation structure

## Testing checklist (run after every edit)

* Open in Chrome → no console errors (F12)
* All 4 tabs switch correctly
* 友だち追加 button navigates to reservation.html
* Calendar shows correct month, past dates greyed
* Time slots show full/available states
* Form validation fires on empty submit
* Confirmation screen shows correct booking data
* Phone button is tappable on every reservation screen
* Looks correct on 390px width (Chrome DevTools)
