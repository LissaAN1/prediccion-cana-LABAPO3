# Laboratorio 1 - Prediccion y clasificacion en la industria azucarera

Este repositorio contiene el desarrollo del Laboratorio 1 del curso, enfocado en el analisis exploratorio, preprocesamiento, modelamiento y evaluacion de modelos de machine learning aplicados a datos de produccion de cana de azucar del Ingenio Providencia.

El objetivo principal es construir modelos para dos tareas:

1. **Regresion:** predecir `TCH` y `%Sac.Cana` a partir del dataset `HISTORICO_SUERTES.xlsx`.
2. **Clasificacion:** clasificar los registros en niveles de desempeno alto, medio y bajo para `TCH` y `%Sac.Cana` usando el dataset `BD_IPSA_1940.xlsx`.

> **Nota:** los datos originales no se incluyen en el repositorio por restricciones de privacidad del laboratorio. El codigo esta preparado para ejecutarse localmente cuando los archivos se ubiquen en la carpeta `datos/`.

## Estructura del repositorio

```text
lab1/
├── lab1.ipynb
├── modelos/
│   └── scalers/
├── outputs/              # generado localmente, no versionado
├── datos/                # creado localmente, no versionado
└── .gitignore
```

### Carpetas principales

- `lab1.ipynb`: notebook principal con el flujo completo del laboratorio.
- `datos/`: carpeta local para ubicar los archivos de entrada. No se sube al repositorio.
- `modelos/scalers/`: escaladores usados en el preprocesamiento de modelos lineales, logisticos, KNN y SVM.
- `outputs/`: graficos, metricas y resultados generados por el notebook. No se versiona.
- `.gitignore`: evita subir datos, salidas generadas y modelos pesados o regenerables.

## Archivos de datos esperados

Para ejecutar el notebook, crear localmente la carpeta `datos/` y ubicar alli los siguientes archivos:

```text
datos/
├── HISTORICO_SUERTES.xlsx
└── BD_IPSA_1940.xlsx
```

Estos archivos no deben publicarse en GitHub.

## Metodologia

El notebook sigue una metodologia de analisis y modelamiento de extremo a extremo:

1. Contextualizacion del problema agricola y productivo.
2. Carga y revision de los datasets.
3. Analisis exploratorio de datos:
   - distribuciones de `TCH` y `%Sac.Cana`,
   - deteccion de outliers,
   - correlaciones entre predictores y variables objetivo,
   - analisis de variables de lluvia y multicolinealidad,
   - revision de valores faltantes.
4. Preprocesamiento:
   - seleccion de variables,
   - imputacion o descarte de variables con alto porcentaje de nulos,
   - codificacion de variables categoricas,
   - escalado para modelos sensibles a la escala.
5. Modelamiento:
   - modelos benchmark,
   - modelos avanzados,
   - validacion hold-out 80/20,
   - validacion cruzada sobre entrenamiento.
6. Evaluacion con metricas de regresion y clasificacion.

## Modelos implementados

### Regresion

Se entrenaron modelos para predecir `TCH` y `%Sac.Cana`:

- Regresion Lineal Multiple
- Random Forest Regressor
- XGBoost Regressor

Metricas usadas:

- `R2`
- `RMSE`
- `MAE`
- `R2_CV`

### Clasificacion

Se entrenaron modelos para clasificar `nivel_TCH` y `nivel_SAC`:

- Regresion Logistica Multinomial
- KNN
- Random Forest Classifier
- XGBoost Classifier
- SVM

Metricas usadas:

- `Accuracy`
- `F1 weighted`
- `Kappa de Cohen`
- `F1_CV`

## Resultados principales

### Regresion: TCH

| Modelo | R2 | RMSE | MAE | R2_CV |
|---|---:|---:|---:|---:|
| Reg. Lineal | 0.2636 | 28.1200 | 21.6154 | 0.2647 |
| Random Forest | 0.4384 | 24.5570 | 18.3224 | 0.3987 |
| XGBoost | **0.4548** | **24.1967** | **18.1622** | **0.4169** |

El mejor modelo para `TCH` fue **XGBoost**, lo que sugiere que la productividad esta influenciada por relaciones no lineales entre variables agronomicas, historicas y de manejo.

### Regresion: %Sac.Cana

