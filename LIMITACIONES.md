# Limitaciones metodológicas

Este documento consolida las limitaciones del diseño de investigación del modelo
tecnológico para su inclusión en la sección metodológica de la tesis. Cada
limitación está documentada con su origen, magnitud estimada y justificación de
por qué esto fortalece la investigación y su continuidad en el estudio.

---

## 1. Horizonte temporal fijo pese a heterogeneidad intra-sectorial

**Descripción:**
El modelo predice breakeven a un horizonte fijo de 4 trimestres para todas las
empresas del universo tecnológico. Si bien la delimitación sectorial (respecto a
un modelo multisectorial) redujo la heterogeneidad de ciclos de maduración, esta
persiste **dentro** del propio sector tecnológico: subsectores como Servicios de
Sistemas Integrados (orientados a proyectos) y Radio & TV Broadcasting (ligado a
ciclos publicitarios y regulatorios) tienen dinámicas de inversión y flujo de
caja distintas a las de Software o Semiconductores.

**Implicación:**
El desempeño predictivo del modelo no es homogéneo entre subsectores.

**Magnitud:**
El AUC-PR fuera de muestra por subsector varía de 0.397 (Radio & TV
Broadcasting, por debajo del baseline aleatorio de 0.567) a 0.934 (Computer
Communications Equipment) — ver Cuaderno 6, Bloque 3.

**Justificación de continuidad:**
Esta heterogeneidad se documenta explícitamente (ver Sección 6) en lugar de
promediarse y ocultarse, en línea con el estándar de reporte transparente de
resultados desfavorables (López de Prado, 2018). Ajustar el horizonte por
subsector queda como línea de investigación futura.

---

## 2. Survivorship bias

**Descripción:**
El universo tecnológico se construyó filtrando por sector los holdings actuales
del ETF SMMD (iShares Russell 2500 ETF), que refleja la composición del índice a
marzo de 2026. El dataset solo incluye empresas que *sobrevivieron* hasta hoy en
el índice; empresas tecnológicas que fueron excluidas del Russell 2500
históricamente — por quiebra, adquisición, o caída en capitalización — no están
representadas.

**Implicación:**
El modelo podría subestimar el riesgo real de no alcanzar el breakeven, al haber
sido entrenado predominantemente sobre empresas que lograron mantenerse en el
índice.

**Magnitud:**
En la literatura de finanzas empíricas, el survivorship bias en índices de
small-cap se estima entre 1-3% de sobreestimación del rendimiento (Brown et al.,
1992). Este efecto es adicionalmente relevante para el backtest (Cuaderno 7),
donde varios tickers del universo presentaron datos de precio incompletos por
posible deslistado durante el periodo de estudio.

**Justificación de continuidad:**
Limitación común en investigaciones que usan índices como universo de estudio,
documentada en la literatura (Gu, Kelly & Xiu, 2020, utilizan CRSP con
limitaciones similares de cobertura histórica).

---

## 3. Exclusión de ADRs y empresas extranjeras

**Descripción:**
El universo tecnológico hereda la exclusión de empresas que no reportan bajo el
estándar US GAAP en SEC EDGAR (ADRs — *American Depositary Receipts* — de
empresas extranjeras que cotizan en NYSE o Nasdaq pero reportan bajo IFRS u
otros estándares locales), aplicada en la etapa de construcción del universo
general antes de la delimitación sectorial.

**Implicación:**
Empresas tecnológicas extranjeras relevantes (ej. ASML) quedan fuera del
análisis, no por su actividad sectorial sino por su estándar contable.

**Justificación de continuidad:**
Necesaria para garantizar comparabilidad contable entre observaciones; es una
limitación estándar en estudios basados en datos de SEC EDGAR.

---

## 4. Delimitación sectorial basada en criterio de clasificación SIC

**Descripción:**
El universo tecnológico se delimitó excluyendo categorías SIC de comercio
minorista y mayorista relacionado con tecnología (ej. *Retail-Computer &
Computer Software Stores*), por considerarse distribución/comercio y no
desarrollo o fabricación tecnológica propiamente dicha.

**Implicación:**
Esta delimitación involucra un juicio de clasificación que, aunque
justificado y documentado (Cuaderno 1), no está exento de zonas grises —
otros investigadores podrían trazar el límite sectorial de forma distinta.

**Magnitud:**
4 categorías SIC excluidas del universo inicial de 271 empresas, resultando en
el universo final de 256.

**Justificación de continuidad:**
El criterio se documentó explícitamente y de forma reproducible (Cuaderno 1),
permitiendo su revisión y ajuste en investigaciones futuras.

---

## 5. Reportes trimestrales no auditados (10-Q)

**Descripción:**
El dataset combina formularios 10-K (auditados, anuales) y 10-Q (no auditados,
trimestrales) para maximizar la cobertura histórica trimestral.

