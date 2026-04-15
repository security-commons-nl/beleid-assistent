# beleid-assistent — Roadmap

---

## Fase 1 — Standalone applicatie (informatiebeveiliging / BIO2)

De kern is gebouwd en getest in een productieomgeving. Fase 1 maakt dit vrij als standalone
open-source applicatie voor beleidsmedewerkers die materie-specifieke beveiligingsdocumenten
opstellen op basis van BIO2.

**Kennisbasis**
- [x] BIO2 v1.3 (9 januari 2026, definitief) → `data/bio2.json` — 148 controls
- [ ] `data/domeinen.json` — ~15 beleidsdomeinen, elk gekoppeld aan relevante BIO2-controls

**API**
- [ ] Standalone FastAPI-applicatie (los van externe afhankelijkheden)
- [ ] Beleid-agent: stelt vragen + genereert beleidsdocument per domein (streaming via SSE)
- [ ] Compliance-scan agent: toetst bestaand document aan BIO2-vereisten
- [ ] `.env.example` met Mistral EU als standaard (OpenAI-compatible, ook Ollama mogelijk)
- [ ] Alembic-migraties voor het datamodel (2 tabellen: documenten + sessies)

**Frontend**
- [ ] Documentenlijst met statusworkflow (concept → ter review → vastgesteld)
- [ ] "Nieuw document" flow: domein kiezen → vragen beantwoorden → document streamt live
- [ ] Document editor: inline bewerken, AI-vervolgvragen, export als Markdown/PDF
- [ ] Compliance-scan pagina

**Infrastructuur**
- [ ] Docker Compose setup: api + frontend + database
- [ ] README + installatie-documentatie

---

## Fase 2 — Uitbreiding kennisbasis

- [ ] BCM-domeinen op basis van ISO 22301 (bedrijfscontinuïteitsbeleid)
- [ ] AVG/privacy-domeinen (privacybeleid, DPIA-ondersteuning)
- [ ] Regelgevingscrawler: automatisch ophalen van IBD-richtlijnen en wetswijzigingen
- [ ] pgvector voor semantisch zoeken in grotere kennisbasis

---

Bijdragen aan de roadmap? Open een [issue](https://github.com/security-commons-nl/beleid-assistent/issues) of start een [discussie](https://github.com/security-commons-nl/beleid-assistent/discussions).
