# Inventario de cuadernos — qué descargar y qué dejar fuera

Revisión de septiembre de 2026. Esta es la lista completa y cerrada: si un cuaderno no
aparece aquí, no forma parte de la cadena.

---

## Los doce archivos de la carpeta

Descarga estos y **solo** estos. Los ocho con sufijo `_REVISADO` sustituyen a los tuyos; los
tres sin sufijo son nuevos.

| # | Archivo | Qué es | ¿Reejecutar? |
|---|---|---|---|
| 1 | `00_descarga_nasa_power_REVISADO.ipynb` | Descarga NASA POWER por punto | **No.** El CSV que tienes sirve |
| 2 | `01_eda_estacion_cesmag_REVISADO.ipynb` | EDA de la estación, datos crudos | Sí, es barato |
| 3 | `02_preprocesamiento_REVISADO.ipynb` | Imputación + índice de claridad corregido | **Sí**, Fase 3 |
| 4 | `03_eda_post_preprocesamiento_REVISADO.ipynb` | EDA de los tres conjuntos imputados | Sí, **después** del 02 |
| 5 | `04_feature_engineering_REVISADO.ipynb` | Variables + partición con embargo | **Sí**, Fase 3 |
| 6 | `05_modelo_informer_entrenamiento_REVISADO.ipynb` | Hiperparámetros | **Sí**, Fase 5 |
| 7 | `06_evaluacion_comparacion_variantes_REVISADO.ipynb` | Entrena los 9 modelos y evalúa | **Sí**, Fases 5 y 6 |
| 8 | `07_diagnostico_modelo_REVISADO.ipynb` | Residuales, sesgo, alta frecuencia | **Sí**, Fase 6 |
| 9 | `08_analisis_espacial_grillas_nasa.ipynb` | **Nuevo.** Mapas de grillas | Sí, Fase 1 |
| 10 | `09_analisis_series_diurno_e_imputacion.ipynb` | **Nuevo.** Franja diurna, ACF, validación de imputación | Sí, Fase 2 |
| 11 | `10_asociacion_particion_ventanas.ipynb` | **Nuevo.** Asociación, partición, ventanas | Sí, Fase 4 |
| 12 | `estilo_tg.py` | Módulo de estilo de figuras. **Ningún cuaderno lo importa** (todos llevan la celda de estilo dentro): es la herramienta para regenerar figuras antiguas con la misma paleta en la Fase 7 | — |

Además va **`verificar_carpeta.ipynb`**, que comprueba que la carpeta esté completa (ver más
abajo). Es un cuaderno, no un script: el `.py` fallaba al ejecutarlo dentro de Colab.

---

## Lo que hay que sacar de la carpeta

Estos ocho son tus versiones anteriores. **Muévelos a una subcarpeta `_anterior/`** en lugar de
borrarlos: si algo sale mal a mitad de camino, quieres poder comparar.

```
00_descarga_nasa_power_v2.ipynb
01_eda_estacion_cesmag.ipynb
02_preprocesamiento.ipynb
03_eda_post_preprocesamiento.ipynb
04_feature_engineering.ipynb
05_modelo_informer_entrenamiento.ipynb
06_evaluacion_comparacion_variantes.ipynb
07_diagnostico_modelo.ipynb
```

El riesgo real de dejarlos al lado es que Colab abra el que no es: los nombres se parecen y el
buscador de archivos los ordena juntos. Una subcarpeta lo resuelve.

---

## El que puedes descartar de la cadena

`00_diagnostico_rutas.ipynb` no es un paso del trabajo: son siete celdas que buscan dónde quedó
el CSV en tu Drive. Es útil como herramienta y no tiene nada que corregir, pero comparte
numeración con el cuaderno de descarga y eso confunde.

**Recomendación:** guárdalo aparte, renombrado como `utilidad_diagnostico_rutas.ipynb`, fuera de
la carpeta numerada. No lo cites en el documento: no produce ningún resultado del trabajo.

Es el único que sobra. Los otros tres que preguntabas —el 00 de descarga, el 01 y el 03— sí
tienen contenido que va al documento y los tres se quedan.

---

## Qué encontré al revisarlos

Los tres tenían algo, y no era menor en dos de ellos.

