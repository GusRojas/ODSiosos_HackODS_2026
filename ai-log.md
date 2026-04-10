# AI Log — Equipo ODSiosos · HackODS 2026

## Herramientas utilizadas

- **Claude Code** (claude.ai/code · modelo claude-sonnet-4-6) — asistente principal para
  ingeniería de datos, visualización y desarrollo del dashboard.
- **GitHub Copilot** — autocompletado puntual en celdas de notebooks durante la exploración
  inicial de los datos crudos de INEGI.

---

## Registro de uso

### 2026-03-24 | Claude Code | Pipeline de ingesta de datos

- **Tarea**: Los 333 indicadores ODS de INEGI llegan en carpetas individuales con dos
  formatos CSV distintos (nivel nacional simple y nivel estatal). Pedimos a Claude Code que
  generara el pipeline de consolidación en `notebook/001_dataWrangling.ipynb`.
- **Prompt**: *"Tenemos 333 carpetas de indicadores ODS descargadas de INEGI. Cada carpeta
  contiene un CSV con la serie de tiempo y un TXT de metadatos. Hay dos formatos: nivel
  nacional (columnas fijas) y nivel estatal (hay que filtrar cvegeo == '00'). Genera el
  notebook de ingesta que consolide todo en tres archivos: tabla pivote, formato largo
  y resumen de metadatos."*
- **Resultado**: Generó el notebook con lectura recursiva de carpetas, detección automática
  del formato CSV, filtro por `cvegeo == '00'` para datos estatales y exportación de los
  tres archivos a `data/consolidated/`.
- **Modificación del equipo**: Ajustamos el manejo de encodings (algunos TXT de INEGI
  llegan en latin-1) y añadimos validación manual de los 41 indicadores que no cargaron,
  verificando caso por caso si el problema era de formato o de datos ausentes.
- **Decisión**: Se aceptó la estructura del pipeline. El equipo revisó celda por celda para
  asegurarse de que la lógica de detección de formato era robusta antes de correrla sobre
  las 333 carpetas.

---

### 2026-03-24 | Claude Code | Elección de método de correlación

- **Tarea**: Necesitábamos decidir qué método de correlación usar para cruzar los 292
  indicadores con datos nacionales. Los indicadores tienen escalas muy distintas (porcentajes,
  tasas por 100k, toneladas, índices) y la mayoría tiene datos dispersos (no todos los años).
- **Prompt**: *"Tenemos 292 series de tiempo de indicadores ODS México. Las series son cortas
  e irregulares (algunas tienen 3 puntos, otras 30), con escalas heterogéneas. ¿Qué método
  de correlación es más apropiado y por qué?"*
- **Resultado**: Claude Code recomendó correlaciones de Spearman por su robustez ante
  relaciones no lineales, invarianza a la escala y tolerancia a empates. Generó el código
  para calcular las correlaciones por pares sobre los años en común, con umbral |r| ≥ 0.7
  para identificar pares significativos.
- **Modificación del equipo**: Ninguna en el método. El equipo revisó la matriz resultante
  y discutió qué pares tenían sentido causal vs. cuáles eran correlaciones espurias por
  tendencias temporales compartidas (p.ej. dos indicadores que simplemente crecen con el
  tiempo). Esa discusión fue enteramente del equipo.
- **Decisión**: Se aceptó Spearman. La decisión sobre qué correlaciones eran *narrativamente
  relevantes* (es decir, cuáles tenían mecanismo causal explicable) la tomó el equipo, no
  la IA.

---

### 2026-03-28 | Claude Code | Identificación de clústeres de ODS problemáticos

- **Tarea**: Con la matriz de correlaciones calculada, el equipo tenía 17 ODS y cientos de
  pares significativos. Pedimos ayuda para visualizar qué ODS estaban más interconectados,
  como punto de partida para la discusión del equipo.
- **Prompt**: *"Con el grafo de correlaciones Spearman entre los 17 ODS de México, genera
  una visualización de red donde el grosor de arista sea proporcional al número de pares
  con |r| ≥ 0.7 y el color indique si la correlación promedio es positiva (sinergia) o
  negativa (trade-off). Necesitamos ver qué ODS actúan como nodos centrales."*
- **Resultado**: Generó el grafo con `networkx` + Plotly, mostrando que ODS 3 (Salud),
  ODS 6 (Agua) y ODS 8 (Trabajo) concentraban el mayor peso de conexiones, y que
  ODS 12 (Consumo) tenía el trade-off más fuerte con agua y salud.
