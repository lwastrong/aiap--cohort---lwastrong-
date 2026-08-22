# CLAUDE.md — FreeWheelers Project Context

Read this first. It contains everything a new session needs to work on this project.

## Who you're working with

Art Thomas — Founder/CEO of LWA (Living With Ataxia), lwastrong.com. Has Acquired Ataxia on top of hereditary Ataxia; went wheelchair → complete independence using his own method. Claude persona: "Alley." Tone: brutally honest, concise, lead with the answer, no corporate speak. Art is non-technical: explain in simple words, never ask him to run terminal commands, show drafts before anything is sent/published, explain before installing software, never ask for passwords in chat.

This project is Art's entry in the **AI Accelerator Program hackathon** (built with a team; Art owns the repo and the main branch).

## The product

**FreeWheelers** — "Access Included. Adventure Guaranteed."
A travel-planning web app for people with disabilities (all neurological disorders and beyond). Type any destination or US zip code → get hotels (Stay), restaurants (Eat), and activities (Do) nearby with real accessibility data, ratings, and booking handoffs. Worldwide search with US priority. Originally named "AccessAbility by LWA"; renamed FreeWheelers per the team project brief. LWA is credited as inspiration ("Inspired by Art Thomas & LWA Strong").

**Differentiators (why it beats AccessNow / Wheelmap / accessibleGO / Wheel the World / Google Accessible Places):**
1. FreeWheelers Verified checklist — a real 10-point access standard (from Art's LWA mobility-checklist), shown on the landing page; roadmap = paid on-site venue certification.
2. The health layer nobody else has: LWA nutrition rules baked into restaurant results (carnivore/keto-first "feel-good fuel").
3. Art's story — wheelchair to independence — is the brand.

## Live deployments

- **GitHub Pages (THE official home — team decision):** https://lwastrong.github.io/aiap--cohort---lwastrong-/ (auto-updates on every push to main)
- Netlify (https://freewheelers-lwa.netlify.app) exists but is NOT used by the team — don't bother redeploying it unless Art asks; it goes stale by design. Note: Netlify blocks curl (401 bot protection) — verify it in a real browser only.
- **Repo:** https://github.com/lwastrong/aiap--cohort---lwastrong- (public, branch `main`, owner account `lwastrong`)
- Downloaded/local copies of the HTML do NOT work (Google key is website-locked). The app only works at the two URLs above and http://localhost:8765 for testing.

## Tech stack (deliberately simple)

- **One self-contained HTML file** — no build system, no framework, no accounts, vanilla JS + CSS variables. File is ~1,000 lines: styles → HTML → one IIFE script.
- **Geocoding:** OpenStreetMap Nominatim (free, no key). Worldwide with US priority: fetch top 5 matches, prefer the first one whose display_name contains "United States", else take global #1 (so "Birmingham" → Alabama, but "Tokyo, Japan" → Japan).
- **Places data:** Google Places API (New), REST via fetch:
  - Hotels: searchNearby, includedTypes ["lodging"], max 20
  - Restaurants: searchText "restaurants" with locationBias circle, paged ×3 → up to 60 (results trimmed to radius client-side)
  - Activities: 2× searchNearby with type groups (culture: tourist_attraction, museum, art_gallery, movie_theater, performing_arts_theater, library, community_center, casino; active: park, zoo, aquarium, amusement_park, national_park, hiking_area, gym, golf_course, bowling_alley)
  - FieldMask includes accessibilityOptions, goodForChildren, rating, userRatingCount, etc.
- **Google Cloud:** Art's account **lwataxia@gmail.com** (authuser=1 in his Chrome), project "My First Project" (project-6bd065bf-…), $300/90-day trial credit active. Key name "Maps Platform API Key", restricted to APIs [Cloud SQL (leftover, harmless), Places API (New)] and websites [http://localhost:8765/*, https://lwastrong.github.io/*, https://freewheelers-lwa.netlify.app/*]. The key string sits in the HTML (const GOOGLE_API_KEY) — safe because website-locked. Any new hosting domain must be added to the key's website restrictions in console.cloud.google.com → Credentials, or the app shows "Google Maps couldn't load places."
- **Netlify:** Art's account (lwataxia@gmail.com, team "LWAstrong"), site `freewheelers-lwa`, site id 2c31afd2-fcfa-453d-8387-883b2870a5e4. CLI used via `npx -y netlify-cli` (not globally installed — global npm needs admin password).

## File structure

Repo root (= deploy root for both hosts):
- `index.html` — MARKETING LANDING PAGE (authored by teammate Geoff, merged from branch `Geoff`; own design: Bricolage Grotesque/Inter fonts, green-amber palette — intentionally NOT the app's gold/teal). Contents: hero with WORKING search (city/zip + radius + Use-my-location → redirects to `app.html?q=…&r=…` or `?loc=1&r=…`); "Feel-good fuel first" toggle (reworded from Geoff's softer "protein-forward/optional" wording to LWA stance, per Art); six SAMPLE venue cards (fake places, labeled "Sample places shown for illustration" with link to real app) with Unsplash stock photos; How-it-works / What-we-verify / For-venues sections; community demo. Its 3 CTA buttons link to `app.html`.
- `app.html` — THE APP (identical copy: `FreeWheelers-App.html`). Direct URL: /app.html on either host. Reads URL params on load: `?q=<text|zip>&r=<1600|3200|8000|16000>` auto-runs a search; `?loc=1` triggers geolocation.
- `README.md` — project page: pitch, Verified checklist, features, data honesty, roadmap
- `LWA-AccessAbility-Hackathon-Workbook-2026-08-20.docx` — completed Phase 1 workbook

Local master copy on Art's Mac (Claude session outputs folder): `LWA-AccessAbility-App-2026-08-20.html` — edit HERE, then copy to repo (see workflow). Also in outputs: the workbook, `LWA-AccessAbility-PRD-CompetitorScan-2026-08-21.docx`, README.md, this CLAUDE.md.
Repo clone on Art's Mac: `/tmp/aiap-repo` (git identity set repo-locally: Art Thomas / art@lwastrong.com; gh CLI authenticated + `gh auth setup-git` done).
Google Drive folder "Lwa app" (id 1Ef8ik295tVTXwYEhiDm8-LMWkeQfePeP): holds OLD pre-rebrand copies — outdated, non-functional (key lock); pending cleanup.

## App feature inventory (all built & tested)

- Search: destination text + "Search for zip code" (US 5-digit) + radius 1/2/5/10 mi + "Use my location". Button label: "Search". Either field works; both = more precise.
- Tabs: 🏨 Stay · 🍽️ Eat · 🎯 Do, with counts. Filters: "Access verified only", "Kid-friendly only". Keyword search across ALL categories when non-empty (display cap 300).
- Badges (two-tier): green **✓ FreeWheelers Approved** = wheelchairAccessibleEntrance AND (restroom for stay/do | feel-good-fuel menu for eat); yellow **✓ Access Verified** = entrance only; amber partial / red no / grey "Not Verified — call ahead". Legend links to #verified.
- **LWA food rules (Eat):** BAD_TYPES_RE filters out fast food/pizza/fried/dessert/sandwich/noodle/BBQ etc. — checked against Google types AND the restaurant NAME. GOOD_CUISINE_RE (steak/grill/seafood/burger/korean/mediterranean/american/breakfast…) earns lwaEats. **Chick-fil-A hard-blocked by name (BLOCKED_NAME_RE) — never shown; framed as "doesn't meet feel-good fuel standards" (NOT as a medical claim).** Vegetarian/vegan restaurants stay listed but carry a red "⛔ Critical — Not Recommended" box (oxalates→kidney stones stated; "inflammatory" framed as belief w/ Chaffee citation) and sort last. Burger spots get on-card tip: "order the patties only — skip the bun."
- Card actions: Directions (Google Maps) · Website · 📞 Call Now (tel:) · 🍽️ Book Reservation (eat → OpenTable deep link `opentable.com/s?term=NAME&latitude=&longitude=&covers=2` — pre-fills!) · 🌻 Seed Oil Check (eat → seedoilscout.com) · 🏨 Book Now (hotels → Booking.com deep link `booking.com/searchresults.html?ss=NAME+CITY` — lands on exact hotel; Trivago/Kayak were tested and rejected: no free-text deep links).
- Cards show ⭐ rating (count), accessible parking/seating chips, kid-friendly flag (goodForChildren or family place types minus bars/casinos).
- **Photos on every card** (places.photos in FieldMask; media URL `places.googleapis.com/v1/{photo.name}/media?maxWidthPx=520&key=…`): first 6 eager, rest lazy (quota protection); missing photo = card renders without it (onerror removes wrapper). **Photos are CLICKABLE** (`a.cphoto-link` wrapping `img.cphoto`) → open that exact venue on Google Maps (`google.com/maps/search/?api=1&query=NAME&query_place_id=ID` — placeId stored per place) in a new tab; **hover/focus magnifies the photo** (scale 1.07 inside overflow-hidden wrapper) as the clickability cue.
- **Legend badges are clickable FILTERS** ("Legend (click to filter)"): clicking a badge shows only that access level (lwa / yes / limited / no / unknown); clicking again clears; active badge gets a dark outline ring; `legendFilter` state var in render(). "what does this mean?" still anchors to #verified.
- **✅ FreeWheelers Verified section** (#verified, landing page): 10-point checklist in 4 groups (Getting in / Getting around / Staying over / The human part), Art's story, honest "badge today vs. where we're headed," venue CTA mailto art@lwastrong.com.
- **📸 Community Corner:** no-account posts with up to 4 canvas-compressed photos (localStorage key `lwa_community_posts`), per-post 👍 likes (toggle) and 💬 comments, delete button. Name placeholder: "e.g. John Doe from the United States". LIMITATION: localStorage = per-device only; real sharing needs a backend (roadmap). No videos (size).
- UX: auto-scroll to loading/errors/results (they sit below the Verified section — without scroll, feedback is invisible → users think buttons are dead). Errors are friendly and specific, incl. missing-key and key-restriction messages.
- Theme: matched to lwastrong.com — gold #C8900A (--gold), teal #00AEAE (--teal), white bg, near-black text, gold-tinted panels #FAF4E6. All theming via CSS variables in :root.

## Workflows (established, use these)

**Edit → test → ship:**
1. Edit the outputs master `LWA-AccessAbility-App-2026-08-20.html` (Read before Edit).
2. Test on Art's Mac: `osascript do shell script` → `cd <outputs>; python3 -m http.server 8765 &` → drive http://localhost:8765/… with claude-in-chrome (tabs_context_mcp → navigate → javascript_tool; add ?v=N to bust cache). Kill with `pkill -f 'http.server 8765'`. (Sandbox bash CANNOT reach Google/OSM — allowlist-blocked; never test there.)
3. Ship: `cd /tmp/aiap-repo && git pull && cp master → app.html && cp app.html FreeWheelers-App.html && git add -A && git commit && git push origin main` (via osascript). Pages redeploys itself (~1 min). NOTE: `index.html` is Geoff's landing page — do NOT overwrite it with the app.
4. Netlify redeploy: `npx -y netlify-cli deploy --prod --dir . --site 2c31afd2-fcfa-453d-8387-883b2870a5e4` from /tmp/aiap-repo.
5. Verify on the live URL(s) before reporting done. Google console changes propagate in ≤5 min.

**Art's standing rules for Claude:** test before shipping; verify prices/names/links; flag unverifiable medical claims loudly and rephrase as LWA belief/opinion with approved sources (Chaffee, Baker, Berry, Saladino, etc.); never present Chick-fil-A/veg warnings as medical fact about named parties; files as real deliverables in outputs + present_files; LWA packages/nutrition facts come from his global instructions — never invent.

## Team & Git state

Collaborators work on branches: `Geoff` (landing page — merged into main with authorship credit), `Mohammads-Branch`, `Otis-Branch`, `landing-page` (was empty). All four branches were synced to equal main after the merge (a stale `Geoff` branch would have re-overwritten the app — that hazard is resolved, but future stale-branch merges remain the #1 repo risk). No PRs have ever actually been opened — a teammate once pushed a branch and *believed* they'd opened one. When a real PR arrives: review "Files changed" (especially anything touching index.html/app.html) before Art approves; Art may paste PR links here for review. Teammates were advised to sync/update their branch before starting new work. Merging to main = instant live deploy on Pages.

## Known issues / quirks

- Google `primaryTypeDisplayName` is occasionally nonsense (e.g. a concert theater labeled "Manufacturer"). Cosmetic; offered to filter, not yet done.
- Netlify snapshot goes stale vs. Pages after every push until redeployed.
- Two leftover Community Corner posts on Art's Chrome (Rose Garden DC, Omni Shoreham) — his own device data.
- Drive folder copies outdated (pending: replace with "open the app here" doc).
- Eat results max ~60 (Google Text Search pagination limit); OSM gave 250+ but worse quality/no ratings — accepted trade-off.

## Roadmap (documented in README, not built)

Shared backend for Community Corner (real cross-device posts/likes/comments, videos) · full on-site FreeWheelers Verified program (venue-paid certification = revenue) · per-trip access profiles · group trip builder + printable itineraries · booking affiliate revenue (Booking.com/OpenTable programs) · AI verification agent · Netlify↔GitHub auto-deploy hookup.

## Commit history landmarks

Initial → app+workbook → FreeWheelers rebrand → Seed Oil Scout → carnivore/Chick-fil-A → veg warnings → Google Places migration + Pages live → lwastrong.com palette → Booking/OpenTable/Call buttons → zip field → "Search" label → worldwide+US priority → John Doe placeholder → likes+comments → Verified checklist → auto-scroll fix → Netlify deploy → CLAUDE.md → Geoff landing page merged as front door (app→app.html) → branch sync/cleanup → landing sample-card photos + hero search wired to app → "Feel-good fuel first" toggle rewording → sample-cards illustration label → result-card photos → clickable photos + hover zoom → Pages declared official/Netlify demoted → legend badges = clickable filters.

## Stale-tab support pattern

When Art reports "it's not working / nothing happens / no change": (1) his open tab usually predates the update — first answer is hard refresh (Cmd+Shift+R) or check WHICH page he's on (landing vs app, downloaded file vs live site); (2) verify the live site yourself in the browser before touching code; (3) remember downloaded copies never work (key lock) and Netlify serves 401 to curl but fine to browsers. This pattern has recurred multiple times.
