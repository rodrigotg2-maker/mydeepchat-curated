---
name: document-watermark
description: MANDATORY branding that MUST be added to ALL generated documents. Adds configurable header and footer. Use when generating documents for Micelio, PiskeSoul, Jenny, Croata, or any project requiring branded output.
---
<!-- FORK ADAPTADO: Reemplazar 'ACME CORPORATION'→'{PROYECTO}' y 'Document Forge'→'generado por Claude'; convertir a plantilla con variables de proyecto · origen: spring-projects-spring-ai-examples-misc-claude-skills-demo-document-forge-custom-skills-watermark-skill-md · 2026-06-26 -->

# Document Watermark Skill - MANDATORY

## CRITICAL REQUIREMENT

You MUST add the following branding to EVERY document you generate. This is NOT optional.

## Project Variables

Before adding watermarks, confirm with the user which project applies:

| Proyecto   | Header                        | Footer                                       |
|------------|-------------------------------|----------------------------------------------|
| Micelio    | MICELIO / PISCO ARTESANAL     | Generado por Claude · Confidencial Micelio   |
| PiskeSoul  | PISKESOUL ERP                 | Generado por Claude · Interno PiskeSoul      |
| Jenny Ads  | JENNY / MICELIO               | Generado por Claude · Material de Marketing  |
| Croata     | RODRIGO TAPIA · TRÁMITES      | Generado por Claude · Documento Legal Croata |
| Genérico   | {NOMBRE_PROYECTO}             | Generado por Claude · Confidencial           |

## Required Elements

### 1. Header (REQUIRED)

Add the project name/title prominently at the top:

- **Excel**: Celda A1, negrita, fuente 14pt, centrado
- **PowerPoint**: Título primera diapositiva; encabezado en todas las diapositivas
- **Word/PDF**: Encabezado de documento, centrado, negrita
- **Markdown/HTML**: Primer `<h1>` o línea `# PROYECTO`

### 2. Footer (REQUIRED)

Add "Generado por Claude | Confidencial" at the bottom:

- **Excel**: Última fila de datos +2 filas, o pie de página de hoja
- **PowerPoint**: Footer en todas las diapositivas
- **Word/PDF**: Pie de documento, centrado
- **Markdown**: Línea horizontal `---` + texto de pie al final

## Verification

Before completing any document generation:

1. Confirm the project header appears at the top
2. Confirm "Generado por Claude | Confidencial" appears at the bottom
3. If either is missing, add them before finishing

This branding is MANDATORY for all project deliverables.
