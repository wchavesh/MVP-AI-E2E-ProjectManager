# 📊 Dashboard de Seguimiento de Proyectos

App web de una sola página para visualizar y gestionar el avance de proyectos directamente desde archivos Excel, sin servidor ni instalación.

---

## ¿Qué hace?

- **Vista Cliente** — resumen ejecutivo con avance global, progreso por fase e hitos. Sin información interna.
- **Vista PM** — tabla completa de tareas con edición de estado y avance, alertas de bloqueos y fechas vencidas, y bitácora de notas con fecha y hora.
- Lee y escribe directamente los archivos `.xlsx` en tu computador. Los datos nunca salen de tu máquina.

---

## Requisitos

- **Chrome o Edge** (versión 86 o superior)
- Firefox **no** es compatible — usa File System Access API, que Firefox no soporta aún

---

## Estructura de carpetas esperada

```
SeguimientoProyectos/
├── ClienteA-NombreProyecto.xlsx
├── ClienteB-OtroProyecto.xlsx
└── ...

Dashboard/
└── app.html   ← este archivo
```

La carpeta `SeguimientoProyectos` puede estar en cualquier ubicación de tu computador. La primera vez que abres la app, el navegador te pedirá seleccionarla. A partir de ahí la recuerda automáticamente.

---

## ▶️ Probarlo rápido (evaluadores)

> Requiere Chrome o Edge — Firefox no es compatible.

1. Descarga o clona el repositorio y copia la carpeta `SeguimientoProyectos-ejemplo/` a tu computador
2. Abre el dashboard: [GitHub Pages](https://wchavesh.github.io/04-AgenteSeguimientoPendientes/Dashboard/app.html)
3. Haz clic en **"Seleccionar carpeta"** y elige la carpeta `SeguimientoProyectos-ejemplo/` que descargaste
4. Listo — el dashboard carga el proyecto de ejemplo automáticamente

Para probar el agente: haz clic en **"🔍 Analizar pendientes"** en la barra superior.

> La carpeta solo se selecciona una vez. La próxima vez que abras el dashboard carga directo sin pedirla de nuevo.

---

## Cómo usar (con tus propios proyectos)

1. Abre `app.html` en Chrome o Edge (doble clic o arrástralo al navegador)
2. Haz clic en **Seleccionar carpeta** y elige tu carpeta `SeguimientoProyectos`
3. La app carga todos los proyectos automáticamente
4. Usa el selector en la barra superior para cambiar de proyecto

---

## Estructura del Excel

Cada archivo `.xlsx` en `SeguimientoProyectos` debe tener la hoja **Tareas** con esta estructura:

| Celda | Contenido |
|-------|-----------|
| B2 | Nombre del proyecto |
| D2 | Nombre del cliente |
| F2 | PM |
| H2 | Fecha de inicio |
| J2 | Fecha de cierre estimada |
| Fila 6 en adelante | Tareas |

Columnas de tareas (a partir de fila 6):

```
A: ID | B: Fase | C: Categoría | D: Tarea | E: Responsable |
F: Fecha inicio | G: Fecha límite | H: Prioridad | I: Estado | J: % Avance | K: Notas
```

Los archivos que contengan la palabra `plantilla` en el nombre son ignorados automáticamente.

---

## Bitácora

Desde la Vista PM, cada tarea tiene un botón 📝 que abre un panel de notas. Cada nota queda registrada con fecha y hora exacta en una hoja llamada **Bitácora** dentro del mismo Excel. Útil para documentar bloqueos, evidencias o actualizaciones de avance.

---

## Datos que escribe la app al Excel

| Acción | Dónde se guarda |
|--------|----------------|
| Cambio de Estado o % Avance | Hoja `Tareas`, columnas I y J |
| Nota de bitácora | Hoja `Bitácora` (se crea si no existe) |

---

## Contexto

Desarrollado como parte del curso de IA de Platzi — Proyecto 3. Complementa el sistema de gestión de proyectos con Excel y el workflow n8n de reportes semanales automáticos.

El requerimiento base fue que la solución fuera **portable**: que funcione sin depender de servidores externos, bases de datos ni cuentas en servicios de terceros. Por eso el Excel es la fuente de verdad y la app corre completamente en el navegador.

---

## Licencia

MIT — úsalo, modifícalo y compártelo libremente.
