# beleid-assistent — Concept

> AI-ondersteunde beleidsondersteuning voor Nederlandse publieke organisaties.

---

## Het probleem

Beleidsmedewerkers bij gemeenten en andere publieke organisaties moeten materie-specifiek beveiligingsbeleid schrijven: een toegangsbeveiligingsbeleid, een incidentbeheerbeleid, een cryptografiebeleid — per domein een apart document, gebaseerd op de BIO2-vereisten. Dat werk kost veel tijd, vereist kennis van de normen, en leidt zonder goede ondersteuning tot generieke of onvolledige documenten.

Bestaande tools zijn generiek. Een assistent die de BIO2-normen kent, begrijpt wat een gemeente nodig heeft, en een professioneel narratief beleidsdocument genereert — bestaat niet als open-source oplossing.

---

## Wat het is

Een AI-assistent die beleidsmedewerkers helpt bij het opstellen van **materie-specifieke beleidsdocumenten** voor informatiebeveiliging, op basis van de Baseline Informatiebeveiliging Overheid 2 (BIO2 v1.3).

De gebruiker kiest een beleidsdomein (bijv. "Toegangsbeveiliging"), voert een adaptief interview met de assistent, en krijgt een volledig beleidsdocument met:

- Inleiding & aanleiding
- Juridisch kader (BIO2-controls + wet- en regelgeving)
- Doelstelling & scope
- Beleidsprincipes & uitgangspunten
- Rollen & verantwoordelijkheden
- Handhaving
- Evaluatie & herziening

### Hoe het gesprek loopt

1. **Optioneel: bronnen uploaden** — bestaand IB-beleid, reglementen of vergelijkbare documenten
   (`.md`, `.txt`, `.pdf`). De assistent gebruikt ze om consistent te zijn met bestaande terminologie en toon.
2. **Adaptief interview** — de assistent stelt vragen totdat hij genoeg weet om kwalitatief te
   schrijven. Geen vaste vragenlimiet: simpel beleid vraagt 3-4 vragen, complex beleid 8-10.
   "Ik weet het niet" is een geldig antwoord — de assistent markeert aannames in het document.
3. **Briefing-samenvatting** — vóór de assistent begint te schrijven toont de tool een samenvatting
   van wat hij heeft begrepen (organisatie, scope, doelgroep, toon, rollen, aannames). De
   gebruiker keurt goed of past antwoorden aan.
4. **Generatie** — het document wordt live gestreamd (Server-Sent Events) zodat de gebruiker
   real-time ziet hoe het wordt opgebouwd.

Elk document is gelabeld als `AI CONCEPT — verifieer handmatig`. De beleidsmedewerker beoordeelt, past aan en stelt vast.

Naast het schrijven ondersteunt de tool een **compliance-scan**: bestaande documenten worden getoetst aan de BIO2-vereisten voor het betreffende domein.

---

## Beleidsdomeinen (v1)

De tool werkt met ~15 beleidsdomeinen die aansluiten op de IBD-sjablonenset voor gemeenten. Elk domein groepeert de relevante BIO2-controls:

| Domein | Voorbeeldcontrols |
|--------|------------------|
| Informatiebeveiligingsbeleid | 5.01 |
| Organisatie & rollen | 5.02–5.08 |
| Bedrijfsmiddelenbeheer | 5.09–5.14 |
| Toegangsbeveiliging | 5.15–5.18 |
| Leveranciersmanagement | 5.19–5.23 |
| Incidentbeheer | 5.24–5.28 |
| Bedrijfscontinuïteit | 5.29–5.30 |
| Compliancy & audit | 5.36–5.37 |
| Personeel & bewustzijn | 6.01–6.08 |
| Fysieke beveiliging | 7.01–7.14 |
| Technische maatregelen | 8.01–8.34 |

---

## Voor wie

- Beleidsmedewerkers bij gemeenten en gemeenschappelijke regelingen
- Juridisch adviseurs in de publieke sector
- Teams die werken aan informatiebeveiligingsbeleid

---

## Wat het niet is

- Geen ICF-tool — de assistent schrijft beleidsdocumenten (narratief), geen control-frameworks met 6 W's per maatregel
- Geen risicoanalyse-tool — risicoanalyse is CISO-werk en valt buiten scope
- Geen BCM- of AVG-tool — v1 richt zich uitsluitend op informatiebeveiliging (BIO2); BCM en AVG zijn toekomstige uitbreidingen
- Geen juridisch advies — de assistent ondersteunt, een jurist beoordeelt
- Geen black box — elke AI-uitvoer is herleidbaar tot de BIO2-bronnen die zijn gebruikt
- Geen systeem dat externe data verzendt zonder toestemming

---

## Technische richting

- **Backend:** FastAPI + Python, async SQLAlchemy
- **LLM:** OpenAI-compatible interface, Mistral EU als standaard
- **Streaming:** Server-Sent Events voor real-time weergave tijdens generatie
- **Kennisbasis v1:** `data/bio2.json` (148 controls, BIO2 v1.3) + `data/domeinen.json` — geen vector-database nodig
- **Frontend:** Next.js + Tailwind CSS
- **Hosting:** Docker Compose, lokaal of EU-gehost

Zie [architectuur.md](architectuur.md) voor de technische details.

---

## Status

Concept-fase. De kern van de functionaliteit is gebaseerd op bewezen onderdelen uit een productieomgeving en wordt vrijgemaakt als standalone open-source module.

Zie [ROADMAP.md](../ROADMAP.md) voor de planning.

Bijdragen welkom via [CONTRIBUTING.md](https://github.com/security-commons-nl/.github/blob/main/CONTRIBUTING.md).
