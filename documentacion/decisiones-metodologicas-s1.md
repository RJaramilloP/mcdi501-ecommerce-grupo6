# Bitácora de decisiones metodológicas

Registro de las decisiones que condicionan el análisis, con su justificación y la evidencia que las respalda.
Las referencias a tablas y figuras corresponden a la numeración del notebook
`notebooks/s1-ecommerce-analisis.ipynb` y a los archivos de `resultados/`.

| ID | Fase | Decisión | Justificación | Evidencia |
|---|---|---|---|---|
| D01 | S1 | La variable objetivo `Reached.on.Time_Y.N` se interpreta como 1 = fuera de plazo y 0 = a tiempo. | Codificación adoptada por el grupo según la descripción del conjunto en Kaggle; documentada en el README. | README; notebook §1 |
| D02 | S1 | No se elimina ni imputa ningún registro. | No hay faltantes, duplicados ni valores fuera de dominio. | Tabla 1 (`tab01_decisiones_tratamiento.csv`) |
| D03 | S1 | Los descuentos altos (hasta 65) se conservan. | No son errores de captura; se reporta la mediana junto a la media. | Tabla 3; Figura 2 |
| D04 | S1 | `Discount_offered` se analiza en su escala original. | Su unidad no está documentada; no se interpreta como porcentaje. | Diccionario §1.3 |
| D05 | S1 | Descuento, llamadas y calificación se interpretan como asociaciones, no como causas. | Se desconoce si se registraron antes o después de la entrega (riesgo de fuga de información). | Tabla 1 |
| D06 | S1 | `ID` se excluye como predictor y se usa como diagnóstico de estructura. | Es un correlativo alineado con el orden del archivo. | §1.4; §2.6 |
| D07 | S1 | Correlación de Spearman como medida principal; Pearson como referencia. | Variables asimétricas, bimodales u ordinales. | Tabla 5; Figura 4 |
| D08 | S1 | IC normales (TCL) para medias y Wald para la proporción; Wilson como complemento. | Métodos del curso; n = 10.999 respalda la aproximación normal. | Tabla 10 |
| D09 | S1 | α = 0,05 y tamaño del efecto en todas las pruebas. | Con n grande, diferencias pequeñas son significativas (ASA, 2016). | §4 |
| D10 | S1 | Prueba 1: t de Welch (peso). | Dos grupos independientes de tamaño distinto; Levene rechaza igualdad de dispersión. | Tablas 12 y 12b; Figura 8 |
| D11 | S1 | Prueba 2: χ² de independencia (importancia × cumplimiento), con V de Cramér, RR y OR. | Ambas variables categóricas; frecuencia esperada mínima 382. | Tablas 13, 14 y 14b; Figura 9 |
| D12 | S1 | **Análisis de sensibilidad en el subconjunto ID > 3.135.** | El archivo tiene dos regímenes: el bloque ID ≤ 3.135 tiene 100 % fuera de plazo, concentra los descuentos > 10 y los pesos de 2.000–3.999 g, y produce reglas deterministas. La inferencia supone una población única. | Tablas 8, 9, 11 y 16; Figuras 7 y 10 |
| D13 | S1 | El análisis principal se mantiene sobre los 10.999 registros. | Exigencia de la evaluación; la sensibilidad es complementaria y exploratoria. | §2.6 |
| D14 | S1 | El subconjunto no se usa para estimar la proporción de retrasos. | El corte se definió en parte con la variable objetivo: la proporción queda sesgada a la baja. | Tabla 11 |
| D15 | S1 | Un contraste "mantiene significancia y dirección" si conserva la decisión al 5 % y el signo en ambos conjuntos; no se usa el término "robusto". | La comparación es exploratoria y post hoc; mantener significancia no demuestra robustez general ni la falta de significancia demuestra ausencia de efecto. Costo y llamadas invierten el signo (efecto de agregación). | Tablas 16 y 17; Figura 10 |
| D16 | S1 | Se agrega el contraste z de dos proporciones (importancia alta vs. resto) en ambos conjuntos, junto al χ² global. | Observación docente de la Formativa 1: en ID > 3.135, χ² (2 gl) da p = 0,059 y z (1 gl) da p = 0,036. El contraste se eligió tras la descriptiva: es exploratorio y sus valores p no se ajustan por multiplicidad. | Tablas 15 y 16 |
| D17 | S1 | La independencia de las observaciones se declara como supuesto no verificable y se explicitan las limitaciones de origen (empresa, período y diseño muestral no documentados). | El origen del archivo no permite verificar independencia ni representatividad; los resultados no se extrapolan a otras operaciones. | §3 y §4 del notebook |

## Implicancias para las fases siguientes

- **S2.** Validar con bootstrap (≥ 10.000; percentil y BCa) los IC de la Tabla 10 y con permutación (≥ 10.000)
  las pruebas 1 y 2, en el conjunto completo y en ID > 3.135. Estimar IC bootstrap de las correlaciones de
  Spearman. Tratar el bloque inicial como subpoblación influyente en el análisis de robustez.
- **S2.** Validar también por permutación el contraste z alta vs. resto (D16).
- **S3.** No hay faltantes: la imputación por regresión requerirá inducirlos de forma controlada (MCAR/MAR) y
  comparar estrategias contra el valor real. En la regresión logística, el descuento producirá separación casi
  perfecta si se incluye sin tratamiento.
