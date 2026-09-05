# Predictor de Punto de Equilibrio de Flujo de Caja — Sector Tecnológico

**Tesis de grado — Universidad EAFIT · 2026**

---

## ¿De qué trata esta investigación?

Esta tesis responde una pregunta concreta de value investing cuantitativo: **¿puede un modelo de machine learning predecir si una empresa tecnológica que hoy quema caja alcanzará el punto de equilibrio de flujo de caja libre en los próximos 4 trimestres?**

El punto de equilibrio de flujo de caja (*FCF breakeven*) es el momento en que una empresa deja de quemar efectivo y comienza a generarlo de forma sostenida. Para un inversionista en empresas tecnológicas de crecimiento, identificar qué empresas están cerca de ese punto — antes de que el mercado lo descuente — representa una ventaja de información significativa.

El modelo se acota específicamente al **sector tecnológico**, dado que los ciclos de maduración e inversión de estas empresas difieren sustancialmente de los de otras industrias, lo que exige un modelo predictivo desarrollado y validado sobre este universo particular.

### Variable objetivo

**Y = 1** si el FCF operativo promedio de los próximos 4 trimestres es positivo
**Y = 0** si no lo es

Solo se incluyen trimestres donde la empresa tenía FCF operativo negativo — la pregunta aplica únicamente a empresas que hoy queman caja.

---

## Universo de estudio

Empresas tecnológicas de pequeña y mediana capitalización (*small y mid-cap*) constituyentes del **Russell 2500**, delimitadas mediante categorías de Clasificación Industrial Estándar (SIC) correspondientes a software, semiconductores, procesamiento de datos, sistemas integrados, y componentes y equipos de comunicación. Se excluyen categorías de comercio minorista/mayorista relacionadas con tecnología, por no representar desarrollo o fabricación tecnológica propiamente dicha.

### Resumen del dataset

| Métrica | Valor |
|---|---|
| Observaciones | 3.390 |
| Empresas | 255 |
| Features | 65 (49 originales + 16 temporales) |
| Período | 2008–2026 |
| Balance de clases | Y=1: 56.7% / Y=0: 43.3% |

---

## Fuente de datos

**SEC EDGAR** — base de datos pública de la Comisión de Valores de Estados Unidos. Completamente gratuita. Se usan formularios 10-Q (trimestral) y 10-K (anual).

**Yahoo Finance** (vía `yfinance`) — precios mensuales ajustados para el backtest.

**Kenneth French Data Library** (vía `pandas_datareader`) — factores de Fama-French de 5 factores.

---

## Resultado principal

El modelo final — **Ensemble de Random Forest y LightGBM** — alcanza un **AUC-PR de 0.783** sobre 7 folds de walk-forward validation (2018–2024), representando una mejora del 38% sobre el baseline aleatorio (0.567) y del 37% sobre el Piotroski F-Score (0.571), el método tradicional de referencia en value investing cuantitativo.

| Modelo | AUC-PR |
|---|---|
| **Ensemble RF + LightGBM (final)** | **0.783** |
| LightGBM + momentum | 0.782 |
| Random Forest | 0.780 |
| Regresión Logística | 0.720 |
| Ridge / Lasso (regularizado) | 0.718 |
| Piotroski F-Score | 0.571 |
| Aleatorio (baseline) | 0.567 |

### Variables más importantes (SHAP)

`capex`, `cuentas_por_cobrar`, `pasivos_corrientes`, `utilidad_operativa`, `gastos_id` — la inversión en I+D emerge como predictor relevante específicamente en el universo tecnológico, con un efecto de umbral más que proporcional.

### Backtest y Fama-French

Portafolio del decil superior de probabilidad vs. benchmark (resto del universo), sobre 80 meses (nov. 2018–jun. 2025): el portafolio muestra un perfil de riesgo notablemente más controlado (Maximum Drawdown -26.21% vs. -52.09% del benchmark). La regresión de 5 factores revela que el benchmark tiene exposición negativa y significativa a RMW (calidad), mientras que el portafolio del modelo no — evidencia de que el modelo evita sistemáticamente empresas de baja calidad.

---

## Estructura del repositorio

