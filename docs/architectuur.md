# beleid-assistent — Architectuur

> Voor contributors die begrijpen willen hoe de beleid-assistent is opgebouwd.

---

## Overzicht

```
Browser (Next.js)
        ↕
  FastAPI (Python)
        ↕                    ↕
  PostgreSQL           Mistral EU (LLM)
  (documenten)         (via API-key in .env)
        ↑
  data/bio2.json
  data/domeinen.json
  (statische kennisbasis)
```

Geen vector-database in v1. De BIO2-kennisbasis is een statisch JSON-bestand dat bij elke
agent-aanroep als platte tekst in de prompt wordt meegestuurd.

---

## Componenten

### 1. Kennisbasis (statisch)

Twee JSON-bestanden in de repo:

| Bestand | Inhoud |
|---------|--------|
| `data/bio2.json` | 148 BIO2-controls v1.3 (id, titel, iso_maatregel, overheidsmaatregel) |
| `data/domeinen.json` | ~15 beleidsdomeinen, elk met een lijst van relevante control-ID's |

Bij een agent-aanroep laadt de API de controls voor het gekozen domein en voegt die als context
toe aan de prompt. Simpel en snel genoeg voor v1.

### 2. LLM-client

OpenAI-compatible interface, configureerbaar via omgevingsvariabelen:

| Variabele | Standaard | Beschrijving |
|-----------|-----------|--------------|
| `AI_API_BASE` | `https://api.mistral.ai/v1` | API-endpoint |
| `AI_API_KEY` | — | API-sleutel |
| `AI_MODEL_NAME` | `mistral-small-latest` | Taalmodel |

Alternatief: volledig lokaal via Ollama (`http://localhost:11434/v1`). Geen vendor lock-in.

### 3. AI-agents

Twee agents voor v1:

| Agent | Verantwoordelijkheid |
|-------|---------------------|
| `beleid_agent` | Voert adaptief interview, maakt briefing-samenvatting, genereert beleidsdocument (streaming) |
| `compliance_scan_agent` | Toetst een bestaand document aan de BIO2-vereisten voor een domein |

**Persona (system prompt):** Senior beleidsadviseur voor Nederlandse publieke sector. Schrijft
zakelijk, objectief en feitelijk op taalniveau B1/B2. Gebruikt BIO2-termen in strikte betekenis.
Tutoyeert nooit. AI is co-piloot: stelt voor, mens beslist (AVG Art. 22).

**Adaptief interview.** De `beleid_agent` stelt vragen totdat een informatiebehoefte-checklist
gevuld is: organisatiecontext, scope, doelgroep, tone & ambitieniveau, juridisch kader, rollen,
handhaving, domeinspecifieke kennis, evaluatiecyclus. Geen harde vragenlimiet — adaptieve diepte
per domein. Meerdere checklist-items kunnen in één vraag samen. "Ik weet het niet" → agent
markeert aannames in het document.

**Bronnen uploaden.** Vóór het interview kan de gebruiker bestaande documenten uploaden
(`.md`, `.txt`, `.pdf`). Tekst wordt geëxtraheerd en als context meegegeven aan de agent voor
terminologie- en toonconsistentie.

**Briefing-samenvatting.** Na het interview, vóór generatie, produceert de agent een beknopte
samenvatting van wat hij heeft begrepen (inclusief aannames). Gebruiker keurt goed of past
antwoorden aan. Pas daarna start de streaming-generatie.

Beide agents:
1. Laden de relevante BIO2-controls voor het domein uit `data/domeinen.json` + `data/bio2.json`
2. Voeren een gesprek met de gebruiker
3. Genereren output gelabeld als `AI CONCEPT — verifieer handmatig`
4. Loggen elke LLM-aanroep voor auditbaarheid

### 4. Streaming (SSE)

De beleid_agent streamt het beleidsdocument via Server-Sent Events, zodat de gebruiker real-time
ziet hoe het document wordt opgebouwd.

### 5. Frontend

Next.js + Tailwind CSS — geen afhankelijkheid van externe UI-bibliotheken. Vier pagina's:

| Route | Functie |
|-------|---------|
| `/` | Documentenlijst met status |
| `/nieuw` | Domein kiezen → vragen beantwoorden → document genereren |
| `/[id]` | Document bekijken, bewerken, exporteren |
| `/scan` | Compliance-scan van bestaand document |

---

## Datamodel

Twee tabellen — minimaal voor v1:

| Tabel | Inhoud |
|-------|--------|
| `beleid_documenten` | Gegenereerde beleidsdocumenten (titel, domein, inhoud als Markdown, status, timestamps) |
| `agent_sessies` | Gespreksgeschiedenis per document: vragen, antwoorden, briefing, geüploade bronnen |

Statussen: `concept` → `ter_review` → `vastgesteld` → `gearchiveerd`

---

## Auditbaarheid

Elke LLM-aanroep wordt vastgelegd (agent, model, tokens, tijdstip). Gegenereerde documenten
vermelden welke BIO2-controls als context zijn gebruikt.

---

## Designkeuzes

**Waarom geen vector-database in v1?**
De kennisbasis (BIO2-controls per domein) is klein genoeg om als platte tekst in de prompt mee
te sturen. pgvector voegt complexiteit toe zonder merkbaar voordeel op deze schaal. Fase 2
(regelgevingscrawler) introduceert pgvector wanneer dat nodig is.

**Waarom domeinen en niet losse controls?**
Een beleidsmedewerker schrijft ~15 materie-specifieke beleidsdocumenten, niet 148. Elk document
dekt een domein (bijv. "Toegangsbeveiliging") en beschrijft de beleidsprincipes voor alle
relevante BIO2-controls in dat domein. Één document per control is onwerkbaar in de praktijk.

**Waarom Mistral EU als standaard?**
Gegevenssoevereiniteit — verwerking blijft binnen de EU. Volledig uitwisselbaar via de
OpenAI-compatible interface.

**Waarom dezelfde agentbasis als grc-platform?**
GRC en beleid zijn verwante domeinen. Gedeelde technologie maakt toekomstige integratie
eenvoudiger en de community groter.
