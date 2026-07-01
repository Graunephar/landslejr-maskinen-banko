# Maskinen Banko — design-spec (v9)

**Pixel-eksakt reference:** [`maskinen-mockup.html`](maskinen-mockup.html) — åbn i en
browser, tænd en hel række for at se festen. Det er den kanoniske sandhed; dette
dokument beskriver den i tal. `index.html` (appen) skal ligne mockup'en.
Tekniske beslutninger (hash, party, hosting m.m.) står i [`../PLAN.md`](../PLAN.md).

---

## Vibe i én sætning

Et retro **kontrolpanel i et motorrum** — cremet metal-kabinet, mørkt panel, varm
orange glød, strejf af magi. Gritty, festet, teknisk. Tallene er helten; alt chrome er
krydderi. Kobling: **at markere et tal = at tænde en lampe på maskinen.**

---

## Palette (eksakte hex)

| Token | Hex | Brug |
|---|---|---|
| `--orange` | `#ff6b1a` | Primær. Titel, tændte tal, batteri-fuld |
| `--orange-deep` | `#e0490f` | Bund af tændt tals glød |
| `--glow` | `#ff922e` | Glød/skær, power-lampe |
| `--hot` | `#ffb257` | Highlight i tændt tal, knap-pointer, amber-batteri |
| `--teal` | `#3fe6cc` | CRT-fosfor (retro-kontrast) |
| `--party` | `#c94fd6` | Discolys, easter egg, vinder-type |
| `--party2` | `#42d6c0` | Disco-akcent |
| `--bg` | `#140f0b` | Yderste baggrund |
| `--panel` | `#1e1712` | Panelflade |
| `--plate` | `#efe8dd` | Tal-felternes off-white skilt |
| `--plate-blank` | `#cdbfae` | Blanke felter (opacity 0.26) |
| `--ink` | `#181310` | Tal-tekst |
| Bezel | `#d8c9a6 → #b39a68` | Cremet metal-ramme (gradient 180°) |
| Metal | `#5a4830` / `#4a3a2a` / `#3a2c1e` | Batteri-ramme, knapper, kanter |
| Batteri grøn | `#46d97a` | Segment 1–5 |
| Batteri rød | `#ff4d3d` | Segment 11–15 |
| Rød knap | `#ff6a5a → #a01508` | Nødknap |

**Font:** `ui-monospace, "SF Mono", Menlo, monospace` overalt.

---

## Layout (top → bund, eksakte mål)

Yderst `.mskn` padding 22px, `radial-gradient(120% 90% at 50% -20%, #43301d, --bg 55%)`.

1. **Bezel** — cremet ramme, padding 7px, radius 15px, **max-width 820px**.
2. **Panel** — `--panel`, border 1px, radius 9px, padding `10px 14px 12px`, inset-shadow
   `0 0 46px rgba(0,0,0,.6)`, `overflow:hidden`. 4 skruer (10px) i hjørnerne, offset 6px.
3. **Header (centreret)** — margin-bund 9px:
   - `MASKINEN` 21px / 800 / letter-spacing .3em, orange m. glød.
   - `BANKO · LANDSLEJR 2026` 9px / .32em / dæmpet.
4. **Kontrol-strip** (`.strip`, flex, gap 9px, padding-bund 9px):
   - **CRT** 56×26px, `#04120f`, teal-grid 8px, scanline (2.6s). Viser tælling **`00/15`** (teal 15px).
   - **Knapper**: 2 drejeknapper (16px), 1 kontakt (10×16px, tændt), 1 rød knap (15px).
   - **Batteri** (`.battery-unit`, flex:1, min-width 120px): batteri-krop 22px høj, ramme
     **2.5px** `#5a4830`, radius 5px, 15 chunky segmenter (gap 3px) + **terminal-nub 6×12px**.
     Fyldes venstre→højre: seg 1–5 grøn, 6–10 amber, 11–15 rød. Fuld → ramme+nub gløder orange.
     *Battery-shaped, ikke progress-bar.*
   - **Hash** (`.hash`): `MSKN-XXXX` 11px, dæmpet. **Deterministisk hash af pladens 15 tal**
     (FNV-1a → 4 hex). Se PLAN.md ADR-11.
