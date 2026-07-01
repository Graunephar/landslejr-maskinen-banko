# Build-plan — Banko, Maskinen (Landslejr 2026)

---

## 1. Hvad der skal bygges

Én statisk `index.html` (HTML + vanilla JS + CSS i samme fil). Hver telefon
genererer sin egen tilfældige 3×9 bankoplade (1–90, klassisk dansk format) på
første åbning og beholder den. Man trykker på tal for at markere. Ingen login,
ingen server, ingen database.

Menneskelig opråber trækker og råber tal som til almindeligt banko. Appen er
**kun** den digitale plade — ikke udtrækning, ikke vinder-tjek.

---

## 2. Beslutninger (ADR-stil)

### ADR-1 — Ingen backend, kun telefoner
- **Status:** Adopted
- **Kontekst:** Opråber råber tal live. Appen skal kun erstatte den printede plade.
- **Valg:** Ren statisk side. Ingen server, ingen database, ingen konti.
- **Fravalgt:** Firebase / backend — anden og skrøbeligere app; en backend gør
  desuden både *plade-loading* og *marker-gemning* afhængig af lejr-wifi, hvilket
  er værre end det, den løser (se ADR-3 og ADR-6).
- **Konsekvens:** Nul driftsrisiko på aftenen. Virker offline. 🔒 one-way door lukket korrekt.
- **Revurder hvis:** I vil have live tal-udtrækning eller garanteret unikke plader
  (se ADR-6).

### ADR-2 — Ingen framework, ingen build
- **Status:** Adopted
- **Valg:** Plain HTML + vanilla JS, én fil. Ingen npm/Svelte/build.
- **Fravalgt:** Svelte (oprindeligt gæt) — build-step uden gevinst på én side.
- **Konsekvens:** Deployer hvor som helst statisk. Kan åbnes direkte fra fil.

### ADR-3 — Marker og plade gemmes lokalt (localStorage)
- **Status:** Adopted
- **Kontekst:** Krydsene kan **ikke** genskabes, hvis de forsvinder midt i spillet
  — man kan ikke bagefter vide, hvilke opråbte tal man havde. Marker-persistens er
  derfor **kritisk**, på niveau med selve pladen.
- **Valg:** Plade + markeringer gemmes i browserens localStorage. Marker skrives
  ved hvert tryk.
- **Hvorfor ikke backend:** localStorage virker **offline** og overlever det
  almindelige (reload, låst skærm, app-skift, tabt net, genstart). En backend ville
  gøre markering afhængig af live wifi — værre på en lejr med mange brugere.
- **Konsekvens:** Robust mod det almindelige. Sårbart kun over for: in-app browser
  (Instagram/Messenger) der genåbnes, privat browsing, manuel rydning, skift af
  enhed. Disse dækkes af reserveplader (ADR-7).

### ADR-4 — Én plade per telefon, kan ikke skiftes (undtagen i fejlfindings-mode)
- **Status:** Adopted
- **Valg:** Pladen genereres på **første** åbning og pinnes. **For deltagere: ingen
  "Ny plade"-knap og ingen "Ryd kryds"-knap** — begge er footguns, der kan nulstille
  en plade eller krydsene ved et uheld midt i spillet, uden mulighed for at genskabe.
- **Undtagelse (ADR-9):** I fejlfindings-mode (`?fejlfinding`) vises en "Ny plade"-knap,
  så arrangørerne kan teste. Knappen findes **ikke** for almindelige deltagere.
- **Rettelse af mis-tryk:** Man fjerner et enkelt kryds ved at trykke på tallet igen.
  Det er nok — der er ikke brug for en nulstillingsknap.
- **Konsekvens:** Ingen destruktive handlinger i deltagernes UI. Simplere skærm.
- **Bemærk:** Hvis localStorage ryddes (se ADR-3), findes pladen ikke længere på
  enheden, og der er ingen knap til at få den igen → deltageren får en reserveplade
  (ADR-7).

### ADR-5 — Look = Maskinen
- **Status:** Adopted
- **Valg:** Senior-orange `#d6833a` på mørk baggrund, monospace-overskrifter.
  Titel: "BANKO — Maskinen · Landslejr 2026".

### ADR-6 — Unikke plader via god tilfældighed, ikke via server
- **Status:** Adopted
- **Kontekst:** Vi vil ikke have to deltagere med samme plade.
- **Fakta:** Der er > 10¹² gyldige plader. Ved 1000 deltagere er chancen for bare
  ét sammenfald ≈ **1 ud af 2 millioner** (k²/2N). Man skal op omkring **en million
  deltagere**, før det bliver sandsynligt. 1000 er ikke i nærheden af et problem.
- **Valg:** Seed tilfældigheden fra **`crypto.getRandomValues()`** (128-bit rigtig
  entropi). **Aldrig** `Date.now()` eller rå `Math.random()` som seed.
- **Hvorfor:** Den reelle kollisionsrisiko er ikke antal deltagere, men en sjusket
  seed — to telefoner der åbner i samme sekund med clock-seed ville få samme plade.
  Crypto-seed fjerner det.
