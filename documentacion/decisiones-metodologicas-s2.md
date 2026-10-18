# Decisiones metodológicas · Sumativa 2

Registro de las decisiones tomadas en `S2/notebooks/s2-ecommerce-validacion.ipynb`, con su justificación. Las decisiones de la Sumativa 1 están en [`decisiones-metodologicas-s1.md`](decisiones-metodologicas-s1.md).

## Insumos y trazabilidad

| Decisión | Justificación |
|---|---|
| Leer los parámetros de S1 desde `S1/resultados/tablas/` y verificar que coincidan con el recálculo (`assert`) | Garantiza que S2 se construye sobre resultados propios de S1 y no sobre cifras transcritas a mano. |
| Repetir cada análisis en el conjunto completo y en ID > 3.135 | Línea de trabajo validada por el docente en la retroalimentación de S1. |
| Verificar el SHA-256 del CSV y la propiedad del bloque inicial (100 % fuera de plazo en ID ≤ 3.135) | Asegura que se analiza la misma versión de los datos y el mismo subconjunto que en S1. |
| Semilla 42 y `numpy.random.default_rng(SEED)` reiniciado al inicio de cada sección | Cada sección es reproducible por separado y no depende de lo ejecutado antes (apunte «Generadores de números aleatorios»). |
| `B_BOOT`, `B_PERM` y `B_MC` como constantes de 10.000, con `assert` | Mínimo exigido por la rúbrica; impide ejecutar con menos réplicas por error. |

## Bootstrap (sección 1)

| Decisión | Justificación |
|---|---|
| Validar los cuatro parámetros de S1 y agregar las medianas de peso y descuento | Las medianas fueron pedidas por el docente; el peso es bimodal y el descuento muy asimétrico. |
| IC clásico de las medianas por estadísticos de orden (binomial exacto) | S1 no tenía IC de medianas; este intervalo no supone ninguna distribución y sirve de referencia clásica. |
| Empates con θ̂ contados como media unidad en z₀ | Convención de `scipy.stats.bootstrap`; evita sesgar z₀ con datos discretos. |
| Variante BC (a = 0) cuando el jackknife es constante | Con empates en el centro, el jackknife de la mediana es constante y la aceleración queda 0/0; el jackknife no es consistente para la mediana (Efron y Tibshirani, 1993). |
| Documentar la mediana degenerada del descuento y complementarla con bootstrap suavizado (ruido U(−0,5; 0,5)) | Es un límite real del método con estimadores no suaves; el suavizado trata el descuento como magnitud redondeada (Hall et al., 1989). |
| Verificación cruzada con `scipy.stats.bootstrap` | Descarta errores de implementación del BCa propio. |

## Permutación (sección 2)

