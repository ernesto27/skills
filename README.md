# Skills

Colección de **skills** (habilidades) para clientes de agentes de IA (como Claude Code, entre otros). Cada skill extiende al agente con conocimiento y flujos de trabajo especializados que se activan automáticamente según la petición del usuario o de forma manual mediante un comando (`/nombre-skill`).

Cada skill vive en su propia carpeta dentro de `skills/` y se define en un archivo `SKILL.md`.

## Skills disponibles

| Skill | Descripción |
|-------|-------------|
| **caveman** | Modo de comunicación ultracomprimido. Reduce el uso de tokens (~75%), manteniendo toda la precisión técnica. Soporta distintos niveles de intensidad. |
| **commit-push** | Hace commit de los cambios en staging y los sube a la rama actual. |
| **deep-research** | Investigación técnica rigurosa. Combina búsqueda web con documentación autorizada (Context7 MCP), verifica afirmaciones entre fuentes y genera un informe estructurado y citado guardado en `research/`. |
| **explain-changes** | Explica en detalle cambios de código y configuración, con diagramas ASCII en la terminal. Explica que cambio, por qué importa, etc |
| **plan-from-spec** | Convierte una especificación o definición de feature en un plan de implementación riguroso y revisado, guardado en `plans/`. Interroga la spec con preguntas hasta no dejar nada librado a suposiciones. |
| **postman-collection-generator** | Genera una colección de Postman (v2.1) importable a partir de una base de código. Escanea las rutas, extrae métodos/paths/params/bodies/headers, agrupa endpoints en carpetas y configura variables de entorno. Agnóstico al lenguaje y framework. |
| **project-estimator** | Estima proyectos de software y genera documentos Word profesionales. Calcula tiempos en meses, equipo propuesto (rol y % de dedicación), consideraciones técnicas y riesgos/warnings. Incluye template `.docx` corporativo. |
| **supabase-postgres-best-practices** | Guía de optimización de rendimiento y buenas prácticas de Postgres mantenida por Supabase. Reglas en 8 categorías priorizadas por impacto, para escribir, revisar u optimizar consultas y esquemas. |
| **tutorial-master** | Crea tutoriales y explicaciones paso a paso, exhaustivas y didácticas. Asume nada y explica todo, fomentando que el aprendiz escriba el código en lugar de copiarlo. Se activa para enseñar conceptos, redactar documentación o guías. |

## Uso

Las skills se activan de dos maneras:

- **Automática**: el agente detecta cuándo una skill aplica según su descripción y la petición del usuario.
- **Manual**: invocando el comando correspondiente, por ejemplo `/commit-push` o `/deep-research`.


