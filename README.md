# Precio esperado y detección de desvíos en publicaciones de Airbnb de CABA

> **Proyecto Final Integrador** · Diplomatura en Ciencia de Datos y Análisis Avanzado (UTN BA)
> Ximena Romero y Carlos Siboni

Casi treinta mil anfitriones de la Ciudad de Buenos Aires fijan el precio de su publicación
comparando informalmente con lo que ven alrededor, sin controlar por capacidad, dormitorios,
baños ni equipamiento. Este proyecto estima el **precio de consenso del mercado** para un
conjunto de atributos y señala las publicaciones cuyo posicionamiento se aparta de forma
significativa de ese valor.

## Resultado principal

Todos los números del informe salen de `proyecto_integrador.ipynb` y quedan en `results/`.

| Métrica sobre el conjunto de prueba | Benchmark (mediana por barrio y tipo) | Regresión lineal | **XGBoost** |
|---|---:|---:|---:|
| Error porcentual mediano (MdAPE) | 28,3 % | 24,5 % | **21,8 %** |
| Error porcentual medio (MAPE) | 36,3 % | 34,5 % | **30,5 %** |
| Error absoluto medio (ARS) | 55.001 | 46.104 | **42.165** |
| R² sobre log(precio) | 0,20 | 0,43 | **0,50** |

El criterio de éxito se fijó en la pre-entrega, **antes de ver resultados**: el modelo final debe
superar al benchmark de negocio **y** a la regresión lineal, ambos con diferencia significativa en
un test t pareado con la corrección de Nadeau y Bengio. Los cuatro modelos no lineales lo cumplen.

### Cómo se eligió el modelo

XGBoost obtiene el menor error, pero **los tres modelos de árboles están estadísticamente
empatados**: frente a Gradient Boosting la diferencia es de 0,12 p.p. (p = 0,56) y frente a
Random Forest de 0,14 p.p. (p = 0,49). XGBoost queda seleccionado por tener el menor error medio
y el menor desvío entre repeticiones, **no porque exista evidencia estadística de que sea superior**.

### El esquema de validación cambia el resultado

El 31,3 % de las publicaciones tiene al menos una **gemela del mismo anfitrión** con idéntico
barrio, tipo, capacidad, dormitorios y baños. Con partición aleatoria, esas copias quedan a ambos
lados y el modelo se evalúa sobre lo que ya vio.

El efecto se midió con un control: la regresión lineal, que no puede memorizar publicaciones
individuales, mejora apenas **0,30 p.p.** al pasar a partición aleatoria, mientras que Random Forest
mejora **1,80 p.p.** y XGBoost **1,67 p.p.** El efecto aparece donde el mecanismo lo predice.

### Posicionamiento y demanda: asociación, no causalidad

Las publicaciones por debajo del precio esperado registran más actividad reciente: la mediana de
reseñas de los últimos doce meses cae de **11 en el segundo decil de brecha a 1 en el decil más caro**
(Spearman −0,144, p < 0,0001). El resultado se sostiene al excluir publicaciones sin reseñas, al
restringir a las de más de un año y al medir la actividad en ventanas de 90 y 180 días.

**Esto no demuestra causalidad.** Las reseñas aproximan la demanda pero no registran reservas, y el
dataset es un corte transversal. Quedan abiertas al menos tres explicaciones: calidad no observada,
causalidad inversa y desfase temporal. Al controlar por estratos de calificación la asociación se
mantiene entre las publicaciones de calificación baja y media (−0,164 y −0,193) pero **desaparece
entre las mejor calificadas** (−0,027, p = 0,29).

### Límite de validez encontrado en la auditoría

El modelo señala como subvaluadas al 24,9 % de las habitaciones privadas y al 30,0 % de las
compartidas, contra el 9,5 % de las unidades completas, y como sobrevaluadas al 60,8 % de las
habitaciones de hotel. Como las unidades completas son casi el 90 % de la oferta, el modelo aprende
de ellas y estima peor los segmentos minoritarios. **No debe usarse fuera de unidades completas.**

### Recomendación

No usarlo como recomendador automático de precios. Su uso razonable es ordenar una cartera por
magnitud de brecha y revisar manualmente los casos señalados, restringido a unidades completas y
reentrenado con cada snapshot trimestral.

## Estructura del repositorio

```text
.
├── README.md
├── proyecto_integrador.ipynb      # notebook ejecutado de punta a punta
├── requirements.txt
├── data_log.md                    # 30 decisiones con motivo y evidencia
├── .gitignore
├── data/
│   └── README.md                  # fuente, licencia, descarga y límite de reproducibilidad
├── results/                       # tablas y figuras que cita el informe
│   └── README.md
└── docs/
    ├── Pre-entrega_TP_Final.pdf        # borrador entregado en la instancia previa
    └── Informe_Final_Proyecto_Integrador.pdf
```

## Cómo ejecutar

### Local

```bash
python -m venv .venv
# Windows:      .venv\Scripts\activate
# Linux/macOS:  source .venv/bin/activate
pip install -r requirements.txt
jupyter notebook proyecto_integrador.ipynb
```

Antes de ejecutar hay que colocar `listings.csv` (o `listings.csv.gz`) en `data/`. El archivo
`reviews.csv` es opcional: si falta, se omite la sección 8.6 de ventanas temporales. Ver
`data/README.md` para la descarga.

### Google Colab

1. Subir `proyecto_integrador.ipynb` a Colab.
2. Subir los CSV al panel de archivos (el notebook los busca en `data/` y en el directorio actual).
3. Instalar las dependencias que falten: `!pip install xgboost shap`.
4. Ejecutar **Entorno de ejecución → Ejecutar todas**.

