# Día 5 - Sesión 1- Modelo de área para estadísticas del mercado de trabajo



La Encuesta Nacional Continua de Fuerza de Trabajo (ENCFT) es una investigación estadística que se realiza en la República Dominicana para proporcionar información precisa y amplia sobre las variables laborales, sociodemográficas y económicas que permiten caracterizar y analizar la dinámica y heterogeneidad del mercado de trabajo en el país. La ENCFT se lleva a cabo mediante una muestra probabilística de hogares, donde se encuestan a los jefes de hogar o a cualquier otro miembro del hogar mayor de 14 años, y se realiza de manera trimestral en todo el territorio nacional. La encuesta permite conocer información relevante acerca de la población económicamente activa, la población ocupada y desocupada, la tasa de actividad y la tasa de desempleo, entre otros indicadores, lo que la convierte en una herramienta valiosa para la elaboración de políticas públicas y programas de empleo. La ENCFT es una encuesta continua, lo que permite hacer seguimiento a la evolución del mercado de trabajo y evaluar el impacto de las políticas públicas y los programas de empleo.

## Definición del modelo multinomial

-   Sea $K$ el número de categorías de la variable de interés $𝑌\sim multinimial\left(\boldsymbol{\theta}\right)$, con $\boldsymbol{\theta}=\left(p_{1},p_{2},\dots ,p_{k}\right)$ y $\sum_{k=1}^{K}p_{k}=1$.

-   Sea $N_i$ el número de elementos en el i-ésiamo dominio y $N_{ik}$ el número de elementos que tienen la k-ésima categoría, note que $\sum_{k=1}^{K}N_{ik}=N_{i}$ y $p_{ik}=\frac{N_{ik}}{N_{i}}$.

-   Sea $\hat{p}_{ik}$ la estimación directa de $p_{ik}$ y $v_{ik}=Var\left(\hat{p}_{ik}\right)$ y denote el estimador de la varianza por $\hat{v}_{ik}=\widehat{Var}\left(\hat{p}_{ik}\right)$


Note que el efecto diseño cambia entre categoría, por tanto, lo primero será definir el tamaño de muestra efectivo por categoría. Esto es:

La estimación de $\tilde{n}$ esta dado por $\tilde{n}_{ik} = \frac{(\tilde{p}_{ik}\times(1-\tilde{p}_{ik}))}{\hat{v}_{ik}},$

$\tilde{y}_{ik}=\tilde{n}_{ik}\times\hat{p}_{ik}$

luego, $\hat{n}_{i} = \sum_{k=1}^{K}\tilde{y}_{ik}$

de donde se sigue que $\hat{y}_{ik} = \hat{n}_i\times \hat{p}_{ik}$


Sea $\boldsymbol{\theta}=\left(p_{1},p_{2}, p_{3}\right)^{T}=\left(\frac{N_{i1}}{N_{i}},\frac{N_{i2}}{N_{i}}\frac{N_{i3}}{N_{i}}\right)^{T}$, entonces el modelo multinomial para el i-ésimo dominio estaría dado por:

$$
\left(\tilde{y}_{i1},\tilde{y}_{i2},\tilde{y}_{i3}\right)\mid\hat{n}_{i},\boldsymbol{\theta}_{i}\sim multinomial\left(\hat{n}_{i},\boldsymbol{\theta}_{i}\right)
$$ 
Ahora, puede escribir $p_{ik}$ como :

$\ln\left(\frac{p_{i2}}{p_{i1}}\right)=\boldsymbol{X}_{i}^{T}\beta_{2} + u_{i2}$ y
$\ln\left(\frac{p_{i3}}{p_{i1}}\right)=\boldsymbol{X}_{i}^{T}\beta_{3}+ u_{i3}$



Dada la restricción $1 = p_{i1} + p_{i2} + p_{i3}$ entonces 
$$p_{i1} + p_{i1}(e^{\boldsymbol{X}_{i}^{T}\boldsymbol{\beta_{2}}}+  u_{i2})+p_{i1}(e^{\boldsymbol{X}_{i}^{T}\boldsymbol{\beta}_{3}} + u_{i3})$$ de donde se sigue que 

$$
p_{i1}=\frac{1}{1+e^{\boldsymbol{X}_{i}^{T}\boldsymbol{\beta_{2}}}+ u_{i2}+e^{\boldsymbol{X_{i}}^{T}\boldsymbol{\beta_{2}}}+ u_{i3}}
$$

Las expresiones para $p_{i2}$ y $p_{i3}$ estarían dadas por: 

$$
p_{i2}=\frac{e^{\boldsymbol{X}_{i}^{T}\boldsymbol{\beta}_{2}} + u_{i2}}{1+e^{\boldsymbol{X}_{i}^{T}\boldsymbol{\beta_{2}}}+ u_{i2}+e^{\boldsymbol{X_{i}}^{T}\boldsymbol{\beta_{2}}}+ u_{i3}}
$$

$$
p_{i3}=\frac{e^{\boldsymbol{X}_{i}^{T}\boldsymbol{\beta}_{3}}+ u_{i3}}{1+e^{\boldsymbol{X}_{i}^{T}\boldsymbol{\beta_{2}}}+ u_{i2}+e^{\boldsymbol{X_{i}}^{T}\boldsymbol{\beta_{3}}}+ u_{i3}}
$$

## Lectura de librerías.

  -   La librería `survey` es una herramienta de análisis estadístico en R que permite trabajar con datos de encuestas complejas, como las encuestas estratificadas, multietápicas o con pesos de muestreo. Ofrece funciones para estimación de parámetros, diseño de muestras, análisis de varianza y regresión, y cálculo de errores estándar.

  -   La librería `tidyverse` es un conjunto de paquetes de R que se utilizan para la manipulación y visualización de datos. Incluye las librerías `dplyr`, `ggplot2`, `tidyr` y otras, y se caracteriza por su enfoque en la programación `tidy` o ordenada, que facilita la exploración y análisis de datos.

  -   La librería `srvyr` es una extensión de la librería `survey` que permite integrar las funciones de `survey` con la sintaxis de `dplyr`, lo que facilita la manipulación de datos de encuestas complejas. Incluye funciones para agrupar, filtrar y resumir datos de encuestas utilizando la sintaxis `tidy`.

  -   La librería `TeachingSampling` es una herramienta de R que se utiliza para la enseñanza de métodos de muestreo estadístico. Incluye funciones para simular diferentes tipos de muestras, estimar parámetros, calcular errores estándar y construir intervalos de confianza, entre otras.

  -   La librería `haven` es una herramienta de R que permite importar y exportar datos en diferentes formatos, incluyendo SPSS, Stata y SAS. Permite trabajar con archivos de datos de encuestas, y ofrece funciones para etiquetar variables, codificar datos faltantes y convertir datos de diferentes formatos.

  -   La librería `bayesplot` es una herramienta de R que se utiliza para la visualización y diagnóstico de modelos Bayesianos. Incluye funciones para graficar distribuciones posteriores, diagnósticos de convergencia, gráficos de diagnóstico de residuos, y otros tipos de gráficos relacionados con el análisis Bayesianos.

  -   La librería `patchwork` es una herramienta de R que permite unir gráficos de manera sencilla y flexible. Esta librería facilita la creación de gráficos complejos al permitir la combinación de múltiples gráficos en una sola visualización, lo que resulta especialmente útil en análisis de datos y modelización.

  -   La librería `stringr` es una herramienta de R que se utiliza para la manipulación de cadenas de texto. Incluye funciones para la extracción, manipulación y modificación de cadenas de texto, lo que resulta especialmente útil en la limpieza y preparación de datos antes de su análisis.

  -   La librería `rstan` es una herramienta de R que se utiliza para la estimación de modelos Bayesianos mediante el método de cadenas de Markov Monte Carlo (MCMC). Esta librería permite la especificación y estimación de modelos complejos mediante un lenguaje sencillo y flexible, y ofrece diversas herramientas para el diagnóstico y visualización de resultados.


```r
library(survey)
library(tidyverse)
library(srvyr)
library(TeachingSampling)
library(haven)
library(bayesplot)
library(patchwork)
library(stringr)
library(rstan)
```

## Lectura de la encuesta y estimaciones directas 

En la primera línea se carga la encuesta desde un archivo RDS y se guarda en un objeto llamado `encuesta`. La segunda línea utiliza la función `transmute()` de la librería `dplyr` para seleccionar las variables de interés en la encuesta y crear nuevas variables a partir de ellas. Luego, se utiliza la variable `id_dominio` para identificar el dominio de estudio. En conjunto, estos pasos son fundamentales para preparar los datos de la encuesta para su posterior estimación del parámetro.



```r
encuesta <- readRDS('Recursos/Día5/Sesion1/Data/encuestaDOM21N1.rds')
region <- readRDS(file = "Recursos/Día5/Sesion1/Data/total_personas_dam2.rds") %>% 
  ungroup() %>% select(region,dam2)

## 
encuesta <-
  encuesta %>%
  transmute(
    dam = haven::as_factor(dam_ee,levels = "values"),
    dam = str_pad(dam,width = 2,pad = "0"),
    dam2,
    fep = `_fep`, 
    upm = `_upm`,
    estrato = `_estrato`,
    empleo = condact3
  ) %>% 
  inner_join(region)
```

El código presentado define el diseño muestral para el análisis de la encuesta "encuesta" en R. La primera línea establece una opción para el tratamiento de las PSU (unidades primarias de muestreo) solitarias, lo que indica que se deben aplicar ajustes en el cálculo de los errores estándar. La segunda línea utiliza la función "as_survey_design" de la librería "survey" para definir el diseño muestral. La función toma como argumentos la variable "encuesta" y los siguientes parámetros:

  -   `strata`: la variable que define las estratas de muestreo en la encuesta, en este caso la variable "estrato".

  -   `ids`: la variable que identifica las PSU en la encuesta, en este caso la variable "upm".

  -   `weights`: la variable que indica los pesos muestrales de cada observación, en este caso la variable "fep".

  -   `nest`: un parámetro lógico que indica si los datos de la encuesta están anidados o no. En este caso, se establece en "TRUE" porque los datos están anidados por dominio.
  
En conjunto, estos pasos permiten definir un diseño muestral que tenga en cuenta las características del muestreo y los pesos asignados a cada observación en la encuesta, lo que es necesario para obtener estimaciones precisas y representativas de los parámetros de interés.


```r
options(survey.lonely.psu= 'adjust' )
diseno <- encuesta %>%
  as_survey_design(
    strata = estrato,
    ids = upm,
    weights = fep,
    nest=T
  )
```

El código presentado es una operación que se realiza en el diseño muestral definido en el código anterior, con el objetivo de obtener un indicador del empleo por dominio. La primera línea define un objeto llamado "indicador_dam". En la segunda línea, se agrupa el diseño muestral según el dominio especificado en la variable "id_dominio". La tercera línea filtra los datos para quedarse con los individuos que tienen empleo (empleo igual a 1), están desempleados (empleo igual a 2) o son inactivos (empleo igual a 3).

A partir de la cuarta línea, se utilizan las funciones "summarise" y "survey_mean" para calcular las estadísticas descriptivas de interés. En particular, se calculan el número de personas ocupadas, desocupadas e inactivas en cada dominio, y la proporción de personas en cada una de estas categorías. La función "survey_mean" se utiliza para calcular la proporción de personas en cada una de estas categorías con sus respectivos errores estándar y efecto de diseño.


```r
indicador_dam <-
  diseno %>% group_by(dam2) %>% 
  filter(empleo %in% c(1:3)) %>%
  summarise(
    n_ocupado = unweighted(sum(empleo == 1)),
    n_desocupado = unweighted(sum(empleo == 2)),
    n_inactivo = unweighted(sum(empleo == 3)),
    Ocupado = survey_mean(empleo == 1,
      vartype = c("se",  "var"),
      deff = T
    ),
    Desocupado = survey_mean(empleo == 2,
                          vartype = c("se",  "var"),
                          deff = T
    ),
    Inactivo = survey_mean(empleo == 3,
                          vartype = c("se",  "var"),
                          deff = T
    )
  )
```

