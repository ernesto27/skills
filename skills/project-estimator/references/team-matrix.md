# Template: Matriz de Equipo

Detalle de roles, dedicación y asignaciones para el proyecto.

---

# Matriz de Equipo - {{NOMBRE_PROYECTO}}

## Resumen de Recursos

| Métrica | Valor |
|---------|-------|
| Total de personas | {{N}} |
| FTE equivalentes | {{X.X}} |
| Duración del proyecto | {{X}} meses |
| Total persona-meses | {{X}} |

---

## Matriz de Dedicación por Mes

| Rol | M1 | M2 | M3 | M4 | M5 | M6 | Promedio |
|-----|----|----|----|----|----|----|----------|
| Tech Lead | 100% | 100% | 100% | 100% | 100% | 100% | 100% |
| Backend Sr | 100% | 100% | 100% | 100% | 80% | 50% | 88% |
| Backend SSr | - | 100% | 100% | 100% | 100% | 80% | 80% |
| Frontend | - | 50% | 100% | 100% | 100% | 80% | 72% |
| QA | - | 25% | 50% | 75% | 100% | 100% | 58% |
| DevOps | 50% | 25% | 25% | 25% | 50% | 100% | 46% |
| **Total FTE** | **2.5** | **4.0** | **4.75** | **5.0** | **5.3** | **5.1** | **4.4** |

---

## Detalle por Rol

### Tech Lead

**Perfil requerido:**
- Seniority: Senior+ (5+ años)
- Experiencia en: {{Stack principal}}
- Soft skills: Liderazgo, comunicación con stakeholders

**Responsabilidades:**
| Actividad | % del tiempo |
|-----------|--------------|
| Arquitectura y diseño | 30% |
| Code review | 25% |
| Desarrollo hands-on | 20% |
| Coordinación y meetings | 15% |
| Mentoría | 10% |

**Dedicación:** 100% todo el proyecto

---

### Backend Developer Senior

**Perfil requerido:**
- Seniority: Senior (4+ años)
- Experiencia en: {{Go/Node/Python}}, {{DB}}, APIs REST
- Nice to have: {{Integraciones específicas}}

**Responsabilidades:**
| Actividad | % del tiempo |
|-----------|--------------|
| Desarrollo de features core | 50% |
| Integraciones complejas | 25% |
| Code review | 15% |
| Documentación técnica | 10% |

**Dedicación:** 100% → ramp-down en meses finales

---

### Backend Developer Semi-Senior

**Perfil requerido:**
- Seniority: Semi-Senior (2-4 años)
- Experiencia en: {{Stack}}, SQL, APIs
- Disposición para aprender

**Responsabilidades:**
| Actividad | % del tiempo |
|-----------|--------------|
| Desarrollo de CRUDs y features | 60% |
| Testing unitario | 20% |
| Soporte en integraciones | 15% |
| Bug fixing | 5% |

**Dedicación:** Ingresa mes 2, 100%

---

### Frontend Developer

**Perfil requerido:**
- Seniority: Semi-Senior+ (3+ años)
- Experiencia en: {{React/Vue}}, TypeScript, CSS
- Nice to have: {{Mobile si aplica}}

**Responsabilidades:**
| Actividad | % del tiempo |
|-----------|--------------|
| Desarrollo de UI | 55% |
| Integración con APIs | 25% |
| Testing de componentes | 10% |
| UX improvements | 10% |

**Dedicación:** Ingresa mes 2, ramp-up gradual

---

### QA Engineer

**Perfil requerido:**
- Seniority: Semi-Senior (2+ años)
- Experiencia en: Testing manual y automatizado
- Herramientas: {{Cypress/Playwright}}, {{Postman}}

**Responsabilidades:**
| Actividad | % del tiempo |
|-----------|--------------|
| Testing manual exploratorio | 30% |
| Automatización E2E | 35% |
| Testing de APIs | 20% |
| Documentación de casos | 15% |

**Dedicación:** Ramp-up gradual, 100% en fases finales

---

### DevOps Engineer

