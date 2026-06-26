---
name: startup-financial-modeling
description: Build comprehensive 3-5 year financial models with revenue projections, cost structures, cash flow analysis, and scenario planning. Adapted for Chilean PYME / manufacturing context (pisco, food & beverage, services). Complements kpi-dashboard-piskesoul.
version: 1.0.0
---
<!-- FORK ADAPTADO: Adaptar ratios SaaS/VC-backed → negocio pisquero/manufactura chilena; cambiar ejemplos ARR/MRR → revenue anual manufactura; agregar contexto PYME Chile · origen: wshobson-agents-plugins-startup-business-analyst-skills-startup-financial-modeling-skill-md · 2026-06-26 -->

# Financial Modeling — PYME / Manufactura

Build comprehensive 3-5 year financial models for Chilean SMEs in manufacturing, food & beverage, and services.

## Overview

Financial modeling provides the quantitative foundation for business strategy, funding, and operational planning. Use cohort-based revenue modeling, detailed cost structures, and scenario analysis to support decision-making.

## Core Components

### Revenue Model

**Producto/Servicio Proyections:**

```
Revenue Anual = Σ (Volumen ventas × Precio promedio × Tasa retención)

Para Micelio/PiskeSoul:
Revenue = (Botellas vendidas × Precio/botella) + (Suscripciones ERP × Precio mensual)
```

**Key Inputs:**

- Volumen de producción mensual (cajas, botellas, unidades)
- Precio de venta promedio (canal mayorista vs minorista vs directo)
- Tasa de retención de clientes por canal
- Mix de productos (reserve, premium, entry-level)
- Expansión: nuevos canales, exportaciones

### Cost Structure — Manufactura

**Estructura de Costos:**

1. **Costo de Ventas (CV / COGS)**
   - Materia prima (uva, materiales de empaque)
   - Mano de obra directa (bodega, producción)
   - Costos de producción variables (energía, agua)
   - Depreciación de equipos de producción

2. **Ventas & Marketing**
   - Comisiones de distribuidores
   - Ferias y degustaciones
   - Marketing digital (Jenny/Meta Ads)
   - Fuerza de ventas

3. **Gastos Generales & Administrativos (G&A)**
   - Equipo administrativo
   - Legal, contabilidad
   - Software (ERP PiskeSoul, herramientas)
   - Seguros y costos regulatorios

4. **Investigación & Desarrollo**
   - Nuevas variedades o productos
   - Certificaciones (denominación de origen, orgánico)

### Cash Flow Analysis

**Components:**

- Saldo inicial de caja
- Flujo de entrada (ventas, financiamiento)
- Flujo de salida (costos operacionales, CapEx bodega)
- Saldo final de caja
- Quema mensual (burn rate)
- Runway (meses de operación con caja disponible)

```
Runway = Caja disponible / Quema mensual
Quema mensual = Ingresos del mes - Gastos del mes
```

### Headcount Planning

**Roles clave:**

- Producción/Bodega: 40-60% del headcount
- Ventas & Marketing: 15-25%
- Administración: 10-20%
- Tech/Software (ERP): 5-15%

## Financial Model Structure

### Three-Scenario Framework

**Conservador (P10):**
- Crecimiento lento de ventas
- Costos de materia prima al alza
- Menor penetración de nuevos canales
- Para gestión de caja y stress test

**Base (P50):**
- Resultados más probables
- Asunciones realistas de mercado chileno
- Escenario principal para planificación y reportes

**Optimista (P90):**
- Crecimiento acelerado (nuevo canal, exportación)
- Mejor mix de productos
- Para planificación de upside

### Time Horizon

- **Año 1**: Detalle mensual
- **Año 2**: Detalle mensual
- **Año 3**: Detalle trimestral
- **Años 4-5**: Proyecciones anuales

## Business Model Templates

### Manufactura / Food & Beverage (Micelio/Pisco)

