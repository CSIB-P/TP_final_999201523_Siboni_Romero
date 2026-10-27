# Datos

- **Fuente:** Inside Airbnb, Buenos Aires (Ciudad Autónoma de Buenos Aires, Argentina).
- **Snapshot:** 29 de junio de 2026.
- **Licencia:** Creative Commons Attribution 4.0 International (CC BY 4.0).
- **Página de descarga:** https://insideairbnb.com/get-the-data/
- **Supuestos de la fuente:** https://insideairbnb.com/data-assumptions/
- **Diccionario oficial de variables:** https://docs.google.com/spreadsheets/d/1iWCNJcSutYqpULSQHlNyGInUvHg2BoUGoNRIGa6Szc4/edit

## Archivos que usa el notebook

| Archivo | Contenido | Obligatorio |
|---|---|---|
| `listings.csv.gz` | Publicaciones con 91 columnas: precio, barrio, tipo, capacidad, dormitorios, baños, amenities, reseñas | Sí |
| `reviews.csv.gz` | Una fila por reseña, con `listing_id` y `date` | No: si falta, la sección 8.6 de ventanas temporales se omite |

Colocar ambos archivos en esta carpeta. No se versionan por tamaño y porque la licencia
exige atribución a la fuente, que corresponde hacer sobre el original.

## Advertencia de reproducibilidad

Inside Airbnb mantiene en su página de descarga, de forma gratuita, aproximadamente los
últimos doce meses de datos trimestrales. El snapshot del 29 de junio de 2026 dejará de
estar disponible allí con el tiempo. Para reproducir exactamente este trabajo más adelante
hay que conservar una copia local de los archivos o solicitar el archivo histórico a
Inside Airbnb mediante https://insideairbnb.com/data-requests/.

Las rutas de descarga directa del snapshot utilizado son:

```
https://data.insideairbnb.com/argentina/ciudad-autónoma-de-buenos-aires/buenos-aires/2026-06-29/data/listings.csv.gz
https://data.insideairbnb.com/argentina/ciudad-autónoma-de-buenos-aires/buenos-aires/2026-06-29/data/reviews.csv.gz
```
