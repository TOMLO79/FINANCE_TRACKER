# Finance Tracker

Osobní aplikace na vedení domácího rozpočtu — výplata, fixní a variabilní výdaje (včetně frekvence plateb: měsíčně/kvartálně/pololetně/ročně), úspory, zlato. Běží čistě v prohlížeči, data se ukládají lokálně (`localStorage`), nic se neposílá na žádný server. Jde o PWA — po otevření na mobilu/PC jde "Přidat na plochu" a appka pak funguje i offline.

## Spuštění lokálně (vývoj)

Service worker (a tím pádem PWA chování) vyžaduje běh přes `http(s)://`, ne přímo dvojklikem na `index.html` (`file://`). Stačí:

```bash
python3 -m http.server 8000
```

a otevřít `http://localhost:8000/`. Na mobilu ve stejné Wi-Fi jde použít IP adresu počítače místo `localhost`.

## Nasazení na GitHub Pages (produkce / PWA na mobilu)

1. Na GitHub.com vytvoř nový repozitář (např. `finance-tracker`).
   ⚠️ **Pokud je účet na free plánu, GitHub Pages fungují jen pro veřejné (public) repozitáře.** Soukromý repo + Pages vyžaduje placený plán (GitHub Pro/Team). Do tohoto repa se neukládají žádná osobní data (viz níže), takže veřejný repo je v pohodě — ale neklikej si export souboru dovnitř.
2. Nahraj obsah této složky:
   ```bash
   cd finance-tracker
   git init
   git add .
   git commit -m "Finance Tracker — PWA"
   git branch -M main
   git remote add origin https://github.com/<tvůj-účet>/finance-tracker.git
   git push -u origin main
   ```
3. V repozitáři: **Settings → Pages → Branch: main, folder: / (root) → Save.**
4. Po chvíli appka poběží na `https://<tvůj-účet>.github.io/finance-tracker/`.
5. Na mobilu otevři tu adresu v Chrome/Safari → menu → **Přidat na plochu**. Appka se pak chová jako normální appka (vlastní ikona, offline chod).

## Vlastní osobní data — NEUKLÁDAT do repozitáře

Appka data drží jen v `localStorage` prohlížeče daného zařízení — mezi zařízeními se nic nesynchronizuje samo. Pro přenos (PC → mobil, záloha, historie) slouží tlačítka **Export / Import** v appce:

- **Export (.txt)** stáhne soubor `finance_tracker_RRRR-MM-DD.txt` — uvnitř je čitelný JSON se vším: výplata, den výplaty, fixní i variabilní výdaje, kategorie, skupiny, úspory, zlato, **a aktuální stav zaplaceno/nezaplaceno včetně ručních korekcí částek**. V hlavičce je datum exportu (`_exportedAt` strojově, `_exportedDate` čitelně) — díky tomu můžeš mít víc souborů vedle sebe a dělat si z nich historii.
  - Přípona je záměrně `.txt` (ne `.json`), aby šel soubor bez potíží nahrát třeba do Google Disku/Keep. Obsah je pořád stejný JSON.
- **Import** přijme jak nový `.txt`, tak starší `.json` export — stačí vybrat soubor, appka ho rozpozná podle obsahu, ne podle přípony.

**Tyto exportované soubory si nech mimo git repozitář** (jsou to tvoje osobní finance) — buď v jiné složce na disku, v Google Disku apod. `.gitignore` v repu je navíc i tak nastavený, aby žádný `finance_tracker_*.txt`/`.json` do commitu omylem nespadl.

### Jak dostat data z PC do mobilu

1. Na PC v appce klikni **Export (.txt)**.
2. Soubor pošli do mobilu (mail, Disk, AirDrop, kabel — cokoliv).
3. Na mobilu otevři appku → **Import** → vyber ten soubor.

## Struktura repozitáře

```
index.html          – appka samotná (HTML + CSS + JS, bez buildu)
manifest.json        – PWA manifest (ikona, název, barvy)
service-worker.js     – offline cache appky
icons/                – ikony pro PWA (192px, 512px, maskable)
```

## Co dál (až budeš chtít)

Appka je teď držená jako jeden prostý HTML soubor — záměrně, kvůli jednoduchosti nasazení na GitHub Pages bez buildovacího kroku. Pokud se někdy budeme vracet k rozdělení na moduly (`state.js`/`ui.js`/...) kvůli dalšímu rozvoji (např. zabalení přes Capacitor pro Google Play), bude to vyžadovat buď bundler krok před nasazením, nebo hosting, co ES moduly zvládá (GitHub Pages to umí taky, jen je potřeba `type="module"` v `index.html`).
