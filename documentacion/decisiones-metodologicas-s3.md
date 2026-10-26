# Decisiones metodológicas · Sumativa 3

Registro de las decisiones tomadas en `S3/notebooks/s3-ecommerce-modelamiento.ipynb`, con su justificación. Las fases anteriores están en [`decisiones-metodologicas-s1.md`](decisiones-metodologicas-s1.md), [`decisiones-metodologicas-s2.md`](decisiones-metodologicas-s2.md) y [`decisiones-metodologicas-f2.md`](decisiones-metodologicas-f2.md).

## Insumos

| Decisión | Justificación |
|---|---|
| Reutilizar la máscara MCAR y la partición 70/30 de F2 (`datos/derivados/`), verificadas con `assert` | Las instrucciones del curso piden ejecutar la rutina de faltantes una sola vez y trabajar F2 y S3 con la misma versión. |
| Modelar en ID > 3.135 | Indicación del docente y recomendación 1 de S2: fuera del bloque inicial no hay separación y las asociaciones no están dominadas por él. |

## Faltantes (sección 1)

| Decisión | Justificación |
|---|---|
| Declarar MCAR | Mecanismo por construcción, verificado en F2 con 20 contrastes. |
| Predictores de imputación: otra variable con faltantes, compras previas e importancia | Matriz de Spearman de S1 con el criterio de estabilidad de S2: \|ρ\| ≥ 0,1 en el completo y en ID > 3.135 con el mismo signo. El descuento queda fuera porque su correlación con el peso depende del bloque (−0,316 frente a 0,001). |
| No usar el objetivo, las llamadas ni la calificación para imputar | Evita trasladar información del resultado, o posiblemente posterior al despacho, a los predictores del modelo. |
| Regresiones ajustadas solo con los casos completos del entrenamiento; imputación encadenada para los envíos sin ambos valores | Sin fuga de información; los 75 + 41 envíos sin costo ni peso necesitan las dos regresiones (van Buuren y Groothuis-Oudshoorn, 2011). |
| Comparar eliminación, mediana y regresión contra los valores verdaderos; regresión con residuo y PMM como sensibilidad | Los faltantes inducidos permiten medir el error de imputación; S2 recomendó métodos que conserven la forma del peso bimodal. |
| Adoptar la imputación por regresión | Menor RMSE (peso: 1.311 g frente a 1.635 g de la mediana) y conserva los 7.864 envíos. Costos declarados: encogimiento de varianza, correlación costo-peso exagerada y pesos imputados en el hueco 2.000-3.999 g (43,6 % de los imputados). |

## Modelos (sección 2)

| Decisión | Justificación |
|---|---|
| Escala común: media y DE de los valores observados del entrenamiento | Coeficientes comparables entre modelos y entre estrategias de imputación. |
| Candidatas solo previas al despacho | Recomendación 3 de S2 (fuga): calificación y llamadas excluidas. |
| M1 validado (importancia alta, costo, peso), M2 stepwise bidireccional por AIC con categóricas como grupo, M3 reducido (importancia alta) | Indicación del docente. El BIC se registra en cada paso y su selección (modelo vacío) justifica la parsimonia de M3. |
| Elección por validación cruzada (5 particiones) con la regla de un error estándar | Se elige sin mirar la prueba; M1 está a menos de un EE de M2 con menos parámetros. |
| Inferencia sin ponderar; clasificación con `class_weight="balanced"` | Coeficientes de máxima verosimilitud interpretables; la ponderación fija un punto de operación con recall útil (indicación del curso). |
| Bootstrap de 10.000 remuestras con IC percentil y BCa (aceleración por jackknife) | Recomendación 7 de S2; mismo rigor que en S2. |
| Frecuencia de selección del stepwise en 1.000 remuestras | La inestabilidad de un modelo elegido por algoritmo también está en qué variables entran. |
| Diagnóstico: VIF, Box-Tidwell, distancia de Cook con reajuste, residuos de Pearson y Hosmer-Lemeshow | Apuntes de regresión logística y de diagnóstico. |
| AUC con IC bootstrap y prueba de permutación (10.000) | Contrasta formalmente si cada modelo supera el azar (Ojala y Garriga, 2010). |

## Impacto e integración (secciones 3 y 4)

| Decisión | Justificación |
|---|---|
| Ajustar el modelo final también con los valores verdaderos | Referencia exacta para medir el desvío de cada estrategia, posible solo con faltantes inducidos. |
| Contraste en el archivo completo: partición aleatoria frente a partición por orden de ID | Recomendación 1 de S2. Muestra que el AUC aparente (0,677) proviene del bloque inicial: con envíos posteriores cae a 0,501. |
| No incluir el indicador del bloque como predictor | Separa perfectamente la clase (100 % de retrasos), así que su coeficiente de máxima verosimilitud diverge. |
| Síntesis en Markdown con control de consistencia | Una celda verifica que cada cifra citada coincida con el cálculo. |