**Perfil requerido:**
- Seniority: Semi-Senior+ (3+ años)
- Experiencia en: {{AWS/GCP}}, Docker, CI/CD
- Nice to have: Kubernetes, Terraform

**Responsabilidades:**
| Actividad | % del tiempo |
|-----------|--------------|
| CI/CD pipelines | 25% |
| Infraestructura | 30% |
| Monitoreo y alertas | 20% |
| Soporte a desarrollo | 15% |
| Seguridad | 10% |

**Dedicación:** Variable según fase, peak en setup y deploy

---

## Gráfico de Dedicación

```
100% |████████████████████████████████████████| Tech Lead
     |████████████████████████████████░░░░░░░░| Backend Sr
     |    ████████████████████████████████░░░░| Backend SSr
     |    ░░██████████████████████████████░░░░| Frontend
     |    ░░░░░░██████████████████████████████| QA
     |████░░░░░░░░░░░░░░░░░░░░██████████████████| DevOps
     |----|----|----|----|----|----|----|----|
      M1   M2   M3   M4   M5   M6

█ = 100%  ░ = 50%  espacio = 0%
```

---

## Alternativas de Staffing

### Opción A: Equipo Mínimo Viable
| Rol | Dedicación | Trade-off |
|-----|------------|-----------|
| Full-stack Sr | 100% | Tech Lead + desarrollo |
| Full-stack SSr | 100% | Backend + Frontend |
| QA/DevOps | 50% | Combinado |

**Impacto:** +40% tiempo, riesgo de burnout

---

### Opción B: Equipo Acelerado
| Rol | Dedicación | Beneficio |
|-----|------------|-----------|
| Tech Lead | 100% | - |
| Backend Sr x2 | 100% | Paralelización |
| Frontend x2 | 100% | UI más rápido |
| QA | 100% | Testing continuo |
| DevOps | 50% | - |

**Impacto:** -25% tiempo, +50% costo mensual

---

## Skills Matrix

| Skill | Tech Lead | Backend Sr | Backend SSr | Frontend | QA | DevOps |
|-------|:---------:|:----------:|:-----------:|:--------:|:--:|:------:|
| {{Go/Node}} | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐ | - | ⭐ | - |
| {{React}} | ⭐⭐ | ⭐ | ⭐ | ⭐⭐⭐ | ⭐⭐ | - |
| SQL | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐ | ⭐ | ⭐⭐ | ⭐ |
| AWS/GCP | ⭐⭐ | ⭐⭐ | ⭐ | - | ⭐ | ⭐⭐⭐ |
| Docker | ⭐⭐ | ⭐⭐ | ⭐ | ⭐ | ⭐ | ⭐⭐⭐ |
| Testing | ⭐⭐ | ⭐⭐ | ⭐⭐ | ⭐⭐ | ⭐⭐⭐ | ⭐ |

⭐⭐⭐ = Experto | ⭐⭐ = Competente | ⭐ = Básico | - = No requerido

---

## Onboarding Timeline

| Persona | Ingreso | Tiempo productivo | Notas |
|---------|---------|-------------------|-------|
| Tech Lead | Día 1 | 3 días | Debe conocer el contexto |
| Backend Sr | Día 1 | 1 semana | Setup + arquitectura |
| Backend SSr | Semana 3 | 1 semana | Con arquitectura definida |
| Frontend | Semana 3 | 1 semana | Cuando hay APIs |
| QA | Semana 5 | 3 días | Cuando hay algo que testear |
| DevOps | Día 1 | 3 días | Setup inicial crítico |

---

## Backup Plan

En caso de baja de algún miembro:

| Rol | Backup interno | Tiempo de reemplazo | Plan de contingencia |
|-----|----------------|---------------------|---------------------|
| Tech Lead | Backend Sr | 2 semanas | Escalar a arquitecto externo |
| Backend Sr | Tech Lead + SSr | 1-2 semanas | Redistribuir carga |
| Backend SSr | Backend Sr | 1 semana | Contratar rápido |
| Frontend | - | 2 semanas | Priorizar features críticas |
| QA | Dev team | 1-2 semanas | Testing manual temporal |
| DevOps | Backend Sr | 1 semana | Soporte externo puntual |
