# 🤖 Agente IA — Reporte Semanal de Proyectos

Workflow n8n que genera y envía automáticamente reportes de avance de proyectos cada viernes a las 4pm, usando GPT-4o y Gmail.

Lee los datos del proyecto desde Excel → analiza con IA → envía dos versiones del reporte: una interna para el equipo y otra ejecutiva para el cliente.

---

## ¿Qué hace?

```
Excel con tareas → GPT-4o analiza → Email interno (equipo) + Email cliente
```

Cada viernes a las 4pm el workflow:

1. Lee las tareas, responsables, avances y fechas del proyecto
2. Llama a GPT-4o para analizar riesgos y redactar los reportes
3. Genera reporte **interno** con semáforos 🟢🟡🔴, alertas críticas y nivel de riesgo
4. Genera reporte **cliente** en tono ejecutivo, sin detalles internos
5. Envía ambos emails automáticamente por Gmail

---

## Contenido del repositorio

```
├── reporte-semanal-n8n.json              # Workflow para importar en n8n
├── ClienteABC-MigracionPlataforma.xlsx   # Ejemplo de proyecto con datos demo
└── sistema de prompts/
    └── sistema-prompts-gestion-proyectos.md  # Sistema de 5 prompts para gestionar proyectos con IA
```

---

## Requisitos

- [n8n](https://n8n.io/) (self-hosted o cloud) — v2.4 o superior
- Cuenta de OpenAI con acceso a GPT-4o
- Cuenta de Gmail conectada en n8n

---

## Cómo usar

### 1. Importar el workflow

En n8n: **Workflows → Import from file** → selecciona `reporte-semanal-n8n.json`

### 2. Conectar las credenciales

El workflow requiere dos credenciales configuradas en n8n:

| Nodo | Credencial |
|------|-----------|
| OpenAI Chat Model | API Key de OpenAI (nómbrala "OpenAI account") |
| Email Interno / Email Cliente | Cuenta Gmail |

### 3. Reemplazar los datos del proyecto

Abre el nodo **"Leer Proyectos Excel"** y reemplaza el array `projects` con los datos reales de tu proyecto. La forma más fácil es:

```bash
# 1. Completa tu Excel en la carpeta SeguimientoProyectos/
# 2. Ejecuta el script para generar el JSON
python analizar_proyectos.py

# 3. Copia el output y pégalo en el nodo "Leer Proyectos Excel"
```

La estructura del Excel se explica más abajo.

### 4. Publicar y probar

- Haz clic en **"Execute workflow"** para una prueba manual
- Si todo funciona, haz clic en **"Publish"**
- El workflow correrá solo cada viernes a las 4pm

---

## Estructura del Excel

El archivo `ClienteABC-MigracionPlataforma.xlsx` incluye tres hojas:

**Hoja `Tareas`**

| Columna | Descripción |
|---------|-------------|
| B2 | Nombre del proyecto |
| D2 | Nombre del cliente |
| F2 | PM |
| H2 | Fecha de inicio |
| J2 | Fecha de cierre estimada |
| Fila 6+ | Tareas: ID, Fase, Categoría, Tarea, Responsable, Fechas, Prioridad, Estado, % Avance, Notas |

**Hoja `Contactos`**

- Sección **CLIENTE** (filas 5–9): a quién se envía el reporte del cliente
- Sección **EQUIPO INTERNO** (filas 13–20): a quién se envía el reporte interno

Los emails en estas secciones son los destinatarios de los correos automáticos.

---

## Sistema de prompts

La carpeta `sistema de prompts/` contiene un sistema de 5 prompts encadenados para gestionar proyectos completos con IA:

| Prompt | Función |
|--------|---------|
| 1 — Inicialización | Captura datos del proyecto y genera el Excel base |
| 2 — Carga de tareas | Agrega o modifica tareas desde texto libre |
| 3 — Standup diario | Procesa el resumen del standup y actualiza el Excel |
| 4 — Análisis de salud | Calcula avance, semáforos y nivel de riesgo |
| 5 — Notificaciones | Genera los reportes interno y cliente |

El workflow n8n automatiza los prompts 4 y 5 cada viernes.

---

## Personalización

**Cambiar el horario:** edita el nodo "Cada Viernes 4pm" → expresión cron `0 16 * * 5`

**Agregar más proyectos:** agrega más objetos al array `projects` en el nodo "Leer Proyectos Excel". El workflow genera un reporte por cada proyecto.

**Cambiar el modelo:** en el nodo "OpenAI Chat Model" puedes cambiar `gpt-4o` por cualquier modelo compatible.

---

## Contexto

Proyecto desarrollado como parte del curso de IA de Platzi. El objetivo fue automatizar una tarea real de trabajo como SDM: escribir el mismo reporte de avance dos veces cada viernes, en dos tonos distintos.

El reto técnico más interesante: n8n en modo local bloquea `fs`, `child_process` y paquetes externos en sus Code nodes. La solución fue extraer los datos del Excel con Python externo y embederlos como JavaScript en el nodo. También fue necesario reemplazar el nodo AI Agent (que usa un loop ReAct y empieza a hacer preguntas en lugar de ejecutar) por Basic LLM Chain para generación directa de JSON.

---

## Licencia

MIT — úsalo, modifícalo y compártelo libremente.