## Selección de dominios 

En la sección anterior, se llevó a cabo una estimación directa para cada categoría individualmente en cada municipio (dominio) presente en la muestra. Ahora, para evaluar la calidad de los resultados obtenidos, realizaremos un análisis descriptivo. Se emplean varias medidas de calidad, entre ellas, se cuenta el número de dominios que tienen dos o más unidades primarias de muestreo (UPM), así como el efecto de diseño mayor a 1 y las varianzas mayores a 0. Estas medidas nos permitirán determinar la fiabilidad de nuestros resultados y tomar decisiones informadas en función de ellos.

Después de realizar las validaciones anteriores se establece como regla incluir en el estudio los dominios que posean 

  - Dos o más upm por dominio. 
  
  - Contar con un resultado en el Deff


```r
n_upm <- encuesta %>% distinct(dam2, upm) %>%
  group_by(dam2) %>% tally(name = "n_upm", sort = TRUE)
indicador_dam <- inner_join(n_upm, indicador_dam)

indicador_dam1 <- indicador_dam %>% 
  filter(n_upm >= 2, !is.na(Desocupado_deff)) %>% 
  mutate(id_orden = 1:n())

saveRDS(object = indicador_dam1, "Recursos/Día5/Sesion1/Data/base_modelo.Rds")
```

<table class="table table-striped lightable-classic" style="width: auto !important; margin-left: auto; margin-right: auto; font-family: Arial Narrow; width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> dam2 </th>
   <th style="text-align:right;"> n_upm </th>
   <th style="text-align:right;"> n_ocupado </th>
   <th style="text-align:right;"> n_desocupado </th>
   <th style="text-align:right;"> n_inactivo </th>
   <th style="text-align:right;"> Ocupado </th>
   <th style="text-align:right;"> Ocupado_se </th>
   <th style="text-align:right;"> Ocupado_var </th>
   <th style="text-align:right;"> Ocupado_deff </th>
   <th style="text-align:right;"> Desocupado </th>
   <th style="text-align:right;"> Desocupado_se </th>
   <th style="text-align:right;"> Desocupado_var </th>
   <th style="text-align:right;"> Desocupado_deff </th>
   <th style="text-align:right;"> Inactivo </th>
   <th style="text-align:right;"> Inactivo_se </th>
   <th style="text-align:right;"> Inactivo_var </th>
   <th style="text-align:right;"> Inactivo_deff </th>
   <th style="text-align:right;"> id_orden </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:right;"> 127 </td>
   <td style="text-align:right;"> 2953 </td>
   <td style="text-align:right;"> 284 </td>
   <td style="text-align:right;"> 2439 </td>
   <td style="text-align:right;"> 0.5210 </td>
   <td style="text-align:right;"> 0.0117 </td>
   <td style="text-align:right;"> 1e-04 </td>
   <td style="text-align:right;"> 3.1326 </td>
   <td style="text-align:right;"> 0.0503 </td>
   <td style="text-align:right;"> 0.0050 </td>
   <td style="text-align:right;"> 0e+00 </td>
   <td style="text-align:right;"> 2.9903 </td>
   <td style="text-align:right;"> 0.4287 </td>
   <td style="text-align:right;"> 0.0119 </td>
   <td style="text-align:right;"> 0.0001 </td>
   <td style="text-align:right;"> 3.3118 </td>
   <td style="text-align:right;"> 1 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03201 </td>
   <td style="text-align:right;"> 109 </td>
   <td style="text-align:right;"> 2841 </td>
   <td style="text-align:right;"> 240 </td>
   <td style="text-align:right;"> 2524 </td>
   <td style="text-align:right;"> 0.5018 </td>
   <td style="text-align:right;"> 0.0128 </td>
   <td style="text-align:right;"> 2e-04 </td>
   <td style="text-align:right;"> 3.6801 </td>
   <td style="text-align:right;"> 0.0481 </td>
   <td style="text-align:right;"> 0.0045 </td>
   <td style="text-align:right;"> 0e+00 </td>
   <td style="text-align:right;"> 2.5120 </td>
   <td style="text-align:right;"> 0.4501 </td>
   <td style="text-align:right;"> 0.0122 </td>
   <td style="text-align:right;"> 0.0001 </td>
   <td style="text-align:right;"> 3.3802 </td>
   <td style="text-align:right;"> 2 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02501 </td>
   <td style="text-align:right;"> 87 </td>
   <td style="text-align:right;"> 3059 </td>
   <td style="text-align:right;"> 121 </td>
   <td style="text-align:right;"> 2133 </td>
   <td style="text-align:right;"> 0.5719 </td>
   <td style="text-align:right;"> 0.0111 </td>
   <td style="text-align:right;"> 1e-04 </td>
   <td style="text-align:right;"> 2.7180 </td>
   <td style="text-align:right;"> 0.0226 </td>
   <td style="text-align:right;"> 0.0034 </td>
   <td style="text-align:right;"> 0e+00 </td>
   <td style="text-align:right;"> 2.8494 </td>
   <td style="text-align:right;"> 0.4055 </td>
   <td style="text-align:right;"> 0.0115 </td>
   <td style="text-align:right;"> 0.0001 </td>
   <td style="text-align:right;"> 2.9408 </td>
   <td style="text-align:right;"> 3 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03203 </td>
   <td style="text-align:right;"> 59 </td>
   <td style="text-align:right;"> 1953 </td>
   <td style="text-align:right;"> 96 </td>
   <td style="text-align:right;"> 1629 </td>
   <td style="text-align:right;"> 0.5301 </td>
   <td style="text-align:right;"> 0.0121 </td>
   <td style="text-align:right;"> 1e-04 </td>
   <td style="text-align:right;"> 2.1909 </td>
   <td style="text-align:right;"> 0.0263 </td>
   <td style="text-align:right;"> 0.0027 </td>
   <td style="text-align:right;"> 0e+00 </td>
   <td style="text-align:right;"> 1.0520 </td>
   <td style="text-align:right;"> 0.4436 </td>
   <td style="text-align:right;"> 0.0124 </td>
   <td style="text-align:right;"> 0.0002 </td>
   <td style="text-align:right;"> 2.3024 </td>
   <td style="text-align:right;"> 4 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03202 </td>
   <td style="text-align:right;"> 42 </td>
   <td style="text-align:right;"> 1050 </td>
   <td style="text-align:right;"> 51 </td>
   <td style="text-align:right;"> 883 </td>
   <td style="text-align:right;"> 0.5290 </td>
   <td style="text-align:right;"> 0.0131 </td>
   <td style="text-align:right;"> 2e-04 </td>
   <td style="text-align:right;"> 1.3768 </td>
   <td style="text-align:right;"> 0.0229 </td>
   <td style="text-align:right;"> 0.0054 </td>
   <td style="text-align:right;"> 0e+00 </td>
   <td style="text-align:right;"> 2.5923 </td>
   <td style="text-align:right;"> 0.4481 </td>
   <td style="text-align:right;"> 0.0137 </td>
   <td style="text-align:right;"> 0.0002 </td>
   <td style="text-align:right;"> 1.5230 </td>
   <td style="text-align:right;"> 5 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01101 </td>
   <td style="text-align:right;"> 38 </td>
   <td style="text-align:right;"> 1203 </td>
   <td style="text-align:right;"> 114 </td>
   <td style="text-align:right;"> 713 </td>
   <td style="text-align:right;"> 0.6092 </td>
   <td style="text-align:right;"> 0.0227 </td>
   <td style="text-align:right;"> 5e-04 </td>
   <td style="text-align:right;"> 4.4247 </td>
   <td style="text-align:right;"> 0.0575 </td>
   <td style="text-align:right;"> 0.0075 </td>
   <td style="text-align:right;"> 1e-04 </td>
   <td style="text-align:right;"> 2.1246 </td>
   <td style="text-align:right;"> 0.3333 </td>
   <td style="text-align:right;"> 0.0227 </td>
   <td style="text-align:right;"> 0.0005 </td>
   <td style="text-align:right;"> 4.7511 </td>
   <td style="text-align:right;"> 6 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03206 </td>
   <td style="text-align:right;"> 32 </td>
   <td style="text-align:right;"> 837 </td>
   <td style="text-align:right;"> 51 </td>
   <td style="text-align:right;"> 728 </td>
   <td style="text-align:right;"> 0.5022 </td>
   <td style="text-align:right;"> 0.0177 </td>
   <td style="text-align:right;"> 3e-04 </td>
   <td style="text-align:right;"> 2.0370 </td>
   <td style="text-align:right;"> 0.0315 </td>
   <td style="text-align:right;"> 0.0080 </td>
   <td style="text-align:right;"> 1e-04 </td>
   <td style="text-align:right;"> 3.3819 </td>
   <td style="text-align:right;"> 0.4663 </td>
   <td style="text-align:right;"> 0.0170 </td>
   <td style="text-align:right;"> 0.0003 </td>
   <td style="text-align:right;"> 1.8859 </td>
   <td style="text-align:right;"> 7 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00901 </td>
   <td style="text-align:right;"> 20 </td>
   <td style="text-align:right;"> 744 </td>
   <td style="text-align:right;"> 83 </td>
   <td style="text-align:right;"> 530 </td>
   <td style="text-align:right;"> 0.5492 </td>
   <td style="text-align:right;"> 0.0194 </td>
   <td style="text-align:right;"> 4e-04 </td>
   <td style="text-align:right;"> 2.0806 </td>
   <td style="text-align:right;"> 0.0595 </td>
   <td style="text-align:right;"> 0.0113 </td>
   <td style="text-align:right;"> 1e-04 </td>
   <td style="text-align:right;"> 3.1142 </td>
   <td style="text-align:right;"> 0.3913 </td>
   <td style="text-align:right;"> 0.0208 </td>
   <td style="text-align:right;"> 0.0004 </td>
   <td style="text-align:right;"> 2.4841 </td>
   <td style="text-align:right;"> 8 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01301 </td>
   <td style="text-align:right;"> 20 </td>
   <td style="text-align:right;"> 738 </td>
   <td style="text-align:right;"> 45 </td>
   <td style="text-align:right;"> 552 </td>
   <td style="text-align:right;"> 0.5359 </td>
   <td style="text-align:right;"> 0.0244 </td>
   <td style="text-align:right;"> 6e-04 </td>
   <td style="text-align:right;"> 3.2173 </td>
   <td style="text-align:right;"> 0.0351 </td>
   <td style="text-align:right;"> 0.0049 </td>
   <td style="text-align:right;"> 0e+00 </td>
   <td style="text-align:right;"> 0.9599 </td>
   <td style="text-align:right;"> 0.4290 </td>
   <td style="text-align:right;"> 0.0262 </td>
   <td style="text-align:right;"> 0.0007 </td>
   <td style="text-align:right;"> 3.7629 </td>
   <td style="text-align:right;"> 9 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02101 </td>
   <td style="text-align:right;"> 20 </td>
   <td style="text-align:right;"> 505 </td>
   <td style="text-align:right;"> 52 </td>
   <td style="text-align:right;"> 460 </td>
   <td style="text-align:right;"> 0.4965 </td>
   <td style="text-align:right;"> 0.0258 </td>
   <td style="text-align:right;"> 7e-04 </td>
   <td style="text-align:right;"> 2.7259 </td>
   <td style="text-align:right;"> 0.0514 </td>
   <td style="text-align:right;"> 0.0116 </td>
   <td style="text-align:right;"> 1e-04 </td>
   <td style="text-align:right;"> 2.8329 </td>
   <td style="text-align:right;"> 0.4521 </td>
   <td style="text-align:right;"> 0.0332 </td>
   <td style="text-align:right;"> 0.0011 </td>
   <td style="text-align:right;"> 4.5559 </td>
   <td style="text-align:right;"> 10 </td>
  </tr>
