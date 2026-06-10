# UFT Tournament App — Interactieve Demo

<div align="center">
  <img src="https://ultimatefootballtournament.nl/wp-content/uploads/2021/01/Logo_wit.png" height="80" alt="UFT Logo">
  
  **Ultimate Football Tournament**
  
  [![Live Demo](https://img.shields.io/badge/Live%20Demo-Bekijk%20hier-2d5be3?style=for-the-badge)](https://daankt.github.io/uft-app)
  [![Status](https://img.shields.io/badge/Status-Demo%20versie-f5a623?style=for-the-badge)](#)
  [![Gebruikers](https://img.shields.io/badge/Capaciteit-2.000%2B%20gebruikers-00e676?style=for-the-badge)](#architectuur)
</div>

---

## Wat is dit?

Een interactieve demo van een toernooi-app op maat voor Ultimate Football Tournament. De app vervangt papieren schema's, handmatige scoreboards en WhatsApp-berichten door één digitaal systeem dat iedereen op zijn telefoon heeft.

**Drie rollen, één platform:**

| Rol | Toegang | Waarvoor |
|-----|---------|----------|
| 🏆 **UFT Organisator** | Inlogaccount | Toernooi beheren, scores corrigeren, meldingen sturen |
| 🟡 **Scheidsrechter** | 5-cijferige PIN | Live scores invoeren per wedstrijd |
| 👥 **Ouder / Coach** | QR-code scannen | Live scores volgen, schema checken, kind's stats zien |

---

## Demo uitproberen

Open `index.html` in je browser, of bekijk de [live demo](#).

### Modus 1 — Ouder/Coach (standaard)

Dit is wat alle ouders en coaches zien nadat ze een QR-code scannen bij de ingang van het toernooi. **Geen account aanmaken. Geen app downloaden.**

| Tab | Wat je ziet |
|-----|-------------|
| 🏠 **Hub** | Toernooioverzicht, countdown naar de finale, snelkoppelingen |
| ⚡ **Live** | Alle wedstrijden — live, komend en gespeeld — met doelpuntenlog |
| 📊 **Stand** | Automatisch bijgewerkte poulestanden, Poule A en B |
| 🏆 **Scorers** | Topscorers en assists, live bijgewerkt |
| 📅 **Schema** | Volledig speelschema per ronde, eigen team gemarkeerd |

> 💡 **Tip:** Laat de demo 20 seconden staan. Er valt automatisch een demo-doelpunt met een push-notificatie.

---

### Modus 2 — Scheidsrechter 🟡

Klik op **"🟡 Scheidsrechter"** bovenaan.

De scheidsrechter krijgt voor zijn wedstrijd een briefje van UFT met een 5-cijferige PIN. Hij opent de app, vult de PIN in, en is direct in zijn wedstrijd. Geen account, geen wachtwoord.

**Demo PINs om uit te proberen:**

| PIN | Wedstrijd |
|-----|-----------|
| `48291` | UFT O10 vs Ajax O10 · Veld A |
| `73614` | Man City vs UFT Blauw · Veld B |

**Na het inloggen:**
1. Je ziet het scorebord van jouw wedstrijd
2. Tik op **"Doelpunt UFT O10"** of **"Doelpunt Ajax O10"** om te scoren
3. Tik op **"↩️ Corrigeer"** om het laatste doelpunt ongedaan te maken
4. Tik op **"⏸ Rust"** bij rust
5. Tik op **"🏁 Einde"** om de wedstrijd af te sluiten

> Elk ingevoerd doelpunt verschijnt **direct** in de ouder/coach weergave als een push-notificatie.

---

### Modus 3 — Organisator 🏆

Klik op **"🏆 Organisator"** bovenaan.

Het linkerpaneel toont het live beheer-dashboard. De organisator ziet alles in één overzicht en kan direct ingrijpen.

**Wat de organisator kan:**
- Live scores aanpassen per wedstrijd (correctie)
- Wedstrijden handmatig starten en beëindigen
- Push notificaties sturen naar alle gebruikers (finale, pauze, veldwijziging)
- Realtime overzicht van alle velden en scheidsrechters

---

## Belastingsimulator (onderaan)

Sleep de slider van **1 → 2.000 gebruikers** en zie live:

| Metric | Wat het betekent |
|--------|-----------------|
| **Latency** | Hoe snel een score-update aankomt bij de gebruiker |
| **WS** | Aantal actieve WebSocket-verbindingen |
| **DB q/s** | Database queries per seconde |
| **Status** | ✅ OK / ⚡ Hoog / ⚠ Kritiek |

> Bij **1.000–2.000 gebruikers** blijft het systeem stabiel in de echte productie-versie. Zie [Architectuur](#architectuur) voor hoe dat werkt.

---

## Wat deze demo wel en niet kan

### ✅ Wat werkt in de demo
- Alle drie de rollen volledig interactief
- Scores invoeren via scheidsrechter → direct zichtbaar voor ouders
- Poulestanden automatisch berekend
- Topscorers live bijgewerkt
- Countdown naar finale
- Load-simulator voor schaalbaarheid

### ⚠️ Wat ontbreekt t.o.v. productie
- **Geen backend** — scores verdwijnen bij pagina-refresh
- **Geen echte synchronisatie** — twee browsers zien niet elkaars scores
- **Geen accounts** — organisator login is niet beveiligd
- **Geen push notificaties** — de toast is een visuele simulatie

Deze beperkingen worden opgelost in de productie-versie. Zie [Volgende stappen](#volgende-stappen).

---

## Architectuur

Voor een toernooi met 1.000–2.000 gelijktijdige gebruikers is de volgende stack aanbevolen:

```
┌─────────────────────────────────────────────────────────┐
│                    GEBRUIKERS                           │
│   Ouders/Coaches        Scheidsrechters    Organisator  │
│   (QR-code/link)        (PIN login)        (account)    │
└──────────────┬──────────────┬──────────────┬────────────┘
               │              │              │
               ▼              ▼              ▼
┌─────────────────────────────────────────────────────────┐
│              PWA (React) — Vercel / GitHub Pages        │
│              Statische hosting — gratis, wereldwijd CDN │
└──────────────────────────┬──────────────────────────────┘
                           │ WebSocket (Supabase Realtime)
                           ▼
┌─────────────────────────────────────────────────────────┐
│                   Supabase Backend                      │
│   PostgreSQL database + Realtime Broadcast channels     │
│   Edge Functions voor push notificaties (wachtrij)      │
└─────────────────────────────────────────────────────────┘
```

**Kosten in productie:**

| Component | Service | Kosten |
|-----------|---------|--------|
| Frontend hosting | Vercel of GitHub Pages | Gratis |
| Backend + database | Supabase Pro | €25/maand |
| Push notificaties | Web Push (browser-native) | Gratis |
| Peak dag upgrade | Supabase Team | €599/maand (tijdelijk) |

**Schaalbaarheid:**
- Tot 500 gelijktijdige verbindingen: Supabase Pro (€25/maand)
- Tot 5.000 verbindingen: Supabase Team (€599/maand)
- Score-updates gebruiken Broadcast channels — alle 2.000 gebruikers luisteren naar hetzelfde kanaal, niet 2.000 aparte verbindingen

---

## Volgende stappen

Om van deze demo naar een echte productie-app te gaan:

- [ ] **Sprint 1 — Backend opzetten** (1 week)
  - Supabase project aanmaken
  - Database tabellen: `toernooien`, `wedstrijden`, `teams`, `doelpunten`
  - Row Level Security instellen
  - Realtime Broadcast kanalen configureren

- [ ] **Sprint 2 — Scheidsrechter app** (1 week)
  - PIN-systeem koppelen aan database
  - Score-invoer live doorsturen via Supabase Realtime
  - Offline fallback (score lokaal opslaan, sync bij reconnect)

- [ ] **Sprint 3 — Publieke app** (1 week)
  - Live scores ophalen via WebSocket
  - Push notificaties (Web Push API)
  - QR-code generatie per toernooi

- [ ] **Sprint 4 — Organisator dashboard** (1 week)
  - Login met Supabase Auth
  - Toernooi wizard (teams, poules, schema auto-generatie)
  - Live velden-overzicht

- [ ] **Load test** (voor livegang)
  - 2.000 gelijktijdige verbindingen simuleren met k6
  - Offline fallback testen voor scheidsrechter
  - Monitoring instellen (Supabase Dashboard)

---

## Tech stack demo

```
HTML5 + CSS3 + Vanilla JavaScript
Geen frameworks, geen dependencies, geen build stap nodig
Werkt in elke moderne browser
```

## Tech stack productie (aanbevolen)

```
Frontend:  React PWA
Backend:   Supabase (PostgreSQL + Realtime + Auth + Edge Functions)
Hosting:   Vercel (frontend) + Supabase Cloud (backend)
Push:      Web Push API (geen App Store nodig)
Testing:   k6 voor load tests
```

---

## Contact

**Ultimate Football Tournament**
- Website: [ultimatefootballtournament.nl](https://www.ultimatefootballtournament.nl)
- Email: info@ultimatefootballtournament.nl

---

<div align="center">
  <sub>Demo gebouwd als concept voor UFT · 2025</sub>
</div>
