# Limitaciones metodológicas

Este documento consolida las limitaciones del diseño de investigación para su inclusión en la sección metodológica de la tesis. Cada limitación está documentada con su origen, magnitud estimada y justificación de por qué no invalida los resultados.

---

## 1. Survivorship bias

**Descripción:**  
El universo de empresas se construyó a partir de los holdings actuales del ETF SMMD (iShares Russell 2500 ETF), que refleja la composición del Russell 2500 a marzo de 2026. Esto significa que el dataset solo incluye empresas que *sobrevivieron* hasta hoy en el índice.

Empresas que formaron parte del Russell 2500 en años anteriores pero fueron excluidas posteriormente — por quiebra, adquisición, o caída en capitalización de mercado — no están representadas en el dataset histórico.

**Implicación:**  
El modelo podría subestimar el riesgo real de no alcanzar el breakeven, porque fue entrenado predominantemente sobre empresas que lograron mantenerse en el índice. Las empresas que quebraron o fueron absorbidas durante el período de estudio están ausentes.

**Magnitud:**  
Difícil de cuantificar sin acceso a composiciones históricas del índice. En la literatura de finanzas empíricas, el survivorship bias en índices de small-cap se estima entre 1-3% de sobreestimación del rendimiento (Brown et al., 1992). Para predicción de breakeven, el efecto podría ser mayor dado que las quiebras son precisamente el resultado que el modelo busca evitar.

**Justificación de continuidad:**  
Esta limitación es común en investigaciones que usan índices como universo de estudio y está documentada en la literatura (Gu, Kelly & Xiu, 2020, utilizan CRSP que también tiene limitaciones de cobertura histórica). Se reconoce explícitamente y no invalida los resultados — solo acota el alcance de las conclusiones al universo de empresas representadas.

---

## 2. Exclusión de ADRs y empresas extranjeras

**Descripción:**  
948 empresas del universo inicial fueron excluidas por no reportar bajo el estándar US GAAP en SEC EDGAR. Corresponden principalmente a ADRs (*American Depositary Receipts*) — empresas extranjeras que cotizan en NYSE o Nasdaq pero reportan sus estados financieros bajo IFRS u otros estándares locales.

Ejemplos representativos: ASML (Países Bajos), Alibaba (China), Toyota (Japón), Novartis (Suiza), AstraZeneca (Reino Unido).

**Desglose de exclusiones:**

| Etapa | Empresas |
|---|---|
| Constituyentes Russell 2500 (SMMD) | 2.433 |
| Encontrados en SEC EDGAR | 2.413 |
| Excluidos por sector (SIC) | 738 |
| Universo post-filtro sectorial | 1.675 |
| Sin datos US GAAP (ADRs/extranjeras) | 48 |
| Dataset final | 1.626 empresas con datos |

**Implicación:**  
El modelo no puede aplicarse a empresas que no reportan bajo US GAAP. Las conclusiones aplican exclusivamente al segmento de empresas domésticas estadounidenses del Russell 2500.

**Justificación:**  
La comparabilidad contable es un requisito metodológico fundamental. Mezclar estados financieros bajo US GAAP con IFRS introduciría ruido sistemático en las variables — diferencias contables que no reflejan diferencias económicas reales. La exclusión es metodológicamente correcta.

---

## 3. Reportes 10-Q no auditados

**Descripción:**  
La mayoría de los datos proviene de formularios 10-Q (reportes trimestrales), que no están auditados externamente. Solo los reportes anuales 10-K son auditados.

**Implicación:**  
Los datos trimestrales pueden contener errores no detectados, reexpresiones posteriores, o diferencias con los valores definitivos que aparecen en el 10-K anual.

**Magnitud:**  
En la práctica, las reexpresiones materiales en empresas del Russell 2500 son poco frecuentes. Estas empresas están bajo escrutinio constante de analistas e inversionistas institucionales, lo que reduce el incentivo y la capacidad de reportar incorrectamente.

**Justificación:**  
Usar exclusivamente datos anuales (10-K) reduciría el dataset de 27.382 a aproximadamente 6.000 observaciones — insuficiente para walk-forward validation con 9 folds. El trade-off entre calidad de datos y tamaño de muestra favorece el uso de 10-Q para este tipo de investigación.

---

## 4. Concept drift temporal

**Descripción:**  
El AUC-PR del modelo en el fold de test 2024 (0.64) es significativamente menor al promedio histórico (0.762). Este fenómeno, conocido como *concept drift*, ocurre cuando los patrones estadísticos que el modelo aprendió durante el entrenamiento cambian en el período de predicción.

