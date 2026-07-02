# Banko — Maskinen 2026

Digital bankoplader. Én statisk fil. Se [PLAN.md](PLAN.md) for beslutninger.

## Kør lokalt

Statisk fil, ingen build. Nemmest — åbn filen direkte:

```sh
open index.html          # localStorage virker også fra file://
```

Eller start en dev-server fra repo-roden (vælg én):

```sh
python3 -m http.server 8177   # → http://localhost:8177/
npx serve .                   # node
php -S localhost:8177         # php
```

Nyttige URL'er:

- `http://localhost:8177/` — appen
- `http://localhost:8177/?test` — self-check (5000 plader, se konsol)
- `http://localhost:8177/?fejlfinding` — NY PLADE-knap + kolonne-info

## Deploy til Cloudflare Pages

Ingen build. Framework preset: **None**. Build command: **tom**. Output dir: **`/`** (roden).

### Hurtigst — drag & drop (ingen CLI, ingen git)
1. dash.cloudflare.com → **Workers & Pages** → **Create** → **Pages** → **Upload assets**.
2. Projektnavn f.eks. `banko-maskinen`.
3. Træk mappen `landslejr-banko` (eller bare `index.html`) ind. **Deploy**.
4. Del linket `https://banko-maskinen.pages.dev`.

### Alternativt — Wrangler CLI
```sh
npx wrangler pages deploy . --project-name banko-maskinen
```
Første gang åbner den browseren for at logge ind på din Cloudflare-konto.

### Alternativt — git-connected (auto-deploy ved push)
Push repoet til GitHub, forbind det i Pages. Build settings som ovenfor (None / tom / `/`).
