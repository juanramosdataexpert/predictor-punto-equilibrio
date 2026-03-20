# Predictor de Punto de Equilibrio de Flujo de Caja

> Modelo de machine learning para predecir cuándo empresas de crecimiento alcanzarán el punto de equilibrio de flujo de caja operativo.

## Sobre el proyecto

Este repositorio es la implementación técnica de la tesis de grado:

**"Predicción del Punto de Equilibrio de Flujo de Caja en Empresas de Crecimiento: Un Enfoque de Value Investing Cuantitativo basado en Machine Learning"**

**Autores**
- Andrea Meneses — investigación principal y análisis financiero
- Juan Ramos — apoyo técnico

**Institución:** Universidad EAFIT

## ¿Qué hace este proyecto?

Dada una empresa de crecimiento con flujo de caja operativo negativo hoy, el modelo predice si alcanzará el punto de equilibrio de caja (FCF > 0) en los próximos 4 trimestres (12 meses).

- **Universo:** Empresas NYSE/NASDAQ, capitalización $100M–$10B USD, excluye financieras y utilities
- **Período:** 2010–2025, datos trimestrales
- **Variable objetivo (Y):** Binaria — 1 si el FCF operativo promedio se vuelve positivo en los próximos 4 trimestres, 0 si no
- **Variables:** 40+ indicadores incluyendo burn rate, runway, margen bruto, componentes del Piotroski F-Score, señales del ciclo de vida de Dickinson y momentum de precio

## Estructura del proyecto

```
predictor-punto-equilibrio/
│
├── datos/                      # Datos crudos y procesados (ignorados por git)
│   ├── crudos/
│   └── procesados/
│
├── cuadernos/                  # Notebooks de exploración y análisis
│   ├── 01_descarga_datos.ipynb
│   ├── 02_analisis_exploratorio.ipynb
│   ├── 03_modelado.ipynb
│   └── 04_backtesting.ipynb
│
├── src/                        # Módulos reutilizables de Python
│   ├── __init__.py
│   ├── descargador.py          # Descarga y parseo con yfinance
│   ├── variables.py            # Construcción de features
│   ├── modelos.py              # Entrenamiento y evaluación
│   └── backtesting.py          # Lógica del backtesting del portafolio
│
├── salidas/                    # Gráficas, tablas y resultados (ignorados por git)
│
├── .devcontainer/              # Configuración para GitHub Codespaces
│   └── devcontainer.json
│
├── requirements.txt
├── .gitignore
├── LICENSE
└── README.md
```

## Inicio rápido (GitHub Codespaces)

1. Clic en **Code → Codespaces → Create codespace on main**
2. Esperar a que el contenedor se construya (~2 min)
3. Abrir `cuadernos/01_descarga_datos.ipynb` y ejecutar la primera celda

El devcontainer instala todas las dependencias automáticamente.

## Stack tecnológico

| Capa | Herramientas |
|---|---|
| Datos | `yfinance`, `pandas` |
| Modelado | `scikit-learn`, `xgboost`, `lightgbm` |
| Explicabilidad | `shap` |
| Visualización | `matplotlib`, `seaborn` |
| Entorno | Python 3.11, GitHub Codespaces |