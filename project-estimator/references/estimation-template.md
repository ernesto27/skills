# Template: Estimación Completa

Copiar y completar este template para cada estimación.

---

# Estimación: {{NOMBRE_PROYECTO}}

**Fecha:** {{FECHA}}  
**Preparado por:** {{AUTOR}}  
**Versión:** 1.0

## Resumen Ejecutivo

| Métrica | Valor |
|---------|-------|
| **Duración estimada** | {{X}}-{{Y}} meses |
| **Equipo requerido** | {{N}} personas |
| **FTE equivalentes** | {{FTE}} |
| **Complejidad** | {{Simple/Moderada/Compleja/Muy Compleja}} |
| **Confianza de la estimación** | {{Alta/Media/Baja}} |

### Alcance en una Línea
{{Descripción de una línea del proyecto}}

---

## Desglose por Fases

### Fase 0: Discovery & Setup ({{X}} semanas)
**Objetivo:** Preparar el ambiente y validar requisitos

| Actividad | Duración | Responsable |
|-----------|----------|-------------|
| Setup de repositorios y CI/CD | 3 días | DevOps |
| Definición de arquitectura | 1 semana | Tech Lead |
| Setup de ambientes (dev/staging) | 3 días | DevOps |
| Spike técnicos si necesario | Variable | Backend |

**Entregables:**
- [ ] Documentación de arquitectura
- [ ] Repositorios configurados
- [ ] Ambiente de desarrollo funcional
- [ ] Pipeline CI/CD básico

---

### Fase 1: {{NOMBRE_FASE}} ({{X}} semanas)
**Objetivo:** {{Objetivo de la fase}}

| Módulo | Tiempo | Responsable | Dependencias |
|--------|--------|-------------|--------------|
| {{Módulo 1}} | {{X}} días | {{Rol}} | Ninguna |
| {{Módulo 2}} | {{X}} días | {{Rol}} | {{Módulo 1}} |

**Entregables:**
- [ ] {{Entregable 1}}
- [ ] {{Entregable 2}}

**Criterios de Aceptación:**
- {{Criterio 1}}
- {{Criterio 2}}

---

### Fase 2: {{NOMBRE_FASE}} ({{X}} semanas)
{{Repetir estructura}}

---

### Fase N: Estabilización & Deploy ({{X}} semanas)
**Objetivo:** Preparar para producción

| Actividad | Duración | Responsable |
|-----------|----------|-------------|
| Testing E2E | 1-2 semanas | QA |
| Performance testing | 3-5 días | QA + DevOps |
| Security review | 1 semana | Security/DevOps |
| Documentación final | 3-5 días | Todo el equipo |
| Deploy a producción | 2-3 días | DevOps |
| Período de hypercare | 1-2 semanas | Todo el equipo |

---

## Equipo Propuesto

### Composición del Equipo

| Rol | Persona | Dedicación | Período | Costo/mes* |
|-----|---------|------------|---------|------------|
| Tech Lead | {{TBD}} | 100% | Todo el proyecto | - |
| Backend Developer Sr | {{TBD}} | 100% | Meses 1-{{N}} | - |
| Backend Developer SSr | {{TBD}} | 100% | Meses 2-{{N}} | - |
| Frontend Developer | {{TBD}} | 80% | Meses 2-{{N}} | - |
| QA Engineer | {{TBD}} | 50% | Meses 2-{{N}} | - |
| DevOps Engineer | {{TBD}} | 30% | Todo el proyecto | - |

*Completar si se requiere estimación de costos

### Responsabilidades por Rol

**Tech Lead**
- Definición de arquitectura
- Code reviews
- Decisiones técnicas
- Coordinación con stakeholders
- Mentoría del equipo

**Backend Developer Sr**
- Implementación de lógica de negocio core
- Integraciones complejas
- Optimización de performance

**Backend Developer SSr**
- Implementación de CRUDs y APIs
- Testing unitario
- Soporte en integraciones

**Frontend Developer**
- Implementación de UI/UX
- Integración con APIs
- Responsive design
- Accesibilidad

**QA Engineer**
- Plan de testing
- Casos de prueba
- Automatización
- Regression testing

**DevOps Engineer**
- Infraestructura como código
- CI/CD pipelines
- Monitoreo y alertas
- Gestión de ambientes

### Alternativas de Equipo

**Opción A - Equipo Reducido:**
- {{N-1}} personas
- Impacto: +{{X}}% tiempo
- Riesgo: {{Descripción}}

**Opción B - Equipo Ampliado:**
- {{N+1}} personas
- Impacto: -{{X}}% tiempo
- Beneficio: {{Descripción}}

---

## Consideraciones Técnicas

### Stack Propuesto