- **Fravalgt:** Backend-register (ville *garantere* unikhed, men gør plade-loading
  afhængig af live wifi — dårlig byttehandel på en lejr).
- **Revurder hvis:** Der skal være **præcis** én vinder som hårdt krav → brug
  per-id links (`?id=1..N`, deterministisk plade per id, mennesker uddeler numre i
  rækkefølge). Stadig offline, stadig ingen backend, men garanteret unikt.


### ADR-8 — Hosting: Cloudflare Pages via GitHub
- **Status:** Adopted
- **Valg:** Repo på GitHub → Cloudflare Pages git-integration → auto-deploy ved push.
- **Build settings:** Framework preset **None**, build command **tom**, output **`/`**.

### ADR-9 — Fejlfindings-mode via `?fejlfinding`
- **Status:** Adopted
- **Kontekst:** Arrangørerne skal kunne teste appen (fx generere friske plader) uden
  at give deltagerne farlige knapper.
- **Valg:** URL-parameteren `?fejlfinding` slår en fejlfindings-mode til. I den mode:
  - vises en **"Ny plade"-knap** (genererer en frisk plade — bekræft først, da den
    overskriver den gemte plade og dens kryds).
  - kan vises ekstra info til fejlfinding (fx antal tal pr. kolonne, seed) — valgfrit.
- **Almindelige deltagere** (link uden parameteren) ser **intet** af dette.
- **Adskilt fra `?test`:** `?test` kører den automatiske self-check (5000 plader,
  jf. sektion 5). `?fejlfinding` er den interaktive test-mode med knap. To forskellige
  ting, to forskellige parametre.
- **Konsekvens:** Ingen destruktiv knap i produktions-UI'et, men arrangørerne kan
  stadig teste. Parameteren er ikke hemmelig — bare ikke i det delte link.

### ADR-10 — Altid landskab, via CSS-rotation (ikke orientation-lock API)
- **Status:** Adopted
- **Kontekst:** Pladen er 9 kolonner bred → landskab passer bedst. UI'et skal altid
  vises i landskab, uanset hvordan telefonen holdes.
- **Valg:** Byg til landskab. I portræt: `@media (orientation: portrait)` roterer
  hele app-containeren 90° med `transform: rotate(90deg)` og bytter dimensioner
  (`width: 100dvh; height: 100vw`, `transform-origin` sat korrekt).
- **Fravalgt:** `screen.orientation.lock('landscape')` — **virker ikke på iOS Safari**
  og kræver fullscreen på Android. Upålideligt.
- **Fravalgt:** "Drej din telefon"-overlay — **fejler for brugere med rotations-lås
  til portræt** (kan ikke dreje sig ind). CSS-rotationen tvinger landskab visuelt
  uanset brugerens rotations-indstilling.
- **Hvorfor det ikke driller med autorotate:** portræt → CSS roterer → landskab;
  landskab → naturligt landskab. Begge veje giver landskab. Ingen API, ingen fullscreen.
- **Konsekvens:** Virker ens på iOS + Android. Kræver at grid'et er fast layout uden
  scroll (det er), ellers bliver scroll i en roteret container klodset. Touch virker
  gennem transformen.

### ADR-11 — Plade-id = hash af pladens tal
- **Status:** Adopted
- **Kontekst:** Panelet viser et serienummer (`MSKN-…`). Det skal ikke være pynt, men et rigtigt id.
- **Valg:** Serienummeret er en **deterministisk hash af pladens 15 tal** (kort hex,
  fx `MSKN-4F2A`). Samme plade → samme hash. Beregnes lokalt ud fra tallene (fx FNV-1a
  → 4 hex-tegn), ingen server.
