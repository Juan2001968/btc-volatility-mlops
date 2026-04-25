# BTC Volatility MLP — Proyecto Integrador 11.15

## Integrantes
JUAN ANDRES RAMOS CARDONA Y SERGIO ANDRÉS CADAVID BRUGES


## Contenido

Este Jupyterbook contiene el desarrollo completo del **Proyecto Integrador 11.15**
 El objetivo es predecir la **volatilidad histórica** del precio de cierre
diario de Bitcoin a un **horizonte de 7 días** mediante un MLP
multisalida, respetando la naturaleza temporal de la serie en cada paso
del pipeline.

1. **Análisis exploratorio y volatilidad.** Carga del dataset
   `btc_1d_data_2018_to_2025.csv`, cálculo de retornos logarítmicos y
   de volatilidad histórica anualizada ($\sqrt{365}$), estadísticos
   descriptivos y visualizaciones clave (precio, retornos, histograma,
   ACF de retornos y de retornos al cuadrado).
2. **Entrenamiento del MLP multisalida.** Construcción de ventanas de
   lags ($L \in \{7, 14, 21, 28\}$), validación cruzada temporal con
   `tsxv.splitTrainValTest.split_train_val_test_groupKFold`,
   estandarización por fold y cálculo de **MAPE, MAE, RMSE y MSE** por
   horizonte $h=1..7$.
3. **Análisis de residuos y test BDS.** Diagnóstico de independencia no
   lineal sobre los residuos del horizonte $h=1$ con el **test BDS**
   (`arch.bootstrap.bds`), curvas de pronóstico por fold
   (mejor / mediano / peor) y conclusiones sobre la bondad del ajuste.
4. **Modelo final para producción.** Re-entrenamiento del MLP ganador
   (`hidden_layers=(32, 32, 128)`, `lag=7`, `lr=1e-3`) sobre toda la
   serie, serialización del artefacto canónico `app/model.joblib` que
   consume la API FastAPI y sanity check del pipeline de inferencia.

## Stack del proyecto

| Componente | Tecnología |
|---|---|
| Modelado | `scikit-learn` (`MLPRegressor`) |
| Validación temporal | `tsxv` (`timeseries-cv`) |
| Diagnóstico de residuos | `arch` (test BDS) |
| API de inferencia | `FastAPI` + `Pydantic` |
| Contenerización | `Docker` (Python 3.10-slim) |
| CI/CD | `GitHub Actions` (`flake8` + `pytest`) |

## Reproducibilidad

El repositorio asociado
([`btc-volatility-mlops`](https://github.com/Juan2001968/btc-volatility-mlops))
contiene:

- Los cuatro notebooks de análisis (EDA, entrenamiento, residuos y modelo final).
- La API (`app/api.py`, `app/schemas.py`) y el artefacto entrenado
  (`app/model.joblib`).
- Tests unitarios (`tests/test_model.py`, `tests/test_api.py`).
- `Dockerfile` para despliegue reproducible.
- Pipeline de CI en `.github/workflows/ci.yml`.

Toda la documentación de uso local y en Docker está en el
[`README.md`](https://github.com/Juan2001968/btc-volatility-mlops/blob/main/README.md).
