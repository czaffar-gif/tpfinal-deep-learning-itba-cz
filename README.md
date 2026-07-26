# Predicción de admisiones en la guardia de un hospital

Trabajo Final — Deep Learning · Master en Management & Analytics (ITBA)
**Autor:** Camila Zaffar

## Descripción del proyecto

La planificación de recursos en los servicios de urgencias es uno de los principales desafíos de la gestión hospitalaria: la demanda diaria de admisiones es muy variable y una estimación imprecisa puede derivar en saturación, mayores tiempos de espera y peor calidad de atención.

Este trabajo aborda el problema como un **pronóstico de series de tiempo multi-horizonte** (+1 a +7 días), utilizando la cantidad diaria de admisiones como proxy de la demanda asistencial. Se comparan siete enfoques —desde baselines simples hasta arquitecturas de Deep Learning— para determinar qué modelo ofrece el mejor sustento para la planificación operativa del hospital.

## Dataset

**Emergency Department Forecasting Dataset** (Peláez-Rodríguez et al., 2024), con registros diarios de admisiones a la guardia de un hospital de Madrid.

- 1.478 observaciones diarias, sin valores faltantes
- Período: septiembre 2018 – septiembre 2022
- Variables temporales (día de la semana, feriados, fase lunar), meteorológicas (temperatura, viento, presión, precipitaciones), autorregresivas (lags `ED_1`…`ED_7`) y objetivo (`Target_1`…`Target_7`, admisiones a +1…+7 días)

> https://github.com/RTLPHD/EmergencyDepartmentForecasting.git 

## Metodología

1. **Análisis exploratorio:** distribución, estacionalidad semanal, tendencia, FFT y autocorrelación de la serie.
2. **Preprocesamiento:** codificación cíclica (seno/coseno) de variables temporales, split cronológico 70/15/15 (train/validación/test) y estandarización (`StandardScaler` ajustado solo con train).
3. **Entrenamiento:** un modelo independiente por horizonte de predicción (+1 a +7 días), evaluado con **MAE**, **RMSE** y **MAPE**. El conjunto de test se usa una única vez, al final, para evitar sesgo de optimismo.
4. **Optimización de hiperparámetros:** grid search sobre train/validación para las arquitecturas LSTM y Transformer.

## Modelos evaluados

| Modelo | Descripción |
|---|---|
| Naive (persistencia / estacional) | Baseline: último valor observado o valor de 7 días antes |
| ARIMA (SARIMAX) | Modelo estadístico clásico con variables exógenas de calendario y clima |
| Prophet | Tendencia + estacionalidad semanal/anual + regresores (incluye lags) |
| LSTM | Red recurrente de dos ramas (secuencia de lags + variables estáticas) |
| Transformer | Encoder con autoatención y embedding posicional aprendible |
| LSTM (optimizado) | LSTM con hiperparámetros ajustados por grid search |
| Transformer (optimizado) | Transformer con hiperparámetros ajustados por grid search |

## Resultados

MAE promedio en test (horizontes +1 a +7):

| Modelo | MAE |
|---|---|
| **LSTM (optimizado)** | **8.96** |
| Transformer (optimizado) | 9.04 |
| LSTM | 9.16 |
| Prophet | 9.29 |
| Transformer | 9.59 |
| Naive | 12.70 |
| ARIMA | 12.88 |

La **LSTM optimizada** obtiene el menor error de test (MAE 8.96, RMSE 11.17, MAPE 14.58%), seguida muy de cerca por el Transformer optimizado. La diferencia entre ambas arquitecturas de Deep Learning es marginal, lo que sugiere que, para secuencias cortas (7 rezagos) y un dataset de tamaño acotado, el sesgo inductivo recurrente resulta al menos tan efectivo como la atención completa.

#### Estructura del repositorio
├── Trabajo_Final_Deep_Learning_-_Zaffar_Camila.ipynb   # Notebook con el desarrollo completo
├── data/
│   └── Madrid_database.csv                             # Dataset (no incluido, ver nota arriba)
├── img/
│   └── Marca-ITBA-Color-ALTA.png                        # Logo institucional
└── README.md


El notebook está organizado en las siguientes secciones: Introducción, Dataset y Análisis Exploratorio, Preprocesamiento, Entrenamiento de modelos, Optimización de Hiperparámetros, Resultados y Conclusiones.