**Implicación:**
Los datos de los tres primeros trimestres de cada año fiscal no cuentan con la
misma garantía de calidad contable que los datos de cierre anual.

**Justificación de continuidad:**
Es la práctica estándar en la literatura de asset pricing empírico con datos de
alta frecuencia (Gu, Kelly & Xiu, 2020); la variable objetivo se calcula sobre
promedios de 4 trimestres, lo que atenúa el impacto de un dato trimestral
puntual con error de medición.

---

## 6. Validación económica de alcance simplificado

**Descripción:**
El diseño metodológico original contemplaba un backtest histórico con retornos
simulados trimestre a trimestre y rebalanceo continuo. Por restricciones de
tiempo en la fase final del proyecto, la validación económica (Objetivo III) se
ejecutó mediante un procedimiento simplificado: portafolio del decil superior de
probabilidad con ventana de tenencia fija de 12 meses, comparado contra el resto
del universo filtrado como benchmark.

**Implicación:**
Los resultados del backtest (Sharpe, Sortino, Maximum Drawdown) deben
interpretarse como una aproximación a la utilidad económica del modelo, no como
una simulación completa de una estrategia de inversión operativa con
rebalanceo dinámico y costos de transacción.

**Magnitud:**
El backtest cubre 80 meses (nov. 2018–jun. 2025), tras excluir meses con menos
de 15 empresas en el portafolio por baja masa muestral en los extremos de la
serie (Cuaderno 7).

**Justificación de continuidad:**
El procedimiento simplificado sigue respondiendo a la pregunta central de
utilidad económica del modelo, y se documenta con total transparencia el ajuste
de alcance respecto al diseño original, en línea con el estándar de honestidad
metodológica adoptado en toda la investigación.

---

## 7. Ausencia de alpha estadísticamente significativo

**Descripción:**
La regresión de atribución de 5 factores de Fama-French (Cuaderno 7) no arrojó
un alpha estadísticamente significativo al 5% ni para el portafolio (p=0.609) ni
para el benchmark (p=0.126).

**Implicación:**
No hay evidencia robusta de que la estrategia basada en el modelo genere un
exceso de retorno no explicado por factores de riesgo sistemático conocidos.

**Magnitud:**
R² de 0.846 (portafolio) y 0.845 (benchmark) — la mayor parte del
comportamiento de ambas series se explica por riesgo sistemático.

**Justificación de continuidad:**
El hallazgo relevante de este análisis no es el alpha (ausente en ambos casos),
sino la diferencia en exposición al factor RMW: el benchmark presenta exposición
negativa y significativa a calidad (β=-0.649, p=0.002), mientras el portafolio
del modelo no (β=-0.117, p=0.443) — evidencia de que el modelo evita
sistemáticamente empresas de baja calidad (Asness et al., 2019), incluso sin
alpha puro.

---

## 8. Contribución marginal de las variables de momentum

**Descripción:**
Las 19 features temporales (cambios trimestrales, rezagos) contribuyen solo
14.5% de la importancia SHAP total del ensemble (Cuaderno 6, Bloque 4),
confirmando una mejora marginal ya observada en el AUC-PR (Cuaderno 3).

**Implicación:**
El modelo depende predominantemente del estado financiero puntual, no de la
tendencia reciente — una diferencia respecto a lo esperado inicialmente en el
diseño de la investigación.

**Justificación de continuidad:**
Se documenta como hallazgo, no como falla: es consistente con la hipótesis de
que los ciclos de maduración tecnológica requieren ventanas de observación más
amplias que la tendencia de pocos trimestres.

---

## 9. Nota de calidad metodológica: corrección de sesgo de anticipación

**Descripción:**
Durante el desarrollo del Cuaderno 6 se identificó que las probabilidades
usadas inicialmente para el análisis por subsector provenían del modelo
reentrenado con el 100% de los datos (destinado a la generación del portafolio
de inversión, Cuaderno 5), lo que generaba un sesgo de anticipación
(*look-ahead bias*) al evaluarse sobre las mismas observaciones de
entrenamiento.

**Implicación:**
Los resultados afectados (AUC-PR por subsector artificialmente elevados, entre
0.876 y 0.996) fueron identificados y descartados antes de su uso en el
análisis final.

**Justificación de continuidad:**
Se reconstruyeron predicciones genuinamente fuera de muestra (walk-forward)
para todo análisis histórico posterior, verificando su consistencia con el
AUC-PR validado del Cuaderno 3 (0.770 vs. 0.783 esperado). Este proceso de
detección y corrección se documenta explícitamente como evidencia del control
de calidad aplicado en la investigación, en línea con los estándares de
reproducibilidad exigidos por López de Prado (2018).
