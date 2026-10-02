# Nonprofit Web Tools: Dark to Light Theme Plan

Status: DRAFT for review (2026-10-02). Nothing has been changed or pushed yet.

## 1. Where we are

The hub (`nonprofittools.org`) was already restyled to brand kit 0.2 (white page, system font, richBlack headings, huduPrimary links, inline SVG icons). Most embedded tools were not, so the iframe content is still the old dark look (richBlack page, charcoal cards, white text, Font Awesome, Google Fonts). The goal is to bring every tool to the same light brand kit look as the hub.

Reference implementation: `Broken-Link-Checker` (already light, uses `--ght-palette-*` tokens plus semantic aliases `--bg --text --head --link --line --wash` and light status colors). Copy its token block, not the dark tools' old variable names.

## 2. Inventory

All repos are in the `Good-Heart-Tech` GitHub org and cloned under `C:\Users\Rambo\GitHub`. All default to `main`.

### A. Our code, dark theme, needs conversion

| Nav item | Subdomain | Repo (visibility) | Stack | Size | Dark surface | Effort |
|---|---|---|---|---|---|---|
| Password Generator | password.nonprofittools.org | Kind-Pass (public) | static HTML/CSS/JS | ~23 KB | styles.css vars | S |
| Image to Text (OCR) | ocr.nonprofittools.org | Image-to-Text (public) | static, Tesseract.js | ~15 KB | styles.css vars | S |
| Wallpaper Generator | wallpaper.nonprofittools.org | Wallpaper-Generator (private) | static, canvas | ~15 KB | styles.css vars | S |
| Text Manipulator | text.nonprofittools.org | Text-Manipulator (public) | static | ~41 KB | styles.css + inline, partly light already | S |
| QR Code Generator | qr.nonprofittools.org | QR-Code-Generator (public) | single index.html, inline CSS, lib/ | ~19 KB | inline `<style>` with hardcoded hex | S |
| Media Converter | convert.nonprofittools.org | Media-Converter (public) | index.html with inline CSS, script.js | ~44 KB | inline `<style>` vars + hardcoded status colors | M |
| Policy Generator | policy.nonprofittools.org | Nonprofit-Policy-Generator (public) | 5 sub-apps (privacy, ai-use, acceptable-use, mobile-device, disclaimer) sharing `shared/styles.css` | ~150 KB | shared dark palette (#121218, #1e2029) plus per-page `<style>` | L |
| Snippet Sharing | snip.nonprofittools.org | Snippet-Share (private) | Cloudflare Worker, HTML inlined in `src/worker.ts` | ~14 KB | inline in worker.ts (#091D20, #181f2a, #222) | M |
| Domain Analyzer (candidate) | not wired yet | DNS-Health-Check (private) | static, large script.js (70 KB) | ~118 KB | styles.css vars | M |

### B. Our code, already light (verify only)

| Nav item | Subdomain | Repo | Note |
|---|---|---|---|
| Broken Link Checker | links.nonprofittools.org | Broken-Link-Checker (private) | Python app plus static/. Reference for tokens. Just verify in iframe. |
| Hub shell | nonprofittools.org | Nonprofit-Tools-Hub (public) | Already done. Needs iframe loading polish only (section 5). |
| Nonprofit Tech Navigator | learn.goodhearttech.org | Nonprofit-Tech-Navigator (public) | GitBook, opens in new tab. Theme is a GitBook setting, not code. |

### C. Third-party software we host or link (no repo of ours, config only)

| Nav item | URL | What it is | Dark today | Action |
|---|---|---|---|---|
| Internet Speed Test | speed.goodheart.tech | OpenSpeedTest (self-hosted, Cloudflare) | Has `prefers-color-scheme: dark` CSS | Find where it is hosted (no repo in org). Likely needs a small CSS override or custom skin. Decide if worth it (see Open Questions). |
| Website Check | webcheck.goodheart.tech | Lissy93 Web Check (Express behind Caddy) | `data-theme="dark"` | Check its theme setting or env var first. May need a custom CSS file mounted into the container. |
| Password Pusher | push.goodheart.tech | Password Pusher (OSS) | Auto by OS (`prefers-color-scheme`) | Opens in new tab. Set default theme to light in its config if available. |
| PDF Editor | pdf.nonprofittools.org | Stirling PDF | Not detected | Opens in new tab. Set UI theme to light via Stirling settings. |
| Domain Analyzer | dnschecking.org | Third-party public site | Not ours | Cannot be themed. Plan to replace with DNS-Health-Check after it is restyled (Phase 2). |

### D. Retired / redirect stubs (no theme work)

- Nonprofit-AI-Use-Policy-Generator and Nonprofit-Privacy-Policy-Generator both redirect to `policy.nonprofittools.org`. Recommend archiving on GitHub after confirming the redirects still work.

## 3. Phase 1: Light theme migration

### 3.1 Rules (copied from the hub's AGENTS.md so every repo follows the same contract)

- Tokens only. Copy `assets/ght-variables.css` from the hub (generated from the brand kit) into each repo and use `var(--ght-palette-*)`. No new hex values, no new fonts.
- Page background white. Headings richBlack. Body text charcoal. Small links huduPrimary. Borders and card washes huduLight. Buttons 8px radius.
- Primary buttons: `primary` fill with white, semibold, large label only (3.12:1). Hover to huduPrimary.
- Never use lightBlue, huduLight, or neutral (#808080) as text on white. Muted helper text should be charcoal (maybe at a smaller weight), not gray.
- Dark surfaces are allowed only for the footer (richBlack with lightBlue text) and plates behind white logos.
- System UI font stack. Remove Google Fonts and Font Awesome. Replace icons with a small inline SVG sprite (same approach as the hub).
- Status colors: reuse Broken-Link-Checker's light set (danger #9B1C1C on #FDECEC, caution #764700 on #FFF4D9, ok #17653A on #E6F5EB). Do not keep the old neon `#ff6b6b` / `#6bff6b`.
- Logo: use `goodhearttech-logo.png` (colored on light) from the hub assets. Do not recolor.
- No em dashes in any text.
- Do not rely on color alone for state (add icon, bar, or weight).

### 3.2 Standard recipe per repo

1. Branch-free workflow per your instruction: work on `main`, one commit per repo, push when verified. (Safety net: each commit is self-contained so `git revert` is a one-step rollback, and Cloudflare Pages also keeps a rollback to the previous deployment.)
2. Add `assets/ght-variables.css` (or `ght-variables.css` at root for flat repos) and link it first in `<head>`.
3. Replace the repo's own `:root` palette with semantic aliases (`--bg --text --head --link --line --wash`) mapped to `--ght-palette-*`.
4. Swap body/background/card/input/button/border colors. Convert every hardcoded hex in inline `<style>` blocks and in JS-generated markup (canvas, toasts, status messages).
5. Remove Google Fonts and Font Awesome links, add inline SVG sprite for the icons actually used.
6. Add visible focus rings (2px huduPrimary outline) and make sure inputs have labels.
7. Add a dark richBlack footer with lightBlue text, matching the hub, with Donate and GitHub links.
8. Add `<meta name="color-scheme" content="light">` so browser form controls and scrollbars render light even if the user's OS is dark.
9. Verify (section 3.4) then commit and push.

### 3.3 Order of work (smallest first, lowest risk, so the recipe is proven before the big repos)

| Step | Repo | Why this slot |
|---|---|---|
| 0 | Hub | Add a short "light theme" note to AGENTS.md and README; no visual change. Optional: add a neutral white loading background on the iframe so there is no dark flash while tools load. |
| 1 | Kind-Pass | Smallest, pure CSS vars. Pilot to prove the recipe and token copy. |
| 2 | Image-to-Text | Small, similar structure. |
| 3 | Wallpaper-Generator | Small; check canvas preview and default colors still make sense on white. |
| 4 | Text-Manipulator | Already partly light; clean up the mixed styles. |
| 5 | QR-Code-Generator | Inline CSS with many hardcoded hex values and comments. Make sure the QR code itself stays high contrast (black on white). |
| 6 | Media-Converter | Inline CSS plus JS-set status colors. Includes progress bars and file lists. |
| 7 | Snippet-Share | Worker-served HTML. Needs `wrangler` deploy, not just a push. Confirm how it deploys before touching. |
| 8 | Nonprofit-Policy-Generator | Largest. Do `shared/styles.css` first, then each sub-app. Printable/downloadable output (PDF/Word text) must stay unchanged. |
| 9 | DNS-Health-Check | Restyle so it is ready for Phase 2 swap into the hub. |
| 10 | Third-party tools | Speed test, Web Check, Password Pusher, Stirling PDF theme settings (section 2C). |
| 11 | Broken-Link-Checker + all | Final verification pass across the live hub in a browser at desktop and phone widths. |

### 3.4 Verification checklist (every repo, before push)

- Open the page locally as a static file (no dev server needed for static repos) at 1280 px and 375 px widths.
- Screenshot before and after; keep them in the commit message or PR notes.
- Run through the main flow of the tool with real input (convert a file, OCR an image, generate a QR, generate a policy, etc.) so JS-generated UI is also checked.
- Contrast: every text and background pair must come from the approved pairs list in the brand UI brief.
- Keyboard: tab through the page, focus ring visible on every control.
- OS dark mode on: page still renders light (color-scheme meta works).
- Grep for leftovers: `#091D20`, `#394053`, `#121218`, `#1e2029`, `#181f2a`, `rgba(255, 255, 255`, `fonts.googleapis`, `font-awesome`.
- Embedded check: load via the hub (`?tool=<slug>`) so the iframe and sandbox attributes are exercised on the deployed URL after push.

### 3.5 Hub changes needed (small)

- Hub `sandbox` includes `allow-same-origin allow-scripts` and each tool is a separate origin, so no theme coupling is required. No change needed.
- Update the welcome screen / llms.txt wording if any copy mentions "dark".
- Fix the GitHub links (see Phase 2 hygiene).

### 3.6 Risks

- Direct to main with no PR review: a bad CSS change goes live in about a minute. Mitigation: pilot on Kind-Pass first, verify locally, push one repo at a time, check the live hub after each.
- Snippet-Share and the third-party tools deploy differently from "push to main". Confirm each deploy path before step 7 and 10.
- Policy Generator has five apps sharing CSS, so a shared change affects all of them. Test each sub-app.
- Public repos expose before/after history; no sensitive content involved.

## 4. Phase 2: Long-hanging fruit (do after Phase 1, repo by repo)

### Cross-cutting (all tool repos)

- **SEO basics are missing almost everywhere.** Only QR and DNS-Health-Check have a meta description; only DNS-Health-Check has canonical, JSON-LD, sitemap, robots, and llms.txt. Add to each tool: unique `<title>` and meta description, canonical, Open Graph and Twitter tags, `SoftwareApplication` (or `WebApplication`) JSON-LD with `isAccessibilityFeature`/`offers: free`, `robots.txt`, `sitemap.xml`, `llms.txt`.
- **Embedding SEO trap.** Tools are indexed on their own subdomains but promoted inside an iframe. Add a canonical on each tool pointing to itself and keep a "Open full page" link visible when the tool detects it is framed, or point canonicals to the hub deep link (`?tool=slug`) only if you want the hub to rank. Pick one strategy (see Open Questions).
- **Privacy and speed.** Removing Google Fonts and Font Awesome (already in Phase 1) means zero third-party calls on most tools, which supports the "all processing happens in your browser" message. Pin and self-host any remaining CDN libs (Tesseract, QR, etc.) with Subresource Integrity hashes.
- **AI-readiness.** Add a short `AGENTS.md` to every tool repo (stack, file map, do and don't, how to deploy, test checklist) modeled on the hub's. Add `README` local-run steps and a one-line purpose at the top.
- **Licensing consistency.** The hub was standardized on AGPL-3.0 but most tool repos are still GPL-3.0 (the Policy Generator is AGPL). Hub README still says GPL v3. Decide on one license and update LICENSE and README in every repo.
- **GitHub links.** Hub (`index.html`, `llms.txt`) and some tools link to `Good-Heart-Technology/...` instead of `Good-Heart-Tech/...`. Fix or confirm the old org redirects.
- **Shared brand footer.** Every tool repeats a footer. Define it once as a copy-paste snippet in the brand kit and paste into each repo to keep them identical.
- **Accessibility.** Add `aria-live` to result and status regions, associate labels with inputs, `aria-pressed`/`aria-selected` on tab buttons, and a skip link. The org already publishes an Accessibility Widget; these tools should model the standard.
- **Error handling.** Replace `alert()` calls (Text-Manipulator 7, DNS-Health-Check 2, plus the two redirected policy stubs) with inline accessible messages.
- **Analytics and errors.** The hub uses Sentry. Decide whether tools should report errors the same way (needs your approval per the hub rules) or stay fully silent.

### Per project

| Repo | Quick wins |
|---|---|
| **Hub** | Add loading indicator and a "Open in new tab" link for each embedded tool. Add a search/filter box in the sidebar. Per-tool landing pages (static `/tools/<slug>/`) with real text so each tool is indexable, not just `?tool=`. Fix license text in README. |
| **Kind-Pass** | Add a "strength / entropy" display, allow length and word-count options, show passphrase in a monospace readable font, copy button announces success to screen readers. Split `script.js` (13 KB) word list into `words.js` for easy updates. |
| **Image-to-Text** | Language selector (Tesseract supports many), drag and drop, show progress bar, download as .txt, remember nothing (privacy). Self-host Tesseract worker and language data. |
| **Wallpaper-Generator** | Presets using brand-safe palettes, size presets (phone, desktop, Teams background), live preview, accessible color pickers with text input. Add "Download PNG" name and format options. |
| **Text-Manipulator** | Group tools into clear categories, add undo/redo, character/word counts, keyboard shortcuts documented, remove `alert()` calls. Consider splitting `script.js` by feature in plain files (no bundler). |
| **QR-Code-Generator** | Add more types (email, phone, SMS, vCard), size and error-correction options, downloadable SVG plus PNG, and a "QR contrast" guard that blocks unreadable color choices. Move inline CSS to a `styles.css` file. |
| **Media-Converter** | Move inline CSS to `styles.css`, split `script.js` (28 KB) by converter type, clear per-file errors, size limit message up front, batch download as ZIP. |
| **Snippet-Share** | Move HTML out of `worker.ts` into static files (or a small template module) so design changes do not require editing TypeScript strings. Add expiry presets, burn-after-read option, rate limiting, and README with deploy steps. |
| **Policy Generator** | Remove the per-page copy of CSS (use only `shared/styles.css`), archive the two redirected repos, move JS to `shared/`, add a template review disclaimer, one-click "email to my board" copy, and consistent output formats. Add `sitemap.xml` entries for each policy page since each is a high-intent search page ("nonprofit privacy policy template"). |
| **DNS-Health-Check** | After restyle, replace `dnschecking.org` in the hub nav (it is a third-party site we cannot theme or control). Split the 70 KB `script.js` into check modules, add plain-language fix instructions per finding for non-technical staff, and an exportable summary PDF. Already has strong SEO files; add AGENTS.md. |
| **Broken-Link-Checker** | Already light. Add rate-limit and abuse notes in README, confirm scan history is not stored, add sitemap/robots/llms.txt. |
| **Speed test / Web Check / Password Pusher / Stirling** | Document the exact hosting and config in `Private-Internal-GitBook` (no repo exists), pin versions, and add a monthly update reminder. |
| **Nonprofit-Tech-Navigator** | Link each tool guide to the matching hub tool page; keep `web-based-tools-for-nonprofits.md` in sync with the hub nav. |

## 5. Suggested rollout schedule

1. Approve this plan and the open questions.
2. Pilot: Kind-Pass (Phase 1 step 1). Review live in the hub.
3. Remaining Phase 1 steps in order; one repo per commit, check live hub after each push.
4. Third-party tool theming.
5. Final pass: full hub walkthrough on desktop and phone, update hub copy and AGENTS.md.
6. Phase 2 starts with cross-cutting SEO and AGENTS.md (cheap, low risk), then per-project items by usage.

## 6. Open questions for Greg

1. **Third-party tools** (speed test, Web Check, Password Pusher, Stirling PDF): theme them now, or leave them as-is since we do not own the code? Speed test and Web Check are the two that sit inside the hub iframe.
2. **Domain Analyzer:** keep `dnschecking.org` for now, or switch the hub to our own DNS-Health-Check as soon as it is light?
3. **Private repos** (Wallpaper-Generator, Snippet-Share, DNS-Health-Check, Broken-Link-Checker): any reason they are private? SEO, trust, and the "open source tools" story would benefit from making them public after cleanup.
4. **SEO strategy for iframes:** should the hub or each tool subdomain be the canonical version for search?
5. **Dark mode later?** If you might want a user-selectable dark mode in the future, we can keep the semantic aliases (`--bg --text ...`) so a dark override is a single block. This plan assumes light only.
6. **Where does Snippet-Share deploy from** (wrangler locally or Cloudflare Git integration)?