### `00_descarga_nasa_power_v2` — una definición duplicada

Calculaba el índice de claridad **con el mismo defecto que el cuaderno 02**: asignaba cero
cuando el denominador valía cero. La columna resultante viajaba en el CSV, pero el cuaderno 02
la recalculaba encima, así que ningún resultado publicado dependía de ella. Aun así, había dos
definiciones de la misma variable en dos sitios: la retiré de aquí y la dejé solo en el 02.

Por eso **no hay que volver a descargar nada**. El CSV que tienes está bien.

Le añadí además una nota que documenta que la consulta es por punto y **no interpolada al punto**
—el servicio devuelve el valor de la celda que contiene esas coordenadas—, que es justo lo que el
documento afirmaba al revés. Y dejé etiquetada la celda suelta del final: la comprobación de que
la precipitación diaria es la suma de las horarias, no su media. Esa comprobación respalda la
unidad de la variable en la tabla de datos, así que vale la pena conservarla explicada.

### `01_eda_estacion_cesmag` — dos definiciones de «diurno»

Llamaba diurnos a los registros con irradiancia positiva. Los cuadernos 06, 07, 09 y 10 la
definen por el reloj, de 6:00 a 18:55. **No son lo mismo**: el criterio de irradiancia positiva
excluye los instantes diurnos realmente oscuros, que son los más difíciles de pronosticar, y por
eso da una media diurna más alta. Si el documento cita la media diurna de este cuaderno junto a
las métricas diurnas del 06, está mezclando dos definiciones y los números no se pueden comparar.

Ahora reporta las dos, y la principal es la del reloj.

También marqué la asimetría y la curtosis: se siguen calculando y se guardan en la tabla, porque
en un anexo tienen sentido, pero el cuaderno avisa por pantalla de que no van al cuerpo. La
directora pidió retirarlas.

### `03_eda_post_preprocesamiento` — decidía algo que ya no le corresponde

Este era el importante. Concluía que las tres técnicas de imputación son «estadísticamente
equivalentes» y recomendaba la interpolación **por ser la de menor suposición estructural**. Ese
argumento dice que la técnica no estropea el conjunto; no dice que acierte. Y es exactamente el
razonamiento que la directora pidió justificar.

Quien lo justifica ahora es el experimento de ocultamiento del cuaderno 09. Así que el 03 lee el
resultado de ese experimento y lo reporta, y sus criterios distribucionales pasan a ser lo que
siempre fueron: una condición necesaria, no un criterio de selección. Si ejecutas el 03 sin haber
corrido el 09, te avisa por pantalla en lugar de inventarse una recomendación.

Le añadí también Spearman junto a Pearson en la tabla de correlaciones, con una advertencia: esas
correlaciones están calculadas sobre los niveles, y sobre los niveles casi cualquier variable con
ciclo diurno correlaciona alto con la irradiancia, porque las dos siguen al Sol. La versión que
va al documento es la del cuaderno 10, sobre anomalías y con el tamaño de muestra corregido. Sin
esa advertencia, las dos tablas se citarían como si midieran lo mismo.

El índice de claridad de este cuaderno no hubo que tocarlo: lee la columna del `dataset_final`, y
el 02 revisado ya la produce corregida. Basta con ejecutarlo después.

---

## Orden de ejecución

```
00  (no ejecutar: el CSV ya está)
      |
01 ---+--> 02 ---> 03
      |     |
      |     +----> 04 ---> 05 ---> 06 ---> 07
      |
      +--> 08     (independiente, puede ir primero)
      +--> 09     (ANTES del 02: decide la técnica de imputación)
            |
            +----> 10   (después del 04, lee su partición)
```

Dos reglas que no se pueden saltar:

1. **El 09 va antes que el 02.** Si la validación por ocultamiento gana una técnica distinta de
   la interpolación, cambia el archivo que alimenta al 04 y a todo lo demás.
2. **El 03 va después que el 02.** Antes seguirías viendo el índice de claridad antiguo.

---

## Cómo comprobar que la carpeta está bien

Pon los doce archivos en la carpeta junto con `verificar_carpeta.ipynb`, ábrelo en Colab y
ejecuta sus celdas de arriba abajo. Localiza la carpeta solo, buscando
`02_preprocesamiento_REVISADO.ipynb` en tu Drive.

