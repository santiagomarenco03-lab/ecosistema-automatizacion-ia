# Ecosistema de Automatización IA — Pipeline de Contenido con Control de Calidad Humano

**Proyecto Final · IA & Automatización · Coderhouse**
Santiago Marenco

---

## Qué resuelve

Producción de contenido para redes de una actividad de clases particulares universitarias, de punta a punta. Entra una **idea semilla** escrita en lenguaje natural y el sistema la interpreta, la clasifica, recupera las directrices de comunicación que correspondan, redacta el post, **se detiene a esperar aprobación humana** y recién entonces lo publica en el canal de salida.

El proceso exige interpretación de lenguaje natural en dos puntos: para decidir de qué habla una idea escrita informalmente, y para redactar respetando reglas de estilo que viven en una base privada.

---

## Stack

| Categoría | Implementación |
|---|---|
| **Orquestador** | Make — 2 escenarios, 14 módulos |
| **Base de datos** | Airtable — 4 tablas vinculadas |
| **Procesamiento IA** | OpenAI `gpt-4o-mini` — 2 nodos (clasificación estructurada + redacción con RAG) |
| **Canal de salida** | Slack — aviso de revisión y publicación |

---

## Arquitectura en una línea

```
Trigger → Filtro → Clasificador IA → Parse JSON → ROUTER
                                                    ├── confianza ≥ 0,7 → RAG → Redactor IA → Airtable → Slack (aviso)
                                                    │                             └── on error → Errores + Resume
                                                    └── confianza < 0,7 → Datos incompletos → Errores
                                                    
                          ⏸  PUNTO HITL — el humano tilda «Aprobado»
                          
Trigger → Filtro (Aprobado = true AND Estado = Aprobado) → Slack (publica) → Estado = Publicado
```

---

## Enlaces

- **Dashboard de control (público, solo lectura)** — https://airtable.com/appBa2raukxsIWANq/shrPPtVYEWWOrAUYq
  Vista agrupada por estado con tasa de errores y tasa de publicación calculadas.

---

## Contenido del repositorio

| Archivo / carpeta | Qué contiene |
|---|---|
| `Proyecto_Final_Ecosistema_IA.pdf` | Documento principal con los cinco entregables |
| `blueprints/` | Los dos escenarios de Make exportados en `.json` |
| `evidencias/` | Capturas de los lienzos, las corridas, las tablas y el dashboard |

Los blueprints no contienen credenciales: Make referencia las conexiones por identificador numérico.

---

## Los cinco entregables

| # | Entregable | Dónde |
|---|---|---|
| 1 | Mapa de arquitectura | PDF, sección 1 |
| 2 | Estructuras de datos documentadas (tablas + esquemas JSON de transferencia) | PDF, sección 2 |
| 3 | Optimización de costos (matriz de decisión por tarea + ahorro estimado) | PDF, sección 3 |
| 4 | Seguridad y resiliencia (minimización, rutas de error, HITL) | PDF, sección 4 |
| 5 | Dashboard de control | Enlace de arriba + PDF, sección 5 |

---

## Rutas de error implementadas

| Qué falla | Ruta | Estado final |
|---|---|---|
| La idea no es clasificable (confianza < 0,7) | Segunda salida del Router | `Datos incompletos`, sin borrador, registro en `Errores` |
| La API de IA no responde | Error handler + directiva `Resume` | `En revisión` con marcador visible, registro en `Errores` con el mensaje crudo |

---

## Test de estrés

Cinco ejecuciones, dos de ellas del «camino infeliz»: una con un dato de entrada fuera de dominio y otra con la API de IA caída, forzada apuntando el nodo a un modelo inexistente. El detalle está en la sección 6 del PDF.
