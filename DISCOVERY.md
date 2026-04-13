# Discovery & Planning — Mishona PdM Checklist App

## Problem statement
Mishona PdM candidates and new hires have no structured tool to track
the product development lifecycle for L Message (LINE automation SaaS).
Tasks, QA gates, and cross-team handoffs are managed ad-hoc, causing
missed steps and inconsistent quality across releases.

## Target users
| User | Goal |
|------|------|
| PdM candidate (Take) | Demonstrate structured PdM thinking for Mishona hiring test |
| Onboarded PdM | Track real sprint progress across dev / design / CS teams |
| Team lead | Verify QA gates are cleared before release |

## Core user pain points
1. No single view of what phase the product is in
2. QA steps are skipped under deadline pressure
3. New PdMs don't know what "done" looks like for each phase
4. No record of what was checked before each release

## Solution
A single-file web app (index.html) that:
- Shows all 6 development phases with expandable task checklists
- Marks QA gates visually (must-pass before proceeding)
- Tracks overall progress with a live summary bar
- Persists state in localStorage so progress is not lost on refresh
- Works on desktop browser — no install needed

## Feature list (MVP)
| # | Feature | Priority |
|---|---------|----------|
| 1 | 6 phase accordion checklist | Must have |
| 2 | QA gate badges (red = blocking) | Must have |
| 3 | Progress bar per phase + overall | Must have |
| 4 | Mark done / reset all | Must have |
| 5 | localStorage persistence | Must have |
| 6 | Dark mode support | Must have |
| 7 | Copy-paste config file generator | Nice to have |
| 8 | Export progress as markdown report | Nice to have |

## Out of scope (MVP)
- User accounts / login
- Backend / database
- Multi-user collaboration
- Mobile app

## Tech decisions
| Decision | Choice | Reason |
|----------|--------|--------|
| Framework | None (vanilla JS) | No build tools needed, single file |
| Styling | CSS variables | Dark mode, theming, no dependencies |
| Storage | localStorage | Free, no backend, persists on refresh |
| AI integration | Anthropic API (optional) | AI phase suggestions — post-MVP |
| File count | 1 (index.html) | Easy to share, open, and submit |

## Success metrics
- All 6 phases render with correct tasks and QA gate markers
- Checkboxes persist after browser refresh (localStorage)
- Progress % updates in real time
- Works in Chrome on Windows without WSL2 or Node

## Competitive reference
- Mishona's own hiring test (LINE Official Account demo)
- Notion / Linear sprint boards (inspiration for phase structure)
- The PdM checklist Claude built in this conversation (direct source)

## Phase breakdown
| Phase | Tasks | QA Gates |
|-------|-------|----------|
| 1. Discovery & planning | 6 | 1 |
| 2. Design & UX | 7 | 3 |
| 3. Development | 7 | 2 |
| 4. Quality assurance | 8 | 1 |
| 5. Release & go-to-market | 7 | 1 |
| 6. Post-release review | 5 | 1 |
| **Total** | **40** | **9** |

## Timeline (estimate)
| Milestone | Time |
|-----------|------|
| Scaffold + config files | Done |
| index.html base structure | 30 min |
| Phase 1–3 checklist logic | 45 min |
| Phase 4–6 + QA gates | 30 min |
| localStorage + progress bar | 20 min |
| Polish + dark mode | 20 min |
| Final test + commit | 15 min |
| **Total** | **~3 hours** |

## PRD sign-off checklist
- [ ] Problem statement agreed
- [ ] Feature list prioritized
- [ ] Tech stack decided
- [ ] Success metrics defined
- [ ] Timeline realistic
- [ ] QA gate — PRD reviewed and signed off
