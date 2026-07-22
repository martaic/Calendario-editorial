# QuantumSec — Calendario Editorial

Herramienta de una sola página (HTML + CSS + JS, sin dependencias ni build) para planificar y seguir el estado de producción del contenido semanal de QuantumSec. Se despliega como sitio estático en `martaic.github.io/Calendario-editorial/`.

## Qué hace

- Muestra el contenido de cada semana en una cuadrícula de 7 días (lunes a domingo).
- Cada pieza es una tarjeta con tipo (LinkedIn / Carrusel / Vídeo animado / Vídeo), plataformas, título, descripción y notas de producción.
- Permite marcar el estado de producción de cada pieza (**Sin empezar → En producción → Listo para revisar**) y, una vez lista, aprobarla o rechazarla.
- Tiene filtros por tipo de contenido y por estado, y una barra de estadísticas con el total de piezas, aprobadas, en revisión, en producción y rechazadas.
- Navegación entre semanas con las flechas ← → junto al nombre de la semana.

## Cómo está hecho

Todo vive en un único archivo `.html`. No hay backend ni build: se abre el archivo (o se sirve como página estática) y todo el estado se guarda **en memoria del navegador** — al recargar la página, el progreso de aprobación/producción vuelve a los valores iniciales definidos en el código. Esto es intencionado: la herramienta es un panel de planificación, no una base de datos.

Estructura interna:
- `CONTENT`: array con todas las piezas de todas las semanas.
- `WEEK_STARTS`: array de fechas (el lunes de cada semana). El índice en este array es el número de `week` que usan las tarjetas.
- El resto es el motor de renderizado (JS vanilla) y los estilos (CSS, sin frameworks).

## Cómo añadir una semana nueva

1. **Añade la fecha del lunes** de esa semana al final del array `WEEK_STARTS`, con un comentario indicando qué semana es:
   ```js
   new Date(2026,7,3), // semana 5: 3-9 agosto
   ```
2. **Añade las tarjetas de contenido** al array `CONTENT`, con `week` igual al índice de esa fecha en `WEEK_STARTS` (empezando en 0). Cada tarjeta necesita:

   | Campo | Qué es |
   |---|---|
   | `id` | Número único en todo el archivo — nunca reutilizar uno ya usado |
   | `week` | Índice de la semana en `WEEK_STARTS` |
   | `dayOffset` | Día dentro de la semana: 0=lunes, 1=martes... 6=domingo |
   | `type` | `'linkedin'`, `'carrusel'`, `'animado'`, `'kike'` o `'short'` |
   | `platforms` | Array de plataformas, ej. `['Reels','LinkedIn']` |
   | `title`, `desc`, `notes` | Texto de la tarjeta y del modal de detalle |
   | `status` | `'pending'`, `'approved'` o `'rejected'` (arranca casi siempre en `'pending'`) |
   | `prod` | `'sinempezar'`, `'enproduccion'` o `'listo'` |
   | `prodNote` | Nota corta de en qué punto está la producción |


## Relación con el resto del pipeline

Este calendario es la capa de planificación. La producción real de cada pieza (vídeos Remotion, carruseles, imágenes de post) se hace aparte, siguiendo el flujo documentado en el README del repo de contenido (`Template_Short.tsx`, `Template_Carrusel.tsx`, `Post_*.py`, etc.). Las notas de cada tarjeta (`notes`, `prodNote`) sirven de puente entre ambos: describen qué contenido va en cada pieza para poder rellenar la plantilla correspondiente.