</tbody>
</table>

## Modelo programando en `STAN`

El código presenta la implementación de un modelo multinomial logístico de área de respuesta utilizando el lenguaje de programación `STAN`. En este modelo, se asume que la variable de respuesta en cada dominio sigue una distribución multinomial. Se asume que los parámetros que rigen la relación entre las variables predictoras y la variable de respuesta son diferentes en cada dominio y se modelan como efectos aleatorios.

La sección de *functions* define una función auxiliar llamada `pred_theta()`, que se utiliza para predecir los valores de la variable de respuesta en los dominios no observados. La sección de `data` contiene las variables de entrada del modelo, incluyendo el número de dominios, el número de categorías de la variable de respuesta, las estimaciones directas de la variable de respuesta en cada dominio, las covariables observadas en cada dominio y las covariables correspondientes a los dominios no observados.

La sección de *parameters* define los parámetros desconocidos del modelo, incluyendo la matriz de parámetros *beta*, que contiene los coeficientes que relacionan las covariables con la variable de respuesta en cada categoría. También se incluyen los desviaciones estándar de los efectos aleatorios.

En la sección de *transformed parameters* se define el vector de parámetros `theta`, que contiene las probabilidades de pertenencia a cada categoría de la variable de respuesta en cada dominio. Se utilizan los efectos aleatorios para ajustar los valores de `theta` en cada dominio.

En la sección de *model* se define la estructura del modelo y se incluyen las distribuciones a priori para los parámetros desconocidos. En particular, se utiliza una distribución normal para los coeficientes de la matriz beta. Finalmente, se calcula la función de verosimilitud de la distribución multinomial para las estimaciones directas de la variable de respuesta en cada dominio.

La sección de *generated quantities* se utiliza para calcular las predicciones de la variable de respuesta en los dominios no observados utilizando la función auxiliar definida previamente.


```r
functions {
  matrix pred_theta(matrix Xp, int p, matrix beta){
  int D1 = rows(Xp);
  real num1[D1, p];
  real den1[D1];
  matrix[D1,p] theta_p;
  
  for(d in 1:D1){
    num1[d, 1] = 1;
    num1[d, 2] = exp(Xp[d, ] * beta[1, ]' ) ;
    num1[d, 3] = exp(Xp[d, ] * beta[2, ]' ) ;
    
    den1[d] = sum(num1[d, ]);
  }
  
  for(d in 1:D1){
    for(i in 2:p){
    theta_p[d, i] = num1[d, i]/den1[d];
    }
    theta_p[d, 1] = 1/den1[d];
   }

  return theta_p  ;
  }
  
}

data {
  int<lower=1> D; // número de dominios 
  int<lower=1> P; // categorías
  int<lower=1> K; // cantidad de regresores
  int y_tilde[D, P]; // matriz de datos
  matrix[D, K] X_obs; // matriz de covariables
  int<lower=1> D1; // número de dominios 
  matrix[D1, K] X_pred; // matriz de covariables
}
  

parameters {
  matrix[P-1, K] beta;// matriz de parámetros 
  real<lower=0> sigma2_u1;       // random effects standard deviations
  real<lower=0> sigma2_u2;       // random effects standard deviations
  vector[D] u1;
  vector[D] u2;
  // declare L_u to be the Choleski factor of a 2x2 correlation matrix
          
}

transformed parameters {
  simplex[P] theta[D];// vector de parámetros;
  real num[D, P];
  real den[D];
  real<lower=0> sigma_u1;       // random effects standard deviations
  real<lower=0> sigma_u2;       // random effects standard deviations
  sigma_u1 = sqrt(sigma2_u1); 
  sigma_u2 = sqrt(sigma2_u2); 

  for(d in 1:D){
    num[d, 1] = 1;
    num[d, 2] = exp(X_obs[d, ] * beta[1, ]' + u1[d]) ;
    num[d, 3] = exp(X_obs[d, ] * beta[2, ]' + u2[d]) ;
    
    den[d] = sum(num[d, ]);
  }
  
  for(d in 1:D){
    for(p in 2:P){
    theta[d, p] = num[d, p]/den[d];
    }
    theta[d, 1] = 1/den[d];
  }
}

model {
 u1 ~ normal(0, sigma_u1);
 u2 ~ normal(0, sigma_u2);
 sigma2_u1 ~  inv_gamma(0.0001, 0.0001);
 sigma2_u2 ~  inv_gamma(0.0001, 0.0001);
  
  for(p in 2:P){
    for(k in 1:K){
      beta[p-1, k] ~ normal(0, 10000);
    }
    }
  
  for(d in 1:D){
    target += multinomial_lpmf(y_tilde[d, ] | theta[d, ]); 
  }
}

  
generated quantities {
  matrix[D1,P] theta_pred;
  theta_pred = pred_theta(X_pred, P, beta);
}
```

## Preparando insumos para `STAN`

  1.    Lectura y adecuación de covariables
  

```r
statelevel_predictors_df <-
  readRDS('Recursos/Día5/Sesion1/Data/statelevel_predictors_df_dam2.rds') 
## Estandarizando las variables para controlar el efecto de la escala. 
statelevel_predictors_df %<>%
  mutate_at(vars("luces_nocturnas", 
                 "cubrimiento_cultivo",
                 "cubrimiento_urbano",
                 "modificacion_humana",
                 "accesibilidad_hospitales",
                 "accesibilidad_hosp_caminado"),
            function(x)as.numeric(scale(x)))

head(statelevel_predictors_df,10) %>% tba()
```

