# Catálogo de Riesgos y Warnings

Referencia rápida de riesgos comunes en proyectos de software.

---

## 🔴 Riesgos Altos

### Integraciones con Terceros

**Riesgo:** APIs de terceros con documentación incorrecta, incompleta o desactualizada
- **Probabilidad:** Alta en LATAM (especialmente fintech/gobierno)
- **Impacto:** +2-4 semanas por integración
- **Señales de alerta:**
  - Documentación solo en PDF
  - Sin sandbox/ambiente de pruebas
  - Soporte solo por email
  - Sin versionamiento de API
- **Mitigación:**
  - Spike técnico temprano (primeras 2 semanas)
  - Contacto directo con equipo técnico del tercero
  - Contratos con SLA de soporte
- **Contingencia:**
  - Buffer de 50% en tiempo de integración
  - Plan B con alternativas identificadas

---

### Requisitos Ambiguos o Cambiantes

**Riesgo:** Scope creep o requisitos que cambian frecuentemente
- **Probabilidad:** Alta si el cliente no tiene PM dedicado
- **Impacto:** +30-50% del tiempo total
- **Señales de alerta:**
  - "Después lo definimos"
  - Múltiples stakeholders con opiniones diferentes
  - Sin documentación de requisitos
- **Mitigación:**
  - Sign-off formal de requisitos antes de cada fase
  - Change request process definido
  - Demos frecuentes (cada 1-2 semanas)
- **Contingencia:**
  - Cláusula de cambio de alcance en contrato
  - Re-estimación formal si cambios > 20%

---

### Dependencias Externas Bloqueantes

**Riesgo:** Equipos externos o terceros que no entregan a tiempo
- **Probabilidad:** Media-Alta
- **Impacto:** Bloqueo total hasta resolución
- **Señales de alerta:**
  - "El otro equipo se encarga"
  - Sin fecha comprometida
  - Sin punto de contacto directo
- **Mitigación:**
  - Identificar dependencias en día 1
  - Acuerdos escritos con fechas
  - Reuniones de seguimiento semanales
- **Contingencia:**
  - Mocks/stubs para desarrollo paralelo
  - Escalamiento temprano a management

---

### Primera Vez con Tecnología

**Riesgo:** Equipo sin experiencia en stack o herramienta crítica
- **Probabilidad:** Media
- **Impacto:** +20-40% en tiempo, bugs de novato
- **Señales de alerta:**
  - "Nunca usamos esto pero parece fácil"
  - Decisión tecnológica por moda, no por fit
- **Mitigación:**
  - Training antes de iniciar
  - Consultor/mentor externo primeras semanas
  - Spike técnico antes de comprometer tiempos
- **Contingencia:**
  - Pivotar a tecnología conocida si es viable
  - Contratar expertise

---

### Compliance y Regulaciones

**Riesgo:** Requisitos de compliance descubiertos tarde
- **Probabilidad:** Alta en fintech, salud, gobierno
- **Impacto:** +1-3 meses, posible rediseño
- **Señales de alerta:**
  - "Después vemos el tema de seguridad"
  - Sin equipo legal/compliance involucrado
  - PCI, HIPAA, GDPR mencionados de pasada
- **Mitigación:**
  - Checklist de compliance día 1
  - Consultoría especializada temprana
  - Arquitectura security-first
- **Contingencia:**
  - Presupuesto de contingencia para auditorías
  - Timeline extendido documentado

---

## 🟡 Riesgos Medios

### Deuda Técnica Heredada

**Riesgo:** Sistema legacy con código problemático
- **Probabilidad:** Alta si hay código existente
- **Impacto:** +20-30% en features que tocan legacy
- **Señales de alerta:**
  - "El sistema tiene sus mañas"
  - Sin tests, sin documentación
  - Rotación alta del equipo original
- **Mitigación:**
  - Auditoría de código antes de estimar
  - Refactoring progresivo
  - Tests antes de modificar
- **Contingencia:**
  - Reescritura parcial si es más eficiente

---

### Performance No Especificada

**Riesgo:** Requisitos de carga descubiertos tarde
- **Probabilidad:** Media
- **Impacto:** +2-4 semanas de optimización
- **Señales de alerta:**
  - "Queremos que sea rápido"
  - Sin métricas específicas (p95, concurrent users)
- **Mitigación:**
  - Definir SLAs de performance día 1
  - Load testing desde MVP
  - Arquitectura preparada para escalar
- **Contingencia:**
  - Fase de optimización post-launch

---

### Rotación de Equipo

**Riesgo:** Miembros clave dejan el proyecto
- **Probabilidad:** Media en proyectos largos (6+ meses)
- **Impacto:** +2-4 semanas por persona (onboarding reemplazo)
- **Señales de alerta:**
  - Una sola persona conoce componente crítico
  - Sin documentación de arquitectura
