# Agente de Seguimiento de Pendientes

## 1. ¿Qué proceso elimina?

El seguimiento manual de tareas estancadas en proyectos activos.

En la gestión de proyectos de software, el PM es responsable de detectar cuándo una tarea lleva días sin avanzar — sin que nadie lo reporte, sin que nadie lo pida. Eso implica abrir cada Excel de cada proyecto, revisar fecha por fecha qué se movió y qué no, cruzar esa información con las notas del equipo, y luego redactar un correo de seguimiento.

**Por qué duele:**
- Es una tarea mecánica y repetitiva que consume entre 30 y 60 minutos diarios
- Requiere concentración alta para no omitir tareas, pero no genera ningún valor intelectual
- Es invisible — nadie ve el esfuerzo, solo notan cuando no se hace
- Interrumpe el flujo de trabajo en el peor momento: primera hora del día o al final cuando ya hay cansancio acumulado
- Genera culpa cuando no se hace, y frustración cuando se hace y no hay respuesta

Es exactamente el tipo de tarea que nadie quiere hacer pero que, si no se hace, se convierte en el origen silencioso de la mayoría de los retrasos del proyecto.

---

## 2. ¿Cada cuánto se repite?

Diariamente en proyectos activos, o cada 2-3 días en proyectos con ritmo más lento.

En la práctica, el PM debería revisarlo cada mañana antes del standup para llegar con visibilidad real del estado del equipo. Sin automatización, esto rara vez ocurre con esa frecuencia — se hace cuando el cliente ya preguntó o cuando el retraso ya es visible.

El agente está configurado para correr cada **1, 3 o 7 días** según el ritmo del proyecto, y se ejecuta automáticamente al abrir el dashboard si ha pasado el intervalo configurado.

---

## 3. ¿Es un proceso bloqueante?

Sí. El seguimiento de pendientes es bloqueante para:

- **El standup diario** — sin visibilidad previa, el PM llega a la reunión reactivo, no proactivo
- **La comunicación con el cliente** — los reportes semanales (automatizados con el workflow n8n) pierden precisión si las tareas no están actualizadas
- **La toma de decisiones** — escalar un bloqueo, reasignar un recurso o ajustar fechas depende de detectar el problema a tiempo
- **La confianza del cliente** — los retrasos no detectados a tiempo se acumulan y explotan en la reunión de seguimiento mensual

Un bloqueo detectado el día 1 toma horas resolver. Detectado el día 5, puede costar días o semanas.

---

## 4. ¿Qué herramientas se usan?

| Herramienta | Rol |
|-------------|-----|
| **Excel (.xlsx)** | Fuente de verdad — tareas, estados, fechas, responsables |
| **Hoja Bitácora** | Registro de notas con fecha y hora por tarea (generado desde el dashboard) |
| **SheetJS** | Lectura de archivos Excel desde el navegador, sin servidor |
| **File System Access API** | Acceso a los archivos locales desde el HTML |
| **localStorage** | Persiste la configuración del schedule y la fecha del último análisis |
| **app.html (Dashboard)** | Interfaz donde vive el agente — botón on demand + schedule automático |

Todo corre en el navegador (Chrome o Edge). Sin servidor, sin instalaciones, sin backend.

---

## 5. Uso paso a paso

### Configuración inicial (una sola vez)

1. Abre `Dashboard/app.html` en Chrome o Edge
2. Selecciona tu carpeta `SeguimientoProyectos` cuando el navegador lo pida
3. En la sección **Agente**, elige el intervalo de análisis: 1, 3 o 7 días
4. Define el umbral de días sin movimiento para considerar una tarea estancada (por defecto: 2 días)

### Uso diario (automático)

1. Abres el dashboard por la mañana
2. Si ha pasado el intervalo configurado desde el último análisis, el agente corre automáticamente
3. Aparece una notificación con el número de tareas estancadas detectadas
4. Haces clic para ver el reporte completo y el email de seguimiento generado
5. Copias el email y lo envías

### Uso on demand

1. En la navbar, haz clic en el botón **"Analizar pendientes"**
2. El agente lee todos los proyectos en tiempo real
3. En segundos muestra el reporte con tareas estancadas agrupadas por proyecto y responsable
4. El email de seguimiento queda listo para copiar y enviar

### ¿Qué analiza el agente por cada tarea?

- Busca la última nota en la hoja **Bitácora** del Excel correspondiente
- Si no hay bitácora, usa la **fecha de inicio** de la tarea como referencia
- Calcula cuántos días han pasado desde esa fecha
- Si supera el umbral configurado y la tarea no está **Completada**, la marca como estancada
- Agrupa las tareas estancadas por proyecto y por responsable
- Genera el texto del email listo para enviar

---

## 6. Métrica de éxito

### Tiempo eliminado

| Actividad manual | Tiempo estimado | Con el agente |
|-----------------|-----------------|---------------|
| Revisar todos los Excel | 15-20 min/día | 0 min |
| Identificar tareas estancadas | 10-15 min/día | 0 min |
| Redactar email de seguimiento | 10-20 min/día | 0 min |
| **Total diario** | **35-55 min/día** | **< 1 min** |
| **Total mensual (22 días hábiles)** | **~13-20 horas** | **~22 min** |

### Impacto cualitativo

- **Detección temprana de bloqueos** — el agente detecta el día 2, no el día 5. Los bloqueos resueltos temprano tienen un impacto mínimo en el cronograma.
- **Standup más efectivo** — el PM llega con visibilidad real, no a improvisar.
- **Menos reuniones de emergencia** — los retrasos no llegan al cliente como sorpresa.
- **Trazabilidad** — cada análisis queda registrado con fecha, permitiendo demostrar que el seguimiento se hizo aunque no hubo respuesta del equipo.

### Definición de éxito para el MVP

El agente es exitoso si:
1. Detecta correctamente el 100% de las tareas sin movimiento en más de 2 días
2. Genera un email de seguimiento coherente sin intervención manual
3. Corre automáticamente al abrir el dashboard si ha pasado el intervalo configurado
4. El PM puede completar su revisión diaria de pendientes en menos de 2 minutos

---

## Contexto del sistema

Este agente es el Proyecto 4 del curso de IA de Platzi. Complementa el sistema de gestión de proyectos construido en los proyectos anteriores:

```
Proyecto 1 — Sistema de prompts → genera y actualiza el Excel
Proyecto 2 — n8n workflow      → reporte semanal automático por email
Proyecto 3 — Dashboard web     → visualización y bitácora de notas
Proyecto 4 — Agente            → seguimiento de pendientes estancados
```

En el Proyecto 5 se conectará a la API de Claude para agregar razonamiento real sobre los datos — priorizando bloqueos, sugiriendo acciones y redactando el email con contexto del historial del proyecto.