- **Modificación del equipo**: El equipo revisó el grafo y debatió durante la sesión qué
  agrupaciones tenían sentido. La red confirmó tres clústeres que el equipo ya sospechaba
  desde el análisis exploratorio, pero reveló uno inesperado: la desconexión estadística
  del ODS 14 (Vida Submarina), que aparecía como nodo aislado por falta de datos.
- **Decisión**: A partir de este análisis visual, el equipo definió las **5 problemáticas
  del proyecto** de forma autónoma: (1) Colapso sanitario, (2) Trilema consumo-agua-salud,
  (3) Desigualdad no medida, (4) Rezago educativo-institucional y (5) Vacío de monitoreo.
  Esa agrupación y su narrativa son decisiones propias del equipo, no de la IA.

---

### 2026-03-28 | Claude Code | Narrativa y selección de indicadores por problemática

- **Tarea**: Para cada una de las 5 problemáticas, el equipo había elegido los ODS
  relevantes. Pedimos a Claude Code que identificara los indicadores con datos suficientes
  (≥ 5 observaciones) dentro de cada grupo de ODS y que propusiera qué visualizaciones
  serían más claras para comunicar el problema a un público no técnico.
- **Prompt**: *"Para el problema 2 (ODS 6, 8, 12), identifica los indicadores con más de
  5 observaciones nacionales. Propón las visualizaciones más efectivas para mostrar que el
  crecimiento del consumo material destruye el acceso al agua, y que eso tiene consecuencia
  en salud. Los gráficos deben ser para un dashboard interactivo en Quarto."*
- **Resultado**: Identificó 18 indicadores con datos suficientes y propuso: scatter de
  correlación consumo vs. agua segura, serie de tiempo del consumo material con proyección,
  panel triple del agua (acceso, estrés hídrico, residuales), diagrama Sankey del ciclo
  causal.
- **Modificación del equipo**: El equipo seleccionó de esa lista las visualizaciones que
  mejor apoyaban la narrativa que ya habíamos construido. Eliminamos un scatter redundante
  y añadimos el gráfico de trabajo informal porque conectaba el ciclo con la exclusión
  social — esa conexión fue identificada por el equipo en la discusión, no por la IA.
- **Decisión**: La narrativa escrita en cada sección del dashboard ("El consumo que explota",
  "El agua que retrocede", "El trabajo informal: el eslabón invisible") es íntegramente del
  equipo. Claude Code generó código, no texto interpretativo.

---

### 2026-04-07 | Claude Code | Construcción del dashboard en Quarto (formato y patrón)

- **Tarea**: Queríamos migrar los notebooks de visualización a un dashboard interactivo
  multi-página en Quarto. El equipo no tenía experiencia previa con `format: dashboard`
  de Quarto y necesitaba establecer el patrón correcto de separación entre celdas de
  setup y celdas de display.
- **Prompt**: *"Tengo notebooks de Plotly con figuras interactivas. Necesito adaptarlos a
  un Quarto Dashboard multi-página con tema oscuro, scrolling por sección, y que las figuras
  no se rendericen varias veces. ¿Cuál es el patrón correcto?"*
- **Resultado**: Estableció el patrón fundamental: (1) una sola celda de setup con
  `#| output: false` donde se crean TODAS las figuras, (2) celdas de display que contienen
  únicamente `fig_xxx.show()`. También configuró el tema oscuro (`DARK_LAYOUT`), la
  estructura de rows con alturas explícitas y el sistema de value boxes.
- **Modificación del equipo**: El equipo definió la paleta de colores por sección
  (gradientes por problemática), el orden de las secciones y el contenido de los
  value boxes. También tuvimos que corregir a Claude Code cuando en un intento olvidó el
  patrón y mezcló creación y display en la misma celda, identificamos el error y lo corregimos.
  Descubrimos que para la herramienta IA, Quarto es nuevo y puede tener varios errores. 
- **Decisión**: El diseño visual (gradientes, tipografía, hero por sección) fue decisión
  del equipo. El patrón técnico de Quarto fue generado por Claude Code y validado por
  el equipo antes de aplicarlo al archivo principal.

---

### 2026-04-09 | Claude Code | Adaptación de notebooks de problemas al dashboard

- **Tarea**: Adaptar los notebooks `007_problema3_desigualdad`, `008_problema4_educacion`
  y `009_problema5_monitoreo` al formato del dashboard, manteniendo coherencia visual con
  las secciones ya construidas (Salud y Trilema).
- **Prompt (ejemplo para Desigualdad)**: *"Adapta el notebook 007_problema3_desigualdad
  al index.qmd del dashboard. Sigue el patrón ya establecido: todas las figuras en una
  celda de setup con output: false, luego celdas de display. Mantén la coherencia con
  los dashboards de Salud y Trilema (hero, value boxes, KPI gauges, narrativa + tabset).
  Sufijo de variables _d para evitar conflictos."*
