# 🤖 Agente de Standup Diario

Agente de IA que procesa el resumen del standup en lenguaje natural, interpreta los cambios con Claude, consulta al PM cuando falta información y actualiza el Excel del proyecto automáticamente.

---

## ¿Qué problema resuelve?

Después del standup, el PM tiene que traducir lo que se dijo en la reunión a actualizaciones concretas en el Excel: cambiar estados, actualizar porcentajes, registrar bloqueos en la bitácora. Es un proceso manual, tedioso y propenso a omisiones — especialmente cuando el standup fue rápido o desordenado.

**Por qué duele:**
- El PM escucha el standup y toma notas mentales, pero la actualización del Excel ocurre horas después o al día siguiente
- La información se pierde, se olvida o se registra incompleta
- Si no se actualiza, el dashboard y el reporte semanal automático pierden precisión
- Es una tarea de transcripción que no requiere criterio — pero sí tiempo y atención

El agente elimina ese paso: el PM dicta lo que pasó en el standup exactamente como lo haría en un mensaje de WhatsApp, y el agente hace el resto.

---

## ¿Cada cuánto se usa?

Diariamente, después de cada standup. El flujo completo toma menos de 2 minutos.

---

## ¿Es un proceso bloqueante?

Sí. Si el Excel no se actualiza después del standup:

- El **Agente de Seguimiento de Pendientes** (Proyecto 4) detecta falsas alarmas — tareas que sí avanzaron pero no están registradas
- El **reporte semanal automático** (Proyecto 2) genera un diagnóstico desactualizado
- La **Vista Cliente del dashboard** (Proyecto 3) muestra avances incorrectos
- El PM pierde trazabilidad de lo que se acordó en cada standup

---

## Herramientas

| Herramienta | Rol |
|-------------|-----|
| **Python** | Lenguaje del script |
| **Claude API (Anthropic)** | Interpreta el texto libre del standup y extrae cambios estructurados |
| **openpyxl** | Lee y escribe el archivo `.xlsx` del proyecto |
| **Claude Code** | Entorno de ejecución — maneja las credenciales de la API |

---

## Flujo del agente

```
1. El PM corre: python standup.py
2. El agente lista los proyectos disponibles en SeguimientoProyectos/
3. El PM elige el proyecto del standup
4. El agente muestra las tareas activas del proyecto
5. El PM dicta el resumen (texto libre o archivo .txt)
6. Claude analiza el texto contra las tareas reales del Excel
7. Si falta información, el agente pregunta al PM (una pregunta a la vez)
8. El agente muestra los cambios detectados para confirmación
9. El PM aprueba → el agente escribe al Excel
10. El agente genera un resumen del standup listo para compartir
```

---

## Uso paso a paso

### Requisitos

```bash
pip install anthropic openpyxl
```

Configura tu API key de Anthropic:
```bash
export ANTHROPIC_API_KEY="sk-ant-..."
```

### Correr el agente

```bash
# Modo interactivo (el PM escribe el resumen en la terminal)
python standup.py

# Modo archivo (el PM prepara el resumen en un .txt)
python standup.py --file resumen-standup.txt
```

### Ejemplo de sesión

```
=== AGENTE DE STANDUP ===

Proyectos disponibles:
  1. ClienteABC-MigracionPlataforma.xlsx
  2. ClienteXYZ-IntegracionAPI.xlsx

¿Qué proyecto corresponde al standup de hoy? (número): 1

Tareas activas de ClienteABC · Migración de Plataforma:
  T-01 · Configurar servidor (En progreso, 40%) — Carlos
  T-02 · Migrar base de datos (Pendiente, 0%) — Luis
  T-03 · Pruebas de integración (Pendiente, 0%) — Ana

¿Qué pasó hoy en el standup? (Enter en línea vacía para terminar):
> Carlos terminó la configuración del servidor, quedó al 100%.
> Luis empezó con la migración pero está bloqueado porque no tiene
> acceso a la base de datos de producción, lo está escalando a infra.
> Ana aún no empieza las pruebas, espera a que Luis termine.
>

Analizando con Claude...

Cambios detectados:
  ✅ T-01 · Configurar servidor → Completado (100%)
  🔴 T-02 · Migrar base de datos → Bloqueado (0%)
         Bloqueo: Sin acceso a base de datos de producción, escalado a infra
  ⏸️  T-03 · Pruebas de integración → Pendiente (0%)
         Nota: En espera de T-02

Tengo una pregunta antes de continuar:
  → T-02: ¿Quieres registrar el bloqueo en la bitácora con más detalle,
    o con "Sin acceso a BD de producción, escalado a infra" es suficiente?

> Con eso está bien.

¿Confirmas estos cambios? (s/n): s

✅ Excel actualizado correctamente.

--- RESUMEN DEL STANDUP ---
Fecha: 01/07/2026

Proyecto: ClienteABC · Migración de Plataforma

Avances:
- Configuración del servidor completada al 100% (Carlos)

Bloqueos:
- Migración de base de datos bloqueada por falta de acceso a producción
  Acción: Escalado a equipo de infraestructura

En espera:
- Pruebas de integración pendientes hasta resolver bloqueo de T-02

Estado general: 🔴 Atención requerida
---------------------------
```

---

## Lo que escribe al Excel

| Campo | Dónde |
|-------|-------|
| Estado de la tarea | Hoja `Tareas`, columna I |
| % Avance | Hoja `Tareas`, columna J |
| Nota de bloqueo o actualización | Hoja `Bitácora` (con fecha y hora) |

---

## Métricas de éxito

| | Manual | Con el agente |
|---|---|---|
| Tiempo de actualización post-standup | 10-20 min | < 2 min |
| Riesgo de omitir una tarea | Alto | Ninguno |
| Trazabilidad del standup | Depende del PM | 100% registrado |

El agente es exitoso si:
1. Interpreta correctamente el 90%+ de los cambios mencionados en el standup
2. Detecta y pregunta cuando falta información antes de guardar
3. No escribe al Excel sin confirmación explícita del PM
4. El resumen generado puede enviarse directamente al equipo sin edición

---

## Contexto del sistema

Este agente es el Proyecto 5 del curso de IA de Platzi. Completa el ciclo del sistema:

```
Proyecto 1 — Sistema de prompts  → crea y actualiza el Excel con IA
Proyecto 2 — n8n workflow        → reporte semanal automático por email
Proyecto 3 — Dashboard web       → visualización, edición y bitácora
Proyecto 4 — Agente pendientes   → detecta tareas estancadas
Proyecto 5 — Agente de standup   → procesa el standup y actualiza el Excel
```

---

## Licencia

MIT — úsalo, modifícalo y compártelo libremente.
