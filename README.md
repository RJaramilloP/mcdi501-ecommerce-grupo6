# Incumplimiento de plazos en envíos de comercio electrónico

**MCDI501 Estadística Computacional para la Toma de Decisiones** · Magíster en Ciencia de Datos e Inteligencia Artificial · Universidad Andrés Bello

**Grupo 6:** Marco Álvarez Araya y Ricardo Jaramillo
**Docente:** Dr. Jean Paul Maidana González

Este repositorio reúne las entregas del proyecto del curso. Cada sumativa tiene su carpeta y la siguiente se construye sobre los resultados de la anterior.

| Entrega | Carpeta | Contenido principal | Fecha |
|---|---|---|---|
| Sumativa 1 · Análisis exploratorio e inferencial | [`S1/`](S1/) | Informe, notebook y resultados (versión evaluada) | 14/10/2026 |
| Sumativa 2 · Validación, simulación y remuestreo | [`S2/`](S2/) | Informe, notebook, resultados y **reporte de resultados validados** | 20/10/2026 |

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
├── documentacion/
│   ├── decisiones-metodologicas-s1.md
│   └── decisiones-metodologicas-s2.md
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
```

## Reproducción

Requisitos: Python 3.13 y el CSV ubicado según la sección **Datos**.

```bash
python3 -m venv .venv
source .venv/bin/activate          # Windows: .\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
```

Abrir el notebook en VS Code o Jupyter, seleccionar el entorno `.venv` y ejecutar todas las celdas (**Run All**). El notebook de S2 tarda entre 4 y 8 minutos (cientos de miles de remuestras, permutaciones e iteraciones). Usa la semilla 42 con un generador reiniciado en cada sección, por lo que reproduce exactamente las mismas tablas en cada ejecución; en otra máquina pueden variar los últimos decimales por diferencias de coma flotante, sin efecto sobre las cifras informadas.

**Orden de ejecución:** S2 lee las tablas de `S1/resultados/tablas/`, que ya están en el repositorio. No es necesario volver a ejecutar S1; si se ejecuta, regenera esas tablas con los mismos valores.

## Limitaciones

- No se documentan la empresa, el período ni el diseño muestral: no puede asegurarse representatividad.
- El corte en ID 3.135 se identificó después de observar los datos; el barrido del corte (S2, sección 5) muestra que las estimaciones del subconjunto no dependen de su valor exacto, pero la comparación sigue siendo exploratoria.
- Se desconoce si el descuento, las llamadas y la calificación se registraron antes o después de la entrega (riesgo de fuga de información para S3).
- La simulación Monte Carlo supone envíos independientes dentro del lote y solo incorpora la importancia del producto, la única señal presente en ambos conjuntos.
