# Seniorfestival — brand & design guideline

Reverse-engineered from the live site **seniorfestival.dk** (captured 2026-09-09).
Tokens ready to paste: [`seniorfestival-tokens.css`](seniorfestival-tokens.css).

> Scope note: this is extracted from the public front page, not from an official
> brand book. It is accurate to what ships, not authoritative for print/logo use.

---

## Vibe in one sentence

**Festival poster energy on a near-black stage.** Huge, tight, ultra-bold Danish
sentences; warm orange→pink heat; soft dark cards with a hard offset shadow like
glued-on paper; occasional warm paper sections that let the loud stuff breathe.

Three rules carry the whole look:
1. **Type is the design.** Weight 1000, negative tracking, line-height below 1.
2. **Heat is the accent.** Orange→pink gradient on everything that acts.
3. **Rhythm dark → paper → dark.** Never a full page of one surface.

---

## Palette

| Token | Hex | Use |
|---|---|---|
| `--sf-ink` | `#111018` | The brand black. Cards, dark sections, body on light |
| `--sf-ink-2` / `--sf-ink-3` | `#201722` / `#241827` | Warm/plum partners in dark gradients |
| `--sf-orange` | `#ff6a00` | Primary. Gradient start, glows, hard shadow |
| `--sf-pink` | `#ff2d72` | Primary partner. Gradient end, button shadow |
| `--sf-red` | `#ff4a1f` | Kickers/eyebrows on light surfaces |
| `--sf-sand` | `#ffb25f` | Warm secondary, cool-gradient end |
| `--sf-cyan` | `#00b5e2` | Cool secondary — sparingly, for contrast pills |
| `--sf-paper` | `#efe7e2` | Warm light section |
| `--sf-paper-2` | `#d9d3cf` | Greyer light section |
| `--sf-white` | `#ffffff` | Text on dark, page base |

**Text on dark uses white at fixed alphas, not grey hexes:** `1` (headline),
`.86`, `.78`, `.72`, `.58` (metadata). Keep to that ladder.

### Gradients (all 135°)
- **Hot** `#ff6a00 → #ff2d72` — buttons, badges, anything clickable.
- **Cool** `#00b5e2 → #ffb25f` — the one alternative pill. Dark text on it.
- **Dark** `#201722 → #111018` — dark section base.
- **Corner glow** `radial-gradient(circle at 100% 0%, rgba(255,106,0,.28), transparent 40%)`
  layered *over* the dark gradient. This is the signature depth trick — one warm
  bloom from a corner, never centered.

---

## Typography

**Work Sans** (variable), everywhere. Google Fonts; load weights 500 + 1000.

| Role | Size | Weight | Tracking | Line-height |
|---|---|---|---|---|
| Hero display | `clamp(40px, 8vw, 102px)` | 1000 | `-0.055em` (−6px @102) | `0.8` |
| Section display | 70–90px | 1000 | `-2` … `-4px` | `0.85` |
| Card title | 28px | 1000 | `-1px` | `0.95` |
| Kicker / eyebrow | 12–14px | 1000 | `+0.4…0.8px`, UPPERCASE | 1.4 |
| Button label | 18px | 950–1000 | normal | 1 |
| Body | 14–18px | 500 | normal | `1.7` |

**The tracking rule:** the bigger the type, the more negative the tracking. Small
uppercase labels go *positive*. This inversion is the most recognizable part of the
brand — get it wrong and it stops looking like Seniorfestival.

Headlines are short, full sentences ending in a period: *"Større. Vildere. Mere os."*,
*"Fordi det kan mærkes."*, *"Vi ses på pladsen."* Uppercase for hero and closing,
sentence case in the middle of the page. Danish, plain, no marketing verbs.

Light sections carry a giant near-invisible background word: same display face,
`rgba(0,0,0,.035)`.

---

## Shape & elevation

- **Radii:** pill `999px` (buttons, badges, nav), card `34px`, panel `22px`, small `14px`.
  Nothing square, nothing sharper than 14px.
- **Card shadow — the signature:**
  `18px 18px 0 rgba(255,106,0,.34), 0 28px 80px rgba(0,0,0,.45)`
  A *hard offset colour block* plus a soft drop. Colour variants: pink `.30` at 14px,
  sand `.28` at 14px.
- **Button shadow:** coloured, not black — `0 18px 45px rgba(255,45,114,.38)`.
- Dark cards on dark sections; separation comes from radius + coloured shadow, not borders.
  Where a border is needed: `1px solid rgba(255,255,255,.12)`.

---

## Layout

- Content max **1080px**, narrow text column **823px**.
- Section padding **102px vertical / 24px horizontal**; grid gutter **30px**.
- Alternate surfaces: dark → paper → dark. Photo sections get a dark scrim
  `linear-gradient(90deg, rgba(0,0,0,.8), rgba(0,0,0,.36))` so white type always holds.
- Sticky top bar: `rgba(20,18,24,.97)`, pill nav items, one gradient CTA, plus a thin
  announcement strip above it in the hot gradient.
- Hero: kicker pill → giant display → date line in orange → scattered rotated cards.
  Cards sit at slight rotations (±3–6°) — deliberate "pinned poster" scatter.

---

## Motion

Restrained. Only two things move:
- **Reveal on scroll:** `fadeIn`/`fadeTop`/`fadeLeft`/`fadeRight`, ~0.6s ease-out.
- **Hover:** `transform .22s, box-shadow .22s` — lift 2px, deepen the coloured shadow.
- One accent pulse (`sfTicketPulse`) on the single most important CTA. **Only one per page.**

No parallax, no autoplay carousels, no confetti. The type does the shouting.

---

## Voice

Danish, direct, collective. "Vi" and "os", never "du bør". Short declaratives with
full stops. Nouns as section titles (*Musikken, Fællesskabet, Oplevelserne*).
Practical info stays plain — no exclamation marks outside the hero.

---

## Building something new in this style — checklist

1. Load Work Sans 500 + 1000, set `--sf-*` tokens from the CSS file.
2. Page base `--sf-ink` with a corner glow; alternate in `--sf-paper` sections.
3. One display headline per section, weight 1000, tracking negative, LH 0.8.
4. Every action = pill with the hot gradient + coloured shadow. One primary per screen.
5. Cards: radius 34, dark fill, hard orange offset shadow, slight rotation if decorative.

### Don'ts
- No third accent hue. Orange/pink/sand/cyan is the whole box.
- No grey text — use white at the alpha ladder.
- No weight 700 headlines. It's 1000 or it's body.
- No black `box-shadow` on interactive elements; shadows are coloured.
- No centred glow. The bloom comes from a corner.

---

## Accessibility notes (verified against the live site)

- White on `--sf-ink` = 18.8:1. Fine.
- **Orange `#ff6a00` on white is 2.9:1 — fails AA for text.** On the live site it only
  appears as large display type or as a background under white text. Keep it that way;
  for small text on light use `--sf-red` at 16px+ bold, or black.
- White on the hot gradient runs 2.9:1 (orange end) to 3.6:1 (pink end). The orange end
  is just under the 3:1 large-text floor — so button labels must stay 18px/950 or bigger,
  and never put small white text on flat orange.
- Honour `prefers-reduced-motion`: drop the reveal animations and the CTA pulse.
