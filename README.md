# Cammino · App iPhone

PWA (Progressive Web App) da aggiungere alla Home Screen di iPhone con Safari.
Mostra ogni giorno i punti del *Cammino* di San Josemaría Escrivá,
procedendo in ordine per capitoli e gruppi di punti.

---

## Struttura dei file

```
cammino-app/
├── index.html    — App principale (PWA)
├── worker.js     — Cloudflare Worker (proxy CORS verso escriva.org)
├── manifest.json — Metadati PWA per iOS
└── icon.png      — Icona 180×180 px (da creare — vedi sotto)
```

---

## Logica dei gruppi giornalieri

- 999 punti in 46 capitoli → circa **197 giorni** per completare il libro, poi ricomincia.
- Ogni giorno mostra **5 punti** dello stesso capitolo, in ordine sequenziale.
- **Regola intelligente per i resti:**
  - Resto 0 → gruppi esatti di 5
  - Resto 1 → ultimo giorno del capitolo ha **6 punti** (non 1 isolato)
  - Resto 2 → ultimo giorno del capitolo ha **7 punti** (non 2 isolati)
  - Resto 3 o 4 → ultimo giorno ha 3 o 4 punti (accettabile)
- Il contatore avanza da **1 Gennaio 2025** (giorno 0) e cicla ogni ~197 giorni.
- Le frecce ‹ › permettono di navigare tra i giorni vicini; "Vai ad oggi" riporta al giorno corrente.

---

## Deploy — passo dopo passo

### 1. Cloudflare Worker (obbligatorio — risolve il blocco CORS)

1. Vai su **dash.cloudflare.com** → accedi o crea un account gratuito.
2. Sidebar → **Workers & Pages** → **Create application** → **Create Worker**.
3. Assegna un nome, es. `cammino-proxy`.
4. Clicca **Edit code**, incolla l'intero contenuto di `worker.js`.
5. Clicca **Deploy**.
6. Copia l'URL mostrato (es. `https://cammino-proxy.tuonome.workers.dev`).
7. Apri `index.html` e alla riga:
   ```js
   const WORKER_URL = 'https://YOUR_WORKER_NAME.workers.dev';
   ```
   Sostituisci con il tuo URL.

### 2. Icona (180×180 px)

Crea un file `icon.png` 180×180 px (sfondo caldo beige, una croce o iniziale).
Puoi usare **favicon.io** → "Text to favicon" con la lettera **C**,
oppure qualsiasi editor grafico.

### 3. GitHub Pages (hosting gratuito)

1. Vai su **github.com** → **New repository**, nome suggerito: `cammino-app`.
2. Assicurati che sia **Public**.
3. Carica i quattro file: `index.html`, `manifest.json`, `icon.png`, `worker.js`
   (worker.js non viene usato da GitHub Pages, ma tienilo nel repo come backup).
4. Vai in **Settings → Pages → Source**: seleziona branch `main`, cartella `/ (root)`.
5. Salva. Dopo pochi secondi l'URL sarà:
   `https://TUOUSERNAME.github.io/cammino-app/`

### 4. Aggiungi alla Home Screen di iPhone

1. Apri **Safari** su iPhone (non Chrome o Firefox — solo Safari supporta le PWA).
2. Vai all'URL GitHub Pages.
3. Tocca l'icona **Condividi** (quadrato con freccia su, in basso).
4. Scorri e tocca **"Aggiungi a Home Screen"**.
5. Modifica il nome se vuoi → **Aggiungi**.
6. L'app appare come icona nativa sul desktop e si apre a schermo intero.

---

## Note tecniche

- Il Worker effettua il fetch della pagina capitolo su escriva.org, la mantiene in
  cache per 7 giorni (il libro non cambia) e restituisce il testo dei punti richiesti.
- In caso di errore di rete, l'app mostra un messaggio con pulsante "Riprova".
- Il design si adatta automaticamente a modalità chiara/scura (dark mode iOS).
- Nessun framework esterno: solo HTML, CSS e JavaScript vanilla.