| Modelo | R2 | RMSE | MAE | R2_CV |
|---|---:|---:|---:|---:|
| Reg. Lineal | 0.2356 | 1.0079 | 0.7825 | 0.2356 |
| Random Forest | **0.4016** | **0.8918** | **0.6841** | **0.3808** |
| XGBoost | 0.3847 | 0.9043 | 0.6957 | 0.3688 |

El mejor desempeno para `%Sac.Cana` lo obtuvo **Random Forest**.

### Clasificacion: nivel_TCH

| Modelo | Accuracy | F1 | Kappa | F1_CV |
|---|---:|---:|---:|---:|
| Reg. Logistica | 0.4589 | 0.4545 | 0.1901 | 0.4663 |
| KNN k=15 | 0.4224 | 0.4194 | 0.1345 | 0.4549 |
| Random Forest | 0.4932 | 0.4936 | 0.2392 | 0.4998 |
| XGBoost | **0.4954** | **0.4973** | **0.2413** | 0.4663 |
| SVM | 0.4429 | 0.4414 | 0.1618 | 0.4727 |

El mejor modelo en prueba para `nivel_TCH` fue **XGBoost**.

### Clasificacion: nivel_SAC

| Modelo | Accuracy | F1 | Kappa | F1_CV |
|---|---:|---:|---:|---:|
| Reg. Logistica | 0.4384 | 0.4205 | 0.1581 | 0.4383 |
| KNN k=15 | 0.4954 | 0.4857 | 0.2410 | 0.4845 |
| Random Forest | **0.5297** | **0.5211** | **0.2945** | 0.5254 |
| XGBoost | 0.4749 | 0.4770 | 0.2134 | **0.5287** |
| SVM | 0.5091 | 0.5070 | 0.2653 | 0.4861 |

El mejor modelo en prueba para `nivel_SAC` fue **Random Forest**.

## Hallazgos relevantes del EDA

- `TCH` y `%Sac.Cana` presentan distribuciones aproximadamente concentradas alrededor de sus medias, pero con presencia de outliers.
- En `HISTORICO_SUERTES`, se identificaron outliers en las variables objetivo, especialmente en valores extremos de productividad y sacarosa.
- Las variables de lluvia presentan correlaciones entre si, por lo que se reviso multicolinealidad mediante VIF.
- Para `TCH`, variables como edad del cultivo, variedad, riego, area y lluvia muestran relacion con el rendimiento.
- Para `%Sac.Cana`, las variables relacionadas con maduracion, dosis de madurante y semanas de maduracion resultaron especialmente relevantes.
- En `BD_IPSA_1940`, el numero de cortes muestra una tendencia decreciente con respecto a la media de `TCH`.
- La clasificacion presenta desempenos moderados, lo cual sugiere que las clases alto, medio y bajo se traslapan y no son completamente separables con las variables disponibles.

## Como ejecutar el proyecto

1. Clonar el repositorio:

```bash
git clone <URL_DEL_REPOSITORIO>
cd lab1
```

2. Crear un entorno virtual:

```bash
python -m venv .venv
```

3. Activar el entorno:

En Windows:

```bash
.venv\Scripts\activate
```

En macOS/Linux:

```bash
source .venv/bin/activate
```

4. Instalar dependencias principales:

```bash
pip install pandas numpy scikit-learn xgboost matplotlib seaborn openpyxl joblib statsmodels jupyter
```

5. Crear la carpeta `datos/` y ubicar los archivos `.xlsx` correspondientes.

6. Abrir y ejecutar el notebook:

```bash
jupyter notebook lab1.ipynb
```

## Notas sobre versionamiento

Este repositorio debe versionar principalmente:

- codigo fuente,
- notebook del laboratorio,
- escaladores necesarios para reproducibilidad si se desean conservar,
- documentacion del proyecto.

No se deben versionar:

- datasets originales,
- archivos generados en `outputs/`,
- modelos pesados o regenerables,
- caches de Python o Jupyter.

## Conclusiones

Los modelos de ensamble superaron a los modelos benchmark en las tareas de regresion y clasificacion. Para `TCH`, XGBoost obtuvo el mejor rendimiento predictivo; para `%Sac.Cana`, Random Forest presento mejor desempeno. En clasificacion, el desempeno fue moderado, indicando que las clases de rendimiento y sacarosa tienen solapamiento y que seria util complementar los datos con variables adicionales como sensores, imagenes satelitales, caracteristicas de suelo mas detalladas y datos climaticos de mayor resolucion.
