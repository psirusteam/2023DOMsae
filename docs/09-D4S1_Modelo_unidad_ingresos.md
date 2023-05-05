
# Día 4 - Sesión 1- Modelo de unidad para la estimación del ingreso medio



Uno de los primeros problemas a los que debemos enfrentarnos es la estimación del ingreso medio, la cual en una variable no simétrica que toma valores en los positivos. Sin embargo, empleando los métodos Bayesiano es posible obtener estimaciones de esta sin realizar una transformación 

<div class="figure">
<img src="09-D4S1_Modelo_unidad_ingresos_files/figure-html/unnamed-chunk-1-1.svg" alt="Distribución del ingreso medio por dam2" width="672" />
<p class="caption">(\#fig:unnamed-chunk-1)Distribución del ingreso medio por dam2</p>
</div>

## Modelo bayesiano.

Para realizar la predicción del ingreso medio en dam2s no observadas se asume que: 

$$
\begin{eqnarray*}
Y_{di} &\sim & N\left(\mu_{di},\sigma_y^{2}\right)\\
\mu_{di}&=&\boldsymbol{x}_{di}^{T}\boldsymbol{\beta}+u_{d}+e_{di} 
\end{eqnarray*}
$$




Donde $Y_{di}$ representa el ingreso medio de la $i-ésima$ persona en el $d-ésimo$ domino, $\boldsymbol{X}$ es la información disponible para la $i-ésima$ persona del $d-ésimo$ domino, $\boldsymbol{\beta}$ es el vector de parámetros $u_d$ es el efecto introducido por el $d-ésimo$ dominio y $e_{di}$ es el error de estimación para la $i-ésima$ personas del $d-ésimo$ dominio.

Note, que $u_{d}\sim N\left(0,\sigma^2_{u}\right)$ y $e_{di}\sim N\left(0,\sigma_{e}^{2}\right)$.

Para este caso se asumen las distribuciones previas

$$
\begin{eqnarray*}
\beta_k & \sim   & N(0, 1000)\\
\sigma^2_y &\sim & IG(0.00203,0.00203)
\end{eqnarray*}
$$ las cuales se toman no informativas.

A continuación se muestra el proceso realizado para la obtención de la predicción del ingreso medio en dominios no observados.

## Proceso de estimación en `R`

Para desarrollar la metodología se hace uso de las siguientes librerías.


```r
# Interprete de STAN en R
library(rstan)
library(rstanarm)
# Manejo de bases de datos.
library(tidyverse)
# Gráficas de los modelos. 
library(bayesplot)
library(patchwork)
# Organizar la presentación de las tablas
library(kableExtra)
library(printr)
```

Un conjunto de funciones desarrolladas para realizar de forma simplificada los procesos están consignadas en la siguiente rutina.


```r
source("Recursos/Día4/Sesion1/0Recursos/funciones_mrp.R")
```

Entre las funciones incluidas en el archivo encuentra

-   *plot_interaction*: Esta crea un diagrama de lineas donde se estudia la interacción entre las variables, en el caso de presentar un traslape de las lineas se recomienda incluir el interacción en el modelo.

-   *Plot_Compare* Puesto que es necesario realizar una homologar la información del censo y la encuesta es conveniente llevar a cabo una validación de las variables que han sido homologadas, por tanto, se espera que las proporciones resultantes del censo y la encuesta estén cercanas entre sí.

-   *Aux_Agregado*: Esta es función permite obtener estimaciones a diferentes niveles de agregación, toma mucha relevancia cuando se realiza un proceso repetitivo.

**Las funciones están diseñada específicamente  para este  proceso**

### Encuesta de hogares

Los datos empleados en esta ocasión corresponden a la ultima encuesta de hogares, la cual ha sido estandarizada por *CEPAL* y se encuentra disponible en *BADEHOG*


```r
encuesta <- readRDS("Recursos/Día4/Sesion1/Data/encuestaDOM21N1.rds")

encuesta_mrp <- encuesta %>% 
  transmute(
  dam = haven::as_factor(dam_ee,levels  = "values"),
  dam = str_pad(string = dam, width = 2, pad = "0"),
  dam2,  
  ingreso = ingcorte, lp, li,
  area = haven::as_factor(area_ee,levels  = "values"),
  area = case_when(area == 1 ~ "1", TRUE ~ "0"),
  logingreso = log(ingcorte + 1),
  sexo = as.character(sexo),
  anoest = case_when(
    edad < 5 | anoest == -1 | is.na(anoest)   ~ "98"  , #No aplica
    anoest == 99 ~ "99", #NS/NR
    anoest == 0  ~ "1", # Sin educacion
    anoest %in% c(1:6) ~ "2",       # 1 - 6
    anoest %in% c(7:12) ~ "3",      # 7 - 12
    anoest > 12 ~ "4",      # mas de 12
    TRUE ~ "Error"  ),
  
  edad = case_when(
    edad < 15 ~ "1",
    edad < 30 ~ "2",
    edad < 45 ~ "3",
    edad < 65 ~ "4",
    TRUE ~ "5"),
  
  fep = `_fep`
) 

tba(encuesta_mrp %>% head(10)) 
```

<table class="table table-striped lightable-classic" style="width: auto !important; margin-left: auto; margin-right: auto; font-family: Arial Narrow; width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> dam </th>
   <th style="text-align:left;"> dam2 </th>
   <th style="text-align:right;"> ingreso </th>
   <th style="text-align:right;"> lp </th>
   <th style="text-align:right;"> li </th>
   <th style="text-align:left;"> area </th>
   <th style="text-align:right;"> logingreso </th>
   <th style="text-align:left;"> sexo </th>
   <th style="text-align:left;"> anoest </th>
   <th style="text-align:left;"> edad </th>
   <th style="text-align:right;"> fep </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:right;"> 54000.00 </td>
   <td style="text-align:right;"> 5622.81 </td>
   <td style="text-align:right;"> 3159.09 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:right;"> 10.8968 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:right;"> 137.0652 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:right;"> 16388.89 </td>
   <td style="text-align:right;"> 5622.81 </td>
   <td style="text-align:right;"> 3159.09 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:right;"> 9.7044 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> 137.0652 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:right;"> 16388.89 </td>
   <td style="text-align:right;"> 5622.81 </td>
   <td style="text-align:right;"> 3159.09 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:right;"> 9.7044 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 5 </td>
   <td style="text-align:right;"> 137.0652 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:right;"> 16388.89 </td>
   <td style="text-align:right;"> 5622.81 </td>
   <td style="text-align:right;"> 3159.09 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:right;"> 9.7044 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> 137.0652 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:right;"> 6224.00 </td>
   <td style="text-align:right;"> 5622.81 </td>
   <td style="text-align:right;"> 3159.09 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:right;"> 8.7363 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:right;"> 137.0652 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:right;"> 6224.00 </td>
   <td style="text-align:right;"> 5622.81 </td>
   <td style="text-align:right;"> 3159.09 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:right;"> 8.7363 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:right;"> 137.0652 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:right;"> 6224.00 </td>
   <td style="text-align:right;"> 5622.81 </td>
   <td style="text-align:right;"> 3159.09 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:right;"> 8.7363 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> 137.0652 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:right;"> 20672.00 </td>
   <td style="text-align:right;"> 5622.81 </td>
   <td style="text-align:right;"> 3159.09 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:right;"> 9.9366 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 5 </td>
   <td style="text-align:right;"> 137.0652 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:right;"> 8495.60 </td>
   <td style="text-align:right;"> 5622.81 </td>
   <td style="text-align:right;"> 3159.09 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:right;"> 9.0474 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:right;"> 137.0652 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:right;"> 8495.60 </td>
   <td style="text-align:right;"> 5622.81 </td>
   <td style="text-align:right;"> 3159.09 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:right;"> 9.0474 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:right;"> 137.0652 </td>
  </tr>
</tbody>
</table>

La base de datos de la encuesta tiene la siguientes columnas: 

-   *dam*: Corresponde al código asignado a la división administrativa mayor del país.

-   *dam2*: Corresponde al código asignado a la segunda división administrativa del país.

-   *lp* y *li* lineas de pobreza y pobreza extrema definidas por CEPAL. 

-   *área* división geográfica (Urbano y Rural). 

-   *sexo* Hombre y Mujer. 

-   Años de escolaridad (*anoest*) 

-   Rangos de edad (*edad*) 

-   Factor de expansión por persona (*fep*)


Ahora, inspeccionamos el comportamiento de la variable de interés: 


```r
media <- mean(encuesta_mrp$logingreso)
Sd <- sd(encuesta_mrp$logingreso)

ggplot(data = encuesta_mrp, aes(x = logingreso)) +
  geom_density(size =2, color = "blue") + labs(y = "") +
  stat_function(fun = dnorm, 
                args = list(mean = media, sd = Sd),
                size =2) +
  theme_bw(base_size = 20) +
  theme(axis.text.y = element_blank(),
        axis.ticks = element_blank())
```

<div class="figure">
<img src="09-D4S1_Modelo_unidad_ingresos_files/figure-html/unnamed-chunk-5-1.svg" alt="Distribuición del ingreso de las personas encuestadas" width="672" />
<p class="caption">(\#fig:unnamed-chunk-5)Distribuición del ingreso de las personas encuestadas</p>
</div>


La información auxiliar disponible ha sido extraída del censo  e imágenes satelitales


```r
statelevel_predictors_df <-
  readRDS("Recursos/Día4/Sesion1/Data/statelevel_predictors_df_dam2.rds") %>% 
    mutate_at(.vars = c("luces_nocturnas",
                      "cubrimiento_cultivo",
                      "cubrimiento_urbano",
                      "modificacion_humana",
                      "accesibilidad_hospitales",
                      "accesibilidad_hosp_caminado"),
            function(x) as.numeric(scale(x)))
tba(statelevel_predictors_df  %>%  head(10))
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


### Niveles de agregación para colapsar la encuesta

Después de realizar una investigación en la literatura especializada y realizar estudios de simulación fue posible evidenciar que las predicciones obtenidas con la muestra sin agregar y la muestra agregada convergen a la media del dominio. Sin embargo, el realizar estas estimaciones con la muestra agregada reduce el tiempo computacional necesario para la convergencia de las cadenas MCMC.  Con esto en mente se se realiza la identificación de las variables por las cuales se agregará la encuesta.


```r
byAgrega <- c("dam", "dam2",  "area", 
              "sexo",   "anoest", "edad" )
```

### Creando base con la encuesta agregada

El resultado de agregar la base de dato se muestra a continuación:


```r
encuesta_df_agg <-
  encuesta_mrp %>%                    # Encuesta  
  group_by_at(all_of(byAgrega)) %>%   # Agrupar por el listado de variables
  summarise(n = n(),                  # Número de observaciones
  # Ingreso medio de las personas con características similares.           
             logingreso = mean(logingreso), 
            .groups = "drop") %>%     
  arrange(desc(n))                    # Ordenar la base.
```

La tabla obtenida es la siguiente: 

<table class="table table-striped lightable-classic" style="width: auto !important; margin-left: auto; margin-right: auto; font-family: Arial Narrow; width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> dam </th>
   <th style="text-align:left;"> dam2 </th>
   <th style="text-align:left;"> area </th>
   <th style="text-align:left;"> sexo </th>
   <th style="text-align:left;"> anoest </th>
   <th style="text-align:left;"> edad </th>
   <th style="text-align:right;"> n </th>
   <th style="text-align:right;"> logingreso </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> 652 </td>
   <td style="text-align:right;"> 9.1000 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> 624 </td>
   <td style="text-align:right;"> 8.8678 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 32 </td>
   <td style="text-align:left;"> 03201 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> 576 </td>
   <td style="text-align:right;"> 8.9571 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 32 </td>
   <td style="text-align:left;"> 03201 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> 548 </td>
   <td style="text-align:right;"> 9.0968 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 25 </td>
   <td style="text-align:left;"> 02501 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> 505 </td>
   <td style="text-align:right;"> 9.2633 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 25 </td>
   <td style="text-align:left;"> 02501 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> 447 </td>
   <td style="text-align:right;"> 9.0084 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:right;"> 412 </td>
   <td style="text-align:right;"> 9.1381 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:right;"> 401 </td>
   <td style="text-align:right;"> 8.8231 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 32 </td>
   <td style="text-align:left;"> 03203 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> 393 </td>
   <td style="text-align:right;"> 8.9166 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 32 </td>
   <td style="text-align:left;"> 03201 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:right;"> 390 </td>
   <td style="text-align:right;"> 9.0601 </td>
  </tr>
</tbody>
</table>
El paso a seguir es unificar las tablas creadas. 


```r
encuesta_df_agg <- inner_join(encuesta_df_agg, statelevel_predictors_df)
```

### Definiendo el modelo multinivel.

Después de haber ordenado la encuesta, podemos pasar a la definición del modelo.


```r
options(MC.cores=parallel::detectCores()) # Permite procesar en paralelo. 
fit <- stan_lmer(
  logingreso ~                               # Ingreso medio (Y)
    (1 | dam2) +                          # Efecto aleatorio (ud)
    edad +                               # Efecto fijo (Variables X)
    sexo  + 
    tasa_desocupacion +
    luces_nocturnas + 
    cubrimiento_cultivo +
    cubrimiento_urbano ,
                  weights = n,            # Número de observaciones. 
                  data = encuesta_df_agg, # Encuesta agregada 
                  verbose = TRUE,         # Muestre el avance del proceso
                  chains = 4,             # Número de cadenas.
                 iter = 1000              # Número de realizaciones de la cadena
                )
saveRDS(fit, file = "Recursos/Día4/Sesion1/Data/fit_ingresos.rds")
```

Después de esperar un tiempo prudente se obtiene el siguiente modelo.


```r
fit <- readRDS("Recursos/Día4/Sesion1/Data/fit_ingresos.rds")
tba(coef(fit)$dam2 %>% head(10))
```

<table class="table table-striped lightable-classic" style="width: auto !important; margin-left: auto; margin-right: auto; font-family: Arial Narrow; width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:right;"> (Intercept) </th>
   <th style="text-align:right;"> edad2 </th>
   <th style="text-align:right;"> edad3 </th>
   <th style="text-align:right;"> edad4 </th>
   <th style="text-align:right;"> edad5 </th>
   <th style="text-align:right;"> sexo2 </th>
   <th style="text-align:right;"> tasa_desocupacion </th>
   <th style="text-align:right;"> luces_nocturnas </th>
   <th style="text-align:right;"> cubrimiento_cultivo </th>
   <th style="text-align:right;"> cubrimiento_urbano </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:right;"> 8.8056 </td>
   <td style="text-align:right;"> 0.2619 </td>
   <td style="text-align:right;"> 0.3041 </td>
   <td style="text-align:right;"> 0.5292 </td>
   <td style="text-align:right;"> 0.5353 </td>
   <td style="text-align:right;"> -0.0924 </td>
   <td style="text-align:right;"> 25.7452 </td>
   <td style="text-align:right;"> 0.1079 </td>
   <td style="text-align:right;"> 0.0256 </td>
   <td style="text-align:right;"> -0.0858 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00201 </td>
   <td style="text-align:right;"> 8.8603 </td>
   <td style="text-align:right;"> 0.2619 </td>
   <td style="text-align:right;"> 0.3041 </td>
   <td style="text-align:right;"> 0.5292 </td>
   <td style="text-align:right;"> 0.5353 </td>
   <td style="text-align:right;"> -0.0924 </td>
   <td style="text-align:right;"> 25.7452 </td>
   <td style="text-align:right;"> 0.1079 </td>
   <td style="text-align:right;"> 0.0256 </td>
   <td style="text-align:right;"> -0.0858 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00202 </td>
   <td style="text-align:right;"> 8.6616 </td>
   <td style="text-align:right;"> 0.2619 </td>
   <td style="text-align:right;"> 0.3041 </td>
   <td style="text-align:right;"> 0.5292 </td>
   <td style="text-align:right;"> 0.5353 </td>
   <td style="text-align:right;"> -0.0924 </td>
   <td style="text-align:right;"> 25.7452 </td>
   <td style="text-align:right;"> 0.1079 </td>
   <td style="text-align:right;"> 0.0256 </td>
   <td style="text-align:right;"> -0.0858 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00203 </td>
   <td style="text-align:right;"> 8.6011 </td>
   <td style="text-align:right;"> 0.2619 </td>
   <td style="text-align:right;"> 0.3041 </td>
   <td style="text-align:right;"> 0.5292 </td>
   <td style="text-align:right;"> 0.5353 </td>
   <td style="text-align:right;"> -0.0924 </td>
   <td style="text-align:right;"> 25.7452 </td>
   <td style="text-align:right;"> 0.1079 </td>
   <td style="text-align:right;"> 0.0256 </td>
   <td style="text-align:right;"> -0.0858 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00204 </td>
   <td style="text-align:right;"> 9.1334 </td>
   <td style="text-align:right;"> 0.2619 </td>
   <td style="text-align:right;"> 0.3041 </td>
   <td style="text-align:right;"> 0.5292 </td>
   <td style="text-align:right;"> 0.5353 </td>
   <td style="text-align:right;"> -0.0924 </td>
   <td style="text-align:right;"> 25.7452 </td>
   <td style="text-align:right;"> 0.1079 </td>
   <td style="text-align:right;"> 0.0256 </td>
   <td style="text-align:right;"> -0.0858 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00205 </td>
   <td style="text-align:right;"> 8.6218 </td>
   <td style="text-align:right;"> 0.2619 </td>
   <td style="text-align:right;"> 0.3041 </td>
   <td style="text-align:right;"> 0.5292 </td>
   <td style="text-align:right;"> 0.5353 </td>
   <td style="text-align:right;"> -0.0924 </td>
   <td style="text-align:right;"> 25.7452 </td>
   <td style="text-align:right;"> 0.1079 </td>
   <td style="text-align:right;"> 0.0256 </td>
   <td style="text-align:right;"> -0.0858 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00206 </td>
   <td style="text-align:right;"> 8.6877 </td>
   <td style="text-align:right;"> 0.2619 </td>
   <td style="text-align:right;"> 0.3041 </td>
   <td style="text-align:right;"> 0.5292 </td>
   <td style="text-align:right;"> 0.5353 </td>
   <td style="text-align:right;"> -0.0924 </td>
   <td style="text-align:right;"> 25.7452 </td>
   <td style="text-align:right;"> 0.1079 </td>
   <td style="text-align:right;"> 0.0256 </td>
   <td style="text-align:right;"> -0.0858 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00208 </td>
   <td style="text-align:right;"> 8.7539 </td>
   <td style="text-align:right;"> 0.2619 </td>
   <td style="text-align:right;"> 0.3041 </td>
   <td style="text-align:right;"> 0.5292 </td>
   <td style="text-align:right;"> 0.5353 </td>
   <td style="text-align:right;"> -0.0924 </td>
   <td style="text-align:right;"> 25.7452 </td>
   <td style="text-align:right;"> 0.1079 </td>
   <td style="text-align:right;"> 0.0256 </td>
   <td style="text-align:right;"> -0.0858 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00210 </td>
   <td style="text-align:right;"> 9.0558 </td>
   <td style="text-align:right;"> 0.2619 </td>
   <td style="text-align:right;"> 0.3041 </td>
   <td style="text-align:right;"> 0.5292 </td>
   <td style="text-align:right;"> 0.5353 </td>
   <td style="text-align:right;"> -0.0924 </td>
   <td style="text-align:right;"> 25.7452 </td>
   <td style="text-align:right;"> 0.1079 </td>
   <td style="text-align:right;"> 0.0256 </td>
   <td style="text-align:right;"> -0.0858 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00301 </td>
   <td style="text-align:right;"> 8.5342 </td>
   <td style="text-align:right;"> 0.2619 </td>
   <td style="text-align:right;"> 0.3041 </td>
   <td style="text-align:right;"> 0.5292 </td>
   <td style="text-align:right;"> 0.5353 </td>
   <td style="text-align:right;"> -0.0924 </td>
   <td style="text-align:right;"> 25.7452 </td>
   <td style="text-align:right;"> 0.1079 </td>
   <td style="text-align:right;"> 0.0256 </td>
   <td style="text-align:right;"> -0.0858 </td>
  </tr>
</tbody>
</table>

Validación del modelo 


```r
library(posterior)
library(bayesplot)
(mcmc_dens_chains(fit,pars = "sigma") +
    mcmc_areas(fit,pars = "sigma"))/
  mcmc_trace(fit,pars = "sigma")
```

<img src="09-D4S1_Modelo_unidad_ingresos_files/figure-html/unnamed-chunk-13-1.svg" width="672" />


```r
var_names <- c("edad2",  "edad3",  "edad4", "edad5", "sexo2",
               "luces_nocturnas", "cubrimiento_urbano","cubrimiento_cultivo")
mcmc_areas(fit,pars = var_names)
```

<img src="09-D4S1_Modelo_unidad_ingresos_files/figure-html/unnamed-chunk-14-1.svg" width="672" />



```r
mcmc_trace(fit,pars = var_names)
```

<img src="09-D4S1_Modelo_unidad_ingresos_files/figure-html/unnamed-chunk-15-1.svg" width="672" />



```r
encuesta_mrp2 <- inner_join(encuesta_mrp, statelevel_predictors_df)
y_pred_B <- posterior_epred(fit, newdata = encuesta_mrp2)
rowsrandom <- sample(nrow(y_pred_B), 100)
y_pred2 <- y_pred_B[rowsrandom, ]

ppc_dens_overlay(y = as.numeric(encuesta_mrp2$logingreso),
                 y_pred2)/
ppc_dens_overlay(y = exp(as.numeric(encuesta_mrp2$logingreso))-1,
                 (exp(y_pred2)-1))  
```

<img src="Recursos/Día4/Sesion1/0Recursos/Log_Ingreso.PNG" width="500px" height="250px" style="display: block; margin: auto;" /><img src="Recursos/Día4/Sesion1/0Recursos/Ingreso.PNG" width="500px" height="250px" style="display: block; margin: auto;" />

## Proceso de estimación y predicción

Obtener el modelo es solo un paso más, ahora se debe realizar la predicción en el censo, el cual a sido previamente estandarizado y homologado con la encuesta. 


```r
poststrat_df <- readRDS("Recursos/Día4/Sesion1/Data/censo_mrp_dam2.rds") %>% 
     left_join(statelevel_predictors_df) 
tba( poststrat_df %>% arrange(desc(n)) %>% head(10))
```

<table class="table table-striped lightable-classic" style="width: auto !important; margin-left: auto; margin-right: auto; font-family: Arial Narrow; width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> dam </th>
   <th style="text-align:left;"> dam2 </th>
   <th style="text-align:left;"> id_municipio </th>
   <th style="text-align:left;"> nombre_region </th>
   <th style="text-align:left;"> region </th>
   <th style="text-align:left;"> area </th>
   <th style="text-align:left;"> sexo </th>
   <th style="text-align:left;"> edad </th>
   <th style="text-align:left;"> anoest </th>
   <th style="text-align:right;"> n </th>
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
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 32 </td>
   <td style="text-align:left;"> 03201 </td>
   <td style="text-align:left;"> 103201 </td>
   <td style="text-align:left;"> Región Ozama </td>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:right;"> 78858 </td>
   <td style="text-align:right;"> 2.7794 </td>
   <td style="text-align:right;"> -1.1311 </td>
   <td style="text-align:right;"> -1.4114 </td>
   <td style="text-align:right;"> -0.3529 </td>
   <td style="text-align:right;"> 4.1625 </td>
   <td style="text-align:right;"> 3.8009 </td>
   <td style="text-align:right;"> 0.9256 </td>
   <td style="text-align:right;"> 0.5173 </td>
   <td style="text-align:right;"> 0.2869 </td>
   <td style="text-align:right;"> 0.2158 </td>
   <td style="text-align:right;"> 0.1599 </td>
   <td style="text-align:right;"> 0.0502 </td>
   <td style="text-align:right;"> 0.2161 </td>
   <td style="text-align:right;"> 0.4041 </td>
   <td style="text-align:right;"> 0.1677 </td>
   <td style="text-align:right;"> 0.0161 </td>
   <td style="text-align:right;"> 0.0200 </td>
   <td style="text-align:right;"> 0.7131 </td>
   <td style="text-align:right;"> 0.0571 </td>
   <td style="text-align:right;"> 0.1791 </td>
   <td style="text-align:right;"> 0.7701 </td>
   <td style="text-align:right;"> 0.0102 </td>
   <td style="text-align:right;"> 0.0245 </td>
   <td style="text-align:right;"> 0.0153 </td>
   <td style="text-align:right;"> 0.2883 </td>
   <td style="text-align:right;"> 0.9252 </td>
   <td style="text-align:right;"> 0.1870 </td>
   <td style="text-align:right;"> 0.0074 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 32 </td>
   <td style="text-align:left;"> 03201 </td>
   <td style="text-align:left;"> 103201 </td>
   <td style="text-align:left;"> Región Ozama </td>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:right;"> 77566 </td>
   <td style="text-align:right;"> 2.7794 </td>
   <td style="text-align:right;"> -1.1311 </td>
   <td style="text-align:right;"> -1.4114 </td>
   <td style="text-align:right;"> -0.3529 </td>
   <td style="text-align:right;"> 4.1625 </td>
   <td style="text-align:right;"> 3.8009 </td>
   <td style="text-align:right;"> 0.9256 </td>
   <td style="text-align:right;"> 0.5173 </td>
   <td style="text-align:right;"> 0.2869 </td>
   <td style="text-align:right;"> 0.2158 </td>
   <td style="text-align:right;"> 0.1599 </td>
   <td style="text-align:right;"> 0.0502 </td>
   <td style="text-align:right;"> 0.2161 </td>
   <td style="text-align:right;"> 0.4041 </td>
   <td style="text-align:right;"> 0.1677 </td>
   <td style="text-align:right;"> 0.0161 </td>
   <td style="text-align:right;"> 0.0200 </td>
   <td style="text-align:right;"> 0.7131 </td>
   <td style="text-align:right;"> 0.0571 </td>
   <td style="text-align:right;"> 0.1791 </td>
   <td style="text-align:right;"> 0.7701 </td>
   <td style="text-align:right;"> 0.0102 </td>
   <td style="text-align:right;"> 0.0245 </td>
   <td style="text-align:right;"> 0.0153 </td>
   <td style="text-align:right;"> 0.2883 </td>
   <td style="text-align:right;"> 0.9252 </td>
   <td style="text-align:right;"> 0.1870 </td>
   <td style="text-align:right;"> 0.0074 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:left;"> 100101 </td>
   <td style="text-align:left;"> Región Ozama </td>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:right;"> 76098 </td>
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
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:left;"> 100101 </td>
   <td style="text-align:left;"> Región Ozama </td>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:right;"> 76002 </td>
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
  </tr>
  <tr>
   <td style="text-align:left;"> 25 </td>
   <td style="text-align:left;"> 02501 </td>
   <td style="text-align:left;"> 012501 </td>
   <td style="text-align:left;"> Región Cibao Norte </td>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:right;"> 52770 </td>
   <td style="text-align:right;"> 1.4723 </td>
   <td style="text-align:right;"> -0.9237 </td>
   <td style="text-align:right;"> -1.0018 </td>
   <td style="text-align:right;"> 0.3619 </td>
   <td style="text-align:right;"> 1.3166 </td>
   <td style="text-align:right;"> 1.6641 </td>
   <td style="text-align:right;"> 0.8601 </td>
   <td style="text-align:right;"> 0.5084 </td>
   <td style="text-align:right;"> 0.2837 </td>
   <td style="text-align:right;"> 0.2250 </td>
   <td style="text-align:right;"> 0.1564 </td>
   <td style="text-align:right;"> 0.0596 </td>
   <td style="text-align:right;"> 0.2622 </td>
   <td style="text-align:right;"> 0.3832 </td>
   <td style="text-align:right;"> 0.1282 </td>
   <td style="text-align:right;"> 0.0114 </td>
   <td style="text-align:right;"> 0.0189 </td>
   <td style="text-align:right;"> 0.8665 </td>
   <td style="text-align:right;"> 0.1021 </td>
   <td style="text-align:right;"> 0.1307 </td>
   <td style="text-align:right;"> 0.7972 </td>
   <td style="text-align:right;"> 0.0134 </td>
   <td style="text-align:right;"> 0.0136 </td>
   <td style="text-align:right;"> 0.0160 </td>
   <td style="text-align:right;"> 0.2118 </td>
   <td style="text-align:right;"> 0.8939 </td>
   <td style="text-align:right;"> 0.1787 </td>
   <td style="text-align:right;"> 0.0044 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 25 </td>
   <td style="text-align:left;"> 02501 </td>
   <td style="text-align:left;"> 012501 </td>
   <td style="text-align:left;"> Región Cibao Norte </td>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:right;"> 51227 </td>
   <td style="text-align:right;"> 1.4723 </td>
   <td style="text-align:right;"> -0.9237 </td>
   <td style="text-align:right;"> -1.0018 </td>
   <td style="text-align:right;"> 0.3619 </td>
   <td style="text-align:right;"> 1.3166 </td>
   <td style="text-align:right;"> 1.6641 </td>
   <td style="text-align:right;"> 0.8601 </td>
   <td style="text-align:right;"> 0.5084 </td>
   <td style="text-align:right;"> 0.2837 </td>
   <td style="text-align:right;"> 0.2250 </td>
   <td style="text-align:right;"> 0.1564 </td>
   <td style="text-align:right;"> 0.0596 </td>
   <td style="text-align:right;"> 0.2622 </td>
   <td style="text-align:right;"> 0.3832 </td>
   <td style="text-align:right;"> 0.1282 </td>
   <td style="text-align:right;"> 0.0114 </td>
   <td style="text-align:right;"> 0.0189 </td>
   <td style="text-align:right;"> 0.8665 </td>
   <td style="text-align:right;"> 0.1021 </td>
   <td style="text-align:right;"> 0.1307 </td>
   <td style="text-align:right;"> 0.7972 </td>
   <td style="text-align:right;"> 0.0134 </td>
   <td style="text-align:right;"> 0.0136 </td>
   <td style="text-align:right;"> 0.0160 </td>
   <td style="text-align:right;"> 0.2118 </td>
   <td style="text-align:right;"> 0.8939 </td>
   <td style="text-align:right;"> 0.1787 </td>
   <td style="text-align:right;"> 0.0044 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 32 </td>
   <td style="text-align:left;"> 03201 </td>
   <td style="text-align:left;"> 103201 </td>
   <td style="text-align:left;"> Región Ozama </td>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> 50744 </td>
   <td style="text-align:right;"> 2.7794 </td>
   <td style="text-align:right;"> -1.1311 </td>
   <td style="text-align:right;"> -1.4114 </td>
   <td style="text-align:right;"> -0.3529 </td>
   <td style="text-align:right;"> 4.1625 </td>
   <td style="text-align:right;"> 3.8009 </td>
   <td style="text-align:right;"> 0.9256 </td>
   <td style="text-align:right;"> 0.5173 </td>
   <td style="text-align:right;"> 0.2869 </td>
   <td style="text-align:right;"> 0.2158 </td>
   <td style="text-align:right;"> 0.1599 </td>
   <td style="text-align:right;"> 0.0502 </td>
   <td style="text-align:right;"> 0.2161 </td>
   <td style="text-align:right;"> 0.4041 </td>
   <td style="text-align:right;"> 0.1677 </td>
   <td style="text-align:right;"> 0.0161 </td>
   <td style="text-align:right;"> 0.0200 </td>
   <td style="text-align:right;"> 0.7131 </td>
   <td style="text-align:right;"> 0.0571 </td>
   <td style="text-align:right;"> 0.1791 </td>
   <td style="text-align:right;"> 0.7701 </td>
   <td style="text-align:right;"> 0.0102 </td>
   <td style="text-align:right;"> 0.0245 </td>
   <td style="text-align:right;"> 0.0153 </td>
   <td style="text-align:right;"> 0.2883 </td>
   <td style="text-align:right;"> 0.9252 </td>
   <td style="text-align:right;"> 0.1870 </td>
   <td style="text-align:right;"> 0.0074 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:left;"> 100101 </td>
   <td style="text-align:left;"> Región Ozama </td>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> 50015 </td>
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
  </tr>
  <tr>
   <td style="text-align:left;"> 32 </td>
   <td style="text-align:left;"> 03201 </td>
   <td style="text-align:left;"> 103201 </td>
   <td style="text-align:left;"> Región Ozama </td>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> 49652 </td>
   <td style="text-align:right;"> 2.7794 </td>
   <td style="text-align:right;"> -1.1311 </td>
   <td style="text-align:right;"> -1.4114 </td>
   <td style="text-align:right;"> -0.3529 </td>
   <td style="text-align:right;"> 4.1625 </td>
   <td style="text-align:right;"> 3.8009 </td>
   <td style="text-align:right;"> 0.9256 </td>
   <td style="text-align:right;"> 0.5173 </td>
   <td style="text-align:right;"> 0.2869 </td>
   <td style="text-align:right;"> 0.2158 </td>
   <td style="text-align:right;"> 0.1599 </td>
   <td style="text-align:right;"> 0.0502 </td>
   <td style="text-align:right;"> 0.2161 </td>
   <td style="text-align:right;"> 0.4041 </td>
   <td style="text-align:right;"> 0.1677 </td>
   <td style="text-align:right;"> 0.0161 </td>
   <td style="text-align:right;"> 0.0200 </td>
   <td style="text-align:right;"> 0.7131 </td>
   <td style="text-align:right;"> 0.0571 </td>
   <td style="text-align:right;"> 0.1791 </td>
   <td style="text-align:right;"> 0.7701 </td>
   <td style="text-align:right;"> 0.0102 </td>
   <td style="text-align:right;"> 0.0245 </td>
   <td style="text-align:right;"> 0.0153 </td>
   <td style="text-align:right;"> 0.2883 </td>
   <td style="text-align:right;"> 0.9252 </td>
   <td style="text-align:right;"> 0.1870 </td>
   <td style="text-align:right;"> 0.0074 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:left;"> 100101 </td>
   <td style="text-align:left;"> Región Ozama </td>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> 49010 </td>
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
  </tr>
</tbody>
</table>
Note que la información del censo esta agregada.

### Distribución posterior.

Para obtener una distribución posterior de cada observación se hace uso de la función *posterior_epred* de la siguiente forma.


```r
epred_mat <- posterior_epred(fit, newdata = poststrat_df,  type = "response")
```

Como el interés es realizar comparaciones entre los países de la región se presenta la estimación del ingreso medio en términos de lineas de pobreza. Para esto procedemos así:

-   Obteniendo las lineas de pobreza por cada post-estrato


```r
(lp <- encuesta_mrp %>% distinct(area,lp,li)) %>% 
  tba()
```

<table class="table table-striped lightable-classic" style="width: auto !important; margin-left: auto; margin-right: auto; font-family: Arial Narrow; width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> area </th>
   <th style="text-align:right;"> lp </th>
   <th style="text-align:right;"> li </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:right;"> 5622.81 </td>
   <td style="text-align:right;"> 3159.09 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:right;"> 4876.69 </td>
   <td style="text-align:right;"> 3061.23 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:right;"> 5710.40 </td>
   <td style="text-align:right;"> 3193.03 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:right;"> 4949.12 </td>
   <td style="text-align:right;"> 3094.12 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:right;"> 5844.03 </td>
   <td style="text-align:right;"> 3291.64 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:right;"> 5070.47 </td>
   <td style="text-align:right;"> 3189.68 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:right;"> 5973.59 </td>
   <td style="text-align:right;"> 3377.04 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:right;"> 5185.77 </td>
   <td style="text-align:right;"> 3272.42 </td>
  </tr>
</tbody>
</table>

-   Ingreso en términos de lineas de pobreza.


```r
lp %<>% group_by(area) %>% summarise(lp = mean(lp),li = mean(li)) 
lp <- inner_join(poststrat_df,lp,by = "area") %>% select(lp)
epred_mat <- (exp(epred_mat)-1)/lp$lp
```

### Estimación del ingreso medio nacional





```r
n_filtered <- poststrat_df$n
mrp_estimates <- epred_mat %*% n_filtered / sum(n_filtered)

(temp_ing <- data.frame(
  mrp_estimate = mean(mrp_estimates),
  mrp_estimate_se = sd(mrp_estimates)
) ) %>% tba()
```
<table class="table table-striped lightable-classic" style="width: auto !important; margin-left: auto; margin-right: auto; font-family: Arial Narrow; width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:right;"> mrp_estimate </th>
   <th style="text-align:right;"> mrp_estimate_se </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 1.6535 </td>
   <td style="text-align:right;"> 0.0052 </td>
  </tr>
</tbody>
</table>


El resultado nos indica que el ingreso medio nacional es 1.65 lineas de pobreza

### Estimación para el dam == "01".

Es importante siempre conservar el orden de la base, dado que relación entre la predicción y el censo en uno a uno.


```r
temp <- poststrat_df %>%  mutate(Posi = 1:n())
temp <- filter(temp, dam == "01") %>% select(n, Posi)
n_filtered <- temp$n
temp_epred_mat <- epred_mat[, temp$Posi]

## Estimando el CME
mrp_estimates <- temp_epred_mat %*% n_filtered / sum(n_filtered)

(temp_dam11 <- data.frame(
  mrp_estimate = mean(mrp_estimates),
  mrp_estimate_se = sd(mrp_estimates)
) ) %>% tba()
```

<table class="table table-striped lightable-classic" style="width: auto !important; margin-left: auto; margin-right: auto; font-family: Arial Narrow; width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:right;"> mrp_estimate </th>
   <th style="text-align:right;"> mrp_estimate_se </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 1.6709 </td>
   <td style="text-align:right;"> 0.0159 </td>
  </tr>
</tbody>
</table>

El resultado nos indica que el ingreso medio en el dam 01 es 1.67 lineas de pobreza

### Estimación para la dam2 == "00203"


```r
temp <- poststrat_df %>%  mutate(Posi = 1:n())
temp <-
  filter(temp, dam2 == "00203") %>% select(n, Posi)
n_filtered <- temp$n
temp_epred_mat <- epred_mat[, temp$Posi]
## Estimando el CME
mrp_estimates <- temp_epred_mat %*% n_filtered / sum(n_filtered)

(temp_dam2_00203 <- data.frame(
  mrp_estimate = mean(mrp_estimates),
  mrp_estimate_se = sd(mrp_estimates)
) ) %>% tba()
```


<table class="table table-striped lightable-classic" style="width: auto !important; margin-left: auto; margin-right: auto; font-family: Arial Narrow; width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:right;"> mrp_estimate </th>
   <th style="text-align:right;"> mrp_estimate_se </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 1.2141 </td>
   <td style="text-align:right;"> 0.0394 </td>
  </tr>
</tbody>
</table>
El resultado nos indica que el ingreso medio en la dam2 00203 es 1.21 lineas de pobreza

Después de comprender la forma en que se realiza la estimación de los dominios no observados procedemos el uso de la función *Aux_Agregado* que es desarrollada para este fin.


```r
(mrp_estimate_Ingresolp <-
  Aux_Agregado(poststrat = poststrat_df,
             epredmat = epred_mat,
             byMap = NULL)
) %>% tba()
```

<table class="table table-striped lightable-classic" style="width: auto !important; margin-left: auto; margin-right: auto; font-family: Arial Narrow; width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> Nacional </th>
   <th style="text-align:right;"> mrp_estimate </th>
   <th style="text-align:right;"> mrp_estimate_se </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Nacional </td>
   <td style="text-align:right;"> 1.6535 </td>
   <td style="text-align:right;"> 0.0052 </td>
  </tr>
</tbody>
</table>

El resultado nos indica que el ingreso medio nacional es 2 lineas de pobreza

De forma similar es posible obtener los resultados para las divisiones administrativas. 


```r
mrp_estimate_dam <-
  Aux_Agregado(poststrat = poststrat_df,
             epredmat = epred_mat,
             byMap = "dam")
tba(mrp_estimate_dam %>% head(10) )
```
<table class="table table-striped lightable-classic" style="width: auto !important; margin-left: auto; margin-right: auto; font-family: Arial Narrow; width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> dam </th>
   <th style="text-align:right;"> mrp_estimate </th>
   <th style="text-align:right;"> mrp_estimate_se </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:right;"> 1.6709 </td>
   <td style="text-align:right;"> 0.0159 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:right;"> 1.5548 </td>
   <td style="text-align:right;"> 0.0340 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:right;"> 1.1277 </td>
   <td style="text-align:right;"> 0.0122 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:right;"> 1.4136 </td>
   <td style="text-align:right;"> 0.0211 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:right;"> 1.5726 </td>
   <td style="text-align:right;"> 0.0460 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:right;"> 1.6536 </td>
   <td style="text-align:right;"> 0.0229 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 07 </td>
   <td style="text-align:right;"> 1.1500 </td>
   <td style="text-align:right;"> 0.0421 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 08 </td>
   <td style="text-align:right;"> 1.7535 </td>
   <td style="text-align:right;"> 0.0258 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 09 </td>
   <td style="text-align:right;"> 1.9046 </td>
   <td style="text-align:right;"> 0.0248 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:right;"> 1.1804 </td>
   <td style="text-align:right;"> 0.0385 </td>
  </tr>
</tbody>
</table>




```r
mrp_estimate_dam2 <-
  Aux_Agregado(poststrat = poststrat_df,
             epredmat = epred_mat,
             byMap = "dam2")

tba(mrp_estimate_dam2 %>% head(10) )
```

<table class="table table-striped lightable-classic" style="width: auto !important; margin-left: auto; margin-right: auto; font-family: Arial Narrow; width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> dam2 </th>
   <th style="text-align:right;"> mrp_estimate </th>
   <th style="text-align:right;"> mrp_estimate_se </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:right;"> 1.6709 </td>
   <td style="text-align:right;"> 0.0159 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00201 </td>
   <td style="text-align:right;"> 1.6356 </td>
   <td style="text-align:right;"> 0.0339 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00202 </td>
   <td style="text-align:right;"> 1.2880 </td>
   <td style="text-align:right;"> 0.0418 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00203 </td>
   <td style="text-align:right;"> 1.2141 </td>
   <td style="text-align:right;"> 0.0394 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00204 </td>
   <td style="text-align:right;"> 2.0706 </td>
   <td style="text-align:right;"> 0.0991 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00205 </td>
   <td style="text-align:right;"> 1.2337 </td>
   <td style="text-align:right;"> 0.0582 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00206 </td>
   <td style="text-align:right;"> 1.3713 </td>
   <td style="text-align:right;"> 0.0429 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00207 </td>
   <td style="text-align:right;"> 1.5580 </td>
   <td style="text-align:right;"> 0.3652 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00208 </td>
   <td style="text-align:right;"> 1.4348 </td>
   <td style="text-align:right;"> 0.0501 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00209 </td>
   <td style="text-align:right;"> 1.4726 </td>
   <td style="text-align:right;"> 0.3456 </td>
  </tr>
</tbody>
</table>



El mapa resultante es el siguiente




<img src="Recursos/Día4/Sesion1/0Recursos/Map_DOM.PNG" width="400%" style="display: block; margin: auto;" />