<table class="table table-striped lightable-classic" style="width: auto !important; margin-left: auto; margin-right: auto; font-family: Arial Narrow; width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> dam2 </th>
   <th style="text-align:right;"> modificacion_humana </th>
   <th style="text-align:right;"> accesibilidad_hospitales </th>
   <th style="text-align:right;"> accesibilidad_hosp_caminado </th>
   <th style="text-align:right;"> cubrimiento_cultivo </th>
   <th style="text-align:right;"> cubrimiento_urbano </th>
   <th style="text-align:right;"> luces_nocturnas </th>
   <th style="text-align:right;"> area1 </th>
   <th style="text-align:right;"> sexo2 </th>
   <th style="text-align:right;"> edad2 </th>
   <th style="text-align:right;"> edad3 </th>
   <th style="text-align:right;"> edad4 </th>
   <th style="text-align:right;"> edad5 </th>
   <th style="text-align:right;"> anoest2 </th>
   <th style="text-align:right;"> anoest3 </th>
   <th style="text-align:right;"> anoest4 </th>
   <th style="text-align:right;"> anoest99 </th>
   <th style="text-align:right;"> tiene_sanitario </th>
   <th style="text-align:right;"> tiene_acueducto </th>
   <th style="text-align:right;"> tiene_gas </th>
   <th style="text-align:right;"> eliminar_basura </th>
   <th style="text-align:right;"> tiene_internet </th>
   <th style="text-align:right;"> piso_tierra </th>
   <th style="text-align:right;"> material_paredes </th>
   <th style="text-align:right;"> material_techo </th>
   <th style="text-align:right;"> rezago_escolar </th>
   <th style="text-align:right;"> alfabeta </th>
   <th style="text-align:right;"> hacinamiento </th>
   <th style="text-align:right;"> tasa_desocupacion </th>
   <th style="text-align:left;"> id_municipio </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:right;"> 3.6127 </td>
   <td style="text-align:right;"> -1.1835 </td>
   <td style="text-align:right;"> -1.5653 </td>
   <td style="text-align:right;"> -1.1560 </td>
   <td style="text-align:right;"> 7.2782 </td>
   <td style="text-align:right;"> 4.9650 </td>
   <td style="text-align:right;"> 1.0000 </td>
   <td style="text-align:right;"> 0.5224 </td>
   <td style="text-align:right;"> 0.2781 </td>
   <td style="text-align:right;"> 0.2117 </td>
   <td style="text-align:right;"> 0.1808 </td>
   <td style="text-align:right;"> 0.0725 </td>
   <td style="text-align:right;"> 0.2000 </td>
   <td style="text-align:right;"> 0.3680 </td>
   <td style="text-align:right;"> 0.2286 </td>
   <td style="text-align:right;"> 0.0193 </td>
   <td style="text-align:right;"> 0.0119 </td>
   <td style="text-align:right;"> 0.7946 </td>
   <td style="text-align:right;"> 0.0673 </td>
   <td style="text-align:right;"> 0.0810 </td>
   <td style="text-align:right;"> 0.6678 </td>
   <td style="text-align:right;"> 0.0033 </td>
   <td style="text-align:right;"> 0.0109 </td>
   <td style="text-align:right;"> 0.0111 </td>
   <td style="text-align:right;"> 0.3694 </td>
   <td style="text-align:right;"> 0.9247 </td>
   <td style="text-align:right;"> 0.1962 </td>
   <td style="text-align:right;"> 0.0066 </td>
   <td style="text-align:left;"> 100101 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00201 </td>
   <td style="text-align:right;"> -0.0553 </td>
   <td style="text-align:right;"> 0.4449 </td>
   <td style="text-align:right;"> 0.2100 </td>
   <td style="text-align:right;"> 0.0684 </td>
   <td style="text-align:right;"> -0.0682 </td>
   <td style="text-align:right;"> -0.1511 </td>
   <td style="text-align:right;"> 0.8904 </td>
   <td style="text-align:right;"> 0.4933 </td>
   <td style="text-align:right;"> 0.2726 </td>
   <td style="text-align:right;"> 0.1849 </td>
   <td style="text-align:right;"> 0.1520 </td>
   <td style="text-align:right;"> 0.0614 </td>
   <td style="text-align:right;"> 0.3149 </td>
   <td style="text-align:right;"> 0.3022 </td>
   <td style="text-align:right;"> 0.0775 </td>
   <td style="text-align:right;"> 0.0082 </td>
   <td style="text-align:right;"> 0.1005 </td>
   <td style="text-align:right;"> 0.7220 </td>
   <td style="text-align:right;"> 0.2261 </td>
   <td style="text-align:right;"> 0.1300 </td>
   <td style="text-align:right;"> 0.9276 </td>
   <td style="text-align:right;"> 0.0664 </td>
   <td style="text-align:right;"> 0.0812 </td>
   <td style="text-align:right;"> 0.0249 </td>
   <td style="text-align:right;"> 0.1501 </td>
   <td style="text-align:right;"> 0.7975 </td>
   <td style="text-align:right;"> 0.3014 </td>
   <td style="text-align:right;"> 0.0007 </td>
   <td style="text-align:left;"> 050201 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00202 </td>
   <td style="text-align:right;"> -0.3758 </td>
   <td style="text-align:right;"> 0.0000 </td>
   <td style="text-align:right;"> 0.1482 </td>
   <td style="text-align:right;"> -0.2345 </td>
   <td style="text-align:right;"> -0.2855 </td>
   <td style="text-align:right;"> -0.4234 </td>
   <td style="text-align:right;"> 0.6799 </td>
   <td style="text-align:right;"> 0.4697 </td>
   <td style="text-align:right;"> 0.2804 </td>
   <td style="text-align:right;"> 0.1895 </td>
   <td style="text-align:right;"> 0.1430 </td>
   <td style="text-align:right;"> 0.0515 </td>
   <td style="text-align:right;"> 0.3757 </td>
   <td style="text-align:right;"> 0.2405 </td>
   <td style="text-align:right;"> 0.0148 </td>
   <td style="text-align:right;"> 0.0014 </td>
   <td style="text-align:right;"> 0.1322 </td>
   <td style="text-align:right;"> 0.9230 </td>
   <td style="text-align:right;"> 0.2693 </td>
   <td style="text-align:right;"> 0.2884 </td>
   <td style="text-align:right;"> 0.9759 </td>
   <td style="text-align:right;"> 0.0625 </td>
   <td style="text-align:right;"> 0.0986 </td>
   <td style="text-align:right;"> 0.0673 </td>
   <td style="text-align:right;"> 0.0278 </td>
   <td style="text-align:right;"> 0.7140 </td>
   <td style="text-align:right;"> 0.3454 </td>
   <td style="text-align:right;"> 0.0001 </td>
   <td style="text-align:left;"> 050202 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00203 </td>
   <td style="text-align:right;"> -0.9259 </td>
   <td style="text-align:right;"> 0.5732 </td>
   <td style="text-align:right;"> -0.1402 </td>
   <td style="text-align:right;"> -0.5511 </td>
   <td style="text-align:right;"> -0.3822 </td>
   <td style="text-align:right;"> -0.5612 </td>
   <td style="text-align:right;"> 0.5814 </td>
   <td style="text-align:right;"> 0.4601 </td>
   <td style="text-align:right;"> 0.2665 </td>
   <td style="text-align:right;"> 0.1733 </td>
   <td style="text-align:right;"> 0.1586 </td>
   <td style="text-align:right;"> 0.0713 </td>
   <td style="text-align:right;"> 0.3778 </td>
   <td style="text-align:right;"> 0.2463 </td>
   <td style="text-align:right;"> 0.0219 </td>
   <td style="text-align:right;"> 0.0052 </td>
   <td style="text-align:right;"> 0.2579 </td>
   <td style="text-align:right;"> 0.7602 </td>
   <td style="text-align:right;"> 0.4824 </td>
   <td style="text-align:right;"> 0.2589 </td>
   <td style="text-align:right;"> 0.9919 </td>
   <td style="text-align:right;"> 0.1937 </td>
   <td style="text-align:right;"> 0.2342 </td>
   <td style="text-align:right;"> 0.1238 </td>
   <td style="text-align:right;"> 0.0485 </td>
   <td style="text-align:right;"> 0.7104 </td>
   <td style="text-align:right;"> 0.2755 </td>
   <td style="text-align:right;"> 0.0001 </td>
   <td style="text-align:left;"> 050203 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00204 </td>
   <td style="text-align:right;"> -1.3166 </td>
   <td style="text-align:right;"> 1.1111 </td>
   <td style="text-align:right;"> 0.4438 </td>
   <td style="text-align:right;"> -0.5027 </td>
   <td style="text-align:right;"> -0.3835 </td>
   <td style="text-align:right;"> -0.6042 </td>
   <td style="text-align:right;"> 0.5708 </td>
   <td style="text-align:right;"> 0.4663 </td>
   <td style="text-align:right;"> 0.2647 </td>
   <td style="text-align:right;"> 0.1683 </td>
   <td style="text-align:right;"> 0.1673 </td>
   <td style="text-align:right;"> 0.0757 </td>
   <td style="text-align:right;"> 0.3306 </td>
   <td style="text-align:right;"> 0.2402 </td>
   <td style="text-align:right;"> 0.0440 </td>
   <td style="text-align:right;"> 0.0049 </td>
   <td style="text-align:right;"> 0.1672 </td>
   <td style="text-align:right;"> 0.6375 </td>
   <td style="text-align:right;"> 0.5040 </td>
   <td style="text-align:right;"> 0.3837 </td>
   <td style="text-align:right;"> 0.9759 </td>
   <td style="text-align:right;"> 0.1403 </td>
   <td style="text-align:right;"> 0.1354 </td>
   <td style="text-align:right;"> 0.0176 </td>
   <td style="text-align:right;"> 0.0873 </td>
   <td style="text-align:right;"> 0.6737 </td>
   <td style="text-align:right;"> 0.2671 </td>
   <td style="text-align:right;"> 0.0002 </td>
   <td style="text-align:left;"> 050204 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00205 </td>
   <td style="text-align:right;"> -0.7474 </td>
   <td style="text-align:right;"> 2.1155 </td>
   <td style="text-align:right;"> 1.2271 </td>
   <td style="text-align:right;"> -0.5838 </td>
   <td style="text-align:right;"> -0.3345 </td>
   <td style="text-align:right;"> -0.5909 </td>
   <td style="text-align:right;"> 0.6937 </td>
   <td style="text-align:right;"> 0.4633 </td>
   <td style="text-align:right;"> 0.2849 </td>
   <td style="text-align:right;"> 0.2107 </td>
   <td style="text-align:right;"> 0.1473 </td>
   <td style="text-align:right;"> 0.0583 </td>
   <td style="text-align:right;"> 0.2794 </td>
   <td style="text-align:right;"> 0.2821 </td>
   <td style="text-align:right;"> 0.0562 </td>
   <td style="text-align:right;"> 0.0067 </td>
   <td style="text-align:right;"> 0.3800 </td>
   <td style="text-align:right;"> 0.6596 </td>
   <td style="text-align:right;"> 0.5014 </td>
   <td style="text-align:right;"> 0.2852 </td>
   <td style="text-align:right;"> 0.9894 </td>
   <td style="text-align:right;"> 0.2309 </td>
   <td style="text-align:right;"> 0.2498 </td>
   <td style="text-align:right;"> 0.0459 </td>
   <td style="text-align:right;"> 0.1016 </td>
   <td style="text-align:right;"> 0.6751 </td>
   <td style="text-align:right;"> 0.4973 </td>
   <td style="text-align:right;"> 0.0001 </td>
   <td style="text-align:left;"> 050205 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00206 </td>
   <td style="text-align:right;"> 0.5157 </td>
   <td style="text-align:right;"> -0.1468 </td>
   <td style="text-align:right;"> -0.1811 </td>
   <td style="text-align:right;"> 1.1894 </td>
   <td style="text-align:right;"> -0.1191 </td>
   <td style="text-align:right;"> -0.4022 </td>
   <td style="text-align:right;"> 0.9563 </td>
   <td style="text-align:right;"> 0.4557 </td>
   <td style="text-align:right;"> 0.2910 </td>
   <td style="text-align:right;"> 0.1814 </td>
   <td style="text-align:right;"> 0.1495 </td>
   <td style="text-align:right;"> 0.0626 </td>
   <td style="text-align:right;"> 0.3793 </td>
   <td style="text-align:right;"> 0.2815 </td>
   <td style="text-align:right;"> 0.0427 </td>
   <td style="text-align:right;"> 0.0052 </td>
   <td style="text-align:right;"> 0.1301 </td>
   <td style="text-align:right;"> 0.8817 </td>
   <td style="text-align:right;"> 0.2565 </td>
   <td style="text-align:right;"> 0.1495 </td>
   <td style="text-align:right;"> 0.9659 </td>
   <td style="text-align:right;"> 0.0629 </td>
   <td style="text-align:right;"> 0.0472 </td>
   <td style="text-align:right;"> 0.0337 </td>
   <td style="text-align:right;"> 0.0835 </td>
   <td style="text-align:right;"> 0.8027 </td>
   <td style="text-align:right;"> 0.2200 </td>
   <td style="text-align:right;"> 0.0001 </td>
   <td style="text-align:left;"> 050206 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00207 </td>
   <td style="text-align:right;"> 1.7368 </td>
   <td style="text-align:right;"> -0.7648 </td>
   <td style="text-align:right;"> -0.4861 </td>
   <td style="text-align:right;"> 0.7170 </td>
   <td style="text-align:right;"> -0.0609 </td>
   <td style="text-align:right;"> 0.0042 </td>
   <td style="text-align:right;"> 0.5201 </td>
   <td style="text-align:right;"> 0.4783 </td>
   <td style="text-align:right;"> 0.2898 </td>
   <td style="text-align:right;"> 0.1675 </td>
   <td style="text-align:right;"> 0.1464 </td>
   <td style="text-align:right;"> 0.0531 </td>
   <td style="text-align:right;"> 0.3552 </td>
   <td style="text-align:right;"> 0.2901 </td>
   <td style="text-align:right;"> 0.0328 </td>
   <td style="text-align:right;"> 0.0061 </td>
   <td style="text-align:right;"> 0.2434 </td>
   <td style="text-align:right;"> 0.5775 </td>
   <td style="text-align:right;"> 0.2758 </td>
   <td style="text-align:right;"> 0.0950 </td>
   <td style="text-align:right;"> 0.9911 </td>
   <td style="text-align:right;"> 0.0717 </td>
   <td style="text-align:right;"> 0.2004 </td>
   <td style="text-align:right;"> 0.1304 </td>
   <td style="text-align:right;"> 0.0714 </td>
   <td style="text-align:right;"> 0.7778 </td>
   <td style="text-align:right;"> 0.3936 </td>
   <td style="text-align:right;"> 0.0001 </td>
   <td style="text-align:left;"> 050207 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00208 </td>
   <td style="text-align:right;"> -0.5942 </td>
   <td style="text-align:right;"> 0.3212 </td>
   <td style="text-align:right;"> -0.1697 </td>
   <td style="text-align:right;"> -0.3627 </td>
   <td style="text-align:right;"> -0.3044 </td>
   <td style="text-align:right;"> -0.4750 </td>
   <td style="text-align:right;"> 0.6625 </td>
   <td style="text-align:right;"> 0.4334 </td>
   <td style="text-align:right;"> 0.2943 </td>
   <td style="text-align:right;"> 0.1875 </td>
   <td style="text-align:right;"> 0.1523 </td>
   <td style="text-align:right;"> 0.0654 </td>
   <td style="text-align:right;"> 0.3557 </td>
   <td style="text-align:right;"> 0.2486 </td>
   <td style="text-align:right;"> 0.0250 </td>
   <td style="text-align:right;"> 0.0054 </td>
   <td style="text-align:right;"> 0.1908 </td>
   <td style="text-align:right;"> 0.8251 </td>
   <td style="text-align:right;"> 0.4152 </td>
   <td style="text-align:right;"> 0.1450 </td>
   <td style="text-align:right;"> 0.9907 </td>
   <td style="text-align:right;"> 0.1458 </td>
   <td style="text-align:right;"> 0.1517 </td>
   <td style="text-align:right;"> 0.0852 </td>
   <td style="text-align:right;"> 0.0509 </td>
   <td style="text-align:right;"> 0.6897 </td>
   <td style="text-align:right;"> 0.3051 </td>
   <td style="text-align:right;"> 0.0001 </td>
   <td style="text-align:left;"> 050208 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00209 </td>
   <td style="text-align:right;"> -1.5280 </td>
   <td style="text-align:right;"> 3.0192 </td>
   <td style="text-align:right;"> 1.9428 </td>
   <td style="text-align:right;"> -0.8078 </td>
   <td style="text-align:right;"> -0.4046 </td>
   <td style="text-align:right;"> -0.6423 </td>
   <td style="text-align:right;"> 0.6798 </td>
   <td style="text-align:right;"> 0.4311 </td>
   <td style="text-align:right;"> 0.2858 </td>
   <td style="text-align:right;"> 0.1687 </td>
   <td style="text-align:right;"> 0.1628 </td>
   <td style="text-align:right;"> 0.0701 </td>
   <td style="text-align:right;"> 0.3648 </td>
   <td style="text-align:right;"> 0.2645 </td>
   <td style="text-align:right;"> 0.0752 </td>
   <td style="text-align:right;"> 0.0061 </td>
   <td style="text-align:right;"> 0.1893 </td>
   <td style="text-align:right;"> 0.5760 </td>
   <td style="text-align:right;"> 0.4096 </td>
   <td style="text-align:right;"> 0.3557 </td>
   <td style="text-align:right;"> 0.9978 </td>
   <td style="text-align:right;"> 0.1097 </td>
   <td style="text-align:right;"> 0.0941 </td>
   <td style="text-align:right;"> 0.0292 </td>
   <td style="text-align:right;"> 0.1357 </td>
   <td style="text-align:right;"> 0.7680 </td>
   <td style="text-align:right;"> 0.2189 </td>
   <td style="text-align:right;"> 0.0001 </td>
   <td style="text-align:left;"> 050209 </td>
  </tr>
