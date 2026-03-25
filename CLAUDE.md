# CLAUDE.md

Este archivo proporciona orientación a Claude Code (claude.ai/code) al trabajar con el código de este repositorio.

## Descripción del proyecto

Proyecto de ciencia de datos para HackODS 2026 que analiza los **333 indicadores ODS (Objetivos de Desarrollo Sostenible) de México** para identificar problemáticas interconectadas entre los 17 objetivos. El análisis cubre series de tiempo a nivel nacional de 1970 a 2026.

## Comandos

El proyecto usa [uv](https://docs.astral.sh/uv/) para gestión de dependencias (Python 3.13).

```bash
# Instalar dependencias
uv sync

# Ejecutar el script principal
uv run python main.py

# Lanzar JupyterLab para trabajar con los notebooks
uv run jupyter lab

# Lanzar el Notebook clásico
uv run jupyter notebook
```

## Arquitectura

### Pipeline de datos (flujo de dos notebooks)

**`notebook/001_dataWrangling.ipynb`** — Ingesta y consolidación de datos:
- Lee las carpetas crudas de indicadores en `data/` (una carpeta por indicador, nombradas como `1.1.1_es`)
- Cada carpeta contiene: `conjunto_de_datos/*_sh_es.csv` (serie de tiempo), `metadatos/*_met_es.txt` (metadatos), `catalogos/claves_codigos_indicadores_es.csv` (catálogo)
- Maneja dos formatos CSV: nivel nacional (simple) y nivel estatal (filtra `cvegeo == '00'` para el agregado nacional)
- Genera tres archivos consolidados en `data/consolidated/`:
  - `indicadores_nacionales.csv` — tabla pivote (años × 292 indicadores)
  - `indicadores_largo.csv` — formato largo con metadatos ODS
  - `metadatos_indicadores.csv` — resumen de metadatos de indicadores

**`notebook/002_correlationAnalysis.ipynb`** — Análisis de correlación:
- Carga los datos consolidados desde `data/consolidated/`
- Calcula **correlaciones de Spearman** (elegidas por su robustez ante relaciones no lineales y distintas escalas)
- Genera: heatmaps por ODS, scatter plots de los pares más relevantes, grafo de red de conexiones entre ODS
- Exporta `data/consolidated/correlaciones_altas.csv` y `pares_ods_correlaciones.csv`

### Estructura de los datos

Cada carpeta de indicador (p. ej. `data/1.1.1_es/`) sigue el formato de datos ODS del INEGI:
- `conjunto_de_datos/` — CSV con la serie de tiempo (`*_sh_es.csv`)
- `metadatos/` — archivo de texto con título, unidad, objetivo, meta y tipo (`*_met_es.txt`)
- `catalogos/` — mapeo de código de indicador a descripción

Los IDs de indicadores siguen la numeración ODS (p. ej. `1.1.1`, `3N.2.1`, `16R.3A`). El sufijo `N` indica indicadores adaptados a nivel nacional; `R` indica reformulados.

### Datos clave
- 333 carpetas de indicadores crudos; 292 cargan datos nacionales exitosamente
- Cobertura de 52 años (1970–2026), aunque la mayoría de indicadores tiene datos dispersos (solo 90 tienen ≥15 observaciones)
- ODS 3 (Salud) tiene el mayor número de indicadores (42); ODS 7 (Energía) tiene el menor (6)