- **Resultado**: Generó las tres secciones completas con el patrón correcto, respetando
  la estructura de rows, los value boxes temáticos y los sufijos de variables aislados
  por sección (`_d`, `_e`, `_m`).
- **Modificación del equipo**: Para la sección de Desigualdad, el equipo decidió reemplazar
  un panel de tipo `go.Indicator` que Claude Code había incluido en `fig_ing_d` por una
  barra agrupada de ingreso real vs. meta — consideramos que el indicador numérico aislado
  no comunicaba la brecha tan claramente como la comparación visual. Para Monitoreo, el
  equipo identificó que el radar de los 17 ODS debía ser una figura independiente (no
  subplot) y lo especificamos explícitamente.
- **Decisión**: El equipo validó cada figura contra la narrativa antes de aceptarla.
  Las figuras que generaban ruido visual sin aportar a la historia del problema fueron
  reemplazadas o eliminadas.

---

### 2026-04-09 | Claude Code | Ajuste de espaciado en gráficas de radar (Desigualdad)

- **Tarea**: Los títulos de los subplots polares (`fig_radar_d`) quedaban superpuestos
  con los propios gráficos de radar, haciéndolos ilegibles.
- **Prompt**: *"De las gráficas de radar fig_radar_d, los subplot_titles están muy cerca
  de los gráficos, dale espacio para mejorar la presentación."*
- **Resultado**: Aplicó `fig_radar_d.update_annotations(yshift=25)` para subir los
  títulos, aumentó `height=600` y el margen superior a `t=100`.
- **Modificación del equipo**: Ninguna, la solución fue directa y el resultado visual
  fue el esperado.
- **Decisión**: Problema técnico puntual resuelto sin modificación.

---

### 2026-04-09 | Claude Code | Citas de fuente en todas las figuras

- **Tarea**: El equipo identificó la necesidad de citar las fuentes de datos en cada
  figura del dashboard para cumplir con buenas prácticas de transparencia y los requisitos
  del concurso.
- **Prompt**: *"Me parecería buena práctica citar fuentes en cada una de las gráficas en
  todos los dashboards. Agrega la cita del origen de los datos graficados y analizados."*
- **Resultado**: Definió una función helper `_add_source(fig, extra, y)` en el setup
  global y añadió llamadas a las ~40 figuras del dashboard, diferenciando las fuentes
  específicas por indicador: ENSANUT, CONEVAL, ENIGH, ENADIS, ENVIPE, ENCIG, OCDE, FAO,
  UNICEF/WHO JME, CONANP, CONAHCYT, entre otras.
- **Modificación del equipo**: Revisamos que cada fuente citada correspondiera
  efectivamente al instrumento estadístico de origen del indicador. Corregimos la cita del
  indicador de stunting para referenciar UNICEF/WHO JME 2023 (no solo INEGI) dado que las
  cifras de comparación internacional provienen de esa fuente.
- **Decisión**: El equipo tomó la decisión de citar fuentes como práctica de rigor
  académico. Claude Code identificó los instrumentos estadísticos de cada indicador;
  el equipo validó la correspondencia.

---

## Decisiones propias del equipo (transversales)

1. **Elección de las 5 problemáticas**: La agrupación de ODS en 5 problemas sistémicos
   fue una decisión analítica del equipo tras revisar la matriz de correlaciones. La IA
   generó las correlaciones y el grafo; el equipo interpretó cuáles tenían mecanismo causal
   explicable y valor narrativo.

2. **Narrativa textual**: Todos los textos de las secciones del dashboard ("El hambre en
   cámara lenta", "18 años sin moverse", "El sistema no colapsó de golpe") fueron escritos
   por el equipo. Claude Code no produjo ningún texto narrativo interpretativo.

3. **Selección de indicadores a graficar**: De los ~292 indicadores con datos, el equipo
   eligió manualmente cuáles incluir en cada sección en función de su relevancia para la
   historia del problema, no en función de disponibilidad de datos.

4. **Rechazos de output de IA**: En múltiples ocasiones el equipo rechazó o reemplazó
   gráficas generadas que eran técnicamente correctas pero no aportaban a la narrativa
   (paneles redundantes, indicadores sin contexto, comparaciones sin mecanismo causal).

5. **Conclusiones y mensajes clave**: Los mensajes de síntesis al final de cada sección
   (blockquotes) son reflexiones propias del equipo sobre lo que los datos revelan para
   México en 2026.