</tbody>
</table>
  
  2.    Seleccionar las variables del modelo y crear matriz de covariables.
  

```r
names_cov <-
  c(
    "dam2",
    "tasa_desocupacion",
    "hacinamiento",
    "piso_tierra",
    "luces_nocturnas",
    "cubrimiento_cultivo",
    "modificacion_humana"
  )
X_pred <-
  anti_join(statelevel_predictors_df %>% select(all_of(names_cov)),
            indicador_dam1 %>% select(dam2))
```

  En el bloque de código se identifican que dominios serán los predichos.  

```r
X_pred %>% select(dam2) %>% 
  saveRDS(file = "Recursos/Día5/Sesion1/Data/dam_pred.rds")
```

  Creando la matriz de covariables para los dominios no observados (`X_pred`) y los observados (`X_obs`)
  

```r
## Obteniendo la matrix 
X_pred %<>%
  data.frame() %>%
  select(-dam2)  %>%  as.matrix()

## Identificando los dominios para realizar estimación del modelo

X_obs <- inner_join(indicador_dam1 %>% select(dam2, id_orden),
                    statelevel_predictors_df %>% select(all_of(names_cov))) %>%
  arrange(id_orden) %>%
  data.frame() %>%
  select(-dam2, -id_orden)  %>%  as.matrix()
```
  
  3. Calculando el n_efectivo y el $\tilde{y}$ 
  

```r
D <- nrow(indicador_dam1)
P <- 3 # Ocupado, desocupado, inactivo.
Y_tilde <- matrix(NA, D, P)
n_tilde <- matrix(NA, D, P)
Y_hat <- matrix(NA, D, P)

# n efectivos ocupado
n_tilde[,1] <- (indicador_dam1$Ocupado*(1 - indicador_dam1$Ocupado))/indicador_dam1$Ocupado_var
Y_tilde[,1] <- n_tilde[,1]* indicador_dam1$Ocupado


# n efectivos desocupado
n_tilde[,2] <- (indicador_dam1$Desocupado*(1 - indicador_dam1$Desocupado))/indicador_dam1$Desocupado_var
Y_tilde[,2] <- n_tilde[,2]* indicador_dam1$Desocupado

# n efectivos Inactivo
n_tilde[,3] <- (indicador_dam1$Inactivo*(1 - indicador_dam1$Inactivo))/indicador_dam1$Inactivo_var
Y_tilde[,3] <- n_tilde[,3]* indicador_dam1$Inactivo
```

  Ahora, validamos la coherencia de los cálculos realizados 
  

```r
ni_hat = rowSums(Y_tilde)
Y_hat[,1] <- ni_hat* indicador_dam1$Ocupado
Y_hat[,2] <- ni_hat* indicador_dam1$Desocupado
Y_hat[,3] <- ni_hat* indicador_dam1$Inactivo
Y_hat <- ceiling(Y_hat)

hat_p <- Y_hat/rowSums(Y_hat)
par(mfrow = c(1,3))
plot(hat_p[,1],indicador_dam1$Ocupado)
abline(a = 0,b=1,col = "red")
plot(hat_p[,2],indicador_dam1$Desocupado)
abline(a = 0,b=1,col = "red")
plot(hat_p[,3],indicador_dam1$Inactivo)
abline(a = 0,b=1,col = "red")
```
  

<img src="Recursos/Día5/Sesion1/0Recursos/theta_ajustado.png" width="900px" height="600px" style="display: block; margin: auto;" />

  4. Compilando el modelo 



```r
X1_obs <- cbind(matrix(1,nrow = D,ncol = 1),X_obs)
K = ncol(X1_obs)
D1 <- nrow(X_pred)
X1_pred <- cbind(matrix(1,nrow = D1,ncol = 1),X_pred)

sample_data <- list(D = D,
                    P = P,
                    K = K,
                    y_tilde = Y_hat,
                    X_obs = X1_obs,
                    X_pred = X1_pred,
                    D1 = D1)


library(rstan)
fit_mcmc2 <- stan(
  file = "Recursos/Día5/Sesion1/Data/modelosStan/00 Multinomial_simple_no_cor.stan",  # Stan program
  data = sample_data,    # named list of data
  verbose = TRUE,
  warmup = 1000,          # number of warmup iterations per chain
  iter = 2000,            # total number of iterations per chain
  cores = 4,              # number of cores (could use one per chain)
)

saveRDS(fit_mcmc2,
        "Recursos/Día5/Sesion1/Data/fit_multinomial_no_cor.Rds")
```


## Validación del modelo 

La validación de un modelo es esencial para evaluar su capacidad para predecir de manera precisa y confiable los resultados futuros. En el caso de un modelo de área con respuesta multinomial, la validación se enfoca en medir la precisión del modelo para predecir las diferentes categorías de respuesta. El objetivo principal de la validación es determinar si el modelo es capaz de generalizar bien a datos no vistos y proporcionar predicciones precisas. Esto implica comparar las predicciones del modelo con los datos observados y utilizar métricas de evaluación para medir el rendimiento del modelo. La validación del modelo es esencial para garantizar la calidad de las predicciones y la confiabilidad del modelo para su uso en aplicaciones futuras.


```r
infile <- paste0("Recursos/Día5/Sesion1/Data/fit_multinomial_no_cor.Rds")
fit <- readRDS(infile)

theta_dir <- indicador_dam1 %>%  
  transmute(dam2,
    n = n_desocupado + n_ocupado + n_inactivo,
            Ocupado, Desocupado, Inactivo) 

color_scheme_set("brightblue")
theme_set(theme_bw(base_size = 15))
y_pred_B <- as.array(fit, pars = "theta") %>%
  as_draws_matrix()
  
rowsrandom <- sample(nrow(y_pred_B), 100)

theta_1<-  grep(pattern = "1]",x = colnames(y_pred_B),value = TRUE)
theta_2<-  grep(pattern = "2]",x = colnames(y_pred_B),value = TRUE)
theta_3<-  grep(pattern = "3]",x = colnames(y_pred_B),value = TRUE)
y_pred1 <- y_pred_B[rowsrandom,theta_1 ]
y_pred2 <- y_pred_B[rowsrandom,theta_2 ]
y_pred3 <- y_pred_B[rowsrandom,theta_3 ]

ppc_dens_overlay(y = as.numeric(theta_dir$Ocupado), y_pred1)/
  ppc_dens_overlay(y = as.numeric(theta_dir$Desocupado), y_pred2)/
  ppc_dens_overlay(y = as.numeric(theta_dir$Inactivo), y_pred3)
```


<img src="Recursos/Día5/Sesion1/0Recursos/ppc.png" width="900px" height="600px" style="display: block; margin: auto;" />

## Estimación de los parámetros. 

El código crea dos matrices, `theta_obs_ordenado` y `theta_pred_ordenado`, que contienen las estimaciones medias de los parámetros del modelo de respuesta multinomial con covariables para los datos de observación y predicción, respectivamente. La función `matrix()` se utiliza para dar formato a los datos con una matriz `nrow` x `ncol`, y se asignan nombres de columna apropiados a la matriz resultante utilizando `colnames()`. Luego se convierten las matrices en marcos de datos (`as.data.frame()`) y se unen mediante `full_join()` para crear una única tabla que contenga todas las estimaciones de los parámetros para los datos de observación y predicción, junto con la información del indicador de área (theta_dir). El resultado final es un marco de datos llamado estimaciones_obs.


```r
dam_pred <- readRDS("Recursos/Día5/Sesion1/Data/dam_pred.rds")
P <- 3 
D <- nrow(indicador_dam1)
D1 <- nrow(dam_pred)
## Estimación del modelo. 
theta_obs <- summary(fit, pars = "theta")$summary[, "mean"]
theta_pred <- summary(fit, pars = "theta_pred")$summary[, "mean"]

## Ordenando la matrix de theta 
theta_obs_ordenado <- matrix(theta_obs, 
                             nrow = D,
                             ncol = P,byrow = TRUE) 

colnames(theta_obs_ordenado) <- c("Ocupado_mod", "Desocupado_mod", "Inactivo_mod")
theta_obs_ordenado%<>% as.data.frame()
theta_obs_ordenado <- cbind(dam2 = indicador_dam1$dam2,
                            theta_obs_ordenado)

theta_pred_ordenado <- matrix(theta_pred, 
                             nrow = D1,
                             ncol = P,byrow = TRUE)

colnames(theta_pred_ordenado) <- c("Ocupado_mod", "Desocupado_mod", "Inactivo_mod")
theta_pred_ordenado%<>% as.data.frame()
theta_pred_ordenado <- cbind(dam2 = dam_pred$dam2, theta_pred_ordenado)
```

## Estimación de la desviación estárdar y el coeficiente de valiación 

Este bloque de código corresponde al cálculo de las desviaciones estándar (sd) y coeficientes de variación (cv) de los parámetros `theta` para los datos observados y predichos. En primer lugar, se utiliza la función `summary()` del paquete `rstan` para extraer los valores de `sd` de los parámetros `theta` observados y predichos, respectivamente, a partir del modelo (`fit`) que contiene la información de la estimación de los parámetros de la distribución Bayesiana. Luego, se organizan los valores de sd en una matriz ordenada por `dam2` y se les asignan los nombres correspondientes. Con esta matriz, se calcula otra matriz que contiene los coeficientes de variación para los parámetros `theta` observados (`theta_obs_ordenado_cv`). De manera similar, se construyen matrices ordenadas por `dam2` para los valores de sd y cv de los parámetros theta predichos (`theta_pred_ordenado_sd` y `theta_pred_ordenado_cv`, respectivamente).



```r
theta_obs_sd <- summary(fit, pars = "theta")$summary[, "sd"]
theta_pred_sd <- summary(fit, pars = "theta_pred")$summary[, "sd"]

theta_obs_ordenado_sd <- matrix(theta_obs_sd, 
                             nrow = D,
                             ncol = P,byrow = TRUE) 

colnames(theta_obs_ordenado_sd) <- c("Ocupado_mod_sd", "Desocupado_mod_sd", "Inactivo_mod_sd")
theta_obs_ordenado_sd%<>% as.data.frame()
theta_obs_ordenado_sd <- cbind(dam2 = indicador_dam1$dam2,
                            theta_obs_ordenado_sd)
theta_obs_ordenado_cv <- theta_obs_ordenado_sd[,-1]/theta_obs_ordenado[,-1]

colnames(theta_obs_ordenado_cv) <- c("Ocupado_mod_cv", "Desocupado_mod_cv", "Inactivo_mod_cv")

theta_obs_ordenado_cv <- cbind(dam2 = indicador_dam1$dam2,
                               theta_obs_ordenado_cv)

theta_pred_ordenado_sd <- matrix(theta_pred_sd, 
                              nrow = D1,
                              ncol = P,byrow = TRUE)

colnames(theta_pred_ordenado_sd) <- c("Ocupado_mod_sd", "Desocupado_mod_sd", "Inactivo_mod_sd")
theta_pred_ordenado_sd%<>% as.data.frame()
theta_pred_ordenado_sd <- cbind(dam2 = dam_pred$dam2, theta_pred_ordenado_sd)

theta_pred_ordenado_cv <- theta_pred_ordenado_sd[,-1]/theta_pred_ordenado[,-1]

colnames(theta_pred_ordenado_cv) <- c("Ocupado_mod_cv", "Desocupado_mod_cv", "Inactivo_mod_cv")

theta_pred_ordenado_cv <- cbind(dam2 = dam_pred$dam2, theta_pred_ordenado_cv)
```

El último paso es realizar la consolidación de la bases obtenidas para la estimación puntual, desviación estándar y coeficiente de variación. 