**Causa probable:**  
El entorno macroeconómico de 2023-2024 — tasas de interés elevadas sostenidas post-ciclo de ajuste de la Fed — es cualitativamente diferente al período 2008-2022 que dominó el entrenamiento. Las empresas de crecimiento small/mid cap son especialmente sensibles a tasas altas porque dependen del financiamiento externo.

**Implicación:**  
El modelo pierde precisión en entornos macroeconómicos no representados en el período de entrenamiento. Un modelo entrenado exclusivamente en períodos de tasas bajas puede subestimar el riesgo de breakeven en períodos de tasas altas.

**Mitigación práctica:**  
En un contexto de inversión real, el modelo se reentrenarí­a periódicamente — trimestral o anualmente — incorporando los datos más recientes. El walk-forward validation simula exactamente este proceso, por lo que el AUC-PR de 2024 es una estimación honesta del rendimiento en condiciones reales de despliegue.

---

## 5. Definición de la variable Y

**Descripción:**  
La variable objetivo Y se define como el promedio del FCF operativo en los próximos 4 trimestres. Esta definición tiene varias implicaciones:

- **Promedio vs. sostenibilidad**: una empresa que tiene tres trimestres muy positivos y uno muy negativo puede tener Y=1 sin haber alcanzado un breakeven verdaderamente sostenido
- **Horizonte de 4 trimestres**: el horizonte es arbitrario — un inversionista podría preferir 2 o 6 trimestres según su horizonte de inversión
- **FCF operativo vs. FCF libre**: se usa FCF operativo (antes de CAPEX) en lugar de FCF libre (después de CAPEX). Una empresa puede tener FCF operativo positivo pero FCF libre negativo si está invirtiendo fuertemente

**Alternativas no exploradas:**  
- Y=1 si al menos 3 de 4 trimestres son FCF positivo (exige más consistencia)
- Y=1 si el FCF máximo de los próximos 4 trimestres es positivo (más permisivo)
- Usar FCF libre en lugar de FCF operativo

**Justificación de la elección:**  
El promedio de 4 trimestres es el balance más razonable entre capturar el breakeven sostenido y no ser excesivamente estricto. FCF operativo es más estable que FCF libre porque excluye la volatilidad de las decisiones de inversión en activos fijos.

---

## 6. Composición actual del índice (no histórica)

**Descripción:**  
Los holdings del ETF SMMD reflejan la composición del Russell 2500 a la fecha de descarga (marzo 2026), no su composición en cada momento histórico del período de estudio (2008-2025).

**Implicación:**  
Algunas empresas incluidas en el dataset pueden no haber formado parte del Russell 2500 en los años más tempranos del período de estudio. Esto es una forma adicional de survivorship bias: empresas que entraron recientemente al índice tienen historia completa en el dataset, mientras que empresas que salieron del índice en años anteriores no están representadas.

**Justificación:**  
Obtener composiciones históricas del Russell 2500 requiere acceso a datos comerciales de FTSE Russell (no gratuitos). El uso del ETF SMMD como proxy es la alternativa metodológicamente más sólida disponible en el contexto de una investigación académica con recursos limitados, y es consistente con investigaciones similares que usan composiciones actuales de índices como universo de estudio.

---

## Resumen

| Limitación | Severidad | Mitigación |
|---|---|---|
| Survivorship bias | Media | Documentada, estimada en literatura en 1-3% |
| Exclusión ADRs | Baja | Metodológicamente justificada por comparabilidad |
| 10-Q no auditados | Baja | Reexpresiones materiales poco frecuentes en Russell 2500 |
| Concept drift 2024 | Media | Walk-forward simula reentrenamiento periódico |
| Definición de Y | Media | Promedio de 4 trimestres es el estándar más razonable |
| Composición no histórica | Media | Alternativa sin costo disponible con las mismas implicaciones |

---

## Referencias

- Brown, S. J., Goetzmann, W., Ibbotson, R. G., & Ross, S. A. (1992). Survivorship bias in performance studies. *The Review of Financial Studies*, 5(4), 553–580.
- Gu, S., Kelly, B., & Xiu, D. (2020). Empirical Asset Pricing via Machine Learning. *The Review of Financial Studies*, 33(5), 2223–2273.
- FTSE Russell. (2026). *Russell US Indexes: Construction and Methodology*. LSEG.