**Revenue Drivers:**
- Litros producidos × precio por litro
- Cajas vendidas por canal (mayorista, retail, exportación, directo)
- Efectividad de fuerza de ventas

**Key Ratios (Manufactura Artesanal):**
- Margen bruto: 35-55% (vs 75%+ en SaaS)
- Costo de ventas: 45-65% de revenue
- Gastos ventas & marketing: 15-25% del revenue
- Capital de trabajo: 60-120 días (ciclo de producción largo)
- CAC payback: 3-9 meses (dependiendo del canal)

**Ejemplo Proyección Micelio:**
```
Año 1: 5,000 botellas × CLP $8,000 = CLP $40M revenue
Año 2: 12,000 botellas × CLP $9,000 = CLP $108M revenue
Año 3: 25,000 botellas × CLP $10,000 = CLP $250M revenue
```

### SaaS / ERP (PiskeSoul)

**Revenue Drivers:**
- Suscripciones mensuales (MRR)
- Módulos adicionales activados
- Implementación one-time

**Key Ratios (SaaS B2B PYME):**
- Margen bruto: 70-80%
- CAC payback: 6-18 meses
- Net Revenue Retention: 90-110%

### Servicios / Agencia

**Revenue Drivers:**
- Horas facturables o proyectos
- Tarifa hora o fee de proyecto
- Utilización del equipo

**Key Ratios:**
- Margen bruto: 40-60%
- Utilización objetivo: 65-75%
- Revenue por FTE

## Fundraising Integration

### Necesidad de Capital

```
Capital requerido = Inversión CapEx (equipos, bodega) + Capital de trabajo (materia prima, stock) + Gastos operacionales hasta breakeven
```

**Fuentes típicas para PYME chilena:**
- Corfo (FOGAIN, créditos blandos)
- Banco BCI, Santander (crédito PYME)
- Angel investors / family & friends
- Crowdfunding (Cumplo, entre otros)

### Milestone-Based Planning

- Producción: 1ª cosecha propia, capacidad instalada X cajas/año
- Ventas: 1er cliente retail, 1ª exportación, 1er supermercado
- Financiero: breakeven operacional, FCF positivo

## Common Pitfalls

**Pitfall 1: Subestimar el ciclo de caja**
- Manufactura tiene ciclos largos (meses de producción antes de venta)
- Modelar ciclo de cobro real (días de cuentas por cobrar)

**Pitfall 2: Ignorar la estacionalidad**
- Ventas de pisco tienen picos fuertes (verano, fiestas patrias)
- Modelar por mes, no como promedio anual

**Pitfall 3: Subestimar costos de distribución**
- Comisiones de distribuidores: 25-40% del precio al distribuidor
- Logística y flete: 5-10% del precio

**Pitfall 4: Asumir crecimiento lineal**
- El primer año de nuevos canales es lento
- El breakeven de un nuevo distribuidor puede tardar 6-12 meses

## Model Validation

**Sanity Checks:**

- [ ] Margen bruto es realista para la industria
- [ ] El ciclo de caja está modelado (no solo P&L)
- [ ] La estacionalidad está reflejada
- [ ] Los costos fijos están separados de variables
- [ ] El CapEx está planificado (bodega, equipos)
- [ ] El capital de trabajo está calculado

## Quick Start

1. **Define modelo de negocio**: canales de venta, productos, pricing
2. **Proyecta revenue**: volumen × precio × retención por canal
3. **Modela costos**: separa fijos/variables, incluye COGS manufactura
4. **Planifica headcount**: roles y momento de contratación
5. **Calcula flujo de caja**: incluye ciclo de cobro y ciclo de producción
6. **Métricas clave**: margen bruto, burn rate, runway, breakeven
7. **Crea escenarios**: conservador, base, optimista
8. **Valida asunciones**: benchmark con industria pisquera/food chilena
9. **Integra financiamiento**: necesidad de capital y fuentes disponibles
