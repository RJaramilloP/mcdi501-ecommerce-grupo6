# Decisiones metodológicas · Formativa 2

Registro de las decisiones tomadas en `F2/notebooks/f2-ecommerce-modelo-logistico.ipynb`, con su justificación. Las de las sumativas anteriores están en [`decisiones-metodologicas-s1.md`](decisiones-metodologicas-s1.md) y [`decisiones-metodologicas-s2.md`](decisiones-metodologicas-s2.md).

## Datos y faltantes

| Decisión | Justificación |
|---|---|
| Modelar en ID > 3.135 (7.864 envíos, 43,6 % fuera de plazo) | S2 mostró que las asociaciones de S1 dependen del bloque inicial; el docente indicó trabajar dentro de ID > 3.135. Allí el descuento no separa las clases y no se necesita Firth. |
| Inducir faltantes MCAR en costo y peso, 12 % por variable (1.320 valores), semilla 42 | La base no tiene faltantes (S1, Tabla 1). Instrucción del curso: 1-2 variables numéricas, 10 %-15 %, mecanismo completamente aleatorio. El costo es el único parámetro robusto de S2; el peso es el caso que S2 advirtió no imputar con supuesto normal. |
| Máscara versionada en `datos/derivados/mascara_mcar.csv` (solo ID y variable) | La rutina se ejecuta una sola vez y S3 la reutiliza; no se versionan valores del dataset (licencia no documentada). Los valores verdaderos permiten medir el error de imputación en S3. |
| Verificar el mecanismo con 20 contrastes y Holm | Comprueba la implementación: ningún contraste es significativo (mínimo p = 0,061; p de Holm = 1). |
| Imputación por la mediana del entrenamiento | Conserva el 22 % de envíos que perdería la eliminación de casos con una potencia ya baja (49 % en S2); la mediana es robusta y es el resumen que S2 recomendó para el peso bimodal. Calculada solo en entrenamiento para evitar fuga. |

## Partición y preparación

| Decisión | Justificación |
|---|---|
| 70/30 aleatoria estratificada por el objetivo, `random_state = 42`, IDs en `datos/derivados/particion_70_30.csv` | Conserva la tasa de retraso en ambos conjuntos. Es válida porque el bootstrap por bloques de S2 no encontró estructura por orden en ID > 3.135 (razón de EE de 0,92 a 1,01). S3 usa la misma partición. |
| Importancia alta como indicador binario; costo y peso estandarizados con media y DE del entrenamiento | S1 y S2 encontraron la diferencia entre alta y el resto. La estandarización hace comparables los odds ratios por desviación estándar. |

## Modelo y evaluación

| Decisión | Justificación |
|---|---|
| Predictores: importancia alta, costo y peso | Evidencia dentro de ID > 3.135 en S2: importancia alta es la única señal que conserva dirección y magnitud en ambos conjuntos (+5,81 y +4,31 pp); costo y peso tienen IC de Spearman que excluyen el 0. |
| Excluir descuento y compras previas | Asociación nula en ID > 3.135 (S2, Tabla 3). |
| Excluir llamadas y calificación | Posible registro posterior al despacho (S1, Tabla 1; S2, recomendación 3). |
| Excluir medio de transporte, bloque de almacén y género | Sin asociación en S1 (χ² del medio p = 0,69; tasas casi iguales por categoría). |
| Holm sobre los tres predictores | Recomendación 8 de S2 para contrastes múltiples en el subconjunto. |
| Dos umbrales: 0,5 y la tasa de entrenamiento | Con probabilidades entre 0,385 y 0,520, el umbral 0,5 reproduce la clase mayoritaria; la tasa de entrenamiento se fija sin mirar la prueba y anticipa la ponderación de clases de S3. |
| Línea base de la clase mayoritaria | Recomendación 6 de S2. |
| IC bootstrap del AUC (10.000) y prueba de permutación (10.000; Ojala y Garriga, 2010) | Cuantifica si el AUC supera el azar con el mismo rigor de remuestreo de S2. |
| Síntesis en Markdown con control de consistencia | Una celda verifica que cada cifra citada coincida con el cálculo. |