**Por qué es un cuaderno y no un script.** El `.py` leía la ruta de `sys.argv`, y dentro de un
cuaderno `sys.argv` no trae la carpeta sino los argumentos del propio kernel: de ahí el error
`No such file or directory: '-f'`. El `.py` sigue en la entrega, ya corregido, por si alguna vez
lo ejecutas desde una terminal; pero en Colab usa el `.ipynb`.

Comprueba tres cosas y las dice una por una:

- que estén los doce archivos;
- que ninguno tenga el **código** cambiado respecto de lo que te entregué;
- que no se haya colado ninguna de las ocho versiones anteriores, ni ningún `.ipynb` ajeno;
- y, si ya ejecutaste cuadernos, que cada figura esté en los tres formatos.

**Ejecutar un cuaderno no lo marca como cambiado.** La huella se calcula solo sobre el texto de
las celdas: las salidas que Colab guarda dentro del `.ipynb` al ejecutarlo, los números de
ejecución y los metadatos quedan fuera a propósito. Así `CAMBIADO` significa de verdad que
alguien editó el código, que es la única situación en la que quieres enterarte. Los cuadernos
que ya corriste salen `OK` con la marca *ya ejecutado*.

Termina con `RESULTADO: carpeta lista.` o con la lista de lo que falta, y añade un inventario
de todo lo que hay en la carpeta. Vuelve a ejecutarlo justo
antes de la reunión con la directora: es un segundo y te ahorra abrir el cuaderno equivocado
delante de ella.

Las huellas de esta entrega:

| Archivo | Huella |
|---|---|
| `00_descarga_nasa_power_REVISADO.ipynb` | `0e4a641cc7c09536` |
| `01_eda_estacion_cesmag_REVISADO.ipynb` | `6e5c37acf14ca98a` |
| `02_preprocesamiento_REVISADO.ipynb` | `b15266f519508876` |
| `03_eda_post_preprocesamiento_REVISADO.ipynb` | `b7850e5b69d76201` |
| `04_feature_engineering_REVISADO.ipynb` | `6fad9168a1178adf` |
| `05_modelo_informer_entrenamiento_REVISADO.ipynb` | `b7a6863edfd26981` |
| `06_evaluacion_comparacion_variantes_REVISADO.ipynb` | `bb0fe5fb879bd369` |
| `07_diagnostico_modelo_REVISADO.ipynb` | `b950bb0beba0f928` |
| `08_analisis_espacial_grillas_nasa.ipynb` | `33d065b35289e571` |
| `09_analisis_series_diurno_e_imputacion.ipynb` | `89186627871f59eb` |
| `10_asociacion_particion_ventanas.ipynb` | `03d78cb563204e76` |
| `estilo_tg.py` | `6e2050ebc69115d0` |

---

## Ronda de septiembre — estilo común de figuras

Los once cuadernos cambiaron de huella en esta ronda porque todos llevan ahora la misma celda
**«Estilo único de figuras»**, colocada justo después de las importaciones y marcada con
`# @@ESTILO_TG@@`. Esa celda fija:

- **La paleta**, anclada en el azul y en orden fijo: `C_AZUL` `#0C60A3`, `C_AMBAR` `#D4771A`,
  `C_CELESTE` `#269AE6`, `C_TEJA` `#963E35`, `C_VERDEAZ` `#0D8F73`. Verificada contra los cinco
  criterios de accesibilidad de color comparando **todos los pares**, no solo los contiguos.
- **Dos mapas continuos**: `tg_azul` (secuencial, reemplaza a `YlOrRd`) y `tg_div` (divergente,
  reemplaza a `RdBu_r` conservando su dirección de lectura).
- **Los tamaños de letra**: ejes 14 pt, números 12 pt, leyenda 12 pt, mínimo del documento 11 pt.
- **`eje_fechas(ax)`**, que ajusta el eje de fechas al rango real y ya no repite `2023 2023 2023`.
- **`TRAZO`**, un trazo propio por técnica (continuo, guiones, puntos) para que T1, T2 y T3 se
  distingan aunque sus curvas coincidan.

