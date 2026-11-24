# ☀️ SolarChhat: from a roof photo to a full solar plan

**Upload a top-down photo of any roof. In about a minute you get the system size, a panel layout drawn on your roof, yearly generation, cost after the PM Surya Ghar subsidy, payback period and CO₂ avoided.**

*Chhat* (छत) means roof in Hindi.

👉 **Live demo:** https://YOUR-GITHUB-USERNAME.github.io/solarchhat/

![SolarChhat screenshot](docs/screenshot.png)

---

## The problem

India's PM Surya Ghar scheme pays up to ₹78,000 toward a rooftop solar system, and it aims to reach 1 crore homes. But the first question every household asks is *"How much solar fits on my roof, and is it worth it?"* Today the answer means waiting for a vendor's site visit, getting a quote, and taking the vendor's word for the numbers.

SolarChhat gives you a credible, independent first answer from a single photo.

## How it works

| Step | What happens |
|---|---|
| **1. Photo** | Upload a satellite screenshot (Google Maps, north up) or a drone photo. |
| **2. Roof mapping** | **Claude (vision)** traces the usable roof outline and every obstruction: water tanks, stairwells (*mumty*), AC units, overhanging trees. It also writes a plain-language shading assessment. Prefer not to use an API key? Trace the roof by hand in five clicks. |
| **3. Scale** | Click two points and enter a known length, or use Claude's estimate from visual cues such as tanks, doors and cars. |
| **4. Sunlight** | Pulls 20+ years of satellite irradiance for your exact coordinates from the **NASA POWER** API (monthly GHI). |
| **5. Panel layout** | A packing algorithm fits real 550 W modules inside the roof while keeping an edge setback and a clearance gap around every obstruction. It tries both portrait and landscape and 36 grid offsets, and keeps the best result. |
| **6. Finance** | Sizes the system to your electricity bill, then applies the PM Surya Ghar subsidy (₹30k/kW for the first 2 kW, ₹18k for the 3rd kW, capped at ₹78k; special-category states supported), an optional state subsidy, net-metering export credit and 0.5 %/yr panel degradation. |

### The engineering details

- **Inter-row spacing for self-shading:** panels are tilted, so each row casts a shadow. Row pitch is computed so there is no shading at solar noon on the winter solstice, when the sun is lowest, at your latitude:
  `gap = L·sin(tilt) / tan(90° − φ − 23.45°)`
- **South-first placement:** recommended panels fill the south edge first, which is least shaded by stairwells and tanks in the northern hemisphere.
- **Monthly yield:** `E_month = kWp × GHI_month × days × PR` with performance ratio 0.77 (editable).
- **Bill-matched sizing:** the plan shows both *what your roof can hold* and *what your bill needs*, so you don't overbuy past the subsidy sweet spot.

## Built with Claude

- **Claude (agentic coding in claude.ai)** wrote and iterated the whole app: geometry engine, packing algorithm, finance model and UI. It also tested every path in a headless browser (sample roof, hand tracing, a mocked Claude API response, and NASA being unreachable).
- **Claude API (vision)** does the core roof survey in the app. It returns structured JSON polygons for the roof and obstructions, plus a homeowner-friendly assessment.
- **Claude chat** ran the research: verifying current PM Surya Ghar subsidy rules, and choosing this idea in the first place.

## Run it

It's a single `index.html` with no build step and no dependencies.

```bash
git clone https://github.com/YOUR-GITHUB-USERNAME/solarchhat
cd solarchhat
python3 -m http.server 8000   # then open http://localhost:8000
```

Or just open `index.html` in a browser. To use auto-detect, open **"Claude API key"** in the app and paste your key. It is stored only in your browser (and only if you tick *remember*) and sent only to `api.anthropic.com`.

## Honest limitations

- **Planning estimate, not a design.** Get a site survey from an empanelled vendor before installing.
- Assumes a **north-up image** and **south-facing panels**. Sloped roofs are treated as flat.
- Shading from **neighbouring tall buildings** isn't modelled unless you mark it as an obstruction.
- Yield uses horizontal irradiance with a fixed performance ratio, without a full plane-of-array transposition model, so tilt gains are slightly understated (a conservative bias).
- Scale from Claude's visual estimate can be off by 10–20 %. Use **Set scale** with a known length for accurate numbers.
- Subsidy rules are as of November 2026 (the scheme runs to March 2027).

## Roadmap

- Fetch the satellite tile directly from an address, with no screenshot needed
- Hourly shading simulation from obstruction heights
- Hindi UI and a WhatsApp-shareable report
- List of empanelled vendors by DISCOM

---

Made by **Himanshu Dhaka**, Energy Science & Engineering, IIT Delhi · MIT License
