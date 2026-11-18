# Alerta Campus Central

**Sistema de análisis de rendimiento académico y detección temprana de riesgo estudiantil en la Ciudad Universitaria, San Salvador**

> Proyecto Integrador · *De la ingesta al valor estratégico* · EdTech / Learning Analytics
> Módulo: Gestión de Datos para Inteligencia Artificial y Big Data (COIDS1604) · ITCA-FEPADE

---

## Tabla de contenido

1. [Descripción del proyecto](#1-descripción-del-proyecto)
2. [Sector y problemática](#2-sector-y-problemática)
3. [Fuentes de datos](#3-fuentes-de-datos)
4. [Herramientas y tecnologías](#4-herramientas-y-tecnologías)
5. [Estructura del repositorio](#5-estructura-del-repositorio)
6. [Instrucciones de reproducción](#6-instrucciones-de-reproducción)
7. [Resultados principales](#7-resultados-principales)
8. [Gobernanza y ética](#8-gobernanza-y-ética)
9. [Limitaciones](#9-limitaciones)
10. [Contacto](#10-contacto)

---

## 1. Descripción del proyecto

**Alerta Campus Central** es una propuesta de plataforma de *Learning Analytics* para la sede central de una universidad pública salvadoreña (Ciudad Universitaria, San Salvador). Integra datos académicos, socioeconómicos y cualitativos que hoy viven en sistemas separados, y los convierte en alertas tempranas para decidir **a quién priorizar, por qué señal y con qué intervención**.

**Pregunta principal del proyecto:**
¿Cómo puede la administración de la universidad analizar el rendimiento académico y anticipar el riesgo de deserción y bajo rendimiento de sus estudiantes, integrando asistencia, calificaciones, participación digital y factores socioeconómicos, para intervenir oportunamente con los recursos limitados que posee?

**Objetivo:** integrar, visualizar y modelar datos para detectar riesgo temprano y orientar acciones oportunas.

**Ruta del proyecto (de la ingesta al valor estratégico):**

| # | Etapa | Qué se hizo |
|---|-------|-------------|
| 01 | **Datos** | 3 fuentes heterogéneas integradas y gobernadas |
| 02 | **Arquitectura** | Zonas de datos, Apache Spark y formato Parquet |
| 03 | **Visualización** | Dashboard en Power BI y catálogo de KPIs |
| 04 | **Territorio** | Mapas y patrones locales con ArcGIS |
| 05 | **Predicción** | Modelos de ML y validación |
| 06 | **Acción** | Matriz de recomendaciones priorizadas |

**Usuarios beneficiados:** rectoría y vicerrectoría académica, decanos, coordinadores de carrera, docentes tutores, Bienestar Estudiantil, Registro Académico, coordinadores de becas y analistas de datos institucionales. De forma indirecta: estudiantes, MINED/MINEDUCYT, empleadores y la sociedad salvadoreña.

---

## 2. Sector y problemática

**Sector principal:** EdTech (Tecnología Educativa), subsector *Learning Analytics*, aplicado a la sede central de una universidad pública en San Salvador.

**Sectores relacionados:** educación superior pública, gobierno (MINED / MINEDUCYT, CONAES, programas de becas), ONG y cooperación, software SaaS (modelo B2G/B2B), Inteligencia Artificial y Big Data, Business Intelligence, telecomunicaciones, salud y bienestar, y transporte.

**Situación actual de la sede central** (≈ 5,000 estudiantes en 6 facultades):

- La asistencia se registra en listas físicas; el sistema académico legado (SIAE) solo muestra notas finales.
- No existe análisis intermedio que detecte a un estudiante reprobando a tiempo.
- Los expedientes (socioeconómico, becas, historial académico) están en sistemas separados: Bienestar Estudiantil, Registro Académico y un Excel por facultad.
- La deserción se detecta cuando el estudiante ya no se matricula: no hay alerta temprana.
- Las becas y la residencia se asignan sin cruzar el riesgo académico.
- La tutoría y el acompañamiento psicológico son reactivos.
- No hay trazabilidad entre facultades: quien cambia de carrera pierde su historial de riesgo.
- Factores locales críticos: estudiantes que trabajan y estudian, largos desplazamientos desde el AMSS, violencia social, falta de conectividad en casa y limitaciones presupuestarias.

**Consecuencias:**

| Impacto | Descripción |
|---------|-------------|
| Deserción | 200–300 estudiantes abandonan por ciclo, sobre todo en primer y segundo año |
| Repitencia | ~1 de cada 4 estudiantes de primer año reprueba al menos 2 materias del ciclo básico |
| Bajo rendimiento | Alta reprobación en Cálculo I, Física I, Química General, Estadística y Redacción |
| Recursos mal asignados | Becas y residencia sin criterio de riesgo académico |
| Carga docente | 6–8 horas semanales en reportes manuales que nadie analiza a tiempo |
| Metas institucionales | No se alcanzan las metas de retención, graduación oportuna y eficiencia terminal |
| Impacto social | Menores oportunidades de movilidad social para jóvenes del AMSS |

**Necesidad específica:** una plataforma que centralice los datos, analice el rendimiento por estudiante/facultad/carrera/materia, **prediga con 4–8 semanas de anticipación** quién tiene alta probabilidad de desertar o reprobar, genere alertas priorizadas, recomiende intervenciones viables, escale con datos masivos, se integre con el SIAE y alimente dashboards en Power BI.

---

## 3. Fuentes de datos

Se integraron **tres fuentes heterogéneas** mediante un `inner join` por `carnet` (1,000 registros):

| Formato | Archivo | Dimensiones | Contenido |
|---------|---------|-------------|-----------|
| CSV | `Alumnado_UES.csv` | 1,000 × 19 | Perfil demográfico y socioeconómico (facultad, carrera, municipio, zona, acceso a internet, trabajo, beca…) |
| SQL → SQLite | `Rendimiento_academicoUES.sql` / `.db` (tabla `Rendimiento_academico`) | 1,000 × 14 | Notas de teoría, laboratorio y final, faltas, materia reprobada, tareas, participación, nivel de riesgo, probabilidad de deserción |
| JSON | `bitacora_estudiantes_ues.json` | 1,000 × 2 | Historial cualitativo de intervenciones (total de observaciones, requiere seguimiento, última intervención…) |

**Datasets derivados:**

| Dataset | Dimensiones | Descripción |
|---------|-------------|-------------|
| `dataset_consolidado_…csv` | 1,000 × 33 | Integración de las 3 fuentes por carné |
| `dataset_limpio_…csv` | 1,000 × 43 | Limpio y con 6 características nuevas |
| `datase_publicate_…csv` | — | Vista protegida (9 columnas, sin datos sensibles) |
| `municipios_alertacampus_guia06_validado.csv`, `alertas_sinteticas_…_validado.csv` | — | Capas para el análisis geoespacial en ArcGIS |

**Características creadas (feature engineering):**
`promedio_academico`, `porcentaje_asistencia`, `actividad_lms`, `riesgo_academico`, `es_fin_semana`, `semana_evaluacion`.

**Limpieza aplicada:** normalización de textos, nombres de columnas, sexo (M/F) y fechas; imputación de horas de trabajo y beca; 0 duplicados; 4 columnas extraídas del JSON.

> **Todos los datos son sintéticos.** Sirven para demostrar el proceso, no para tomar decisiones reales (ver [Limitaciones](#9-limitaciones)).

---

## 4. Herramientas y tecnologías

| Capa | Herramientas |
|------|--------------|
| Entorno | Google Colab, Python 3 |
| Ingesta y limpieza | `pandas`, `numpy`, `sqlite3`, `json`, `pathlib` |
| Big Data | Apache Spark (`pyspark`: DataFrames, Spark SQL, particiones), formato **Parquet** |
| Gobernanza | `hashlib` (tokens SHA-256), enmascaramiento, matriz de riesgos y política de gobernanza |
| Visualización / BI | Power BI Desktop, `matplotlib`, `seaborn` |
| Geoespacial | ArcGIS (capas por municipio) |
| Machine Learning | `scikit-learn` (Regresión Logística, Árbol de Decisión, Random Forest, `GridSearchCV`, `StratifiedKFold`), `scipy`, `joblib` |
| Reportería | `jinja2`, `weasyprint`, `python-docx`, `openpyxl` |
| Control de versiones | Git / GitHub |

**Arquitectura por zonas:**

```
Fuentes (CSV · SQL · JSON)
   └─► Zona cruda       datos/crudos
        └─► Zona limpia      datos/procesados
             └─► Zona analítica   datos/analiticos
                  └─► Consumo: Power BI · ArcGIS · IA
```

---

## 5. Estructura del repositorio

```
proyecto_datos_ia/
├── datos/
│   ├── crudos/            # Fuentes originales (CSV, SQL/DB)
│   ├── procesados/        # Datasets consolidado y limpio
│   ├── externos/          # Bitácora JSON y datos externos
│   ├── analiticos/        # Agregados, features y Parquet
│   └── gobernanza/        # Vista protegida y clasificación
├── notebooks/
│   ├── guia_01_fuentes_heterogeneas.ipynb
│   ├── guia_02_limpieza_ingenieria_caracteristicas.ipynb
│   ├── guia_03_arquitectura_bigdata_spark.ipynb
│   ├── guia_04_gobernanza_etica_datos.ipynb
│   ├── guia_05_visualizacion_powerbi.ipynb
│   ├── guia_06_analisis_geoespacial_arcgis.ipynb
│   ├── guia_07_reporteria_profesional.ipynb
│   ├── guia_08_fundamentos_prediccion_ia.ipynb
│   ├── guia_09_entrenamiento_modelos_ml.ipynb
│   ├── guia_10_optimizacion_hiperparametros.ipynb
│   ├── guia_11_automatizacion_flujos.ipynb
│   └── guia_12_interpretacion_decisiones.ipynb
├── scripts/
│   ├── conexion_fuentes.py
│   ├── limpieza_datos.py
│   └── pipeline_automatizado.py
├── documentos/
│   ├── inventario_fuentes.xlsx
│   ├── diccionario_datos.xlsx
│   ├── clasificacion_datos_guia04.xlsx
│   ├── matriz_riesgos_guia04.xlsx
│   ├── politica_gobernanza_guia04.md
│   ├── catalogo_kpis_guia05.xlsx
│   ├── matriz_recomendaciones_guia07.xlsx
│   ├── matriz_trazabilidad_guia07.xlsx
│   ├── informe_gerencial_final.pdf
│   └── bitacora_semana_{01_04, 05_08, 09_13}.docx
├── visualizaciones/
│   └── [proyecto]_guia05.pbix          # Dashboard Power BI
├── geo/
│   ├── sucursales_[proyecto]_guia06.csv
│   └── demanda_[proyecto]_guia06.csv
├── sql/
│   └── estructura_inicial.sql
├── evidencias/
│   ├── guia05_dashboard/
│   ├── guia06_mapa/
│   └── guia07_informe/
├── presentacion/
│   └── defensa_final.pptx
└── README.md
```

---

## 6. Instrucciones de reproducción

Los notebooks fueron desarrollados en **Google Colab** y se ejecutan **en orden numérico**: la salida de cada guía es la entrada de la siguiente.

### 6.1 Requisitos

- Cuenta de Google (Colab) o Python 3.10+ en local
- Power BI Desktop (para abrir el `.pbix`) y acceso a ArcGIS (para las capas geoespaciales)
- Librerías principales:

```bash
pip install pandas numpy scipy scikit-learn matplotlib seaborn joblib \
            pyspark openpyxl jinja2 weasyprint python-docx
```

### 6.2 Pasos

1. **Clonar el repositorio**
   ```bash
   git clone https://github.com/<isela-q05>/<https://github.com/isela-q05/ProyectoFinal_EdTech_Learning_Analytics_IDS221.git>.git
   cd proyecto_datos_ia
   ```
2. **Ubicar los datos de origen** (`Alumnado_UES.csv`, `Rendimiento_academicoUES.sql`, `bitacora_estudiantes_ues.json`) en `datos/crudos/` y `datos/externos/`.
3. **Abrir los notebooks en Colab** (o Jupyter) y ejecutarlos en orden:

   | Guía | Notebook | Resultado |
   |------|----------|-----------|
   | 01 | Fuentes heterogéneas | Dataset consolidado (1,000 × 33) |
   | 02 | Limpieza e ingeniería de características | Dataset limpio (1,000 × 43) |
   | 03 | Arquitectura Big Data con Spark | Parquet en 4 particiones, zonas de datos |
   | 04 | Gobernanza y ética | Clasificación de 43 campos, matriz de riesgos, vista protegida |
   | 05 | Visualización Power BI | Dashboard y catálogo de KPIs |
   | 06 | Análisis geoespacial ArcGIS | Capas y mapas por municipio |
   | 07 | Reportería profesional | Informe gerencial, matrices de recomendaciones y trazabilidad |
   | 08 | Fundamentos de predicción | Línea base y métricas iniciales |
   | 09 | Entrenamiento de modelos | Comparación de 3 modelos |
   | 10 | Optimización de hiperparámetros | `GridSearchCV` y comparación antes/después |
   | 11 | Automatización de flujos | Pipeline automatizado (`scripts/`) |
   | 12 | Interpretación y decisiones | Recomendaciones finales |

4. **Ajustar rutas:** los notebooks usan rutas de Colab (`/content/datos/...`). En local, cambiar `BASE_DIR` por la ruta del proyecto.
5. **Spark:** la guía 03 usa `SparkSession` local; en Colab basta con `pip install pyspark`.
6. **Dashboard:** abrir `visualizaciones/EdTech_Learning_Analytics_guia05.pbix` en Power BI Desktop y actualizar la ruta del origen de datos.

> Las semillas aleatorias se fijan en los notebooks de ML (Guías 8–10) para que los resultados sean reproducibles.

---

## 7. Resultados principales

### 7.1 Dashboard y KPIs (Power BI)

| KPI | Valor |
|-----|-------|
| Estudiantes analizados | **1,000** |
| Estudiantes en riesgo académico alto | **467 (46.7 %)** |
| Promedio académico | **7.06** |
| Asistencia promedio | **76.1 %** |
| Actividad LMS promedio | **9.5** |

### 7.2 Hallazgos descriptivos

- **Por materia:** Inglés concentra el mayor riesgo alto (53.0 %) y Estadística el menor (40.8 %), una brecha de ~12 puntos.
- **Por facultad:** la brecha es menor, de 41.4 % (Medicina) a 50.0 % (Humanidades e Ingeniería y Arquitectura).

### 7.3 Análisis geoespacial (ArcGIS, 12 municipios)

- Riesgo alto entre **34.6 %** (La Libertad Sur) y **56.5 %** (San Miguel Centro).
- Máximo de materia reprobada: **32.0 %** en San Salvador Central.
- Máximo de internet limitado: **41.7 %** en San Salvador Oeste.
- Implicación: acompañamiento con enfoque local, no solo académico. Las diferencias aún **no son concluyentes**.

### 7.4 Modelos predictivos

Objetivo: clasificar el riesgo en **Alto / Medio / Bajo**. División 70 % entrenamiento · 15 % validación · 15 % prueba. Métrica principal: **F1 macro**.

| Modelo | F1 macro (CV) | F1 macro (prueba) | Observación |
|--------|---------------|-------------------|-------------|
| Línea base (clase más frecuente) | 0.21 | 0.212 | Referencia |
| **Regresión logística**  | 0.50 | **0.584** | Brecha train–test de 0.07: generaliza |
| Árbol de decisión | 0.49 | 0.46 | Sobreajuste (1.00 en entrenamiento) |
| Random Forest | 0.48 | 0.58 | Sobreajuste (1.00 en entrenamiento) |

**Modelo seleccionado — Regresión logística:** F1 macro 0.584 (+175 % sobre la línea base), accuracy 0.607, ROC-AUC 0.776. Acierta el 63 % de los casos de riesgo alto (44 de 70, recall 0.629).
Variables con mayor peso: `materia_reprobada` (1.04) y algunas carreras (Administración de Empresas, Ing. de Sistemas Informáticos, Mercadeo Internacional). Los coeficientes miden asociación, **no causalidad**.

### 7.5 Recomendaciones priorizadas

| Prioridad | Acción | Plazo | Responsable |
|-----------|--------|-------|-------------|
| Alta | Asignar intervención a los **69 estudiantes** en riesgo alto sin ninguna | 4 semanas | Coordinadores de carrera y Bienestar Estudiantil |
| Media | Validar con más ciclos el mayor riesgo en San Miguel Centro, San Salvador Sur y Usulután Este | Próximo ciclo | Coordinadores y analista de datos |
| Baja | Monitorear el riesgo alto por facultad, empezando por Humanidades e Ingeniería y Arquitectura | Cada ciclo | Analista de datos institucional |

**Meta:** 100 % del riesgo alto con intervención (hoy 85.2 %; 14.8 % sin intervención registrada).

---

## 8. Gobernanza y ética

Los datos se tratan con **protección desde el diseño**:

| Pilar | Medida |
|-------|--------|
| **Minimización** | Vista protegida con 9 de las 43 columnas: sin notas ni datos socioeconómicos |
| **Privacidad** | Carné → token SHA-256 · teléfono `****9617` · correo `r***@ues.edu.sv` · edad en rangos |
| **Acceso** | Administrador: datos crudos · Analista: solo vista protegida · Auditor: solo evidencias |
| **Retención y ética** | Datos eliminados al cerrar el ciclo · sin conclusiones fuera de contexto |

**Clasificación de los 43 campos:** Confidencial 15 · Privado 14 · Restringido 12 · Público 2 — cada campo con finalidad y control.

**Matriz de riesgos** (puntaje = probabilidad × impacto, escala 1–5):

| Riesgo | Prob. | Impacto | Puntaje | Nivel | Control |
|--------|:----:|:------:|:------:|-------|---------|
| Exposición de identificadores directos | 4 | 5 | 20 | Crítico | Eliminar o enmascarar campos |
| Acceso excesivo al dataset crudo | 3 | 5 | 15 | Alto | Mínimo privilegio |
| Reidentificación por combinación de atributos | 3 | 4 | 12 | Alto | Generalizar y revisar granularidad |
| Conclusiones sesgadas por segmentación | 3 | 4 | 12 | Alto | Revisión ética |
| Uso del dato para finalidad no definida | 2 | 5 | 10 | Alto | Registrar propósito |
| Retención indefinida de copias | 3 | 3 | 9 | Moderado | Regla de retención |

Documentos de soporte: `documentos/politica_gobernanza_guia04.md`, `matriz_riesgos_guia04.xlsx` y `clasificacion_datos_guia04.xlsx`.

---

## 9. Limitaciones

1. **Datos sintéticos.** Sirven para demostrar el proceso, no para decidir; los grupos son pequeños y las diferencias no son concluyentes. Además, el diseño original contempla ≈ 5,000 estudiantes, pero el prototipo trabaja con una muestra de 1,000.
2. **Optimización sin mejora.** Se intentó optimizar el modelo (Guía 10, `GridSearchCV`), pero el desempeño fue inferior al de la configuración inicial.
3. **Fuga de información (*data leakage*).** El riesgo se define con la nota final y la probabilidad de deserción, y el modelo incluye las notas. Sin ellas, el modelo **no supera la línea base** (F1 macro 0.31 frente a 0.212 según la defensa final). Para una alerta temprana real se requieren predictores previos a la evaluación.
4. **Seguimiento limitado.** Solo se registra la última intervención (157 estudiantes sin fecha), por lo que no se mide la evolución del riesgo en el tiempo.
5. **Análisis territorial preliminar.** Validar con más ciclos antes de priorizar zonas.

> **Conclusión:** el proyecto es una base para una alerta temprana, siempre que se valide con datos reales y predictores previos a la evaluación.

---

## 10. Contacto

**Equipo — ITCA-FEPADE · Módulo COIDS1604**

| Integrante |Guías a cargo | Contacto |
|------------|---------------------|----------|
| Luis Alonso Hernández Mundo |--> Guías 1, 8 | <!-- TODO: luis.hernandez25@itca.edu.sv 
| Isela Alexandra Quintanilla Cruz |--> Guía 2,9 | <!-- TODO: isela.quintanilla25@itca.edu.sv
| Gabriela Michelle García Pérez |--> Guía 3,7 | <!-- TODO: gabriela.garcia25@itca.edu.sv
| Glenda Beatriz Cruz García |--> Guía 4 | <!-- TODO: glenda.cruz25@itca.edu.sv
| Emerson Alexander Granadeño Valiente |--> Guia 6| <!-- TODO: emerson.granadeno25@itca.edu.sv
| Cristina Raquel Alfaro Aguilar |--> Guia 5, 10 | <!-- TODO: cristina.alfaro25@itca.edu.sv

**Repositorio:**  https://github.com/isela-q05/ProyectoFinal_EdTech_Learning_Analytics_IDS221.git

---

<sub>Proyecto académico con fines educativos · Datos sintéticos · ITCA-FEPADE</sub>