- **Brug:** Nemt at referere en konkret plade ved manuelt vinder-tjek ("hvad er dit
  MSKN-nummer?"), og kan bruges til at spotte dubletter.
- **Konsekvens:** Id'et falder ud af tallene; ingen central udstedelse.

### ADR-12 — Vinder-feedback: typer + party-sekvens
- **Status:** Adopted
- **Kontekst:** Banko har flere vinder-typer (én række, flere rækker, fuld plade).
  Appen skal fejre gevinst lokalt og fedt.
- **Valg:**
  - Appen registrerer typen og **skriver den** i overlay: `1 RÆKKE`, `2 RÆKKER`, `FULD PLADE`.
  - **Party-sekvens** ved gevinst: **confetti + discolys** + en kort **hård techno-stinger
    ~10 sek** ("UMP UMP" — royalty-fri eller genereret i browseren via Web Audio,
    four-on-floor kick + offbeat stab). Hele festen varer **20 sekunder**.
  - En synlig **retur-indikator** (nedtælling/bjælke: "PLADEN KOMMER TILBAGE") viser at
    festen slutter og pladen + skærmen kommer tilbage — så spilleren ved det.
  - Lyd starter på **spillerens tryk** (user gesture — krav for at mobil-browsere må afspille lyd).
- **Vigtigt:** Auto-registreringen er **kun lokal fejring, ikke autoritativ**. Rigtigt
  vinder-tjek er stadig manuelt hos opråberen (jf. ADR-1). En fejl-tap trigger bare en
  lokal fest — harmløst.
- **Konsekvens:** Fed feedback uden at ændre selve spillets styring.

### ADR-13 — Panel-readouts: CRT viser tælling, hash vises separat
- **Status:** Adopted
- **Valg:** CRT-skærmen viser tællingen i formatet **`00/15`** (tændte/15). Det tidligere
  separate `00/15`-felt var redundant og erstattes af **`MSKN-<hash>`** (ADR-11).
- **Batteri-indikator:** Skal se ud som en **rigtig batteri-indikator** (batteri-krop +
  terminal-nub, chunky segmenter) — **ikke** en tynd progress-bar. 15 segmenter = ét pr.
  tal, grøn→amber→rød, fuld = BANKO nært.
- **Overskrift:** `MASKINEN` + `BANKO · LANDSLEJR 2026` skal være **centreret** på panelet.

---

## 3. Pladegenerering (eneste ikke-trivielle logik — byg den rigtigt)

Klassisk dansk banko:
- 3 rækker × 9 kolonner.
- **15 tal i alt**, **5 tal pr. række** (4 blanke felter pr. række).
- **1–3 tal pr. kolonne.**
- Kolonne-intervaller: kol 1 = 1–9, kol 2 = 10–19, … kol 8 = 70–79, kol 9 = **80–90**.
- Tal i en kolonne **sorteret oppefra og ned**. Alle 15 tal unikke.

Algoritme (kendt og robust):
1. **Seed fra `crypto.getRandomValues()`** (jf. ADR-6).
2. Kolonne-antal: start alle 9 kolonner på 1 (= 9 tal). Fordel de resterende 6
   tilfældigt, max 3 pr. kolonne.
3. Rækkefordeling: placér hver kolonnes tal i forskellige rækker, så hver række
   ender med præcis 5. Brug rejection-sampling (prøv igen til gyldig).
4. Tal: for hver kolonne, træk `antal` unikke tal fra dens interval, sortér, placér
   oppefra og ned i de valgte rækker.
5. **Output en fuld 3×9 grid med eksplicitte blanke felter** — ikke bare en liste
   af 15 tal. Så kan visningen ikke placere blanke forkert.

Faldgruber at undgå:
- 5-pr-række er den svære constraint; naiv "vælg 15 tilfældige felter" giver skæve
  rækker.
- kol 9 er 80–**90** (11 tal) — off-by-one får ellers 90 til at forsvinde.
- Overlap ikke kolonne-intervaller (det er det, der garanterer unikke tal).

---

## 4. Interaktion
- Tryk på et tal → markér (orange cirkel, hvid tekst). Tryk igen → fjern.
- **Deltagere: ingen "Ny plade"- eller "Ryd kryds"-knap** (ADR-4).
- **`?fejlfinding`: "Ny plade"-knap vises** (kun til arrangørernes test, ADR-9).
- **Altid landskab** (ADR-10): holdes telefonen i portræt, roteres UI'et 90° via CSS.
- Store tryk-felter (telefon, blandet aldersgruppe). Fast layout, ingen zoom/scroll.

---

## 5. Acceptkriterier (Definition of Done)
- [ ] En plade viser altid: 15 tal, 5 pr. række, 1–3 pr. kolonne, korrekt interval, sorteret.
- [ ] Tilfældigheden seedes fra `crypto.getRandomValues()` — ikke clock/`Math.random()`.
- [ ] Pladen genereres kun **første** gang og pinnes; åbnes linket igen på samme
      telefon, vises den **samme** plade med de **samme** kryds.
- [ ] Markeringer overlever reload, låst skærm, app-skift og genstart (localStorage).
- [ ] Ingen destruktive knapper i deltagernes UI.
- [ ] `?fejlfinding` viser en "Ny plade"-knap; uden parameteren findes den ikke.
- [ ] UI vises i landskab i **både** portræt (CSS-roteret 90°) og landskab; testet på iOS + Android.
- [ ] Ser ud som Maskinen (orange `#d6833a`, mørk, monospace overskrift).
- [ ] **Self-check:** `index.html?test` genererer 5000 plader og logger 0 regelbrud i konsollen.
- [ ] Deployet på Cloudflare Pages, delbart link virker på en telefon.

---

## 6. Kendte lofter (bevidst valgt)
- Menneskelig opråber råber tal; appen = digital plade, intet auto-vinder-tjek.
- Marker gemmes lokalt per enhed; in-app browser / privat browsing / rydning /
  enheds-skift kan slette dem → dækkes af reserveplader (ADR-7).
- Unikke plader er *astronomisk sandsynligt*, ikke *garanteret*. Hvis "præcis én
  vinder" bliver et hårdt krav → per-id links (ADR-6), ikke backend.
- Engangsbrug til én aften — ingen analytics, ingen konti, ingen central persistering.
