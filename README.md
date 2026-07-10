# piattaforma-ricette-cucina-web

Piattaforma web per creare, organizzare e condividere ricette di cucina.

## Overview

# Product Requirements Document (PRD)

## 1. Project Overview

**Nome Progetto:** piattaforma-ricette-cucina-web

La piattaforma-ricette-cucina-web è una soluzione web moderna che consente agli utenti di creare, organizzare, condividere e pianificare ricette di cucina in modo centralizzato e intuitivo. L’obiettivo è risolvere la frammentazione delle ricette (distribuite tra siti, appunti, social) e offrire strumenti avanzati per la gestione personale e la condivisione pubblica, inclusa la pianificazione settimanale dei pasti e la generazione automatica della lista della spesa.

---

## 2. Goals & Success Metrics

### Obiettivi

- Centralizzare la gestione delle ricette personali e pubbliche.
- Facilitare la pianificazione settimanale dei pasti.
- Permettere la condivisione semplice e sicura delle ricette.
- Automatizzare la creazione della lista della spesa.
- Garantire la moderazione efficace dei contenuti pubblici.

### Success Metrics

- **% di utenti attivi mensili** che creano o modificano almeno una ricetta.
- **Tempo medio di risposta delle API** < 2 secondi per il 95% delle richieste.
- **% di ricerche che restituiscono risultati rilevanti** (tasso di click su risultati).
- **Numero di ricette pubbliche condivise tramite link**.
- **Tasso di successo nella generazione/esportazione della lista della spesa** (>95%).
- **Tempo medio di risoluzione delle segnalazioni/moderazioni** da parte degli amministratori.

---

## 3. Target Users

### Utenti Finali

- **Utente Registrato**
  - Crea, modifica, elimina e organizza le proprie ricette.
  - Può rendere pubbliche le ricette per condividerle.
  - Salva ricette tra i preferiti.
  - Pianifica i pasti settimanali.
  - Consulta ed esporta la lista della spesa.
  - Commenta le ricette pubbliche.

- **Amministratore**
  - Modera ricette pubbliche, immagini, commenti e segnalazioni.
  - Gestisce utenti (attivazione, sospensione, eliminazione).
  - Gestisce categorie e tag.
  - Consulta log delle attività amministrative.
  - Ripristina contenuti rimossi.

---

## 4. Core Features

### 4.1 Gestione Account

- **Registrazione e autenticazione** (via email/password, Laravel Sanctum).
- **Gestione profilo** (modifica dati personali, cambio password).
- **Recupero password** (email di reset).
- **Ruoli**: Utente, Amministratore.

### 4.2 Gestione Ricette

- **CRUD ricette**: creazione, modifica, eliminazione.
- **Campi ricetta**: titolo, descrizione, ingredienti (con quantità), preparazione, tempo di cottura, difficoltà, immagini, categoria, tag, visibilità (privata/pubblica).
- **Caricamento immagini**: upload su storage S3 compatibile.
- **Gestione visibilità**: private per default, pubbliche su scelta dell’utente.
- **Condivisione**: link pubblico per ricette pubbliche (accessibile anche senza login).

### 4.3 Categorie e Tag

- **Categorie**: ogni ricetta appartiene a una categoria (es. primi, dolci).
- **Tag**: sistema N:N per etichettare ricette con più tag.

### 4.4 Ricerca e Filtri

- **Ricerca ricette**: per nome, ingrediente, categoria.
- **Filtri**: tempo di preparazione, difficoltà, tipologia.
- **Ricette pubbliche**: solo queste compaiono nei risultati per altri utenti e visitatori.

### 4.5 Preferiti

- **Salvataggio ricette**: aggiunta/rimozione dai preferiti personali.
- **Gestione lista preferiti**: visualizzazione, rimozione.

### 4.6 Pianificazione Pasti

- **Calendario settimanale**: pianificazione ricette per colazione/pranzo/cena in date specifiche.
- **Visualizzazione pianificazione**: calendario interattivo.
- **Gestione pianificazioni**: aggiunta, modifica, cancellazione.

### 4.7 Lista della Spesa

- **Generazione automatica**: aggregazione ingredienti dalle ricette pianificate.
- **Gestione lista**: spunta ingredienti acquistati, modifica quantità.
- **Esportazione**: PDF, invio via email (CSV e integrazione app note in futuro).

### 4.8 Moderazione e Amministrazione

- **Gestione utenti**: attivazione, sospensione, eliminazione.
- **Moderazione ricette pubbliche**: modifica, rimozione.
- **Moderazione immagini, commenti, segnalazioni**.
- **Gestione categorie e tag**.
- **Log attività amministrative**.
- **Ripristino contenuti rimossi**.

---

## 5. Technical Architecture

### 5.1 Stack Tecnologico

- **Backend**: Laravel (PHP)
- **Frontend**: Vue.js (SPA)
- **Database**: MySQL
- **Autenticazione**: Laravel Sanctum (token-based)
- **Storage immagini**: S3 compatibile
- **Containerizzazione**: Docker

### 5.2 Modello Dati Principale

#### Utente

| Campo      | Tipo     | Note                |
|------------|----------|---------------------|
| id         | int      | PK                  |
| nome       | string   |                     |
| email      | string   | univoco             |
| password   | string   | hash                |
| ruolo      | enum     | utente, admin       |

