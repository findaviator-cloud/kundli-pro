# Kundli Pro / कुण्डली प्रो

A browser-based Vedic astrology (Jyotish) toolkit — no backend, no build step, pure static HTML/CSS/JS.

## What's here

| File | Description |
|---|---|
| `index.html` | Landing page / portal |
| `kundali.html` | Complete Janmpatri (birth chart) generator — ~120-page Parashari-style report: Lagna & Chandra Kundali, Navamsa & other divisional (Shodashvarga) charts, Vimshottari Dasha, Yogas, Doshas, Shadbala, and more |
| `milan.html` | Kundali Milan (Ashtakoot Guna matching) — 36-point marriage compatibility report for two people |

## How it works

Both generators run entirely client-side:

1. Convert the entered birth date/time/timezone to Julian Day (UT).
2. Compute geocentric ecliptic longitudes for the Sun, Moon, and five classical
   planets using low-precision orbital-element formulas (P. Schlyter,
   *"How to compute planetary positions"*; solar equation-of-center after Meeus).
   Accuracy is roughly 1 arcminute for the Sun/outer planets and a few
   arcminutes for the Moon — more than enough to place a planet correctly in
   its sign, house, and nakshatra.
3. Convert tropical longitudes to sidereal using a Lahiri/KP/Raman ayanamsa
   approximation.
4. Compute the Ascendant (Lagna) from Local Sidereal Time, obliquity of the
   ecliptic, and the entered latitude/longitude.
5. Derive sign, house (whole-sign), nakshatra, pada, retrograde motion,
   Vimshottari Dasha balance, and classical dosha rules (Kaal Sarpa, Pitru,
   Shrapit, Grahan, Mangal) from those real positions.
6. `milan.html` reuses the same engine for both partners and runs the
   classical Ashtakoot (Varna/Vashya/Tara/Yoni/Graha Maitri/Gana/Bhakoot/Nadi)
   scoring on the real nakshatra/rashi values.

**Note on scope:** Shadbala numeric tables and the deeper divisional
(varga) charts beyond D-9 use a simplified, chart-derived approximation
rather than full classical point-by-point computation — flagged in code
comments as a follow-up item. Everything else (sign, house, nakshatra,
Lagna, Dasha, and the five dosha checks) is computed from real planetary
positions, not randomized.

## AI Quick-Fill

Both forms have an "⚡ AI Quick-Fill" box at the top: paste a messy
free-text birth detail (Hindi/English/Hinglish, e.g. copied from
WhatsApp) and it auto-fills the individual fields.

This uses a **bring-your-own-key** pattern — there is no backend:

- The browser sends the pasted text directly to
  `https://api.anthropic.com/v1/messages` (Claude Haiku 4.5) using
  the `anthropic-dangerous-direct-browser-access` CORS header, with
  the visitor's own Anthropic API key.
- The key is stored only in that browser's `localStorage`
  (`kp_ai_key`) and is never sent to any Kundli Pro server — there
  isn't one.
- The prompt includes a one-shot example (sample input → expected
  JSON output) so the model returns predictable, parseable fields.

Get a key at [console.anthropic.com](https://console.anthropic.com/settings/keys).
Anyone who can view the page source/devtools on a visitor's own
machine can see a key they typed in — that's an accepted trade-off
of the no-backend design, and it only exposes *that visitor's own*
key, never anyone else's.

## Running locally

No build step — just open the HTML files in a browser, or serve the folder:

```bash
python3 -m http.server 8000
# then visit http://localhost:8000
```

## License

MIT — see [LICENSE](LICENSE).