| Decisión | Justificación |
|---|---|
| Permutar las etiquetas de cumplimiento (manteniendo su número) | Bajo H₀ de no asociación, las etiquetas son intercambiables. |
| Estadístico t de Welch estudentizado | Mantiene la validez asintótica con varianzas distintas (Chung y Romano, 2013); Levene las detectó en S1. |
| p = (#extremos + 1)/(B + 1) | Incluye la asignación observada y evita valores p iguales a cero. |
| 100.000 permutaciones en el subconjunto | Los valores p de S1 están cerca de 0,05; el error Monte Carlo baja de cerca de 0,0024 a 0,0007. |
| Diferencia de medianas del peso, con la prueba de Mood como referencia clásica | Sugerencia del docente; es robusta a la bimodalidad. |
| Holm sobre las cuatro pruebas del subconjunto | El análisis de sensibilidad multiplica los contrastes; distingue señales confirmadas de exploratorias. Es una familia conservadora (Welch y medianas miden el mismo efecto); con solo las tres pruebas de S1 la conclusión no cambia (z: p ajustado 0,107). |

## Correlaciones (sección 3)

| Decisión | Justificación |
|---|---|
| ρ de Spearman, re-ordenando en cada remuestra | Coherente con S1; el IC refleja también la variabilidad del ordenamiento con empates. |
| Clasificación por dirección, significancia y magnitud | Con n ≈ 10.000 una correlación mínima puede excluir el 0 sin relevancia práctica; por eso «estable» exige conservar al menos la mitad de la magnitud. |

## Monte Carlo y datos sintéticos (sección 4)

| Decisión | Justificación |
|---|---|
| Modelo de lote con importancia alta frente al resto | Es la única señal presente en ambos conjuntos en S1; agregar variables sin respaldo sería error de modelo. |
| Parámetros exclusivamente de la Tabla 15 de S1, extraídos de N(θ̂, EE²) en cada iteración | Propaga la incertidumbre de la estimación (simulación en dos niveles). |
| Umbral = límite superior del IC de Wald de S1 | Contrasta la incertidumbre del parámetro con la variabilidad de un lote. |
| Misma semilla para todos los escenarios; los normales del nivel de parámetros se generan siempre | Los escenarios con igual N comparten exactamente los mismos números aleatorios (números aleatorios comunes), incluido el contrafactual de la intervención. |
| Fracción de varianza atribuible a los parámetros: Var(E[tasa | θ]) / Var(tasa) con los mismos θ, contrastada con el método delta | Evita comparar varianzas de dos flujos aleatorios distintos. |
| Error Monte Carlo también para P(lote > umbral) y P99 | Son las cantidades que se usan para decidir, no solo la media (que es conocida analíticamente). |
| Convergencia: media acumulada, EE en log-log, Geweke y cinco semillas | Diagnósticos del apunte «Análisis de convergencia y precisión»; Geweke se interpreta junto a los demás porque las iteraciones son independientes. |
| Validar la fidelidad del generador antes de usarlo; comparar un generador normal con uno de perturbación para el peso | Apunte «Generación de datos sintéticos»: fidelidad antes que utilidad. |
| Curva de potencia del contraste z (2 a 6 pp) y n necesario para 80 %, con control bajo H₀ | La «potencia observada» al efecto estimado es una función del valor p y no aporta evidencia nueva (Hoenig y Heisey, 2001). |
| Verificación de la implementación (tasas reales dentro del intervalo central sintético) separada de la fidelidad (peso) | Las tasas son parámetros del generador: su coincidencia verifica el código, no la fidelidad. |
| Estudio de cobertura de Wald, percentil y BCa con datos sintéticos (n = 30 a 7.864; 2.000 ensayos × 2.000 remuestras) | Explora el comportamiento de los estimadores (ID2.2) y explica por qué los tres métodos coinciden con n grande. |
| Tornado con cada entrada en los extremos de su IC de S1 | Análisis de sensibilidad local del tutorial «Análisis de sensibilidad» (ID2.3). |

## Robustez (sección 5)

| Decisión | Justificación |
|---|---|
| Media recortada al 5 %, media sin el 1 % superior, mediana y jackknife | Separa la influencia de casos aislados de la influencia de grupo. |
| Bootstrap de bloques móviles circulares (ℓ = 1, 50, 200, 500) | Evalúa el supuesto de independencia frente al orden del archivo (Künsch, 1989; Politis y Romano, 1992). Como la razón no se estabiliza, se interpreta como diagnóstico de estructura por orden y no como EE corregido. |
| IC de las correlaciones con bloques de 500 envíos | Comprueba que la clasificación de la sección 3 sobrevive a la dependencia según el orden. |
| Barrido del corte entre 0 y 6.000 | Responde a que el corte de S1 se definió después de mirar los datos. |

## Documentación (sección 6)

| Decisión | Justificación |
|---|---|
| Reporte de resultados validados generado por el notebook en CSV y Markdown | Archivo separado exigido por la plantilla; al generarse desde el código, sus cifras coinciden con el informe. |
| Regla única en dos dimensiones (coherente entre métodos; estable entre conjuntos) y tres estados: Robusto, Señal exploratoria, Dependiente del bloque | Aplica el mismo criterio a parámetros, pruebas y correlaciones; las pruebas se juzgan con Holm en el subconjunto. |