5. **Plade** (`.grid`): 9×3, gap 5px, padding 6px, `#100b07`, border 1px.
   - **Tal-felt**: kvadrat, `--plate`, tal `--ink` `clamp(17px,4vw,30px)`/800, radius 6px.
     Analog knap: inset bund-skygge 3px; `:active` synker 2px.
   - **Blankt**: `--plate-blank`, opacity 0.26.
   - **Tændt**: radial `--hot→--orange→--orange-deep`, hvid tekst, glød-ring + bloom, `pulse` 2.1s.
6. **Status-bar**: power-lampe (9px, `breathe` 2.4s) + `TRYK FOR AT TÆNDE LYSET` (10px, højre).
7. **Celebrate-overlay** (skjult, `.on` = flex) — se næste sektion.

**Small-phone (`max-width:520px`):** titel 17px, tal-clamp `15px..24px`, batteri min-width 80px, BANKO 44px.

---

## Vinder-fest (celebrate-overlay)

Udløses når en hel række (eller hele pladen) tændes. Se PLAN.md ADR-12.

- **Vinder-type-tekst** (`.wintype` 18px): `1 RÆKKE` / `2 RÆKKER` / `FULD PLADE`.
- **`BANKO!`** (`.winbanko` 56px/900, roteret -5°, glød).
- **Discolys** (`.disco`): roterende `conic-gradient` i alle party-farver, blur 26px,
  `mix-blend:screen`, opacity .32, `spin` 2.6s.
- **Confetti**: 90 stk, 7×12px, party-farver, `fall`-animation (falder 340px + roterer 540°),
  tilfældig left/delay/varighed.
- **Techno-lyd**: ~10 sek hård four-on-floor (Web Audio, genereret → royalty-fri):
  kick 160→46 Hz + offbeat sawtooth-stab, 142 BPM. Starter på spillerens tryk (mobil-krav).
- **Retur-indikator** (`.return` + `.returnbar`): "PLADEN KOMMER TILBAGE" + bjælke der
  tømmes over **20 sek** (width 100%→0). Hele festen varer 20 sek, så forsvinder overlay.
- Auto-registrering er **kun lokal fejring**, ikke autoritativ (opråber tjekker rigtigt).

---

## Animationer

| Navn | Varighed | Hvad |
|---|---|---|
| `pulse` | 2.1s ∞ | Tændte tal ånder |
| `breathe` | 2.4s ∞ | Power-lampe opacity |
| `scan` | 2.6s ∞ | CRT-scanline |
| `spin` | 2.6s ∞ | Discolys roterer |
| `fall` | 1.8–3.8s ∞ | Confetti falder |
| retur-bjælke | 20s | Nedtælling til pladen kommer tilbage |

---

## Interaktion & easter eggs

- **Tryk tal** → tænd/sluk lampe. Opdaterer CRT (`00/15`) + batteri.
- **Hel række / fuld plade** → fest (se ovenfor) med korrekt type-tekst.
- **Tryk MASKINEN 3× hurtigt** (<800ms) → skjult `⚡ MAGIEN ELLER TEKNOLOGIEN? ⚡`.
- Alle easter eggs på **ikke-funktionelle** elementer → forstyrrer aldrig markering.

---

## Copy

- Titel `MASKINEN` · Undertitel `BANKO · LANDSLEJR 2026`
- Status: `TRYK FOR AT TÆNDE LYSET`
- Tæller: `00/15` · Id: `MSKN-XXXX`
- Vinder: `1 RÆKKE` / `2 RÆKKER` / `FULD PLADE` + `BANKO!`
- Retur: `PLADEN KOMMER TILBAGE`
- Easter egg: `⚡ MAGIEN ELLER TEKNOLOGIEN? ⚡`

---

## Guardrails (ufravigelige)

1. **Tal > effekt.** Glød/metal/teal/fest er krydderi. Tallene skal altid være knivskarpe
   og store tap-mål.
2. **Chrome fylder mindst.** Strip + batteri er tynde; pladen dominerer.
3. **Landskab-låst** (PLAN.md ADR-10).
4. **Ingen destruktive knapper** for deltagere (ADR-4). Chrome-knapper er ren pynt.
5. Mockup'en er ét fast eksempel — appen genererer en **tilfældig** plade (crypto-seed, ADR-6).
6. **Festen skal altid slutte selv** (20s) og vise pladen igen — spilleren må aldrig sidde fast i fest.