predictor-punto-equilibrio/
│
├── cuadernos/
│ ├── 01_universo_tecnologico.ipynb # Definición del universo tecnológico
│ ├── 02_construccion_features_tech.ipynb # Limpieza, features y variable Y
│ ├── 03_modelado_tech.ipynb # Entrenamiento y evaluación de modelos
│ ├── 04_explicabilidad_tech.ipynb # Análisis SHAP del ensemble final
│ ├── 05_portafolio_tech.ipynb # Portafolio recomendado
│ ├── 06_analisis_corporativo_tech.ipynb # Análisis corporativo profundo (8 bloques)
│ └── 07_backtesting_fama_french_tech.ipynb # Backtest y regresión Fama-French
│
├── datos/
│ ├── crudos/
│ │ ├── universo_tecnologico.csv # 256 empresas tecnológicas con CIK
│ │ └── variables_financieras_tech.csv.gz # Dataset crudo filtrado a tecnología
│ └── procesados/
│ ├── dataset_modelo_tech.csv # Dataset final (3.390 obs)
│ └── precios_historicos_tech.csv # Precios mensuales para el backtest
│
├── salidas/
│ ├── modelo_rf_final_tech.pkl # Random Forest entrenado
│ ├── modelo_lgbm_final_tech.pkl # LightGBM entrenado
│ ├── features_modelo_tech.csv # Lista de 65 features del modelo final
│ ├── resultados_walkforward_tech.csv # AUC-PR por fold del ensemble
│ ├── portafolio_recomendado_tech.csv # Portafolio final recomendado
│ ├── metricas_riesgo_ajustado_tech.csv # Sharpe, Sortino, Maximum Drawdown
│ └── predicciones_historicas_walkforward_tech.csv # Predicciones fuera de muestra
│
├── .devcontainer/devcontainer.json # Configuración GitHub Codespaces
├── requirements.txt # Dependencias Python
├── README.md # Descripción del proyecto y guía de reproducción
└── LIMITACIONES.md # Limitaciones metodológicas para la tesis


---

## Cómo reproducir

### Requisitos

- Python 3.11+
- GitHub Codespaces (recomendado) o entorno local equivalente

### Instalación

```bash
git clone https://github.com/juanramosdataexpert/predictor-punto-equilibrio
cd predictor-punto-equilibrio
git checkout Modelo-Sector-Tecnologico
pip install -r requirements.txt
```

### Orden de ejecución

Los cuadernos deben ejecutarse en orden. Cada uno depende del anterior:

| Cuaderno | Qué hace |
|---|---|
| `01_universo_tecnologico.ipynb` | Delimita el universo tecnológico a partir de las variables financieras ya extraídas |
| `02_construccion_features_tech.ipynb` | Limpia, transforma y construye las features y la variable Y |
| `03_modelado_tech.ipynb` | Entrena y evalúa todos los modelos con walk-forward validation (2018–2024) |
| `04_explicabilidad_tech.ipynb` | Calcula valores SHAP y genera las visualizaciones de explicabilidad |
| `05_portafolio_tech.ipynb` | Construye el portafolio recomendado sobre datos actuales |
| `06_analisis_corporativo_tech.ipynb` | Análisis de negocio profundo: perfiles, trayectorias, subsectores, errores del modelo |
| `07_backtesting_fama_french_tech.ipynb` | Backtest histórico y regresión de atribución Fama-French |

---

## Dependencias principales

pandas
numpy
scikit-learn
xgboost
lightgbm
shap
matplotlib
yfinance
statsmodels
pandas_datareader
ipykernel


Ver `requirements.txt` para versiones exactas.

---

## Referencias

- Gu, S., Kelly, B., & Xiu, D. (2020). Empirical Asset Pricing via Machine Learning. *The Review of Financial Studies*, 33(5), 2223–2273.
- Fama, E. F., & French, K. R. (2015). A five-factor asset pricing model. *Journal of Financial Economics*, 116(1), 1-22.
- Lundberg, S. M., & Lee, S. I. (2017). A unified approach to interpreting model predictions. *Advances in Neural Information Processing Systems*, 30.
- López de Prado, M. (2018). *Advances in Financial Machine Learning*. John Wiley & Sons.
- Piotroski, J. D. (2000). Value investing: The use of historical financial statement information to separate winners from losers. *Journal of Accounting Research*.
- FTSE Russell. (2026). *Russell US Indexes: Construction and Methodology*. LSEG.
- SEC EDGAR: https://data.sec.gov/api/xbrl/companyfacts/
