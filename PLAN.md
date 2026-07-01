# Banko — Maskinen, Landslejr 2026

Digital bankoplader til seniorernes Hyggeaften i Maskinen. Deltagerne spiller på
egen telefon i stedet for at printe plader.

## Hvad det er

Én statisk HTML-side. Hver telefon får sin egen tilfældige 3×9 bankoplade
(1–90, klassisk dansk format). Man trykker på tal for at markere. Ingen login,
ingen server, ingen database.

## Beslutninger (ADR-stil)

### 1. Ingen backend — menneskelig opråber beholdes
- **Kontekst:** Banko på Hyggeaften kører med en opråber der trækker og råber
  tal. Appen skal kun erstatte den printede plade.
- **Valg:** Ren statisk side. Ingen live-udtrækning, ingen auto-BANKO, ingen sync.
- **Fravalgt:** Firebase / realtidssync — ville være en helt anden (og skrøbeligere)
  app, med reelle one-way doors. Ikke nødvendigt for opgaven.
- **Konsekvens:** Nul driftsrisiko på aftenen. Virker offline. Vinder-tjek sker
  manuelt som til almindeligt banko.
- **Revurder hvis:** I vil have appen til at trække tal og pushe til alle telefoner.

### 2. Ingen framework, ingen build
- **Kontekst:** Én side, engangsbrug til én aften.
- **Valg:** Plain HTML + vanilla JS i én fil. Ingen npm, ingen Svelte, ingen build.
- **Fravalgt:** Svelte (oprindeligt gæt) — build-step uden gevinst på én side.
- **Konsekvens:** Deployer hvor som helst statisk. Kan åbnes direkte fra fil.

### 3. State i localStorage
- **Valg:** Plade + markeringer gemmes i browserens localStorage.
- **Konsekvens:** Overlever reload og skærmlås. Ryddes hvis man clearer browserdata
  — fint for én aften, per enhed.

### 4. Pladegenerering (den eneste ikke-trivielle logik)
- Klassisk dansk banko: 3 rækker × 9 kolonner, 15 tal, 5 tal pr. række.
- Kolonne-intervaller: kol 1 = 1–9, kol 2 = 10–19 … kol 8 = 70–79, kol 9 = 80–90.
- 1–3 tal pr. kolonne, sorteret oppefra og ned.
- **Verificeret:** self-check kører 5000 plader mod alle regler (`index.html?test`,
  se konsollen). 20.000 plader kørt i node = nul regelbrud.

### 5. Look = Maskinen
- Senior-orange `#d6833a` på mørk baggrund, monospace-overskrifter (maskine-vibe).

## Filer
- `index.html` — hele appen.
- `PLAN.md` — dette dokument.

## Hosting — Cloudflare Pages
Se `README.md`.

## Kendte lofter
- Menneskelig opråber råber tal; appen = digital plade, intet auto-vinder-tjek.
- localStorage per enhed; clear af browserdata sletter pladen.
