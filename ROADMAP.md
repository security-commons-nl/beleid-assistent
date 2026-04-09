# beleid-assistent — Roadmap

---

## Fase 1 — Standalone applicatie

De kern van de beleid-assistent is uitgewerkt en getest. In fase 1 wordt deze omgezet naar een volledig standalone open-source applicatie:

- [ ] Standalone FastAPI-applicatie (los van externe afhankelijkheden)
- [ ] Docker Compose setup: api + frontend + database
- [ ] JWT-authenticatie (zelfde patroon als grc-platform)
- [ ] `.env.example` met Mistral EU als standaard
- [ ] Alembic-migraties voor het datamodel
- [ ] README + installatie-documentatie

## Fase 2 — Regelgevingskennisbank

- [ ] Regelgevingscrawler: automatisch ophalen en indexeren van Nederlandse wet- en regelgeving
- [ ] Startset: AVG, Omgevingswet, Gemeentewet, IBD-richtlijnen
- [ ] Update-mechanisme: periodieke verversing via Celery

## Fase 3 — Integratie met grc-platform

- [ ] cisochat-integratie: beleidscontext beschikbaar in GRC-vragen
- [ ] Gedeelde kennisbank: normatieve documenten gedeeld tussen beleid-assistent en grc-platform
- [ ] Single sign-on tussen de twee platforms

---

Bijdragen aan de roadmap? Open een [issue](https://github.com/security-commons-nl/beleid-assistent/issues) of start een [discussie](https://github.com/security-commons-nl/beleid-assistent/discussions).
