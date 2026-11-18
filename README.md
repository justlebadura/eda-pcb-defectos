# EDA — ¿Puede la IA detectar si una placa PCB está bien o mal fabricada?

Análisis exploratorio de datos (Python + Pandas + Google Colab) sobre el dataset
**DeepPCB** de defectos en placas de circuito impreso, como base para una propuesta
de investigación sobre **inspección automática de PCB con modelos de IA**.

## Equipo

| Estudiante | Código | GitHub |
|---|---|---|
| Juan Diego Garzón Cordero | 2254270 | [@JD202730](https://github.com/JD202730) |
| Yusmary Tatiana Castañeda García | 2254254 | [@YusTati27](https://github.com/YusTati27) |
| Libardo Enrique Argumedo Mejía | 2264084 | — |

## Pregunta de investigación

> ¿Es viable usar modelos de IA (visión por computadora) para detectar automáticamente
> si una placa de circuito impreso (PCB) está bien o mal ensamblada/fabricada?

## Dataset

| | |
|---|---|
| **Nombre** | DeepPCB |
| **Fuente** | https://github.com/tangsanli5201/DeepPCB (MIT; uso académico) |
| **Paper** | Ding, R. et al., *TDD-net: a tiny defect detection network for printed circuit boards*, CAAI Trans. on Intelligence Technology, 4(2), 2019 |
| **Contenido** | 1,500 pares de imágenes 640×640 px (~48 px/mm, escáner CCD lineal): plantilla sin defectos + placa con defectos |
| **Anotaciones** | Bounding boxes `x1 y1 x2 y2 tipo` para 6 defectos: open, short, mousebite, spur, spurious copper, pin-hole |
| **Total analizado** | **10,013 defectos** en 1,500 imágenes |

## Procedimiento

1. **Descarga:** `git clone` del repositorio del dataset directamente en Colab (sin API de Kaggle).
2. **Carga:** se parsean los 1,501 archivos `.txt` de anotación y se construye un DataFrame con una fila por defecto (14 columnas: imagen, tipo, coordenadas, tamaño, centroide…).
3. **Limpieza:** verificación de nulos, duplicados, bounding boxes degenerados y tipos fuera de rango. El dataset resultó limpio (10,013 registros válidos, 0 nulos, 0 duplicados).
4. **Estadísticas descriptivas:** `describe()` global y agregaciones por tipo de defecto (mediana de ancho/alto/área, cuartiles).
5. **Visualización:** 4 gráficos + un ejemplo visual con bounding boxes dibujados.

## Resultados principales

| Hallazgo | Evidencia |
|---|---|
| Clases casi balanceadas | mousebite 1,965 · open 1,942 · spur 1,625 · short 1,506 · pin-hole 1,501 · spurious copper 1,474 |
| Defectos pequeños | Mediana de área por tipo en el orden de cientos–miles de px² sobre imágenes de 640×640 → objetos pequeños, el mayor reto para detectores |
| Varias decisiones por placa | En promedio **6.7 defectos por imagen** (rango 1–15); el sistema debe localizar y clasificar múltiples defectos antes del veredicto "pasa/rechaza" |
| Distribución espacial amplia | Los defectos se concentran en varias zonas de la placa (mapa de calor hexbin); el modelo debe aprender contexto global |

### Gráficos generados

1. **Barras horizontales** — distribución de defectos por tipo (balance de clases).
2. **Boxplot logarítmico** — área del bounding box por tipo (tamaño de los defectos).
3. **Histograma** — defectos por imagen (carga de decisión por placa).
4. **Mapa de calor hexbin** — concentración espacial de defectos sobre la placa.
5. **BONUS** — placa con defectos vs. plantilla, con bounding boxes coloreados por tipo.

## Conclusiones

- **Sí es viable:** el paper original reporta **98.6% mAP / 98.2% F-score** con una red profunda sobre este dataset; la comparación automática placa-vs-plantilla es la base de los sistemas AOI (Automated Optical Inspection) reales.
- El balance de clases del dataset favorece un entrenamiento sin sesgo hacia clases mayoritarias.
- El tamaño pequeño de los defectos justifica arquitecturas especializadas (TDD-net, YOLO con alta resolución).
- El modelo no decide "bien/mal" con una sola salida: debe detectar N defectos por placa y luego aplicar una regla de aceptación.

## Limitaciones

- **Defectos sintéticos:** agregados digitalmente con Photoshop sobre imágenes reales; puede no generalizar a defectos de fábrica reales.
- **Alcance:** cubre defectos de *fabricación del circuito* (trazas, cobre, agujeros), no de *ensamblaje SMT* (componentes faltantes o mal soldados) — para eso se complementaría con datasets como PCB-AoI.
- **Un solo diseño de placa:** la generalización a otros PCB no está garantizada.
- **Uso académico** (restricción de la licencia del dataset original).

## Cómo ejecutar

Abrir `eda-pcb-defectos.ipynb` en [Google Colab](https://colab.research.google.com) y ejecutar todas las celdas (`Entorno de ejecución → Ejecutar todo`). La primera celda clona el dataset (~130 MB) automáticamente; no se requiere configuración adicional.