```r
theta_obs_ordenado <- full_join(theta_obs_ordenado,theta_obs_ordenado_sd) %>% 
  full_join(theta_obs_ordenado_cv)

theta_pred_ordenado <- full_join(theta_pred_ordenado,theta_pred_ordenado_sd) %>% 
  full_join(theta_pred_ordenado_cv)


estimaciones <- full_join(indicador_dam1,
                              bind_rows(theta_obs_ordenado, theta_pred_ordenado))

saveRDS(object = estimaciones, file = "Recursos/Día5/Sesion1/Data/estimaciones.rds")
tba(head(estimaciones,10))
```

<table class="table table-striped lightable-classic" style="width: auto !important; margin-left: auto; margin-right: auto; font-family: Arial Narrow; width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> dam2 </th>
   <th style="text-align:right;"> n_upm </th>
   <th style="text-align:right;"> n_ocupado </th>
   <th style="text-align:right;"> n_desocupado </th>
   <th style="text-align:right;"> n_inactivo </th>
   <th style="text-align:right;"> Ocupado </th>
   <th style="text-align:right;"> Ocupado_se </th>
   <th style="text-align:right;"> Ocupado_var </th>
   <th style="text-align:right;"> Ocupado_deff </th>
   <th style="text-align:right;"> Desocupado </th>
   <th style="text-align:right;"> Desocupado_se </th>
   <th style="text-align:right;"> Desocupado_var </th>
   <th style="text-align:right;"> Desocupado_deff </th>
   <th style="text-align:right;"> Inactivo </th>
   <th style="text-align:right;"> Inactivo_se </th>
   <th style="text-align:right;"> Inactivo_var </th>
   <th style="text-align:right;"> Inactivo_deff </th>
   <th style="text-align:right;"> id_orden </th>
   <th style="text-align:right;"> Ocupado_mod </th>
   <th style="text-align:right;"> Desocupado_mod </th>
   <th style="text-align:right;"> Inactivo_mod </th>
   <th style="text-align:right;"> Ocupado_mod_sd </th>
   <th style="text-align:right;"> Desocupado_mod_sd </th>
   <th style="text-align:right;"> Inactivo_mod_sd </th>
   <th style="text-align:right;"> Ocupado_mod_cv </th>
   <th style="text-align:right;"> Desocupado_mod_cv </th>
   <th style="text-align:right;"> Inactivo_mod_cv </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:right;"> 127 </td>
   <td style="text-align:right;"> 2953 </td>
   <td style="text-align:right;"> 284 </td>
   <td style="text-align:right;"> 2439 </td>
   <td style="text-align:right;"> 0.5210 </td>
   <td style="text-align:right;"> 0.0117 </td>
   <td style="text-align:right;"> 1e-04 </td>
   <td style="text-align:right;"> 3.1326 </td>
   <td style="text-align:right;"> 0.0503 </td>
   <td style="text-align:right;"> 0.0050 </td>
   <td style="text-align:right;"> 0e+00 </td>
   <td style="text-align:right;"> 2.9903 </td>
   <td style="text-align:right;"> 0.4287 </td>
   <td style="text-align:right;"> 0.0119 </td>
   <td style="text-align:right;"> 0.0001 </td>
   <td style="text-align:right;"> 3.3118 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.5217 </td>
   <td style="text-align:right;"> 0.0496 </td>
   <td style="text-align:right;"> 0.4287 </td>
   <td style="text-align:right;"> 0.0115 </td>
   <td style="text-align:right;"> 0.0051 </td>
   <td style="text-align:right;"> 0.0114 </td>
   <td style="text-align:right;"> 0.0221 </td>
   <td style="text-align:right;"> 0.1019 </td>
   <td style="text-align:right;"> 0.0266 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03201 </td>
   <td style="text-align:right;"> 109 </td>
   <td style="text-align:right;"> 2841 </td>
   <td style="text-align:right;"> 240 </td>
   <td style="text-align:right;"> 2524 </td>
   <td style="text-align:right;"> 0.5018 </td>
   <td style="text-align:right;"> 0.0128 </td>
   <td style="text-align:right;"> 2e-04 </td>
   <td style="text-align:right;"> 3.6801 </td>
   <td style="text-align:right;"> 0.0481 </td>
   <td style="text-align:right;"> 0.0045 </td>
   <td style="text-align:right;"> 0e+00 </td>
   <td style="text-align:right;"> 2.5120 </td>
   <td style="text-align:right;"> 0.4501 </td>
   <td style="text-align:right;"> 0.0122 </td>
   <td style="text-align:right;"> 0.0001 </td>
   <td style="text-align:right;"> 3.3802 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 0.5035 </td>
   <td style="text-align:right;"> 0.0477 </td>
   <td style="text-align:right;"> 0.4488 </td>
   <td style="text-align:right;"> 0.0122 </td>
   <td style="text-align:right;"> 0.0052 </td>
   <td style="text-align:right;"> 0.0121 </td>
   <td style="text-align:right;"> 0.0243 </td>
   <td style="text-align:right;"> 0.1095 </td>
   <td style="text-align:right;"> 0.0269 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02501 </td>
   <td style="text-align:right;"> 87 </td>
   <td style="text-align:right;"> 3059 </td>
   <td style="text-align:right;"> 121 </td>
   <td style="text-align:right;"> 2133 </td>
   <td style="text-align:right;"> 0.5719 </td>
   <td style="text-align:right;"> 0.0111 </td>
   <td style="text-align:right;"> 1e-04 </td>
   <td style="text-align:right;"> 2.7180 </td>
   <td style="text-align:right;"> 0.0226 </td>
   <td style="text-align:right;"> 0.0034 </td>
   <td style="text-align:right;"> 0e+00 </td>
   <td style="text-align:right;"> 2.8494 </td>
   <td style="text-align:right;"> 0.4055 </td>
   <td style="text-align:right;"> 0.0115 </td>
   <td style="text-align:right;"> 0.0001 </td>
   <td style="text-align:right;"> 2.9408 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 0.5703 </td>
   <td style="text-align:right;"> 0.0240 </td>
   <td style="text-align:right;"> 0.4057 </td>
   <td style="text-align:right;"> 0.0108 </td>
   <td style="text-align:right;"> 0.0034 </td>
   <td style="text-align:right;"> 0.0106 </td>
   <td style="text-align:right;"> 0.0190 </td>
   <td style="text-align:right;"> 0.1413 </td>
   <td style="text-align:right;"> 0.0262 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03203 </td>
   <td style="text-align:right;"> 59 </td>
   <td style="text-align:right;"> 1953 </td>
   <td style="text-align:right;"> 96 </td>
   <td style="text-align:right;"> 1629 </td>
   <td style="text-align:right;"> 0.5301 </td>
   <td style="text-align:right;"> 0.0121 </td>
   <td style="text-align:right;"> 1e-04 </td>
   <td style="text-align:right;"> 2.1909 </td>
   <td style="text-align:right;"> 0.0263 </td>
   <td style="text-align:right;"> 0.0027 </td>
   <td style="text-align:right;"> 0e+00 </td>
   <td style="text-align:right;"> 1.0520 </td>
   <td style="text-align:right;"> 0.4436 </td>
   <td style="text-align:right;"> 0.0124 </td>
   <td style="text-align:right;"> 0.0002 </td>
   <td style="text-align:right;"> 2.3024 </td>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:right;"> 0.5300 </td>
   <td style="text-align:right;"> 0.0271 </td>
   <td style="text-align:right;"> 0.4428 </td>
   <td style="text-align:right;"> 0.0118 </td>
   <td style="text-align:right;"> 0.0038 </td>
   <td style="text-align:right;"> 0.0118 </td>
   <td style="text-align:right;"> 0.0222 </td>
   <td style="text-align:right;"> 0.1401 </td>
   <td style="text-align:right;"> 0.0266 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03202 </td>
   <td style="text-align:right;"> 42 </td>
   <td style="text-align:right;"> 1050 </td>
   <td style="text-align:right;"> 51 </td>
   <td style="text-align:right;"> 883 </td>
   <td style="text-align:right;"> 0.5290 </td>
   <td style="text-align:right;"> 0.0131 </td>
   <td style="text-align:right;"> 2e-04 </td>
   <td style="text-align:right;"> 1.3768 </td>
   <td style="text-align:right;"> 0.0229 </td>
   <td style="text-align:right;"> 0.0054 </td>
   <td style="text-align:right;"> 0e+00 </td>
   <td style="text-align:right;"> 2.5923 </td>
   <td style="text-align:right;"> 0.4481 </td>
   <td style="text-align:right;"> 0.0137 </td>
   <td style="text-align:right;"> 0.0002 </td>
   <td style="text-align:right;"> 1.5230 </td>
   <td style="text-align:right;"> 5 </td>
   <td style="text-align:right;"> 0.5267 </td>
   <td style="text-align:right;"> 0.0241 </td>
   <td style="text-align:right;"> 0.4493 </td>
   <td style="text-align:right;"> 0.0133 </td>
   <td style="text-align:right;"> 0.0041 </td>
   <td style="text-align:right;"> 0.0132 </td>
   <td style="text-align:right;"> 0.0252 </td>
   <td style="text-align:right;"> 0.1693 </td>
   <td style="text-align:right;"> 0.0294 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01101 </td>
   <td style="text-align:right;"> 38 </td>
   <td style="text-align:right;"> 1203 </td>
   <td style="text-align:right;"> 114 </td>
   <td style="text-align:right;"> 713 </td>
   <td style="text-align:right;"> 0.6092 </td>
   <td style="text-align:right;"> 0.0227 </td>
   <td style="text-align:right;"> 5e-04 </td>
   <td style="text-align:right;"> 4.4247 </td>
   <td style="text-align:right;"> 0.0575 </td>
   <td style="text-align:right;"> 0.0075 </td>
   <td style="text-align:right;"> 1e-04 </td>
   <td style="text-align:right;"> 2.1246 </td>
   <td style="text-align:right;"> 0.3333 </td>
   <td style="text-align:right;"> 0.0227 </td>
   <td style="text-align:right;"> 0.0005 </td>
   <td style="text-align:right;"> 4.7511 </td>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:right;"> 0.5973 </td>
   <td style="text-align:right;"> 0.0605 </td>
   <td style="text-align:right;"> 0.3422 </td>
   <td style="text-align:right;"> 0.0209 </td>
   <td style="text-align:right;"> 0.0102 </td>
   <td style="text-align:right;"> 0.0203 </td>
   <td style="text-align:right;"> 0.0349 </td>
   <td style="text-align:right;"> 0.1681 </td>
   <td style="text-align:right;"> 0.0594 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03206 </td>
   <td style="text-align:right;"> 32 </td>
   <td style="text-align:right;"> 837 </td>
   <td style="text-align:right;"> 51 </td>
   <td style="text-align:right;"> 728 </td>
   <td style="text-align:right;"> 0.5022 </td>
   <td style="text-align:right;"> 0.0177 </td>
   <td style="text-align:right;"> 3e-04 </td>
   <td style="text-align:right;"> 2.0370 </td>
   <td style="text-align:right;"> 0.0315 </td>
   <td style="text-align:right;"> 0.0080 </td>
   <td style="text-align:right;"> 1e-04 </td>
   <td style="text-align:right;"> 3.3819 </td>
   <td style="text-align:right;"> 0.4663 </td>
   <td style="text-align:right;"> 0.0170 </td>
   <td style="text-align:right;"> 0.0003 </td>
   <td style="text-align:right;"> 1.8859 </td>
   <td style="text-align:right;"> 7 </td>
   <td style="text-align:right;"> 0.5007 </td>
   <td style="text-align:right;"> 0.0319 </td>
   <td style="text-align:right;"> 0.4674 </td>
   <td style="text-align:right;"> 0.0170 </td>
   <td style="text-align:right;"> 0.0060 </td>
   <td style="text-align:right;"> 0.0169 </td>
   <td style="text-align:right;"> 0.0339 </td>
   <td style="text-align:right;"> 0.1879 </td>
   <td style="text-align:right;"> 0.0361 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00901 </td>
   <td style="text-align:right;"> 20 </td>
   <td style="text-align:right;"> 744 </td>
   <td style="text-align:right;"> 83 </td>
   <td style="text-align:right;"> 530 </td>
   <td style="text-align:right;"> 0.5492 </td>
   <td style="text-align:right;"> 0.0194 </td>
   <td style="text-align:right;"> 4e-04 </td>
   <td style="text-align:right;"> 2.0806 </td>
   <td style="text-align:right;"> 0.0595 </td>
   <td style="text-align:right;"> 0.0113 </td>
   <td style="text-align:right;"> 1e-04 </td>
   <td style="text-align:right;"> 3.1142 </td>
   <td style="text-align:right;"> 0.3913 </td>
   <td style="text-align:right;"> 0.0208 </td>
   <td style="text-align:right;"> 0.0004 </td>
   <td style="text-align:right;"> 2.4841 </td>
   <td style="text-align:right;"> 8 </td>
   <td style="text-align:right;"> 0.5487 </td>
   <td style="text-align:right;"> 0.0578 </td>
   <td style="text-align:right;"> 0.3935 </td>
   <td style="text-align:right;"> 0.0192 </td>
   <td style="text-align:right;"> 0.0090 </td>
   <td style="text-align:right;"> 0.0192 </td>
   <td style="text-align:right;"> 0.0349 </td>
   <td style="text-align:right;"> 0.1552 </td>
   <td style="text-align:right;"> 0.0487 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01301 </td>
   <td style="text-align:right;"> 20 </td>
   <td style="text-align:right;"> 738 </td>
   <td style="text-align:right;"> 45 </td>
   <td style="text-align:right;"> 552 </td>
   <td style="text-align:right;"> 0.5359 </td>
   <td style="text-align:right;"> 0.0244 </td>
   <td style="text-align:right;"> 6e-04 </td>
   <td style="text-align:right;"> 3.2173 </td>
   <td style="text-align:right;"> 0.0351 </td>
   <td style="text-align:right;"> 0.0049 </td>
   <td style="text-align:right;"> 0e+00 </td>
   <td style="text-align:right;"> 0.9599 </td>
   <td style="text-align:right;"> 0.4290 </td>
   <td style="text-align:right;"> 0.0262 </td>
   <td style="text-align:right;"> 0.0007 </td>
   <td style="text-align:right;"> 3.7629 </td>
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:right;"> 0.5356 </td>
   <td style="text-align:right;"> 0.0363 </td>
   <td style="text-align:right;"> 0.4281 </td>
   <td style="text-align:right;"> 0.0226 </td>
   <td style="text-align:right;"> 0.0082 </td>
   <td style="text-align:right;"> 0.0228 </td>
   <td style="text-align:right;"> 0.0423 </td>
   <td style="text-align:right;"> 0.2257 </td>
   <td style="text-align:right;"> 0.0532 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02101 </td>
   <td style="text-align:right;"> 20 </td>
   <td style="text-align:right;"> 505 </td>
   <td style="text-align:right;"> 52 </td>
   <td style="text-align:right;"> 460 </td>
   <td style="text-align:right;"> 0.4965 </td>
   <td style="text-align:right;"> 0.0258 </td>
   <td style="text-align:right;"> 7e-04 </td>
   <td style="text-align:right;"> 2.7259 </td>
   <td style="text-align:right;"> 0.0514 </td>
   <td style="text-align:right;"> 0.0116 </td>
   <td style="text-align:right;"> 1e-04 </td>
   <td style="text-align:right;"> 2.8329 </td>
   <td style="text-align:right;"> 0.4521 </td>
   <td style="text-align:right;"> 0.0332 </td>
   <td style="text-align:right;"> 0.0011 </td>
   <td style="text-align:right;"> 4.5559 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> 0.4993 </td>
   <td style="text-align:right;"> 0.0491 </td>
   <td style="text-align:right;"> 0.4516 </td>
   <td style="text-align:right;"> 0.0261 </td>
   <td style="text-align:right;"> 0.0114 </td>
   <td style="text-align:right;"> 0.0263 </td>
   <td style="text-align:right;"> 0.0523 </td>
   <td style="text-align:right;"> 0.2313 </td>
   <td style="text-align:right;"> 0.0582 </td>
  </tr>
