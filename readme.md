# Retail Store Sales: Data Cleaning and Statistical Imputation

## Dirty Retail Store Sales Dataset

**Author:** John Jairo Prado  
**Project:** Data Cleaning, Data Quality and Statistical Imputation  
**Languages:** Python and R  
**Dataset:** Dirty Retail Store Sales  
**Source:** Kaggle — Retail Store Sales: Dirty for Data Cleaning  
**Project Data / Datos del proyecto:** [Google Drive](https://drive.google.com/drive/folders/1k1GzqHLIrN4VRVJ2OGi3enQqoCvlhRD4?usp=sharing)  
**Reports / Reportes:** Open the `reportes` folder / Ingrese a la carpeta `reportes`

---

## 1. Project Overview / Descripción del proyecto

### English

This project develops a reproducible, traceable, and validated data-cleaning pipeline for the **Dirty Retail Store Sales Dataset**, a synthetic transactional dataset containing **12,575 records and 11 original variables**.

The workflow is divided into two complementary stages:

1. **Python — Data quality assessment and deterministic cleaning**
2. **R — Statistical imputation, deterministic reconstruction, validation, and final export**

The methodological principle is to avoid arbitrary replacement of missing values. Whenever a value can be recovered from validated business relationships, deterministic reconstruction is preferred. Statistical imputation is used only when deterministic recovery is not possible and its performance can be evaluated.

### Español

Este proyecto desarrolla un pipeline reproducible, trazable y validado para la limpieza del conjunto de datos **Dirty Retail Store Sales**, una base sintética de transacciones que contiene **12.575 registros y 11 variables originales**.

El flujo de trabajo se divide en dos etapas complementarias:

1. **Python — Evaluación de calidad y limpieza determinística**
2. **R — Imputación estadística, reconstrucción determinística, validación y exportación final**

El principio metodológico consiste en evitar el reemplazo arbitrario de valores faltantes. Cuando un valor puede recuperarse mediante relaciones de negocio previamente validadas, se prioriza la reconstrucción determinística. La imputación estadística se utiliza únicamente cuando la recuperación determinística no es posible y su desempeño puede ser evaluado.

---

# 2. Pipeline

```text
RAW DATA
retail_store_sales.csv
        │
        ▼
┌───────────────────────────────────────┐
│ 01 — PYTHON                           │
│ Data Quality & Deterministic Cleaning │
│ Calidad y limpieza determinística     │
└───────────────────────────────────────┘
        │
        ├── Structural inspection
        ├── Missing-value diagnosis
        ├── Duplicate validation
        ├── Domain validation
        ├── Business-rule validation
        ├── Recover Item
        ├── Recover Price Per Unit
        └── Post-cleaning validation
        │
        ▼
retail_sales_pre_imputation.parquet
        │
        ▼
┌───────────────────────────────────────┐
│ 02 — R                                │
│ Statistical Imputation & Validation   │
│ Imputación estadística y validación   │
└───────────────────────────────────────┘
        │
        ├── Missing-data diagnosis
        ├── Quantity model validation
        ├── PMM / CART / Random Forest
        ├── Quantity imputation
        ├── Total Spent reconstruction
        ├── Discount Applied evaluation
        ├── Logistic / CART / RF
        ├── Discount_Status
        └── Final validation
        │
        ├───────────────────────────────┐
        ▼                               ▼
SCIENTIFIC DATABASE              OPERATIONAL DATABASE
Base científica                  Base operacional
        │                               │
        ▼                               ▼
Parquet + CSV                    Parquet + CSV
```

---

# 3. 01 — Python: Data Quality and Deterministic Cleaning

## 3.1 Input / Entrada

```text
data/raw/retail_store_sales.csv
```

The original dataset contains:

| Characteristic | Value |
|---|---:|
| Records / Registros | 12,575 |
| Variables / Variables | 11 |
| Categories / Categorías | 8 |
| Items / Productos | 200 |
| Customers / Clientes | 25 |

The original variables are:

- `Transaction ID`
- `Customer ID`
- `Category`
- `Item`
- `Price Per Unit`
- `Quantity`
- `Total Spent`
- `Payment Method`
- `Location`
- `Transaction Date`
- `Discount Applied`

---

## 3.2 Initial Data Quality Assessment / Evaluación inicial de calidad

### English

The Python stage begins by preserving the original dataset and creating an independent working copy.

The analysis evaluates:

- Dataset dimensions.
- Variable types.
- Missing values.
- Cardinality.
- Duplicate records.
- Duplicate transaction identifiers.
- Zero values in numerical variables.
- Valid numerical domains.
- Transaction-date validity.
- Product/category relationships.
- Product/price relationships.
- Arithmetic consistency.

### Español

La etapa en Python comienza conservando intacto el dataset original y creando una copia independiente de trabajo.

El análisis evalúa:

- Dimensiones del dataset.
- Tipos de variables.
- Valores faltantes.
- Cardinalidad.
- Registros duplicados.
- Identificadores de transacción duplicados.
- Valores iguales a cero en variables numéricas.
- Dominios numéricos válidos.
- Validez de las fechas.
- Relaciones producto/categoría.
- Relaciones producto/precio.
- Consistencia aritmética.

---

## 3.3 Initial Missing Values / Valores faltantes iniciales

| Variable | Missing / Faltantes | Percentage / Porcentaje |
|---|---:|---:|
| `Discount Applied` | 4,199 | 33.39% |
| `Item` | 1,213 | 9.65% |
| `Price Per Unit` | 609 | 4.84% |
| `Quantity` | 604 | 4.80% |
| `Total Spent` | 604 | 4.80% |

No completely duplicated rows or duplicated `Transaction ID` values were detected.

---

## 3.4 Business Rules / Reglas de negocio

The cleaning process evaluates validated relationships among the transactional variables.

A central arithmetic relationship is:

```text
Total Spent = Price Per Unit × Quantity
```

The product structure is also evaluated through relationships such as:

```text
Item → Category
Item → Price Per Unit
```

These relationships allow missing values to be recovered only when the available information uniquely determines the missing value.

---

## 3.5 Deterministic Recovery / Recuperación determinística

### English

Python deterministically recovered:

| Variable | Before | After | Recovered |
|---|---:|---:|---:|
| `Item` | 1,213 | 0 | 1,213 |
| `Price Per Unit` | 609 | 0 | 609 |

A total of:

**1,822 traceable deterministic transformations**

were performed.

No arbitrary statistical imputation was applied during this stage.

### Español

Python permitió recuperar de forma determinística:

| Variable | Antes | Después | Recuperados |
|---|---:|---:|---:|
| `Item` | 1.213 | 0 | 1.213 |
| `Price Per Unit` | 609 | 0 | 609 |

En total se realizaron:

**1.822 transformaciones determinísticas trazables**

Durante esta etapa no se aplicaron imputaciones estadísticas arbitrarias.

---

## 3.6 Missing Values After Python / Faltantes después de Python

After deterministic cleaning, only three variables still required treatment:

| Variable | Missing / Faltantes |
|---|---:|
| `Quantity` | 604 |
| `Total Spent` | 604 |
| `Discount Applied` | 4,199 |

The missing values of `Quantity` and `Total Spent` occur in the same records.

Because `Price Per Unit` is available for these observations, the following sequential strategy becomes possible:

```text
Impute Quantity
        ↓
Total Spent = Price Per Unit × Quantity
```

Therefore, `Total Spent` does not require independent statistical imputation.

---

## 3.7 Python Output / Salida de Python

The cleaned dataset is exported for the statistical stage in R:

```text
data/processed/retail_sales_pre_imputation.parquet
data/processed/retail_sales_pre_imputation.csv
```

Dimensions:

```text
12,575 records × 11 variables
```

---

# 4. 02 — R: Statistical Imputation and Validation

## 4.1 Input / Entrada

R receives the Parquet dataset generated by Python:

```text
data/processed/retail_sales_pre_imputation.parquet
```

Initial state in R:

| Characteristic | Value |
|---|---:|
| Records / Registros | 12,575 |
| Variables / Variables | 11 |
| Missing values / Valores faltantes | 5,407 |

The remaining missingness is:

```text
Discount Applied     4,199
Quantity               604
Total Spent             604
```

---

# 5. Quantity Statistical Imputation / Imputación estadística de Quantity

## 5.1 Predictors / Predictores

The following variables are used as predictors:

```text
Item
Payment Method
Location
Anio
Mes
```

`Anio` and `Mes` are derived from `Transaction Date`.

The transaction period extends from:

```text
01/01/2022 – 18/01/2025
```

---

## 5.2 Artificial Validation / Validación artificial

Before imputing the genuinely missing values, the candidate methods are evaluated using known observations.

A reproducible random sample of **10% of the observed Quantity values** is artificially masked.

```text
Observed Quantity values:       11,971
Artificial validation cases:     1,197
Original missing values:           604
Missing during validation:        1,801
```

Random seed:

```text
5477976
```

This strategy makes it possible to compare imputed values against their known true values.

---

## 5.3 Compared Methods / Métodos comparados

Three methods are evaluated:

1. **Predictive Mean Matching (PMM)**
2. **CART**
3. **Random Forest**

The methods are evaluated using:

- Exact agreement.
- Agreement within ±1.
- Agreement within ±2.
- MAE.
- Median Absolute Error.
- RMSE.
- Bias.
- Distribution preservation.

---

## 5.4 Quantity Results / Resultados de Quantity

| Method | Exact | ±1 | ±2 | MAE | RMSE |
|---|---:|---:|---:|---:|---:|
| PMM | 9.52% | 29.8% | 47.5% | 3.20 | 3.98 |
| CART | 10.1% | 28.7% | 44.9% | 3.33 | 4.12 |
| Random Forest | 9.44% | 26.7% | 42.4% | 3.29 | 4.00 |

**PMM was selected** because of its balanced validation performance and its ability to preserve the observed distribution.

The final procedure imputes the **604 missing `Quantity` values** while preserving all originally observed values.

Final valid domain:

```text
Quantity ∈ [1, 10]
```

---

# 6. Total Spent Reconstruction / Reconstrucción de Total Spent

`Total Spent` is not statistically imputed.

Instead, it is reconstructed using the validated deterministic relationship:

```text
Total Spent = Price Per Unit × Quantity
```

After completing `Quantity`, this relationship produces a complete calculated `Total Spent`.

The original observed values were audited against the calculated values before the reconstructed variable replaced the incomplete original variable.

Final result:

```text
Missing Total Spent = 0
```

---

# 7. Discount Applied Evaluation / Evaluación de Discount Applied

`Discount Applied` originally contains:

| State | Records |
|---|---:|
| TRUE | 4,219 |
| FALSE | 4,157 |
| Missing | 4,199 |

Missing percentage:

```text
33.39%
```

Unlike `Total Spent`, no deterministic business rule allows the missing discount state to be recovered.

Therefore, predictive modeling was evaluated.

---

## 7.1 Models / Modelos

The following classification models were compared:

1. **Logistic Regression**
2. **CART**
3. **Random Forest**

Predictors included:

```text
Item
Quantity
Total Spent
Payment Method
Location
Anio
Mes
```

A stratified artificial validation sample was constructed from observed `Discount Applied` cases.

Validation sample:

```text
838 records
TRUE  = 422
FALSE = 416
```

---

## 7.2 Classification Results / Resultados de clasificación

| Method | Accuracy | Sensitivity | Specificity | F1 | Balanced Accuracy |
|---|---:|---:|---:|---:|---:|
| Logistic Regression | 0.495 | 0.455 | 0.536 | 0.476 | 0.496 |
| CART | 0.504 | 0.389 | 0.620 | 0.441 | 0.504 |
| Random Forest | 0.481 | 0.472 | 0.490 | 0.478 | 0.481 |

The majority-class baseline accuracy is approximately:

```text
50.36%
```

The evaluated models therefore did not provide sufficient predictive improvement to justify assigning the missing observations as `TRUE` or `FALSE`.

Consequently, **no statistical imputation was applied to the original `Discount Applied` variable**.

---

# 8. Explicit Unknown State / Estado desconocido explícito

To provide a complete variable for operational use without inventing discount information, a new variable was created:

```text
Discount_Status
```

with three possible states:

```text
TRUE
FALSE
UNKNOWN
```

`UNKNOWN` means that the original `Discount Applied` value was missing.

It is important to emphasize:

> `UNKNOWN` is an explicit representation of missing information, not a statistical imputation.

---

# 9. Final Databases / Bases de datos finales

Two final datasets are generated for different purposes.

## Scientific Database / Base científica

Object:

```text
datos_final_cientifico
```

Files:

```text
data/processed/retail_sales_final_scientific.parquet
data/processed/retail_sales_final_scientific.csv
```

Characteristics:

```text
Records:          12,575
Variables:             14
Missing values:     4,199
```

The **4,199 missing values are preserved in the original `Discount Applied` variable**.

This version is intended for:

- Scientific analysis.
- Reproducibility.
- Methodological auditing.
- Missing-data research.
- Traceability.

---

## Operational Database / Base operacional

Object:

```text
datos_final_operacional
```

Files:

```text
data/processed/retail_sales_final_operational.parquet
data/processed/retail_sales_final_operational.csv
```

Characteristics:

```text
Records:          12,575
Variables:             13
Missing values:         0
```

The incomplete original `Discount Applied` variable is removed, while `Discount_Status` is retained.

This version is suitable for:

- Dashboards.
- Reporting.
- Exploratory analysis.
- Operational workflows.
- Subsequent modeling pipelines requiring a complete dataset.

---

# 10. Final Data Quality Summary / Resumen final de calidad

| Stage | Item Missing | Price Missing | Quantity Missing | Total Spent Missing | Discount Applied Missing |
|---|---:|---:|---:|---:|---:|
| Original RAW | 1,213 | 609 | 604 | 604 | 4,199 |
| After Python | 0 | 0 | 604 | 604 | 4,199 |
| Scientific Final | 0 | 0 | 0 | 0 | 4,199 |
| Operational Final | 0 | 0 | 0 | 0 | — |

The complete workflow therefore separates three fundamentally different missing-data treatments:

```text
Item + Price Per Unit
        │
        └── Deterministic recovery
                 ↓
Quantity
        │
        └── Statistical imputation (PMM)
                 ↓
Total Spent
        │
        └── Deterministic reconstruction
                 ↓
Discount Applied
        │
        ├── Predictive models evaluated
        ├── Insufficient predictive performance
        └── Original missingness preserved
                 ↓
Discount_Status
        └── UNKNOWN for operational representation
```

---

# 11. Project Structure / Estructura del proyecto

```text
0_Retail_Store_Sales_Dirty_for_Data_Cleaning/
│
├── README.md
│
├── 01_data_quality_dirty_retail.ipynb
├── 02_imputacion_retail.Rmd
├── 02_imputacion_retail.html
├── styles.css
│
├── data/
│   ├── raw/
│   │   └── retail_store_sales.csv
│   │
│   ├── processed/
│   │   ├── retail_sales_pre_imputation.parquet
│   │   ├── retail_sales_pre_imputation.csv
│   │   ├── retail_sales_final_scientific.parquet
│   │   ├── retail_sales_final_scientific.csv
│   │   ├── retail_sales_final_operational.parquet
│   │   └── retail_sales_final_operational.csv
│   │
│   └── reports/
│
└── outputs/
    └── figures/
```

```
# 11.b. Data and Reports / Datos y reportes

### English

The datasets used and generated during this project are available through the following shared Google Drive folder:

**Data / Datos:**  
[Open dataset folder in Google Drive](https://drive.google.com/drive/folders/1k1GzqHLIrN4VRVJ2OGi3enQqoCvlhRD4?usp=sharing)

The shared folder contains the data associated with the different stages of the project, including the input and processed datasets.

To review the complete analysis files and generated reports, open the **`reportes`** folder. This folder contains the project reports documenting the Python data-quality and deterministic-cleaning stage and the R statistical-imputation and validation stage.

### Español

Los conjuntos de datos utilizados y generados durante este proyecto se encuentran disponibles en la siguiente carpeta compartida de Google Drive:

**Datos / Data:**  
[Abrir carpeta de datos en Google Drive](https://drive.google.com/drive/folders/1k1GzqHLIrN4VRVJ2OGi3enQqoCvlhRD4?usp=sharing)

La carpeta compartida contiene los datos asociados con las diferentes etapas del proyecto, incluyendo las bases de entrada y los conjuntos de datos procesados.

Para consultar los archivos completos de los análisis y los reportes generados, ingrese a la carpeta **`reportes`**. Allí se encuentran los informes del proyecto correspondientes a la etapa de calidad y limpieza determinística desarrollada en Python y a la etapa de imputación estadística y validación desarrollada en R.
```
---

# 12. Main Technologies / Tecnologías principales

### Python

- pandas
- NumPy
- Matplotlib
- PyArrow

### R

- arrow
- tidyverse
- mice
- rpart
- randomForest
- DT
- writexl

### Statistical Methods / Métodos estadísticos

- Predictive Mean Matching (PMM)
- CART
- Random Forest
- Logistic Regression
- Artificial masking validation
- Classification metrics
- Distributional validation

---

# 13. Reproducibility / Reproducibilidad

The project follows a sequential pipeline:

```text
01_data_quality_dirty_retail.ipynb
                ↓
retail_sales_pre_imputation.parquet
                ↓
02_imputacion_retail.Rmd
                ↓
Final Scientific + Operational Databases
```

To reproduce the analysis:

1. Place the original dataset in `data/raw/`.
2. Run `01_data_quality_dirty_retail.ipynb`.
3. Verify the creation of `retail_sales_pre_imputation.parquet`.
4. Run `02_imputacion_retail.Rmd`.
5. Verify the four final files in `data/processed/`.

The R analysis uses fixed random seeds for the artificial validation procedures to improve reproducibility.

---

# 14. Final Result / Resultado final

### English

The project transforms a deliberately dirty transactional dataset into two validated final products while maintaining a clear distinction between **observed values, deterministic recoveries, statistical imputations, deterministic reconstructions, and unresolved missing information**.

The pipeline recovered `Item` and `Price Per Unit` deterministically, statistically imputed `Quantity` using a validated PMM procedure, reconstructed `Total Spent` from the validated business relationship, and preserved the unresolved uncertainty in `Discount Applied`.

The final pipeline completed successfully:

**FINAL PIPELINE STATUS: OK**

### Español

El proyecto transforma un conjunto de datos transaccionales deliberadamente sucio en dos productos finales validados, manteniendo una distinción explícita entre **valores observados, recuperaciones determinísticas, imputaciones estadísticas, reconstrucciones determinísticas e información faltante no resuelta**.

El pipeline recuperó determinísticamente `Item` y `Price Per Unit`, imputó estadísticamente `Quantity` mediante un procedimiento PMM validado, reconstruyó `Total Spent` a partir de la relación de negocio comprobada y preservó la incertidumbre no resuelta de `Discount Applied`.

El pipeline finalizó satisfactoriamente:

**FINAL PIPELINE STATUS: OK**

---

## Author / Autor

**John Jairo Prado**

Project focused on reproducible data cleaning, statistical validation, missing-data treatment, and preparation of analytical datasets using **Python and R**.