`estilo_tg.py` quedó alineado con esa misma paleta y esos mismos tamaños, para las figuras del
documento que **no** salen de estos cuadernos.

**Si cambias un color, cámbialo en los once cuadernos.** El verificador te dirá que un cuaderno
cambió, pero no puede saber si el cambio está en todos.

### Segunda corrección de esta ronda: una sola carpeta

Los cuadernos volvieron a cambiar de huella porque todos llevan ahora la celda
**«Dónde vive el proyecto»**, marcada con `# @@RUTAS_TG@@`. Antes cada uno buscaba un archivo de
datos por todo el Drive y se quedaba con la primera coincidencia; como hay una copia del proyecto
del semestre anterior, a veces ganaba la vieja y las figuras se escribían allí.

Ahora la carpeta se decide por una línea explícita, `CARPETA_PROYECTO = 'Proyecto Aplicado III'`,
y si hay cero o más de una candidata el cuaderno se detiene y las lista. Todo lo que se escribe
—`datos/`, `datos/arrays/`, `modelos/`, `resultados/`, `figuras/`, `figuras_SVG/`, `figuras_PDF/`—
cuelga de esa carpeta. Los archivos de entrada que sigan en la copia vieja se leen de allí con un
aviso visible que dice a dónde copiarlos.

`verificar_carpeta.ipynb` usa el mismo criterio y trae una celda nueva que busca carpetas de
salida fuera del proyecto.

**Si mueves el proyecto, cambia `CARPETA_PROYECTO` en los once cuadernos y en el verificador.**

### Tercera corrección: el 08 ya no depende del 02

El cuaderno 08 leía `dataset_final_interpolacion.csv`, que produce el 02, y por eso fallaba al
ejecutarlo en la Fase 1 como indica el plan. Ahora usa la **medición real** del CSV de la
estación con la limpieza física mínima (negativos a cero, luz nocturna a faltante, noche a cero)
y descarta los días con menos del 80 % de cobertura. Además de resolver el orden, es
metodológicamente mejor: compara el satélite contra la medición, no contra una serie imputada.

El orden documentado también estaba mal para el 09, que **sí** necesita los tres conjuntos del
02: en el paso a paso, el 02 pasó a ser el Paso 2.1 y el 09 el Paso 2.2.

Y la `fig_01d_perfil_por_anio` dejó de ser catorce curvas superpuestas: ahora es un mapa de calor
año × hora más un panel con la mediana, su banda P10–P90 y los dos años que más se apartan.

### Cuarta corrección: la comparación del perfil mensual y los mapas

- **`fig_02c` (cuaderno 02) y panel mensual de `fig_03c` (cuaderno 03).** Comparaban la media
  mensual del original —calculada solo sobre sus válidos— contra la de los conjuntos imputados
  —calculada sobre todos los instantes—. Con 31 % de la franja diurna ausente, en la media del
  original los ceros de la noche pesan más y su curva cae. Se verificó con una serie de verdad
  conocida: las curvas imputadas se apartan 0,34 W/m² de la verdad y la del original 41,20 W/m².
  Ahora la comparación se hace sobre la franja diurna y se muestra la cobertura por mes.
- **`fig_08d_mapa_zona` y `fig_08e_zoom_celda_estacion` (cuaderno 08).** Mapas nuevos de la zona
  con la malla de NASA POWER encima y un acercamiento que demuestra que puntos separados 141 km
  caen en la misma celda. El cuaderno instala `geopandas` y `contextily`.

---

## Los documentos, que van en otra carpeta

No los mezcles con los cuadernos.

| Archivo | Qué es |
|---|---|
| `PASO_A_PASO.md` | El plan de trabajo. **Empieza por aquí** |
| `01_auditoria_aspectos_daniel.md` | Los 19 aspectos de evaluación que firmas, con su estado |
| `02_mapa_de_propagacion.md` | Qué secciones toca cada cambio |
| `04_parches_notebooks.md` | Solo de consulta: qué hace cada parche y por qué |
| `INFORME_AUDITORIA.md` | Qué se probó de cada cuaderno y qué se corrigió |
| `B1`…`E1` (`.tex`) | Los textos para insertar en el documento |