</tbody>
</table>

## Metodología de Benchmarking 

  1. Conteos de personas agregados por dam2, personas mayores de 15 años de edad. 
  

```r
conteo_pp_dam <- readRDS("Recursos/Día5/Sesion1/Data/censo_mrp_dam2.rds") %>%
  filter(edad > 1)  %>% 
  group_by(dam , dam2) %>% 
  summarise(pp_dam2 = sum(n),.groups = "drop")

conteo_pp_dam <- inner_join(conteo_pp_dam,region) %>% 
   group_by(region) %>% 
mutate(pp_region = sum(pp_dam2))

head(conteo_pp_dam) %>% tba()
```

<table class="table table-striped lightable-classic" style="width: auto !important; margin-left: auto; margin-right: auto; font-family: Arial Narrow; width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> dam </th>
   <th style="text-align:left;"> dam2 </th>
   <th style="text-align:right;"> pp_dam2 </th>
   <th style="text-align:left;"> region </th>
   <th style="text-align:right;"> pp_region </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:right;"> 717099 </td>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:right;"> 2366172 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> 00201 </td>
   <td style="text-align:right;"> 61287 </td>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:right;"> 697131 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> 00202 </td>
   <td style="text-align:right;"> 7470 </td>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:right;"> 697131 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> 00203 </td>
   <td style="text-align:right;"> 11799 </td>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:right;"> 697131 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> 00204 </td>
   <td style="text-align:right;"> 13547 </td>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:right;"> 697131 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> 00205 </td>
   <td style="text-align:right;"> 10697 </td>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:right;"> 697131 </td>
  </tr>
</tbody>
</table>

  2.    Estimación del parámetro `theta` al nivel que la encuesta sea representativa.
  

```r
indicador_agregado <-
  diseno %>% group_by(region) %>% 
  filter(empleo %in% c(1:3)) %>%
  summarise(
    Ocupado = survey_ratio(numerator = (empleo == 1), 
                           denominator = 1 ),
    Desocupado = survey_ratio(numerator =( empleo == 2),denominator = 1
                             
    ),
    Inactivo = survey_ratio(numerator =  (empleo == 3), denominator = 1
                           
    )
  ) %>% select(region,Ocupado,Desocupado, Inactivo)

tba(indicador_agregado)
```

<table class="table table-striped lightable-classic" style="width: auto !important; margin-left: auto; margin-right: auto; font-family: Arial Narrow; width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> region </th>
   <th style="text-align:right;"> Ocupado </th>
   <th style="text-align:right;"> Desocupado </th>
   <th style="text-align:right;"> Inactivo </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:right;"> 0.5537 </td>
   <td style="text-align:right;"> 0.0236 </td>
   <td style="text-align:right;"> 0.4227 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:right;"> 0.5492 </td>
   <td style="text-align:right;"> 0.0349 </td>
   <td style="text-align:right;"> 0.4159 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:right;"> 0.5236 </td>
   <td style="text-align:right;"> 0.0855 </td>
   <td style="text-align:right;"> 0.3909 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:right;"> 0.5136 </td>
   <td style="text-align:right;"> 0.0337 </td>
   <td style="text-align:right;"> 0.4526 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:right;"> 0.4623 </td>
   <td style="text-align:right;"> 0.0423 </td>
   <td style="text-align:right;"> 0.4954 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:right;"> 0.4999 </td>
   <td style="text-align:right;"> 0.0298 </td>
   <td style="text-align:right;"> 0.4703 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 07 </td>
   <td style="text-align:right;"> 0.5295 </td>
   <td style="text-align:right;"> 0.0174 </td>
   <td style="text-align:right;"> 0.4532 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 08 </td>
   <td style="text-align:right;"> 0.5639 </td>
   <td style="text-align:right;"> 0.0678 </td>
   <td style="text-align:right;"> 0.3683 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 09 </td>
   <td style="text-align:right;"> 0.5257 </td>
   <td style="text-align:right;"> 0.0505 </td>
   <td style="text-align:right;"> 0.4238 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:right;"> 0.5144 </td>
   <td style="text-align:right;"> 0.0402 </td>
   <td style="text-align:right;"> 0.4453 </td>
  </tr>
</tbody>
</table>

Organizando la salida como un vector. 

```r
temp <-
  gather(indicador_agregado, key = "agregado", value = "estimacion", -region) %>%
  mutate(nombre = paste0("region_", region,"_", agregado))

Razon_empleo <- setNames(temp$estimacion, temp$nombre)
```

  
  3.  Definir los pesos por dominios. 
  

```r
names_cov <-  "region"
estimaciones_mod <- estimaciones %>% transmute(
  region,
  dam2,Ocupado_mod,Desocupado_mod,Inactivo_mod) %>% 
  inner_join(conteo_pp_dam ) %>% 
  mutate(wi = pp_dam2/pp_region)
```
  
  4. Crear variables dummys 
  

```r
estimaciones_mod %<>%
  fastDummies::dummy_cols(select_columns = names_cov,
                          remove_selected_columns = FALSE)

Xdummy <- estimaciones_mod %>% select(matches("region_")) %>% 
  mutate_at(vars(matches("_\\d")) ,
            list(Ocupado = function(x) x*estimaciones_mod$Ocupado_mod,
                 Desocupado = function(x) x*estimaciones_mod$Desocupado_mod,
                 Inactivo = function(x) x*estimaciones_mod$Inactivo_mod)) %>% 
  select((matches("Ocupado|Desocupado|Inactivo"))) 
head(Xdummy) %>% tba()
```

<table class="table table-striped lightable-classic" style="width: auto !important; margin-left: auto; margin-right: auto; font-family: Arial Narrow; width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:right;"> region_01_Ocupado </th>
   <th style="text-align:right;"> region_02_Ocupado </th>
   <th style="text-align:right;"> region_03_Ocupado </th>
   <th style="text-align:right;"> region_04_Ocupado </th>
   <th style="text-align:right;"> region_05_Ocupado </th>
   <th style="text-align:right;"> region_06_Ocupado </th>
   <th style="text-align:right;"> region_07_Ocupado </th>
   <th style="text-align:right;"> region_08_Ocupado </th>
   <th style="text-align:right;"> region_09_Ocupado </th>
   <th style="text-align:right;"> region_10_Ocupado </th>
   <th style="text-align:right;"> region_01_Desocupado </th>
   <th style="text-align:right;"> region_02_Desocupado </th>
   <th style="text-align:right;"> region_03_Desocupado </th>
   <th style="text-align:right;"> region_04_Desocupado </th>
   <th style="text-align:right;"> region_05_Desocupado </th>
   <th style="text-align:right;"> region_06_Desocupado </th>
   <th style="text-align:right;"> region_07_Desocupado </th>
   <th style="text-align:right;"> region_08_Desocupado </th>
   <th style="text-align:right;"> region_09_Desocupado </th>
   <th style="text-align:right;"> region_10_Desocupado </th>
   <th style="text-align:right;"> region_01_Inactivo </th>
   <th style="text-align:right;"> region_02_Inactivo </th>
   <th style="text-align:right;"> region_03_Inactivo </th>
   <th style="text-align:right;"> region_04_Inactivo </th>
   <th style="text-align:right;"> region_05_Inactivo </th>
   <th style="text-align:right;"> region_06_Inactivo </th>
   <th style="text-align:right;"> region_07_Inactivo </th>
   <th style="text-align:right;"> region_08_Inactivo </th>
   <th style="text-align:right;"> region_09_Inactivo </th>
   <th style="text-align:right;"> region_10_Inactivo </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 0.5217 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0.0496 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0.4287 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 0.5035 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0.0477 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0.4488 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 0.5703 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0.0240 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0.4057 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 0.5300 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0.0271 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0.4428 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 0.5267 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0.0241 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0.4493 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 0.5973 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0.0605 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0.3422 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
