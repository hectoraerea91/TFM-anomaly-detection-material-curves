# TFM — Detección automática de anomalías en curvas de ensayos de materiales

Repositorio de reproducibilidad del Trabajo Fin de Máster del **Máster Universitario en Big Data y Ciencia de Datos (VIU)**.

## Objetivo

El proyecto desarrolla y evalúa una metodología de Machine Learning para detectar anomalías en curvas de nanoindentación. El flujo incluye validación de datos, análisis exploratorio, partición agrupada por muestra física, tres representaciones de las curvas, validación cruzada, ajuste de hiperparámetros, selección de umbral y evaluación final sobre un conjunto de prueba independiente.

## Dataset

Se utiliza el conjunto público:

**Henriksen, A., Zakariya, P., Babuska, T., & Mundt, M. (2025). _A ground truth dataset for anomaly detection on load-displacement nanoindentation curves for NiFe electroplated samples_. Materials Data Facility.**

DOI: `10.18126/yvgn-w386`

El dataset **no se incluye en este repositorio**. Descárguelo desde su fuente original y conserve el ZIP con un nombre que comience por:

`anomaly_detection_on_nanoindentation`

## Entorno reproducible

La ejecución original del TFM utilizó:

- Semilla: `42`
- scikit-learn: **1.6.1**

La versión de scikit-learn es crítica porque `StratifiedGroupKFold(shuffle=True)` cambió de comportamiento en versiones posteriores. El notebook instala y verifica explícitamente `scikit-learn==1.6.1`.

## Ejecución en Google Colab

1. Abra `TFM_Nanoindentacion_Deteccion_Anomalias.ipynb` en Google Colab.
2. Ejecute las celdas desde el inicio.
3. El notebook instalará `scikit-learn==1.6.1`.
4. Disponga el ZIP del dataset de una de estas formas:
   - súbalo directamente a la sesión de Colab;
   - guárdelo en Google Drive;
   - o defina la variable de entorno `TFM_DATASET_ZIP` con su ruta.
5. Ejecute **Run all**.
6. Los resultados se guardarán por defecto en:
   - Google Drive: `/content/drive/MyDrive/14MBID_TFM/resultados`, si Drive está montado;
   - `./resultados`, en una ejecución local.

## Partición y prevención de fuga de información

La variable de agrupación es `sample_num`. Las curvas pertenecientes a una misma muestra física permanecen siempre dentro de una misma partición.

Se reserva un conjunto de prueba independiente y la selección de representaciones, modelos, hiperparámetros y umbrales se realiza exclusivamente con el conjunto de desarrollo.

El directorio `manifests/` contiene el manifiesto de la separación desarrollo/test correspondiente a la ejecución del TFM. El notebook genera además un manifiesto para los cinco folds internos de validación cruzada.

## Modelos evaluados

- Isolation Forest
- Local Outlier Factor (LOF)
- One-Class Support Vector Machine (One-Class SVM)

Representaciones:

- A: señal secuencial completa, 800 variables;
- B: 19 características físicas y geométricas;
- C: PCA conservando el 95 % de la varianza.

## Configuraciones finales

- LOF: `n_neighbors = 20`
- One-Class SVM: `nu = 0.10`, `gamma = 4 × gamma_base`
- Umbral: percentil 0.95 de las puntuaciones obtenidas sobre curvas normales de desarrollo

## Resultados principales sobre test independiente

- LOF: ROC-AUC `0.9352`, F1 `0.8067`, Balanced Accuracy `0.8489`
- One-Class SVM: ROC-AUC `0.9375`, F1 `0.8289`, Balanced Accuracy `0.8702`

Los resultados deben interpretarse dentro del dominio experimental analizado y no como una validación universal para otros materiales, equipos o tipos de ensayo.

## Estructura del repositorio

```text
.
├── TFM_Nanoindentacion_Deteccion_Anomalias.ipynb
├── README.md
├── requirements.txt
├── .gitignore
└── manifests/
    └── TFM_split_manifest_seed42.csv
```

## Nota sobre reproducibilidad

No modifique la versión de scikit-learn si desea reproducir exactamente las particiones y métricas documentadas en la memoria del TFM. Si se ejecuta con una versión diferente, los folds generados por `StratifiedGroupKFold` pueden cambiar.
