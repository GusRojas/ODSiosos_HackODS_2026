# México y los Objetivos de Desarrollo Sostenible: Una Deuda Sistémica


<p align="left">
  <img src="assets/ods.png" width="45%">
  <a href="https://www.youtube.com/watch?v=MCKH5xk8X-g">
    <img src="https://img.youtube.com/vi/MCKH5xk8X-g/0.jpg" width="45%">
  </a>
</p>


<p align="justify">Los Objetivos de Desarrollo Sostenible son el plan maestro para conseguir un futuro sostenible para todos. 
Se interrelacionan entre sí e incorporan los desafíos globales a los que nos enfrentamos día a día, 
como la pobreza, la desigualdad, el clima, la degradación ambiental, la prosperidad, la paz y la justicia. 
Para no dejar a nadie atrás, es importante que logremos cumplir con cada uno de estos objetivos para 2030 (ONU, 2026).</p>

<br>

## Objetivos de Desarrollo Sostenible y realidad mexicana: un análisis desde la evidencia
<p align="justify">Este proyecto tuvo como objetivo analizar datos relacionados con los Objetivos de Desarrollo Sostenible (ODS) para identificar relaciones relevantes entre distintos indicadores y problemáticas en México. A través del uso de técnicas de análisis de datos, como el cálculo de correlaciones entre indicadores, se busca explorar cómo diferentes dimensiones sociales, económicas y ambientales se interrelacionan. El proyecto integra procesamiento de datos, análisis en Jupyter Notebook y desarrollo de un dashboard, con el fin de generar herramientas que permitan comprender mejor los desafíos asociados a los ODS.</p>

<p align="justify">El propósito es comprender las interrelaciones existentes entre distintas problemáticas a nivel social, económico y ambiental. A partir de este análisis general, se identificaron patrones y vínculos relevantes entre indicadores, lo que permitió focalizar el estudio en áreas específicas de mayor impacto. En particular, se profundiza en los ODS relacionados con la salud y la pobreza, debido a su alta relevancia en el contexto actual y a su estrecha conexión con otras dimensiones del desarrollo sostenible, lo que los convierte en ejes clave para la interpretación y construcción de la narrativa del proyecto.</p>

<p align="justify">Los 333 indicadores ODS de México (INEGI, 1970–2026) revelan cinco problemáticas interconectadas que no pueden entenderse por separado: cada una alimenta a las demás en un ciclo que el dato aislado no logra mostrar.

El sistema no colapsó de golpe. Colapsó en silencio, indicador por indicador, año tras año. </p>

| # | Problemática | ODS | Mecanismo de contagio |
| :--- | :--- | :--- | :--- |
| **01** | Colapso sanitario | ODS 3 | Retracción presupuestal + epidemiología ambiental |
| **02** | Trilema consumo-agua-salud | ODS 6, 8, 12 | Crecimiento que destruye recursos vitales |
| **03** | Desigualdad no medida | ODS 10, 1, 5 | Sistema estadístico que invisibiliza brechas |
| **04** | Rezago educativo-institucional | ODS 4, 16 | Violencia y corrupción como techos estructurales |
| **05** | Vacío de monitoreo | ODS 2, 14 | Decisiones políticas sobre qué registrar |

<br>

## Decisiones metodológicas

### ¿Por qué correlación de Spearman y no Pearson?

Los 333 indicadores ODS cubren dimensiones muy distintas (pobreza, salud, medio ambiente, instituciones) con escalas, unidades y distribuciones heterogéneas. Pearson asume linealidad y sensibilidad a valores atípicos, condiciones que rara vez se cumplen en series socioeconómicas de largo plazo. Spearman opera sobre rangos, lo que lo hace robusto ante relaciones no lineales, series con datos dispersos y diferencias de escala entre indicadores. Dado que el objetivo era detectar *si dos indicadores se mueven juntos*, no *cuánto*, Spearman es la elección metodológicamente más honesta para este conjunto de datos.

### ¿Por qué estos 5 problemas y no otros?

A partir de las 3,758 correlaciones significativas (|r| ≥ 0.7, p < 0.05) se construyó un grafo de red entre los 17 ODS. Los 5 problemas seleccionados responden a tres criterios aplicados simultáneamente:

