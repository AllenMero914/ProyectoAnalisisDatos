# 📊 Análisis de Datos de Consultas Externas en Ecuador (2020-2025)

> **Escuela Politécnica Nacional**  
> **Autores:** Allen Jahir Mero Chicaiza · Cristopher Paul Balseca Nuñez  
> **Correos:** allen.mero@epn.edu.ec · cristopher.balseca@epn.edu.ec

---

## 📋 Descripción del Proyecto

Este proyecto analiza los registros de **Consultas Externas** del sistema de salud pública del Ecuador en el periodo **2020 a 2025**. La Consulta Externa es un servicio ambulatorio para pacientes con cita previa que acceden a atenciones médicas para diferentes tipos de diagnósticos.

- **Datos reales (2021–2024):** Fuente oficial del Ministerio de Salud Pública del Ecuador, con un volumen total superior a **29 GB** distribuidos en cuatro archivos anuales.
- **Datos sintéticos (2020 y 2025):** Generados con Python a partir de los datos reales, con inconsistencias controladas (~11% de celdas afectadas) para validar el pipeline de limpieza ETL.

El flujo de trabajo combina **KNIME Analytics Platform** y **Python** para el proceso ETL, y **Power BI** para la visualización y análisis.

---

## 🗂️ Estructura del Repositorio

```
DatasetsAnalisis/
│
├── 📂 data/
│   ├── raw/                          # ⚠️ NO incluido (archivos CSV originales de 6-8 GB c/u)
│   │   ├── Consulta Externa 2021.csv
│   │   ├── Consulta Externa 2022.csv
│   │   ├── Consulta Externa 2023.csv
│   │   └── Consulta Externa 2024.csv
│   │
│   ├── samples/                      # Muestras de 450,000 registros por año
│   │   ├── Consulta_Externa_2021_Muestra.csv
│   │   ├── Consulta_Externa_2022_Muestra.csv
│   │   ├── Consulta_Externa_2023_Muestra.csv
│   │   ├── Consulta_Externa_2024_Muestra.csv
│   │   ├── Consulta_Externa_2020_Muestra_Sucio.csv  # Generado sintéticamente
│   │   └── Consulta_Externa_2025_Muestra_Sucio.csv  # Generado sintéticamente
│   │
│   └── processed/                    # Dataset final limpio y normalizado
│       └── Consulta_Externa_Muestra_Limpia.csv
|       └── Consulta_Externa_Limpiada_KnimeAndPy.csv
│
├── 📂 notebooks/
│   ├── Generar_datasets_sucios.ipynb     # Genera y añade inconsistencias a 2020 y 2025
│   └── Normalizacion_CSV_Knime.ipynb     # Normalización avanzada post-KNIME (Python)
│
├── 📂 workflows_knime/
│   ├── Muestreo_Consultas_Externas.knwf  # Extrae 450,000 registros de los CSV originales
│   └── Limpieza_Consultas_Externas.knwf  # Limpieza y estandarización principal
│
├── 📂 dashboards/
│   └── Dashboard_Consultas_Externas.pbix # Modelo Power BI con 4 páginas y medidas DAX
│
├── 📂 docs/
│   ├── Informe_IEEE.pdf              # Informe técnico formato IEEE doble columna
│   └── Diccionario_Datos.xlsx             # Descripción de columnas y valores
│
├── 📂 Anexos/
│   └── *.jpeg                        # Evidencias del proceso en MySQL, KNIME, etc.
│
├── README.md
└── requirements.txt
```

---

## ⚙️ Requisitos del Sistema

| Herramienta | Versión recomendada |
|---|---|
| Python | 3.9 o superior |
| KNIME Analytics Platform | 5.x o superior |
| Power BI Desktop | Última versión disponible |
| RAM disponible | Mínimo 8 GB (recomendado 16 GB) |

### Instalación de dependencias Python

```bash
pip install -r requirements.txt
```

---

## 🔄 Flujo del Proyecto (ETL)

```
[CSV Originales 6-8 GB]
        │
        ▼
[1. MUESTREO - KNIME]
    Muestreo_Consultas_Externas.knwf
    → 450,000 registros/año (2021-2024)
        │
        ▼
[2. GENERACIÓN SINTÉTICA - Python]
    Generar_datasets_sucios.ipynb
    → Datasets 2020 y 2025 (~11% inconsistencias)
        │
        ▼
[3. LIMPIEZA - KNIME]
    Limpieza_Consultas_Externas.knwf
    → Eliminación de duplicados, outliers, nulos
        │
        ▼
[4. NORMALIZACIÓN - Python]
    Normalizacion_CSV_Knime.ipynb
    → UTF-8, fechas yyyy-mm-dd, sin tildes
        │
        ▼
[Dataset Limpio Final]
        │
        ▼
[5. ANÁLISIS - Power BI]
    Dashboard_Consultas_Externas.pbix
    → 4 páginas · Medidas DAX · Visualizaciones
```

---

## 📊 Datasets y Columnas

Cada dataset contiene **27 columnas**:

| Categoría | Columnas |
|---|---|
| **Temporal** | `FECHA_ATENCION` |
| **Geográfica** | `ZONA`, `PROVINCIA`, `CANTON`, `PROVINCIA_RESIDENCIA`, `CANTON_RESIDENCIA` |
| **Demográfica** | `SEXO`, `EDAD_ANIOS`, `FECHA_NACIMIENTO`, `NACIONALIDAD`, `AUTOIDENTIFICACION_ETNICA` |
| **Diagnóstica** | `CODIGO_CIE10_1/2/3`, `DESCRIPCION_CIE10_1/2/3`, `CONDICION_DIAGNOSTICO1/2/3` |
| **Administrativa** | `TIPO_ATENCION1/2/3`, `GRUPO_PRIORITARIO_1/2/3` |

### Inconsistencias en datasets sintéticos (2020 y 2025)

| Tipo de Inconsistencia | Porcentaje | Celdas aprox. (por dataset) |
|---|---|---|
| Valores nulos | ~2.5% | ~675,000 celdas |
| Espacios extra (inicio/final) | ~2% | ~269,000 valores |
| Capitalización inconsistente | ~2% | ~220,000 valores |
| Valores genéricos (N/A, "-") | ~2% | ~214,000 celdas |
| Duplicados in-place | ~1.5% | 6,750 filas |
| **Total modificado** | **~11%** | **~1,380,000 celdas** |

---

## 📈 Dashboard de Power BI

El dashboard responde a más de 12 preguntas de negocio a través de 4 páginas:

1. **Resumen Ejecutivo** – KPIs de atenciones totales, promedio de edad, distribución por sexo.
2. **Análisis Geográfico** – Distribución de demanda por provincia y cantón.
3. **Diagnósticos CIE-10** – Top enfermedades y morbilidad por año.
4. **Tendencias Temporales** – Evolución mensual/anual 2020-2025.

---

## 🎥 Videos Explicativos

- **Video 1 – Proceso ETL y Calidad de Datos:** [Insertar enlace]
- **Video 2 – Análisis del Dashboard y Conclusiones:** [Insertar enlace]

---

## ⚠️ Nota sobre los Datos

Los archivos CSV originales (6-8 GB por año) **no están incluidos** en este repositorio debido a su tamaño. Si necesitas acceso a los datos completos, solicítalos a los autores o descárgalos desde el portal de datos abiertos del Ministerio de Salud Pública del Ecuador.

---

## 📄 Licencia

Este proyecto es de carácter académico. Los datos provienen de fuentes públicas del Estado ecuatoriano.
