# beleid-assistent — Concept

> AI-ondersteunde beleidsondersteuning voor Nederlandse publieke organisaties.

---

## Het probleem

Beleidsmedewerkers bij gemeenten en andere publieke organisaties werken met een veelheid aan wet- en regelgeving: AVG, Omgevingswet, Gemeentewet, sectorale verordeningen, rijksbeleid. Het bijhouden van wijzigingen, het vertalen naar intern beleid, en het schrijven van beleidsdocumenten kost veel tijd — en gaat regelmatig mis door gemiste updates of inconsistente interpretaties.

Bestaande tools zijn generiek. Een beleidsassistent die de Nederlandse publieke sector kent — inclusief gemeentelijke structuren, begrippenkaders en relevante wetgeving — bestaat niet als open-source oplossing.

---

## Wat het is

Een set van vijf gespecialiseerde AI-agents die beleidsmedewerkers ondersteunen bij:

| Agent | Wat het doet |
|-------|-------------|
| **Beleidsschrijver** | Genereert concept-beleidsdocumenten op basis van gesprekscontext en regelgeving |
| **Compliance-scanner** | Scant bestaande documenten tegen actuele wet- en regelgeving |
| **Impact-analyzer** | Analyseert impact van nieuwe of gewijzigde regelgeving op bestaand beleid |
| **Samenvatting** | Vat wetgeving en regelgeving samen in begrijpelijke taal |
| **Regelgeving-scanner** | Ontdekt welke regelgeving relevant is voor een gegeven beleidsdomein |

Alle agents genereren **concepten** — elk document is gelabeld als `AI CONCEPT — verifieer handmatig`. De medewerker beoordeelt, past aan en stelt vast.

---

## Voor wie

- Beleidsmedewerkers bij gemeenten en gemeenschappelijke regelingen
- Juridisch adviseurs in de publieke sector
- Griffiers en raadsadviseurs
- Teams die werken aan privacybeleid, informatiebeveiligingsbeleid, of sectorale beleidsplannen

---

## Wat het niet is

- Geen juridisch advies — de assistent ondersteunt, een jurist beoordeelt
- Geen vervanging van beleidsjuristen of FG's
- Geen systeem dat externe data verzendt zonder toestemming
- Geen black box — elke AI-uitvoer is herleidbaar tot de bronnen die zijn gebruikt

---

## Technische richting

- **Backend:** FastAPI + Python, async SQLAlchemy
- **LLM:** OpenAI-compatible interface, Mistral EU als standaard
- **Streaming:** Server-Sent Events voor real-time antwoorden
- **Regelgevingscrawler:** geautomatiseerd ophalen en indexeren van wet- en regelgeving
- **Frontend:** Next.js (zelfde stack als grc-platform)
- **Hosting:** Docker Compose, lokaal of EU-gehost

Zie [architectuur.md](architectuur.md) voor de technische details.

---

## Status

Concept-fase. De kern van de functionaliteit is uitgewerkt en getest in een productieomgeving. Het project wordt momenteel vrijgemaakt als standalone open-source module.

Zie [ROADMAP.md](../ROADMAP.md) voor de planning.

Bijdragen welkom via [CONTRIBUTING.md](https://github.com/security-commons-nl/.github/blob/main/CONTRIBUTING.md).
