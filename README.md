# Incumplimiento de plazos en envíos de comercio electrónico

**MCDI501 Estadística Computacional para la Toma de Decisiones** · Magíster en Ciencia de Datos e Inteligencia Artificial · Universidad Andrés Bello

**Grupo 6:** Marco Álvarez Araya y Ricardo Jaramillo
**Docente:** Dr. Jean Paul Maidana González

Este repositorio reúne las entregas del proyecto del curso. Cada sumativa tiene su carpeta y la siguiente se construye sobre los resultados de la anterior.

| Entrega | Carpeta | Contenido principal | Fecha |
|---|---|---|---|
| Sumativa 1 · Análisis exploratorio e inferencial | [`S1/`](S1/) | Informe, notebook y resultados (versión evaluada) | 14/10/2026 |
| Sumativa 2 · Validación, simulación y remuestreo | [`S2/`](S2/) | Informe, notebook, resultados y **reporte de resultados validados** | 20/10/2026 |
| Formativa 2 · Primer modelo de regresión logística | [`F2/`](F2/) | Notebook con faltantes MCAR, modelo logístico, diagnóstico y desempeño | 25/10/2026 |
| Sumativa 3 · Modelamiento predictivo integrado | [`S3/`](S3/) | Informe final, notebook maestro, resultados | 28/10/2026 |

## Sumativa 3

| Entregable | Archivo |
|---|---|
| Informe final (PDF, máx. 10 páginas) | [`S3/informe-s3-ecommerce.pdf`](S3/informe-s3-ecommerce.pdf) |
| Notebook maestro ejecutado | [`S3/notebooks/s3-ecommerce-modelamiento.ipynb`](S3/notebooks/s3-ecommerce-modelamiento.ipynb) |
| Tablas y figuras citadas en el informe | [`S3/resultados/`](S3/resultados/) |
| Bitácora de decisiones metodológicas | [`documentacion/decisiones-metodologicas-s3.md`](documentacion/decisiones-metodologicas-s3.md) |

**Pregunta.** ¿Qué modelo usar para anticipar retrasos, qué tan estable es y qué cambia según cómo se traten los faltantes? Todo se ajusta en ID > 3.135 con la máscara MCAR y la partición 70/30 de F2.

| Sección | Método |
|---|---|
| 1. Faltantes | Imputación por regresión lineal múltiple (predictores de la matriz de S1 estables en ID > 3.135, ajuste solo en entrenamiento, imputación encadenada); comparación de eliminación, mediana y regresión contra los valores verdaderos, con regresión + residuo y PMM como sensibilidad |
| 2. Logística | Tres modelos (validado por S1/S2, stepwise por AIC, reducido), validación cruzada con regla de un error estándar, bootstrap de 10.000 remuestras (percentil y BCa) y frecuencia de selección del stepwise, VIF, Box-Tidwell, distancia de Cook, Hosmer-Lemeshow, métricas con ponderación de clases, AUC con IC bootstrap y prueba de permutación |
| 3. Impacto | Modelo final ajustado con cada estrategia y con los valores verdaderos |
| 4. Integración | Contraste en el archivo completo (partición aleatoria frente a orden por ID) y trazabilidad de las ocho recomendaciones de S2 |

**Resultado principal.** El modelo final cumple los supuestos y está calibrado, pero no discrimina mejor que el azar (AUC de prueba 0,515 [0,492; 0,539]; permutación p = 0,104), y sus tres coeficientes son inestables. El mismo modelo alcanza un AUC de 0,677 con una partición aleatoria del archivo completo y de 0,501 cuando se evalúa en envíos posteriores: la capacidad predictiva aparente proviene del bloque inicial. La estrategia de faltantes no cambia las conclusiones (desvíos menores que un error estándar respecto de los valores verdaderos).

## Formativa 2

| Entregable | Archivo |
|---|---|
| Notebook ejecutado | [`F2/notebooks/f2-ecommerce-modelo-logistico.ipynb`](F2/notebooks/f2-ecommerce-modelo-logistico.ipynb) |
| Tablas y figuras | [`F2/resultados/`](F2/resultados/) |
| Máscara de faltantes MCAR y partición 70/30 (reutilizadas en S3) | [`datos/derivados/`](datos/derivados/) |
| Bitácora de decisiones metodológicas | [`documentacion/decisiones-metodologicas-f2.md`](documentacion/decisiones-metodologicas-f2.md) |