</tbody>
</table>
  
  5.    Calcular el ponderador para cada nivel de la variable. 
  
#### Ocupado {-}
    

```r
library(sampling)
names_ocupado <- grep(pattern = "_O", x = colnames(Xdummy),value = TRUE)
gk_ocupado <- calib(Xs = Xdummy[,names_ocupado] %>% as.matrix(), 
            d =  estimaciones_mod$wi,
            total = Razon_empleo[names_ocupado] %>% as.matrix(),
            method="linear",max_iter = 5000) 

checkcalibration(Xs = Xdummy[,names_ocupado] %>% as.matrix(), 
                 d =estimaciones_mod$wi,
                 total = Razon_empleo[names_ocupado] %>% as.matrix(),
                 g = gk_ocupado,)
```

#### Desocupado {-} 
    

```r
names_descupados <- grep(pattern = "_D", x = colnames(Xdummy),value = TRUE)

gk_desocupado <- calib(Xs = Xdummy[,names_descupados]%>% as.matrix(), 
                    d =  estimaciones_mod$wi,
                    total = Razon_empleo[names_descupados]%>% as.matrix(),
                    method="linear",max_iter = 5000) 

checkcalibration(Xs = Xdummy[,names_descupados]%>% as.matrix(), 
                 d =estimaciones_mod$wi,
                 total = Razon_empleo[names_descupados]%>% as.matrix(),
                 g = gk_desocupado,)
```

#### Inactivo {-}


```r
names_inactivo <- grep(pattern = "_I", x = colnames(Xdummy),value = TRUE)

gk_Inactivo <- calib(Xs = Xdummy[,names_inactivo]%>% as.matrix(), 
                    d =  estimaciones_mod$wi,
                    total = Razon_empleo[names_inactivo]%>% as.matrix(),
                    method="linear",max_iter = 5000) 

checkcalibration(Xs = Xdummy[,names_inactivo]%>% as.matrix(), 
                 d =estimaciones_mod$wi,
                 total = Razon_empleo[names_inactivo]%>% as.matrix(),
                 g = gk_Inactivo,)
```
  
  6.    Validar los resultados obtenidos. 
  

```r
par(mfrow = c(1,3))
hist(gk_ocupado)
hist(gk_desocupado)
hist(gk_Inactivo)
```


<img src="Recursos/Día5/Sesion1/0Recursos/Plot_Bench_gk.jpeg" width="900px" height="600px" style="display: block; margin: auto;" />


  7.    Estimaciones ajustadas por el ponderador
  

```r
estimacionesBench <- estimaciones_mod %>%
  mutate(gk_ocupado, gk_desocupado, gk_Inactivo) %>%
  transmute(
    region,
    dam,
    dam2,
    wi,gk_ocupado, gk_desocupado, gk_Inactivo,
    Ocupado_Bench = Ocupado_mod*gk_ocupado,
    Desocupado_Bench = Desocupado_mod*gk_desocupado,
    Inactivo_Bench = Inactivo_mod*gk_Inactivo
  )
```

  8.    Validación de resultados. 
  

```r
estimacionesBench %>%
  group_by(region) %>% 
  summarise(Ocupado_Bench = sum(wi*Ocupado_Bench),
            Desocupado_Bench = sum(wi*Desocupado_Bench),
            Inactivo_Bench = sum(wi*Inactivo_Bench)) %>% 
  inner_join(indicador_agregado) %>% tba()
```

<table class="table table-striped lightable-classic" style="width: auto !important; margin-left: auto; margin-right: auto; font-family: Arial Narrow; width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> region </th>
   <th style="text-align:right;"> Ocupado_Bench </th>
   <th style="text-align:right;"> Desocupado_Bench </th>
   <th style="text-align:right;"> Inactivo_Bench </th>
   <th style="text-align:right;"> Ocupado </th>
   <th style="text-align:right;"> Desocupado </th>
   <th style="text-align:right;"> Inactivo </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:right;"> 0.5537 </td>
   <td style="text-align:right;"> 0.0236 </td>
   <td style="text-align:right;"> 0.4227 </td>
   <td style="text-align:right;"> 0.5537 </td>
   <td style="text-align:right;"> 0.0236 </td>
   <td style="text-align:right;"> 0.4227 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:right;"> 0.5492 </td>
   <td style="text-align:right;"> 0.0349 </td>
   <td style="text-align:right;"> 0.4159 </td>
   <td style="text-align:right;"> 0.5492 </td>
   <td style="text-align:right;"> 0.0349 </td>
   <td style="text-align:right;"> 0.4159 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:right;"> 0.5236 </td>
   <td style="text-align:right;"> 0.0855 </td>
   <td style="text-align:right;"> 0.3909 </td>
   <td style="text-align:right;"> 0.5236 </td>
   <td style="text-align:right;"> 0.0855 </td>
   <td style="text-align:right;"> 0.3909 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:right;"> 0.5136 </td>
   <td style="text-align:right;"> 0.0337 </td>
   <td style="text-align:right;"> 0.4526 </td>
   <td style="text-align:right;"> 0.5136 </td>
   <td style="text-align:right;"> 0.0337 </td>
   <td style="text-align:right;"> 0.4526 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:right;"> 0.4623 </td>
   <td style="text-align:right;"> 0.0423 </td>
   <td style="text-align:right;"> 0.4954 </td>
   <td style="text-align:right;"> 0.4623 </td>
   <td style="text-align:right;"> 0.0423 </td>
   <td style="text-align:right;"> 0.4954 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:right;"> 0.4999 </td>
   <td style="text-align:right;"> 0.0298 </td>
   <td style="text-align:right;"> 0.4703 </td>
   <td style="text-align:right;"> 0.4999 </td>
   <td style="text-align:right;"> 0.0298 </td>
   <td style="text-align:right;"> 0.4703 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 07 </td>
   <td style="text-align:right;"> 0.5295 </td>
   <td style="text-align:right;"> 0.0174 </td>
   <td style="text-align:right;"> 0.4532 </td>
   <td style="text-align:right;"> 0.5295 </td>
   <td style="text-align:right;"> 0.0174 </td>
   <td style="text-align:right;"> 0.4532 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 08 </td>
   <td style="text-align:right;"> 0.5639 </td>
   <td style="text-align:right;"> 0.0678 </td>
   <td style="text-align:right;"> 0.3683 </td>
   <td style="text-align:right;"> 0.5639 </td>
   <td style="text-align:right;"> 0.0678 </td>
   <td style="text-align:right;"> 0.3683 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 09 </td>
   <td style="text-align:right;"> 0.5257 </td>
   <td style="text-align:right;"> 0.0505 </td>
   <td style="text-align:right;"> 0.4238 </td>
   <td style="text-align:right;"> 0.5257 </td>
   <td style="text-align:right;"> 0.0505 </td>
   <td style="text-align:right;"> 0.4238 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:right;"> 0.5144 </td>
   <td style="text-align:right;"> 0.0402 </td>
   <td style="text-align:right;"> 0.4453 </td>
   <td style="text-align:right;"> 0.5144 </td>
   <td style="text-align:right;"> 0.0402 </td>
   <td style="text-align:right;"> 0.4453 </td>
  </tr>
</tbody>
</table>


  9. Guardar resultados 
  

```r
estimaciones <- inner_join(estimaciones,estimacionesBench)
saveRDS(object = estimaciones, file = "Recursos/Día5/Sesion1/Data/estimaciones_Bench.rds")
```



## Mapas del mercado de trabajo.

El código carga las librerías `sp`, `sf` y `tmap`. Luego, se lee un archivo shapefile con información geográfica y se utiliza la función 'inner_join' para unirlo con las estimaciones de la encuesta previamente calculadas. Posteriormente, se definen los puntos de corte para la generación de los intervalos de clase en los mapas de cada variable de interés (ocupados, desocupados e inactivos) y se asignan a las variables 'brks_ocupado', 'brks_desocupado' y 'brks_inactivo', respectivamente.


```r
library(sp)
library(sf)
library(tmap)
ShapeSAE <- read_sf("Shape/DOM_dam2.shp") %>% 
  rename(dam2 = id_dominio) %>% 
  mutate(dam2 = str_pad(
                 string = dam2,
                 width = 5,
                 pad = "0"
               ))

P1_empleo <- tm_shape(ShapeSAE %>%
                           inner_join(estimaciones))
brks_ocupado <- seq(0.2,0.8,0.1)
brks_desocupado <- seq(0,0.3,0.05)
brks_inactivo <- seq(0.15,0.65, 0.1)
```

### Ocupado {-}

Este código está creando un mapa de la variable "Ocupado" utilizando la función `tm_fill()` de la librería `tmap.` Los valores de la variable se clasifican en diferentes categorías utilizando la función breaks, y se establece un título para la leyenda del mapa con el argumento title. Se utiliza una paleta de colores llamada "-Blues" para representar las diferentes categorías de la variable en el mapa. La función tm_layout se utiliza para establecer algunas características del diseño del mapa, como el tamaño de la leyenda, el tamaño de la fuente, y la relación de aspecto del mapa. Finalmente, el mapa se guarda en la variable Mapa_ocupado. 


```r
Mapa_ocupado <-
  P1_empleo +
  tm_fill("Ocupado_Bench",
          breaks = brks_ocupado,
          title = "Ocupado",
          palette = "-Blues") +
  tm_layout(
    legend.only = FALSE,
     legend.height = -0.95,
     legend.width = -0.95,
    asp = 2.1,
    legend.text.size = 3,
    legend.title.size = 3
  )
Mapa_ocupado
```


<img src="Recursos/Día5/Sesion1/0Recursos/Ocupados.png" width="900px" height="600px" style="display: block; margin: auto;" />

### Desocupado {-} 

Este código utiliza la función `tm_fill()` de la librería `tmap` para crear un mapa temático del indicador de "desocupado" a nivel de las áreas geográficas definidas en el archivo de polígonos `ShapeSAE`. La paleta de colores utilizada para representar los valores del indicador es la "YlOrRd". Se especifican los mismos parámetros de `tm_layout()` que en el mapa anterior para definir el diseño general del mapa.


```r
Mapa_desocupado <-
  P1_empleo + tm_fill(
    "Desocupado_Bench",
    breaks = brks_desocupado,
    title =  "Desocupado",
    palette = "YlOrRd"
  ) +  tm_layout(
    legend.only = FALSE,
     legend.height = -0.95,
     legend.width = -0.95,
    asp = 2.1,
    legend.text.size = 3,
    legend.title.size = 3
  )
Mapa_desocupado
```


<img src="Recursos/Día5/Sesion1/0Recursos/Desocupados.png" width="900px" height="600px" style="display: block; margin: auto;" />


### Inactivo {-} 

Este código genera un mapa temático de la variable "Inactivo" utilizando la librería `tmap`. Primero se carga el archivo de `shapefile` y se hace una unión con las estimaciones previamente calculadas. Luego se utiliza la función `tm_fill()` para especificar que se desea utilizar el valor de la variable "Inactivo" para el relleno del mapa. Se definen los intervalos de la paleta de colores con la variable "brks_inactivo" y se especifica el título del mapa con la opción "title". Finalmente, se configura el diseño del mapa con la función `tm_layout()`, donde se ajustan parámetros como el tamaño del texto y de la leyenda, y se establece el aspecto del mapa en 1.5 para una mejor visualización.


```r
Mapa_Inactivo <-
  P1_empleo + tm_fill(
      "Inactivo_Bench",
    title =  "Inactivo",
    breaks = brks_inactivo,
    palette = "YlGn"
  ) +  tm_layout(
    legend.only = FALSE,
     legend.height = -0.95,
     legend.width = -0.95,
    asp = 2.1,
    legend.text.size = 3,
    legend.title.size = 3
  )
Mapa_Inactivo
```

<img src="Recursos/Día5/Sesion1/0Recursos/Inactivo.png" width="900px" height="600px" style="display: block; margin: auto;" />

    
  
