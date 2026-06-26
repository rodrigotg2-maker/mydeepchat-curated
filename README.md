# mydeepchat-curated

Marketplace de **150 skills curados** de [skills.mydeepchat.com](https://skills.mydeepchat.com) para los proyectos de Rodrigo (B1, Jenny, PiskeSoul, Croata).

Seleccionados de 894 skills analizados, auditados por seguridad (prompt-injection, scripts destructivos, secretos, compatibilidad Windows): **144 OK, 6 con advertencia, 0 rechazados**.

## Instalación en Cowork

1. Plugins → Agregar Marketplace
2. URL: `rodrigotg2-maker/mydeepchat-curated`
3. Sincronizar → Instalar el plugin **mydeepchat-curated**

Los skills aparecen con namespace `mydeepchat-curated:<skill>`.

## Cobertura por proyecto

| Proyecto | Foco |
|---|---|
| Transversal | IA (eval, prompting, multimodal), Python, APIs, research, docs |
| PiskeSoul | ERP FastAPI/React, Postgres, testing, finanzas, manufactura |
| Jenny | Video (remotion, editing, whisper), diseño, ads, SEO, social |
| B1 | Scraping (firecrawl, tavily), leads, OSINT, DevOps/Docker |
| Croata | Documentos, OCR, traducción |

## Advertencias de auditoría

6 skills contienen scripts bash Unix/macOS o rutas absolutas `/etc/` en sus ejemplos
(grafana-dashboards, media-processing, plan-eng-review, design-shotgun, design-consultation,
office-hours). Son **seguros para leer como referencia**, pero sus snippets no deben ejecutarse
literalmente en Windows. Cada uno lleva una nota `<!-- NOTA AUDITORIA -->` al inicio del SKILL.md.

---

Curaduría y auditoría: 2026-06-26. Plugin v1.0.0.