Ensayo del flujo de modelamiento de la Sumativa 3, ajustado en el subconjunto ID > 3.135 que S2 identificó como régimen sin el bloque inicial. Se eliminan al azar (MCAR) el 12 % de los valores de costo y peso, con semilla 42 y una sola vez: la máscara (solo ID y variable, sin valores) queda versionada en `datos/derivados/mascara_mcar.csv`. Los faltantes se imputan por la mediana del entrenamiento, se ajusta una logística con importancia alta, costo y peso (elegidas por su evidencia en S1 y S2), y se evalúa en el 30 % de prueba. Resultado: el modelo no discrimina mejor que el azar (AUC = 0,515 [0,491; 0,538]; prueba de permutación p = 0,107), coherente con la dependencia del bloque inicial documentada en S2.

## Sumativa 2

| Entregable | Archivo |
|---|---|
| Informe técnico (PDF, 7 páginas) | [`S2/informe-s2-ecommerce.pdf`](S2/informe-s2-ecommerce.pdf) |
| Notebook ejecutado | [`S2/notebooks/s2-ecommerce-validacion.ipynb`](S2/notebooks/s2-ecommerce-validacion.ipynb) |
| Reporte de resultados validados para la Sumativa 3 | [`S2/resultados_validados.md`](S2/resultados_validados.md) · [`.csv`](S2/resultados_validados.csv) |
| Tablas y figuras citadas en el informe | [`S2/resultados/`](S2/resultados/) |
| Bitácora de decisiones metodológicas | [`documentacion/decisiones-metodologicas-s2.md`](documentacion/decisiones-metodologicas-s2.md) |

### Pregunta de esta etapa

¿Qué resultados de la Sumativa 1 se sostienen cuando se abandonan los supuestos paramétricos, se simula su efecto sobre lotes de envíos y se los somete a pruebas de robustez? La S2 no recalcula S1 desde cero: el notebook lee los parámetros exportados por S1 (`S1/resultados/tablas/`), verifica que coincidan con el recálculo y los pone a prueba en el conjunto completo y en el subconjunto ID > 3.135.

### Métodos

| Sección | Método | Réplicas |
|---|---|---|
| 1. Intervalos de S1 | Bootstrap no paramétrico, IC percentil y BCa; medianas con IC exacto por estadísticos de orden y bootstrap suavizado | 10.000 |
| 2. Pruebas de S1 | Permutación de Welch (t estudentizado), χ², z y diferencia de medianas; Holm en el subconjunto | 10.000 (100.000 en ID > 3.135) |
| 3. Correlaciones | IC bootstrap del ρ de Spearman con clasificación de estabilidad | 10.000 |
| 4. Monte Carlo | Lote de N envíos con parámetros de S1 e incertidumbre en dos niveles; convergencia, escenarios, tornado; datos sintéticos (fidelidad, potencia y cobertura de los IC) | 10.000 por escenario |
| 5. Robustez | Estimadores robustos y jackknife; bootstrap por bloques móviles (también para las correlaciones); barrido del corte del subconjunto | 10.000 |
| 6. Documentación | Reporte de resultados validados para S3, generado por el notebook, con una regla única de clasificación (robusto, señal exploratoria, dependiente del bloque) | — |

### Resultados principales

- **Los IC de S1 se validan:** percentil, BCa y el IC normal de S1 coinciden en medias y proporción (|z₀| ≤ 0,011; |a| ≤ 0,003). La mediana del peso es 4.149 g [4.119; 4.183]; la del descuento tiene una distribución bootstrap degenerada (100 % de las réplicas = 7), documentada como límite del método.
- **Las pruebas de S1 se validan:** las ocho comparaciones de permutación concuerdan con las pruebas clásicas. En ID > 3.135 el contraste de importancia alta frente al resto (p = 0,0357) no resiste la corrección de Holm (p = 0,143).
- **Ninguna correlación es estable:** la del descuento (0,335) desaparece en ID > 3.135, la del peso se atenúa 91 % y las de costo y llamadas cambian de signo entre conjuntos, también con bootstrap por bloques.
- **Monte Carlo:** un lote de 1.000 envíos varía entre 56,5 % y 62,8 % (intervalo central del 95 %), mucho más que el IC del parámetro; 29,5 % de los lotes supera el límite superior del IC de S1 sin que el proceso cambie. En un escenario hipotético, no causal, en que los productos de importancia alta tuvieran la tasa del resto, se evitarían en promedio 4,9 retrasos por cada 1.000 envíos.
- **Datos sintéticos:** un generador normal con los parámetros de S1 no reproduce la bimodalidad del peso; con n = 7.864, la potencia para detectar 4 pp entre importancia alta y el resto es 49 %; y con muestras pequeñas el BCa se acerca más a la cobertura nominal que el IC clásico; con n grande los tres métodos coinciden.
- **Robustez:** en el conjunto completo el error estándar de la proporción crece 11,3 veces con bootstrap por bloques de 500 envíos, sin estabilizarse (estructura según el orden del archivo); en el subconjunto, no. Las estimaciones del subconjunto no dependen del corte exacto.

