# Resultados

Todos los archivos de esta carpeta se generan al ejecutar `proyecto_integrador.ipynb` y
son los que cita el informe final. `log_ejecucion.txt` guarda la salida completa de la corrida.

| Archivo | Contenido |
|---|---|
| `eda_diccionario.csv` | Diccionario de las 10 variables principales con nulos, mediana y rango |
| `eda_calidad.csv` | Nulos en el resto de las variables relevantes |
| `eda_bedrooms.csv`, `eda_bedrooms_por_tipo.csv` | Patrón de nulos de `bedrooms` |
| `eda_concentracion.csv` | Concentración de la oferta por anfitrión |
| `eda_barrios_top10.csv` | Diez barrios con mayor oferta |
| `prep_embudo.csv` | Pasos de construcción del universo de análisis |
| `prep_particion.csv` | Partición desarrollo y holdout agrupada por anfitrión |
| `holdout_host_ids.csv` | Identificadores de los anfitriones apartados en el holdout |
| `modelado_grillas.csv` | Búsqueda de hiperparámetros, GroupKFold de 3 folds |
| `modelado_cv_agrupada.csv` | Validación cruzada agrupada 5x4, con y sin agrupación |
| `modelado_criterio_exito.csv` | Test t pareado corregido contra benchmark y regresión lineal |
| `modelado_ganador_vs_rivales.csv` | El modelo elegido frente a los demás modelos de árboles |
| `fig_validacion.png` | Error según el esquema de partición |
| `holdout_metricas.csv` | Evaluación única sobre el holdout |
| `robustez_benchmark.csv` | Benchmark por mediana y por promedio, con y sin recorte |
| `robustez_bedrooms.csv` | Sensibilidad de la imputación de `bedrooms` |
| `shap_global.csv`, `fig_shap_global.png` | Importancia global agrupada por variable original |
| `kpi_desvio.csv`, `kpi_por_barrio.csv` | KPI de desvío, total y por barrio |
| `casos_ejemplo.csv` | Casos representativos traducidos a "X% por encima o por debajo" |
| `calidad_extremos.csv` | Publicaciones con brecha extrema, sospechosas de error de carga |
| `demanda_asociacion.csv`, `demanda_deciles.csv`, `fig_demanda_deciles.png` | Asociación entre brecha y actividad reciente |
| `demanda_por_calificacion.csv` | La asociación dentro de estratos de calificación |
| `demanda_ventanas.csv` | La asociación según la ventana temporal de reseñas |
| `auditoria_etica.csv`, `auditoria_escala_anfitrion.csv` | Reparto del señalamiento entre grupos |
| `escenario_economico.csv` | Escenario paramétrico de magnitud de la brecha |
| `resumen.csv` | Números principales del proyecto |
