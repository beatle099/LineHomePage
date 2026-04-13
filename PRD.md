# Product Requirements Document (PRD)
## Mishona PdM Checklist App — v1.0 MVP

**Author:** Take  
**Date:** 2026-04-13  
**Status:** Draft — pending sign-off  

---

## 1. Overview
A single-file interactive checklist web app that guides a Product Manager
through Mishona's 6-phase product development lifecycle, with built-in
QA gates that must be cleared before moving to the next phase.

## 2. Problem
Mishona's PdM role requires coordinating across development, design,
marketing, and customer support. Without a structured tracker, steps
get skipped and quality suffers — especially QA gates before release.

## 3. Goals
- Give the PdM a clear view of what phase the product is in
- Enforce QA gate checkpoints at critical transitions
- Persist progress so work is not lost between sessions
- Be simple enough to open in a browser with zero setup

## 4. Non-goals
- No login or user management
- No backend or server
- No mobile-native app
- No real-time collaboration

## 5. User stories
| As a... | I want to... | So that... |
|---------|-------------|-----------|
| PdM | See all phases in one view | I know the full scope of the release |
| PdM | Check off tasks as I complete them | Progress is tracked automatically |
| PdM | See QA gates highlighted clearly | I don't miss critical checkpoints |
| PdM | Resume where I left off | Refreshing the browser doesn't lose my work |
| Team lead | See overall completion % | I can assess release readiness at a glance |

## 6. Functional requirements
### FR-01 Phase accordion
- Display 6 phases as collapsible sections
- Each phase shows: title, role, progress bar, task count
- Click header to expand/collapse

### FR-02 Task checklist
- Each task is a checkbox row
- Clicking anywhere on the row toggles the checkbox
- Checked items show strikethrough text

### FR-03 QA gate markers
- Tasks marked as QA gates show a red "QA gate" badge
- Regular QA tasks show a purple "QA" badge
- Summary shows how many QA gates have been passed

### FR-04 Progress tracking
- Per-phase progress bar fills as tasks are checked
- Top summary shows: completed, total, QA gates passed, overall %

### FR-05 Persistence
- All checkbox state saved to localStorage on every change
- State restored automatically on page load

### FR-06 Reset
- "Reset all" button clears all checkboxes (with confirmation)

### FR-07 Dark mode
- All colors use CSS variables
- Renders correctly in both light and dark browser themes

## 7. Non-functional requirements
- Loads in < 1 second (no external requests on load)
- Works in Chrome 120+ on Windows
- Single file — no build step, no npm required
- File size under 100KB

## 8. Acceptance criteria
| # | Criteria | Test method |
|---|----------|-------------|
| AC-01 | All 6 phases display with correct tasks | Visual check |
| AC-02 | Checking a task updates progress bar | Manual test |
| AC-03 | QA gate badges appear on correct tasks | Visual check |
| AC-04 | Progress persists after F5 refresh | Manual test |
| AC-05 | Reset all clears all checkboxes | Manual test |
| AC-06 | App renders in dark mode | Toggle OS dark mode |
| AC-07 | No console errors on load | Chrome DevTools |

## 9. Open questions
- [ ] Should the app export a progress report? (Post-MVP)
- [ ] Should QA gates block proceeding to next phase? (UX decision)
- [ ] Add Anthropic API for AI-generated task suggestions? (Post-MVP)

---
## PRD Sign-off
- [ ] PdM (Take) reviewed
- [ ] QA gate — PRD approved, ready for Design phase
