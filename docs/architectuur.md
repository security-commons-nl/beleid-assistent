# beleid-assistent — Architectuur

> Voor contributors die begrijpen willen hoe de beleid-assistent is opgebouwd.

---

## Overzicht

```
Gebruiker → Frontend (Next.js) → API (FastAPI) → Agents → LLM (Mistral EU)
                                              ↓
                                    Regelgevingsdatabase (pgvector)
                                              ↑
                                    Regelgevingscrawler (Celery)
```

---

## Componenten

### 1. LLM-client

OpenAI-compatible interface, configureerbaar via omgevingsvariabelen:

| Variabele | Standaard | Beschrijving |
|-----------|-----------|--------------|
| `AI_API_BASE` | `https://api.mistral.ai/v1` | API-endpoint |
| `AI_API_KEY` | — | API-sleutel |
| `AI_MODEL_NAME` | `mistral-small-latest` | Taalmodel |
| `AI_EMBEDDING_MODEL` | `mistral-embed` | Embedding-model voor RAG |

Alternatief: volledig lokaal via Ollama (`http://localhost:11434/v1`). Geen vendor lock-in.

### 2. AI-agents

Vijf gespecialiseerde agents, elk met een eigen verantwoordelijkheid:

| Agent | Bestand | Verantwoordelijkheid |
|-------|---------|---------------------|
| `beleid_agent` | `agents/beleid_agent.py` | Genereert concept-beleidsdocumenten via gesprek |
| `compliance_scan_agent` | `agents/compliance_scan_agent.py` | Scant documenten tegen regelgeving |
| `impact_agent` | `agents/impact_agent.py` | Analyseert impact van regelgevingswijzigingen |
| `samenvatting_agent` | `agents/samenvatting_agent.py` | Vat wet- en regelgeving samen |
| `regelgeving_scan_agent` | `agents/regelgeving_scan_agent.py` | Ontdekt relevante regelgeving voor een domein |

Elke agent:
1. Haalt relevante regelgeving op via RAG
2. Voert een gesprek met de gebruiker
3. Genereert een concept-document (gelabeld `AI CONCEPT — verifieer handmatig`)
4. Logt elke LLM-aanroep voor auditbaarheid

### 3. RAG-pipeline

Semantisch zoeken via pgvector cosine similarity.

**Kennislagen:**
- **Normatief** — gedeelde regelgevingsteksten: AVG, Omgevingswet, sectorale verordeningen, IBD-richtlijnen
- **Organisatie** — organisatiespecifieke beleidsdocumenten en eerder opgeleverde outputs

Bij elke agent-aanroep worden de top-3 meest relevante fragmenten als context meegestuurd.

### 4. Regelgevingscrawler

Celery-taak die periodiek wet- en regelgeving ophaalt en indexeert:
- Ophalen van regelgevingsdocumenten (PDF, HTML)
- Chunken en embedden
- Opslaan in pgvector

Dit houdt de kennisbank actueel zonder handmatige interventie.

### 5. Streaming (SSE)

Agents streamen hun output via Server-Sent Events, zodat de gebruiker real-time ziet hoe het concept wordt opgebouwd. Dit is vooral relevant bij langere documenten.

### 6. Frontend

Next.js — zelfde stack als grc-platform. Per agent een aparte pagina; gedeelde componenten voor chat-interface en document-preview.

---

## Datamodel (kern)

| Tabel | Wat het bevat |
|-------|--------------|
| `beleid_documenten` | Gegenereerde en goedgekeurde beleidsdocumenten |
| `regelgeving` | Geïndexeerde wet- en regelgevingsteksten |
| `regelgeving_chunks` | Vectorrepresentaties voor RAG |
| `agent_sessies` | Gesprekgeschiedenis per agent-aanroep |
| `ai_audit_log` | Elke LLM-aanroep (model, tokens, agent, tijdstip) |

---

## Auditbaarheid

Elke LLM-aanroep wordt vastgelegd: welke agent, welk model, hoeveel tokens, op welk tijdstip. Gegenereerde documenten zijn altijd herleidbaar tot de regelgevingsfragmenten die als context zijn gebruikt.

---

## Designkeuzes

**Waarom vijf losse agents en geen één generieke?**  
Elk beleidsdomein vereist een andere aanpak: een compliance-scan is fundamenteel anders dan het schrijven van een nieuw beleidsdocument. Losse agents met gerichte systeem-prompts presteren beter dan één generieke assistent.

**Waarom Celery voor de crawler?**  
Regelgevingsupdates moeten asynchroon en periodiek verwerkt worden zonder de API te blokkeren. Celery is de standaardkeuze voor Python-achtergrondtaken.

**Waarom dezelfde stack als grc-platform?**  
GRC en beleid zijn verwante domeinen — organisaties die het ene gebruiken, hebben vaak ook het andere nodig. Gedeelde technologie (FastAPI, pgvector, Next.js) maakt integratie eenvoudiger en de community groter.