1. **Centralidad en la red**: ODS con mayor número de conexiones fuertes con otros objetivos (ODS 3, 6, 8).
2. **Magnitud del deterioro**: indicadores con tendencias negativas sostenidas en la serie de tiempo disponible.
3. **Vacíos de monitoreo**: ODS con cobertura de datos críticamente baja, donde la ausencia de medición es en sí misma una señal de política pública (ODS 2, 14).

Los problemas que quedaron fuera (p. ej. cambio climático — ODS 13, o biodiversidad — ODS 15) tienen pocas series con datos suficientes para análisis estadístico robusto, por lo que incluirlos habría comprometido la integridad del argumento.

### Audiencia objetivo

Este análisis está dirigido a dos audiencias:

- **Academia e investigación**: equipos de ciencias sociales, salud pública y ciencias de datos interesados en el uso de correlaciones multidimensionales para diagnóstico sistémico de indicadores de desarrollo.
- **Ciudadanía informada y sociedad civil**: personas y organizaciones que buscan evidencia accesible sobre la interrelación de problemas estructurales en México, más allá de los indicadores aislados que suelen reportarse en medios.

<br>

## Conocenos
<p align="justify">Preocupados y ODSiosos por la creciente complejidad de los desafíos sociales y su impacto en la calidad de vida de la población, este proyecto busca aportar una perspectiva basada en datos que permita comprender mejor la interrelación entre distintos factores asociados a los ODS. A través de un enfoque analítico y visual, se pretende no solo evidenciar problemáticas, sino también contribuir a la reflexión y al desarrollo de estrategias que favorezcan un futuro más equitativo y sostenible.</p>

<br>
<p align="right"> ODSiosos
  <br> Metztli Donaji Pablo Aparicio <br> Estudiante de Maestría en Ciencias de Computación por el CIC - IPN
  <br>Gustavo Mandujano Rojas <br> Estudiante de Maestría en Ingeniería de Cómputo, del Lab RyM, por el CIC - IPN
  <br>Rubén Oropeza Sánchez <br> Estudiante de Doctorado en Genética y Biología Molecular por el CINVESTAV
</p>



<br>

## Fuentes de datos

Los datos utilizados en este proyecto son públicos y abiertos, provistos por instituciones oficiales:

| Fuente | Descripción | Fecha de descarga | Licencia | Enlace |
| :--- | :--- | :---: | :---: | :--- |
| **INEGI / Agenda 2030 México** | 333 indicadores ODS de México — series de tiempo nacionales y estatales (1970–2026) | 2026-03-24 | [Libre uso MX](https://datos.gob.mx/libreusomx) | [agenda2030.mx — Indicadores ODS](https://agenda2030.mx/ODSopc.html?ti=T&goal=0&lang=es#/ind) |
| **ONU / IAEG-SDGs** | Marco global de indicadores ODS — metadata y definiciones oficiales | — | Uso libre con atribución | [unstats.un.org/sdgs/indicators](https://unstats.un.org/sdgs/indicators/indicators-list/) |

### Descripción de variables principales

Cada indicador descargado del portal Agenda 2030 México incluye tres archivos:

| Archivo | Contenido |
| :--- | :--- |
| `conjunto_de_datos/*_sh_es.csv` | Serie de tiempo del indicador. Columnas principales: `anio` (año de observación), `valor` (valor numérico del indicador), `cvegeo` (clave geográfica; `'00'` = nivel nacional) |
| `metadatos/*_met_es.txt` | Ficha descriptiva: nombre del indicador, unidad de medida, ODS al que pertenece, meta específica y tipo de indicador (global, nacional adaptado `N`, o reformulado `R`) |
| `catalogos/claves_codigos_indicadores_es.csv` | Catálogo de códigos: mapeo entre el identificador ODS (p. ej. `3.B.1`) y su descripción textual |

Los identificadores de indicador siguen la numeración oficial ODS (p. ej. `1.1.1`, `3N.2.1`, `16R.3A`). El sufijo `N` indica indicadores adaptados a nivel nacional; `R` indica indicadores reformulados por México.

<br>

Este trabajo está bajo una Licencia Creative Commons Atribución-CompartirIgual 4.0 Internacional.






