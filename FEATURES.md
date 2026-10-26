# ✨ CineDB — Complete Feature Matrix & Capabilities

> A granular technical breakdown of every user-facing feature and background capability in CineDB.

---

## 📑 Feature Index

1. [Discovery & Catalog Browsing](#1-discovery--catalog-browsing)
2. [Indian Regional Cinema Ecosystem](#2-indian-regional-cinema-ecosystem)
3. [Three-Tab Recommendations](#3-three-tab-recommendations)
4. [Media Detail & Metadata Hub](#4-media-detail--metadata-hub)
5. [Streaming Console & Failover Engine](#5-streaming-console--failover-engine)
6. [Original Audio & Subtitle Control](#6-original-audio--subtitle-control)
7. [Watch Experience, History & Sync](#7-watch-experience-history--sync)
8. [Account Management & Authentication](#8-account-management--authentication)
9. [Community Reviews & Ratings](#9-community-reviews--ratings)
10. [UI/UX & Mobile Optimization](#10-uiux--mobile-optimization)
11. [SEO, Performance & Search Visibility](#11-seo-performance--search-visibility)
12. [API Resilience & Deployment](#12-api-resilience--deployment)

---

## 1. Discovery & Catalog Browsing

- **Rotating hero** — auto-advancing featured titles with backdrop, rating, year, synopsis, trailer shortcut, and watchlist toggle.
- **Quick filters** — one-tap chips for All, Telugu, Hindi, Indian, and the major genres, switching the home page into a filtered movie grid.
- **Curated shelves** — horizontal rails covering popular, regional, series, sports documentaries, sports TV, top rated, upcoming, and individual genres.
- **Category feeds:**
  - `Popular` — most viewed titles worldwide
  - `Top Rated` — highest TMDB community ratings
  - `Upcoming` — forthcoming releases
  - `Now Playing` — current cinema releases
  - TV equivalents: `Popular`, `Top Rated`, `Trending`, `On The Air`
- **Genre taxonomy** — 20 genre entries across 19 unique TMDB IDs (History and Sport share ID 36), covering Action, Adventure, Animation, Comedy, Crime, Documentary, Drama, Family, Fantasy, History/Sport, Horror, Music, Mystery, Romance, Science Fiction, TV Movie, Thriller, War, and Western.
- **Sort engine** — most/least popular, highest/lowest rated, newest/oldest, highest/lowest grossing.
- **Listing filters** — release year, minimum rating, genre, sort, and page, all carried in shareable URL parameters; language targeting lives in the dedicated regional hubs.
- **Search** — navbar typeahead plus a paginated `/search?q=` page restricted to movies and TV series.

---

## 2. Indian Regional Cinema Ecosystem

Dedicated landing hubs for the industries the target audience cares about:

| Hub | Route | TMDB filter |
|---|---|---|
| Telugu (Tollywood) | `/category/telugu` | `language=te` |
| Hindi (Bollywood) | `/category/hindi` | `language=hi` |
| Tamil (Kollywood) | `/category/tamil` | `language=ta` |
| Malayalam (Mollywood) | `/category/malayalam` | `language=ml` |
| Pan-Indian | `/category/indian` | `hi\|te\|ta\|ml\|kn` |
| Telugu series | `/category/telugu_series` | TV + `language=te` |
| Hindi series | `/category/hindi_series` | TV + `language=hi` |

These hubs are not cosmetic: the player's routing logic recognises Indian titles and Indian audio choices and starts them on a mirror that carries regional catalogue entries.

---

## 3. Three-Tab Recommendations

Every movie and TV detail page ends with a contextual discovery surface:

1. **🎯 Recommended For You** — TMDB recommendations, re-ranked with a genre-affinity score.
2. **🎬 Similar Titles** — TMDB's similarity graph for the current title.
3. **⭐ Top Rated** — the strongest titles in the same genre space.

- **Carousel and grid views** with tab counts and a view toggle.
- **Live counts** per tab.
- **Watchlist toggle** on each card without leaving the page.

---

## 4. Media Detail & Metadata Hub

- **Cinematic hero** — full-bleed backdrop, poster, tagline, rating, year, and runtime or season count.
- **Trailer modal** — prioritised official YouTube trailers and teasers, with autoplay when opened from the home hero.
- **Where to Watch** — JustWatch/TMDB provider data with a 12-region selector, grouped into Stream, Rent, and Buy columns with direct platform links.
- **Metadata rows** — rating, votes, runtime, release year, and budget/revenue when available; status and original language in the details grid.
- **Cast carousel** — top 12 by default with a "View All" expansion and character names.
- **Crew cards** — director, writers, and producers linked to their person pages.
- **Community section** — rating distribution and full review list.
- **Coming Soon state** — unreleased titles display a release notice instead of a broken player.

---

## 5. Streaming Console & Failover Engine

CineDB does not rely on a single embed. It runs an eight-mirror health-and-rotation loop:

| Mechanism | Value / behaviour |
|---|---|
| **Reachability preflight** | 6s host check after a load timeout, before rotating |
| **Per-mirror load timeout** | 10s |
| **Stuck-stream watchdog** | 15s with no viewer interaction → *"Try next source"* |
| **Rotation** | Silent switch to the next mirror in priority order |
| **Full retry** | Resets all mirrors and starts again |
| **Total-failure dashboard** | Explains the likely carrier block and offers new-tab playback |
| **Status UI** | `Stream Online` / `Switching source…` with an `Auto-Failover` pill |

Additional playback capabilities:

- **Hidden server selection** — mirror names are never exposed; the engine chooses silently.
- **Reload and Popout** — manual reload of the active source, or open it outside the iframe to bypass carrier inspection.
- **TV episode navigator** — season and episode pills with real episode counts from TMDB; a synthetic fallback keeps the picker usable if a season call fails.
- **Theater Mode** — fullscreen, lights-off overlay with a visible exit control.
- **Unreleased guard** — playback and theater mode are disabled until release.

---

## 6. Original Audio & Subtitle Control

### Audio

- **`Original` is the default** and resolves to the title's TMDB `original_language`.
- **VidLink** receives `?selectedLanguage=<lowercase language name>`. Lowercase is required: the player compares track labels case-sensitively against a lowercased value.
- **`?multiLang=true`** is added only when the viewer explicitly chooses a dub (audio ≠ original).
- **Language-aware routing** — Indian audio selections and Indian titles start on the Indian-capable mirror order.
- **Honest feedback** — a toast names the requested track and points to the player's ⚙ → Audio menu when the source lacks it.

### Subtitles

- **English default-on** where the source provides it.
- **17 languages** plus **Source default** and **Off**: English, Hindi, Telugu, Tamil, Malayalam, Kannada, Bengali, Marathi, Spanish, French, German, Portuguese, Arabic, Russian, Japanese, Korean, Chinese.
- **Smart routing** — non-English choices switch to `vidsrc.party`, the only mirror with a working subtitle parameter (`?sub=<iso>`).
- **No pointless reloads** — changing only the subtitle preference does not restart a mirror that cannot apply it.
- **Persistence** — preferences are stored under `cinedb_audio_language` and `cinedb_subtitle_language`.

> Embeds only carry the tracks their catalogues contain. CineDB forces the correct starting track and picks the most capable source; it cannot create a missing dub or subtitle file.

---

## 7. Watch Experience, History & Sync

- **Dedicated watch page** — player, poster side panel, rating, year, runtime or season/episode counts, and top cast.
- **Content tabs** — Overview, Cast, and Reviews below the player.
- **TV history links** — the Recently Watched card links to `/watch/tv/:id?s=<season>&e=<episode>` and displays the recorded position as `S1 E3`.
- **Recently Watched row** — signed-in users see their last titles on the home page with a one-tap return.
- **Real-time watchlist** — Firestore `onSnapshot` keeps every open tab in sync; mutations show toast feedback.
- **Cross-device continuity** — watchlist and history follow the account across desktop and mobile.

---

## 8. Account Management & Authentication

- **Google OAuth** — one-tap popup sign-in.
- **Facebook Login** — one-tap popup sign-in.
- **Email & password** — registration and sign-in with client-side field validation.
- **Mandatory email verification** — unverified accounts are routed to `/verify-email`, polled for confirmation, and can resend the email.
- **Password reset** — Firebase reset link from the login screen.
- **Verified-only writes** — watchlist, history, and reviews are blocked until `email_verified` is true, both in the client and in Firestore rules.
- **Owner-scoped data** — every user document path is bound to the authenticated UID.

---

## 9. Community Reviews & Ratings

- **1–5 star rating** with a descriptive label per score.
- **One review per user per title** — the document ID is the user's UID, enforced by rules.
- **Full lifecycle** — create, edit, and delete your own review.
- **Rating distribution** — live bar chart across 1–5 stars.
- **Likes** — one like per user, tracked with `arrayUnion` / `arrayRemove`.
- **Replies** — threaded responses attached to each review.
- **Review length limits** — 100-character title and 2,000-character body keep the UI predictable.

---

## 10. UI/UX & Mobile Optimization

- **Hand-written CSS** — no UI framework; a token-driven palette of gold (`#fbbf24`) on deep black (`#000`–`#161616`).
- **OLED-first dark theme** — high contrast, low-light surfaces, subtle gold borders.
- **Glassmorphic surfaces** — blurred, translucent panels with restrained shadows.
- **Native-feel mobile navigation** — bottom bar for Home, Explore, Search, Watchlist, and Account.
- **Responsive shelves** — touch scrolling on mobile, hover arrows on desktop.
- **Toast engine** — consistent feedback for auth, watchlist, reviews, and player events.
- **Accessible controls** — labelled icon buttons, `role="tablist"` navigation, and explicit ARIA roles on player status surfaces.

---

## 11. SEO, Performance & Search Visibility

- **Prerendered HTML shells** — `dist/movie/<id>.html`, `dist/tv/<id>.html`, and 18 category pages with unique title, description, canonical, Open Graph, Twitter, and JSON-LD.
- **Runtime head manager** — `CineDBSEOManager` updates meta on SPA navigation and restores baseline tags on unmount.
- **Structured data** — `WebSite` + `SearchAction`, `Organization`, `SiteNavigationElement`, `Movie`/`TVSeries`, `Person`, `CollectionPage`, and `BreadcrumbList`.
- **Canonical discipline** — `/movie/:id` and `/tv/:id` are the indexable entities; `/series/:id` resolves to the same canonical TV page.
- **Crawl hygiene** — `/watch/*`, `/search`, auth screens, `/profile`, and `/watchlist` are `noindex`; faceted listing URLs canonicalise away from hubs.
- **Sitemap** — generated after every production build at `dist/sitemap.xml` (roughly 360 URLs, catalog-dependent).
- **Performance** — lazy routes, three stable vendor chunks, immutable asset caching, responsive TMDB images, and font preconnects.

---

## 12. API Resilience & Deployment

- **Same-origin TMDB proxy** — the browser calls `/api/tmdb/*` by default; Vercel rewrites it to `https://api.themoviedb.org/3/*`, bypassing Indian ISP DNS poisoning of the TMDB host.
- **Automatic endpoint fallback** — a proxy failure (network error, 404, or 5xx) retries against TMDB directly; a blocked direct endpoint falls back to the proxy.
- **Retry policy** — 8s request timeout, two exponential-backoff retries (1s, 2s).
- **Local DNS hardening** — the Vite dev proxy and sitemap script resolve TMDB through Google/Cloudflare DNS (`8.8.8.8`, `1.1.1.1`).
- **Edge configuration** — apex→www 308 redirect, `cleanUrls`, SPA rewrites, asset caching, and route-level `X-Robots-Tag` headers.
- **Analytics** — first-party Vercel page-view measurement, initialised before React mounts.

---

<div align="center">
  <p><strong>CineDB Feature Documentation © 2026 Makoju Suman Kumar</strong></p>
</div>