- **Mitigación:**
  - Pair programming
  - Documentación continua
  - Knowledge sharing sessions
- **Contingencia:**
  - Backup identificado para roles críticos
  - Período de transición obligatorio

---

### Comunicación con Cliente

**Riesgo:** Demoras en feedback y aprobaciones
- **Probabilidad:** Media-Alta
- **Impacto:** +1-2 semanas por ciclo de feedback
- **Señales de alerta:**
  - Múltiples niveles de aprobación
  - Stakeholder clave con poca disponibilidad
- **Mitigación:**
  - SLA de respuesta acordado (ej: 48h)
  - Un solo punto de contacto
  - Decisiones documentadas por escrito
- **Contingencia:**
  - Escalamiento definido
  - Continuar con supuestos documentados

---

### Testing Insuficiente

**Riesgo:** QA reducido o eliminado para "ahorrar tiempo"
- **Probabilidad:** Media
- **Impacto:** +50-100% bugs en producción
- **Señales de alerta:**
  - "Los devs van a testear"
  - "Es MVP, no necesita testing"
- **Mitigación:**
  - QA desde el inicio aunque sea parcial
  - Automated testing como mínimo
  - Definition of Done incluye tests
- **Contingencia:**
  - Fase de estabilización extendida

---

## 🟢 Riesgos Bajos (Awareness)

### Vacaciones y Feriados

**Consideración:** Impacto de ausencias planificadas
- **Impacto típico:** 5-10% del timeline
- **Mitigación:**
  - Mapear feriados al inicio
  - Consultar vacaciones planificadas
  - No asumir 100% disponibilidad

---

### Ambiente de Desarrollo

**Consideración:** Setup de ambiente complejo
- **Impacto típico:** 1-3 días por persona
- **Mitigación:**
  - Docker/scripts de setup
  - Documentación de onboarding
  - Pair setup primeros días

---

### Documentación

**Consideración:** Tiempo para documentar suele subestimarse
- **Impacto típico:** 5-10% del proyecto
- **Mitigación:**
  - Documentación continua (no al final)
  - Templates predefinidos
  - Incluir en Definition of Done

---

### Coordinación de Horarios

**Consideración:** Equipos distribuidos o diferentes husos horarios
- **Impacto típico:** -10-20% eficiencia
- **Mitigación:**
  - Horas de overlap definidas
  - Comunicación asíncrona efectiva
  - Documentación como fuente de verdad

---

## Matriz de Riesgos por Tipo de Proyecto

| Riesgo | MVP | Plataforma | Migración | Integración |
|--------|:---:|:----------:|:---------:|:-----------:|
| Requisitos ambiguos | 🔴 | 🟡 | 🟢 | 🟡 |
| Integraciones terceros | 🟢 | 🟡 | 🟡 | 🔴 |
| Deuda técnica | 🟢 | 🟡 | 🔴 | 🟡 |
| Performance | 🟢 | 🔴 | 🟡 | 🟡 |
| Compliance | 🟢 | 🔴 | 🟡 | 🟡 |
| Rotación equipo | 🟢 | 🔴 | 🟡 | 🟢 |

---

## Checklist de Identificación de Riesgos

Usar al inicio de cada estimación:

### Contexto del Proyecto
- [ ] ¿Es la primera vez que el equipo trabaja con este cliente?
- [ ] ¿Hay sistemas legacy involucrados?
- [ ] ¿Hay integraciones con terceros?
- [ ] ¿Hay requisitos de compliance?
- [ ] ¿El equipo conoce el stack propuesto?

### Requisitos
- [ ] ¿Están documentados los requisitos?
- [ ] ¿Hay un Product Owner/BA dedicado?
- [ ] ¿Se definieron criterios de aceptación?
- [ ] ¿Hay requisitos no funcionales especificados?

### Equipo
- [ ] ¿El equipo está confirmado?
- [ ] ¿Hay dependencias con otros equipos?
- [ ] ¿Hay vacaciones planificadas en el período?
- [ ] ¿Hay roles que dependen de una sola persona?

### Técnico
- [ ] ¿Hay acceso a todos los sistemas necesarios?
- [ ] ¿La documentación de APIs externas está disponible?
- [ ] ¿Hay ambientes de prueba/staging?
- [ ] ¿Hay requisitos de seguridad especiales?

### Cliente/Negocio
- [ ] ¿Hay fecha límite inamovible?
- [ ] ¿Hay proceso de aprobación claro?
- [ ] ¿El presupuesto está aprobado?
- [ ] ¿Hay otros proyectos que compiten por recursos?
