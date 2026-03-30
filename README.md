# Predictor de Punto de Equilibrio de Flujo de Caja

**Tesis de grado — Universidad EAFIT · 2026**  
**Autores:** Andrea Meneses · Juan Ramos

---

## ¿De qué trata esta investigación?

Esta tesis responde una pregunta concreta de value investing cuantitativo: **¿puede un modelo de machine learning predecir si una empresa que hoy quema caja alcanzará el punto de equilibrio de flujo de caja libre en los próximos 4 trimestres?**

El punto de equilibrio de flujo de caja (*FCF breakeven*) es el momento en que una empresa deja de quemar efectivo y comienza a generarlo de forma sostenida. Para un inversionista en empresas de crecimiento, identificar qué empresas están cerca de ese punto — antes de que el mercado lo descuente — representa una ventaja de información significativa.

### Variable objetivo

**Y = 1** si el FCF operativo promedio de los próximos 4 trimestres es positivo  
**Y = 0** si no lo es

Solo se incluyen trimestres donde la empresa tenía FCF operativo negativo — la pregunta aplica únicamente a empresas que hoy queman caja.

---

## Universo de estudio

**Russell 2500** — las 2.500 empresas de menor capitalización del Russell 3000, el índice de referencia institucional para el segmento *small y mid-cap* del mercado accionario estadounidense (FTSE Russell, 2026).

Se excluyen cuatro sectores cuya dinámica financiera distorsiona el FCF operativo: Financials (SIC 6000–6799), Utilities (4900–4999), Real Estate (6500–6552) y Oil & Gas (1300–1399, 2900–2999).

### Resumen del dataset

| Métrica | Valor |
|---|---|
| Observaciones | 27.382 |
| Empresas | 1.608 |
| Features | 65 (49 originales + 16 temporales) |
| Período | 2008–2025 |
| Balance de clases | Y=1: 48.2% / Y=0: 51.8% |

---

## Fuente de datos

**SEC EDGAR** — base de datos pública de la Comisión de Valores de Estados Unidos. Completamente gratuita. Se usan formularios 10-Q (trimestral) y 10-K (anual).

No se requiere ninguna API de pago. Solo se necesita registrar un User-Agent con nombre y correo para cumplir con las políticas de uso de la SEC.

---

## Resultado principal

El modelo final — **Ensemble de Random Forest y LightGBM con features de momentum** — alcanza un **AUC-PR de 0.762** sobre 9 folds de walk-forward validation, representando una mejora del 58% sobre el baseline aleatorio (0.482) y del 56% sobre el Piotroski F-Score (0.487), el método tradicional de referencia en value investing cuantitativo.

| Modelo | AUC-PR |
|---|---|
| **Ensemble RF + LightGBM (final)** | **0.762** |
| Random Forest + momentum | 0.760 |
| LightGBM + momentum | 0.757 |
| Random Forest afinado | 0.756 |
| Regresión Logística | 0.661 |
| Piotroski F-Score | 0.487 |
| Aleatorio (baseline) | 0.482 |

---

## Estructura del repositorio

```
predictor-punto-equilibrio/
│
├── cuadernos/
│   ├── 01_descarga_datos.ipynb        # Descarga de datos desde SEC EDGAR
│   ├── 02_construccion_features.ipynb # Limpieza, features y variable Y
│   ├── 03_modelado.ipynb              # Entrenamiento y evaluación de modelos
│   └── 04_explicabilidad.ipynb        # Análisis SHAP del ensemble final
│
├── datos/
│   ├── crudos/
│   │   ├── variables_financieras_sec.csv.gz  # Dataset crudo (4.4M registros, 40 variables)
│   │   ├── universo_empresas.csv             # 1.675 empresas del Russell 2500 con CIK
│   │   ├── empresas_exitosas.csv             # Log de empresas descargadas correctamente
│   │   ├── empresas_con_error.csv            # Log de errores de descarga
│   │   └── empresas_sin_datos_usgaap.csv     # ADRs excluidos por no tener datos US GAAP
│   └── procesados/
│       └── dataset_modelo.csv               # Dataset final listo para modelado (27.382 obs)
│
├── salidas/
│   ├── modelo_rf_final.pkl            # Random Forest entrenado (componente del ensemble)
│   ├── modelo_lgbm_final.pkl          # LightGBM entrenado (componente del ensemble)
│   ├── features_modelo.csv            # Lista de 65 features del modelo final
│   └── resultados_walkforward.csv     # AUC-PR por fold del ensemble
│
├── .env                               # Credenciales SEC (NO incluido en el repositorio)
├── .devcontainer/devcontainer.json    # Configuración GitHub Codespaces
├── requirements.txt                   # Dependencias Python
├── README.md                          # Descripción del proyecto y guía de reproducción
└── LIMITACIONES.md                    # Limitaciones metodológicas para la tesis
```

---

## Cómo reproducir

### Requisitos

- Python 3.11+
- GitHub Codespaces (recomendado, 8GB RAM) o entorno local equivalente

### Instalación

```bash
git clone https://github.com/juanramosdataexpert/predictor-punto-equilibrio
cd predictor-punto-equilibrio
pip install -r requirements.txt
```

### Configurar credenciales

Crear un archivo `.env` en la raíz del proyecto:

```
SEC_USER_AGENT=Tu Nombre tuemail@email.com
```

La SEC requiere identificación del consumidor de su API. No se necesita registro previo — solo nombre y correo válidos.

### Orden de ejecución

Los cuadernos deben ejecutarse en orden. Cada uno depende del anterior:

| Cuaderno | Tiempo estimado | Qué hace |
|---|---|---|
| `01_descarga_datos.ipynb` | ~65 minutos | Descarga datos de SEC EDGAR para las 1.675 empresas del Russell 2500 |
| `02_construccion_features.ipynb` | ~5 minutos | Limpia, transforma y construye las features y la variable Y |
| `03_modelado.ipynb` | ~45 minutos | Entrena y evalúa todos los modelos con walk-forward validation |
| `04_explicabilidad.ipynb` | ~15 minutos | Calcula valores SHAP y genera las visualizaciones de explicabilidad |

> El cuaderno 1 descarga ~4.4 millones de registros en dos etapas: primero obtiene los CIKs (~10 min) y luego las variables financieras (~45 min). Cada etapa guarda su progreso automáticamente — si el kernel se interrumpe, la siguiente ejecución retoma desde donde quedó.

---

## Dependencias principales

```
pandas
numpy
scikit-learn
xgboost
lightgbm
shap
matplotlib
python-dotenv
requests
ipykernel
```

Ver `requirements.txt` para versiones exactas.

---

## Referencias

- Gu, S., Kelly, B., & Xiu, D. (2020). Empirical Asset Pricing via Machine Learning. *The Review of Financial Studies*, 33(5), 2223–2273.
- FTSE Russell. (2026). *Russell US Indexes: Construction and Methodology*. LSEG.
- Lundberg, S. M., & Lee, S. I. (2017). A unified approach to interpreting model predictions. *Advances in Neural Information Processing Systems*, 30.
- SEC EDGAR: https://data.sec.gov/api/xbrl/companyfacts/