## Datos

Se utiliza [E-Commerce Shipping Data](https://www.kaggle.com/datasets/prachi13/customer-analytics) (Prachi13, Kaggle): 10.999 envíos y 12 variables. `Reached.on.Time_Y.N` = 1 indica entrega fuera de plazo.

**El CSV no se incluye en el repositorio** (su licencia de redistribución no está documentada). Para reproducir:

1. Descargar el archivo desde Kaggle y guardarlo como `datos/originales/ecommerce-shipping.csv`.
2. Verificar la versión (los notebooks también lo comprueban):

   ```bash
   shasum -a 256 datos/originales/ecommerce-shipping.csv                    # macOS / Linux
   ```
   ```powershell
   Get-FileHash datos\originales\ecommerce-shipping.csv -Algorithm SHA256   # Windows
   ```

   Valor esperado: `dcdc3fd8f0507ec400174ef70d88d634321cc2ce8f4ea11bf4e8c847fdd325b3`

## Estructura

```text
.
├── README.md
├── requirements.txt
├── datos/originales/             # CSV local (no versionado)
├── datos/derivados/              # máscara MCAR y partición 70/30 (solo ID, versionadas)
├── documentacion/
│   ├── decisiones-metodologicas-s1.md
│   ├── decisiones-metodologicas-s2.md
│   ├── decisiones-metodologicas-f2.md
│   └── decisiones-metodologicas-s3.md
├── S1/
│   ├── informe-s1-ecommerce.pdf
│   ├── notebooks/s1-ecommerce-analisis.ipynb
│   └── resultados/{tablas,figuras}/
└── S2/
    ├── informe-s2-ecommerce.pdf
    ├── notebooks/s2-ecommerce-validacion.ipynb
    ├── resultados/{tablas,figuras}/   # s2_tab*.csv, s2_fig*.png
    ├── resultados_validados.csv
    └── resultados_validados.md
├── F2/
│   ├── notebooks/f2-ecommerce-modelo-logistico.ipynb
│   └── resultados/{tablas,figuras}/   # f2_tab*.csv, f2_fig*.png
└── S3/
    ├── informe-s3-ecommerce.pdf
    ├── notebooks/s3-ecommerce-modelamiento.ipynb
    └── resultados/{tablas,figuras}/   # s3_tab*.csv, s3_fig*.png
```

## Reproducción

Requisitos: Python 3.13 y el CSV ubicado según la sección **Datos**.

```bash
python3 -m venv .venv
source .venv/bin/activate          # Windows: .\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
```

Abrir el notebook en VS Code o Jupyter, seleccionar el entorno `.venv` y ejecutar todas las celdas (**Run All**). El notebook de S2 tarda entre 4 y 8 minutos (cientos de miles de remuestras, permutaciones e iteraciones). Usa la semilla 42 con un generador reiniciado en cada sección, por lo que reproduce exactamente las mismas tablas en cada ejecución; en otra máquina pueden variar los últimos decimales por diferencias de coma flotante, sin efecto sobre las cifras informadas.

**Orden de ejecución:** S2 lee las tablas de `S1/resultados/tablas/`, y F2 lee las de S1 y S2, todas ya incluidas en el repositorio. No es necesario volver a ejecutar S1 ni S2; si se ejecutan, regeneran esas tablas con los mismos valores salvo diferencias de coma flotante en los últimos decimales. F2 tarda menos de un minuto y S3, unos 7 minutos (10.000 remuestras bootstrap con reajuste del modelo, jackknife y 1.000 selecciones stepwise). S3 lee la máscara y la partición de `datos/derivados/`, que crea F2: ejecutar F2 antes de S3 si esos archivos no existen.

## Limitaciones

- No se documentan la empresa, el período ni el diseño muestral: no puede asegurarse representatividad.
- El corte en ID 3.135 se identificó después de observar los datos; el barrido del corte (S2, sección 5) muestra que las estimaciones del subconjunto no dependen de su valor exacto, pero la comparación sigue siendo exploratoria.
- Se desconoce si el descuento, las llamadas y la calificación se registraron antes o después de la entrega (riesgo de fuga de información para S3).
- La simulación Monte Carlo supone envíos independientes dentro del lote y solo incorpora la importancia del producto, la única señal presente en ambos conjuntos.