| Capa | Tecnología | Justificación |
|------|------------|---------------|
| Backend | {{Go/Node/Python}} | {{Razón}} |
| Frontend | {{React/Vue/Angular}} | {{Razón}} |
| Mobile | {{React Native/Flutter/Nativo}} | {{Razón}} |
| Base de datos | {{PostgreSQL/MySQL/MongoDB}} | {{Razón}} |
| Cache | {{Redis/Memcached}} | {{Razón}} |
| Queue | {{RabbitMQ/SQS/Redis}} | {{Razón}} |
| Cloud | {{AWS/GCP/Azure}} | {{Razón}} |

### Integraciones Externas

| Integración | Complejidad | Tiempo Est. | Documentación | Riesgo |
|-------------|-------------|-------------|---------------|--------|
| {{API 1}} | Alta | 3 semanas | Buena | Medio |
| {{API 2}} | Media | 1 semana | Regular | Bajo |
| {{API 3}} | Baja | 3 días | Excelente | Bajo |

### Requisitos No Funcionales

| Requisito | Target | Cómo se logrará |
|-----------|--------|-----------------|
| Uptime | 99.9% | Multi-AZ, health checks |
| Response time | <200ms p95 | Caching, CDN |
| Concurrent users | {{N}} | Load balancing, autoscaling |
| Data retention | {{X}} años | Archiving strategy |

---

## ⚠️ Warnings y Riesgos

### 🔴 Riesgos Altos (requieren atención inmediata)

#### {{Nombre del Riesgo}}
- **Descripción:** {{Qué puede pasar}}
- **Impacto:** {{Tiempo/Costo/Alcance}} - {{Cuánto}}
- **Probabilidad:** Alta
- **Trigger:** {{Cómo sabremos que ocurrió}}
- **Mitigación:** {{Qué hacer para prevenirlo}}
- **Contingencia:** {{Qué hacer si ocurre}}

---

### 🟡 Riesgos Medios (monitorear activamente)

#### {{Nombre del Riesgo}}
- **Descripción:** {{Qué puede pasar}}
- **Impacto:** {{Tiempo/Costo/Alcance}} - {{Cuánto}}
- **Probabilidad:** Media
- **Mitigación:** {{Qué hacer para prevenirlo}}

---

### 🟢 Riesgos Bajos (awareness)

- **{{Riesgo 1}}:** {{Descripción breve}}
- **{{Riesgo 2}}:** {{Descripción breve}}

---

## Supuestos y Dependencias

### Supuestos (esta estimación asume que...)

1. **Requisitos:** Los requisitos están definidos al 80%+ antes de iniciar desarrollo
2. **Equipo:** El equipo propuesto está disponible en las fechas indicadas
3. **Accesos:** Se proveerán credenciales y accesos a sistemas externos en la primera semana
4. **Decisiones:** Las decisiones de negocio se tomarán en máximo 48 horas
5. **Feedback:** Los ciclos de review no tomarán más de 3 días hábiles
6. {{Supuesto adicional específico del proyecto}}

### Dependencias

| Dependencia | Proveedor | Fecha Necesaria | Estado | Plan B |
|-------------|-----------|-----------------|--------|--------|
| API de {{X}} | {{Cliente/Tercero}} | Mes 1 | Pendiente | {{Alternativa}} |
| Acceso a BD legacy | {{Cliente}} | Semana 1 | Pendiente | Mock data |
| Credenciales {{Servicio}} | {{Tercero}} | Mes 2 | N/A | N/A |

---

## Escenarios de Timeline

| Escenario | Duración | Equipo | Condiciones |
|-----------|----------|--------|-------------|
| **🎯 Optimista** | {{X}} meses | {{N}} personas | Sin cambios de alcance, integraciones sin issues, equipo estable |
| **📊 Realista** | {{Y}} meses | {{N}} personas | Algunos ajustes menores de alcance, 1-2 bloqueos resueltos en <1 semana |
| **⚠️ Pesimista** | {{Z}} meses | {{N+1}} personas | Cambios de alcance significativos, problemas de integración, rotación de equipo |

---

## Cronograma Visual

```
Mes 1      Mes 2      Mes 3      Mes 4      Mes 5      Mes 6
|----------|----------|----------|----------|----------|----------|
[=== Discovery ===]
           [======== Fase 1: Core ========]
                      [======== Fase 2: Features ========]
                                 [=== Integraciones ===]
                                            [=== Testing ===]
                                                       [Deploy]
```

---

## Próximos Pasos

1. [ ] Validar estimación con stakeholders
2. [ ] Confirmar disponibilidad del equipo
3. [ ] Definir fecha de inicio
4. [ ] Kick-off meeting
5. [ ] Setup inicial (repositorios, ambientes)

---

## Historial de Cambios

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0 | {{Fecha}} | {{Autor}} | Versión inicial |

---

*Esta estimación tiene una validez de 30 días. Cambios en el alcance o requisitos pueden impactar significativamente los tiempos y costos estimados.*