#### Ricetta

| Campo              | Tipo     | Note                    |
|--------------------|----------|-------------------------|
| id                 | int      | PK                      |
| utente_id          | int      | FK -> Utente            |
| titolo             | string   |                         |
| descrizione        | text     |                         |
| tempo_preparazione | int      | minuti                  |
| difficoltà         | enum     | facile, media, difficile|
| visibilità         | enum     | privata/pubblica        |
| categoria_id       | int      | FK -> Categoria         |

#### Categoria

| Campo | Tipo   | Note |
|-------|--------|------|
| id    | int    | PK   |
| nome  | string |      |

#### Tag

| Campo | Tipo   | Note |
|-------|--------|------|
| id    | int    | PK   |
| nome  | string |      |

#### Ricetta_Tag (pivot N:N)

| ricetta_id | int | FK |
| tag_id     | int | FK |

#### Ingredienti (per ricetta)

| id         | int    | PK   |
| ricetta_id | int    | FK   |
| nome       | string |      |
| quantità   | string |      |

#### PianificazionePasti

| id         | int    | PK   |
| utente_id  | int    | FK   |
| ricetta_id | int    | FK   |
| data       | date   |      |
| pasto      | enum   | colazione, pranzo, cena |

#### Preferito

| id         | int    | PK   |
| utente_id  | int    | FK   |
| ricetta_id | int    | FK   |

#### ListaSpesa

| id         | int    | PK   |
| utente_id  | int    | FK   |
| ingrediente| string |      |
| quantità   | string |      |
| acquistato | bool   |      |

### 5.3 API REST Principali

#### Ricette

- `GET /api/recipes`
- `GET /api/recipes/{id}`
- `POST /api/recipes`
- `PUT /api/recipes/{id}`
- `DELETE /api/recipes/{id}`

**Esempio payload creazione:**
```json
{
  "title": "Lasagne",
  "description": "Ricetta tradizionale",
  "category_id": 2,
  "tags": [1, 4],
  "preparation_time": 60,
  "visibility": "private"
}
```

#### Pianificazione pasti

- `GET /api/meal-plans`
- `POST /api/meal-plans`
- `DELETE /api/meal-plans/{id}`

**Esempio payload:**
```json
{
  "recipe_id": 15,
  "date": "2026-07-15",
  "meal": "dinner"
}
```

#### Lista della spesa

- `GET /api/shopping-list`
- `GET /api/shopping-list/export` (PDF, email)

#### Preferiti

- `GET /api/favorites`
- `POST /api/favorites`
- `DELETE /api/favorites/{id}`

#### Autenticazione

- `POST /api/register`
- `POST /api/login`
- `POST /api/logout`
- `POST /api/password/forgot`
- `POST /api/password/reset`

#### Moderazione/Admin

- `GET /api/admin/users`
- `PUT /api/admin/users/{id}`
- `DELETE /api/admin/users/{id}`
- `GET /api/admin/recipes`
- `PUT /api/admin/recipes/{id}`
- `DELETE /api/admin/recipes/{id}`
- `GET /api/admin/logs`
- `POST /api/admin/restore`

### 5.4 Autorizzazioni e Sicurezza

- **Utente**: può gestire solo le proprie ricette, preferiti, pianificazioni.
- **Amministratore**: accesso completo a utenti, ricette pubbliche, moderazione.
- **Ricette pubbliche**: visibili a tutti, anche non autenticati tramite link.
- **Ricette private**: visibili solo al proprietario.
- **RBAC**: predisposizione per ruoli intermedi futuri.

---

## 6. Non-Functional Requirements

- **Interfaccia responsive**: usabile da desktop, tablet, mobile.
- **Performance**: tempo di risposta API < 2 secondi (95° percentile).
- **Scalabilità**: architettura modulare, container Docker, storage S3.
- **Sicurezza**: autenticazione token-based, autorizzazioni granulari, validazione input, rate limiting API.
- **Affidabilità**: backup periodici DB, logging attività critiche.
- **Internazionalizzazione**: predisposizione per multi-lingua.
- **Accessibilità**: rispetto standard WCAG 2.1 AA.

---

## 7. Out of Scope (v1)

- Integrazione con app di note/promemoria (es. Google Keep, Apple Notes).
- Esportazione lista spesa in CSV.
- Notifiche push/mobile.
- Sistema di rating/valutazione ricette.
- Ruoli intermedi/granulari (oltre utente/admin).
- Social login (Google, Facebook, ecc.).
- Versionamento avanzato delle ricette.
- Analytics avanzati per utenti finali.
- Marketplace di ingredienti o acquisto online.

---

## 8. Open Questions

- **Commenti**: Modalità di moderazione automatica (es. filtri antispam) da definire.
- **Gestione immagini**: Limiti di dimensione/formato da specificare.
- **Importazione ricette**: Da altre piattaforme o tramite parsing automatico? (non previsto v1, ma da valutare per roadmap).
- **Gestione allergeni**: Prevedere campi specifici per allergeni/etichette alimentari?
- **Gestione notifiche**: Email per eventi (es. ricetta pubblicata, pianificazione pasti) da dettagliare.
- **Privacy**: Policy per dati sensibili e gestione account eliminati da approfondire.

---

**Fine documento**