Tiempo de ejecución completo: **unos 3 minutos** en una máquina de cuatro núcleos. Lo más costoso
es la validación cruzada agrupada repetida de Random Forest.

## Flujo metodológico (CRISP-DM)

| Sección del notebook | Contenido | Salidas en `results/` |
|---|---|---|
| 1. Negocio | KPI, benchmark, métrica y criterio de éxito fijados ex ante | – |
| 2. Datos | diccionario, calidad, nulos, concentración por anfitrión | `eda_*.csv` |
| 3. Preparación | universo, variables derivadas, partición agrupada | `prep_*.csv`, `holdout_host_ids.csv` |
| 4. Modelado | grillas, CV agrupada 5×4, test t corregido | `modelado_*.csv`, `fig_validacion.png` |
| 5. Evaluación | conjunto de prueba, **una sola vez** | `holdout_metricas.csv` |
| 6. Robustez | benchmark mediana vs promedio, imputación de `bedrooms` | `robustez_*.csv` |
| 7. Interpretabilidad | SHAP global agrupado por variable original | `shap_global.csv`, `fig_shap_global.png` |
| 8. Profundización | brecha, KPI, casos, demanda, calificación, ventanas, ética | `kpi_*.csv`, `demanda_*.csv`, `auditoria_*.csv` |
| 9. Negocio | escenario económico paramétrico | `escenario_economico.csv` |
| 10. Resumen | números principales | `resumen.csv` |

## Decisiones metodológicas clave

- **El conjunto de prueba se aparta agrupando por anfitrión** y se evalúa una sola vez. No participa
  del universo, ni de las grillas de hiperparámetros, ni del umbral de desvío.
- **El umbral de desvío se fijó en la pre-entrega** en 0,4253 en escala logarítmica (+53,0 % / −34,6 %)
  y no se recalculó con el modelo final, para que el KPI sea comparable entre etapas.
- **Se excluye `availability_365`** de las variables de entrada: refleja en parte la ocupación, que es
  lo que se analiza en la segunda etapa. Con ella, la asociación entre brecha y demanda se atenúa de
  −0,159 a −0,134.
- **El benchmark es la mediana, no el promedio**: el promedio por grupo queda arrastrado por los
  extremos (MdAPE 39,2 % con recorte, 83,8 % sin él) y es incoherente con un modelo entrenado en
  escala logarítmica, cuya retransformación estima la mediana condicional.
- **Las publicaciones señaladas son candidatas a revisión**, no errores de precio comprobados: el
  umbral deriva de la dispersión de los residuos, así que el nivel del KPI refleja en parte esa
  dispersión. Su valor está en la comparación entre segmentos.
- **SHAP explica el comportamiento del modelo, no relaciones causales.**
- **Hallazgo de calidad de datos:** 35 publicaciones del conjunto de prueba (0,61 %) tienen brechas
  superiores al 500 %, con precios medianos de 917.289 ARS por noche contra 105.488 del conjunto.
  Son candidatas a error de carga, no a decisiones de precio.
- **El snapshot cambió respecto de la pre-entrega**, que usó el del 29 de junio de 2026. Inside Airbnb
  conserva gratuitamente alrededor de un año de datos trimestrales y al retomar el proyecto el
  vigente era el de septiembre. Se trabajó con el dato más reciente.

## Consideraciones éticas y de privacidad

Los datos provienen de extracción automatizada del sitio público de Airbnb, no de entrega voluntaria
de los anfitriones, e incluyen identificadores y nombres de personas. La fuente desplaza las
coordenadas hasta unos 150 metros para dificultar la localización exacta de una vivienda. En el
informe no se publican identificadores ni nombres: los casos de ejemplo se presentan por atributos
de la unidad. La auditoría por grupos está en `results/auditoria_etica.csv`; diferencias entre grupos
no prueban ausencia de sesgo.

Inside Airbnb publica estos datos para documentar el impacto del alquiler temporario sobre el mercado
residencial. Una herramienta que optimice ingresos puede aumentar el atractivo de destinar vivienda a
ese uso. El proyecto se presenta como análisis de mercado y no como recomendación automatizada de precios.

## Fuente de datos

**Fuente:** Inside Airbnb, Buenos Aires (CABA, Argentina), snapshot del 27 de septiembre de 2026
**Descarga:** https://insideairbnb.com/get-the-data/
**Supuestos de la fuente:** https://insideairbnb.com/data-assumptions/
**Licencia:** Creative Commons Attribution 4.0 International (CC BY 4.0)
**Volumen:** 29.753 publicaciones con 90 variables, y 1.241.902 reseñas con fecha

Los datos **no se versionan**: ver `data/README.md` para la descarga y para la limitación de
reproducibilidad que implica el ciclo de publicación de la fuente.

## Bibliografía

- Nadeau, C. y Bengio, Y. (2003). *Inference for the generalization error*. Machine Learning, 52, 239-281. https://doi.org/10.1023/A:1024068626366
- Chen, T. y Guestrin, C. (2016). *XGBoost: A scalable tree boosting system*. Proceedings of the 22nd ACM SIGKDD, 785-794. https://doi.org/10.1145/2939672.2939785
- Lundberg, S. y Lee, S. (2017). *A unified approach to interpreting model predictions*. NeurIPS 30.
- scikit-learn. *Statistical comparison of models using grid search*. https://scikit-learn.org/stable/auto_examples/model_selection/plot_grid_search_stats.html
