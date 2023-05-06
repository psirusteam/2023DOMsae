
# Día 4 - Sesión 3- Modelos de unidad - Estimación de head ratio para H 





-   La pobreza es, y ha sido, uno de los temas principales en las agendas nacionales e internacionales de los países durante décadas. Un ejemplo reciente es el primer objetivo de la agenda **2030** para el Desarrollo Sostenible (ODS): __“Poner fin a la pobreza en todas sus formas en todo el mundo”__, así como su indicador 1.2.2 que mide __“la proporción de hombres, mujeres y niños de todas las edades que viven en pobreza en todas sus dimensiones según las definiciones nacionales”__

-   Tradicionalmente los organismos nacionales e internacionales exigen la medida de pobreza unidimensional basada en ingresos y/o gastos. 

-   La pobreza es un fenómeno complejo que debe ser analizado considerando un conjunto de factores y no solo el monetario. 

-   En está ocasión se aborda el problema multidimensional de la pobreza utilizando métodos de áreas pequeñas proporcionando una estimación del índice de privación multidimensional (H) en Colombia. 

## Índice de Privación Multidimensional (H)

-   El H propuesto por CEPAL es una herramienta comparable entre los países de la región, para estudiar los fenómenos de la pobreza considerando varios aspectos o dimensiones. **En ningún caso el H busca reemplazar los indicadores pobreza unidimensional o multidimensional que hayan definido los países u organismos internacionales**

-   El índice requiere la información para cada individuo $i = 1,\cdots,N_d$ en $d = 1, \cdots, D$ dominios, donde $N_d$ denota el tamaño de la población del dominio $d$. El índice para el dominio $d$ se calcula como:

    $$
    H_d = \frac{1}{N_d}\sum_{i=1}^{N_d}I\left(q_{di} > 0.4  \right).
    $$
    
    La función del índicador  $I\left( \cdot \right)$ es igual a 1 cuando la condición $q_{di} > 0.4$. 

-   $q_{di}$ es una cantidad ponderada de la siguiente forma: 

    $$
    q_{di} = \frac{1}{8}\sum_{k=1}^{8}y_{di}^{k}
    $$

    Donde: 
    a. $y_{di}^{1}$ = Privación en material de construcción de la vivienda
    
    b. $y_{di}^{2}$ = Hacinamiento en el hogar. 
    
    c. $y_{di}^{3}$ = Privación de acceso al agua potable. 
    
    d. $y_{di}^{4}$ = Privación en saneamiento.
    
    e. $y_{di}^{5}$ = Acceso al servicio energía eléctrica. 
    
    f. $y_{di}^{6}$ = Privación de acceso al combustible para cocinar.
    
    g. $y_{di}^{7}$ = Privación en material de los techo 
    
    h. $y_{di}^{8}$ = Privación el material de las paredes.  


    Note que, $y_{di}^{k}$ es igual a **1** si la persona tiene privación en la $k-ésima$ dimesión y **0** en el caso que de no tener la privación. 
    
    
## Definición del modelo 

En muchas aplicaciones, la variable de interés en áreas pequeñas puede ser binaria, esto es $y_{dj} = 0$ o $1$ que representa la ausencia (o no) de una característica específica. Para este caso, la estimación objetivo en cada dominio $d = 1,\cdots , D$ es la proporción $\theta_d =\frac{1}{N_d}\sum_{i=1}^{N_d}y_{di}$ de la población que tiene esta característica, siendo $\theta_{di}$ la probabilidad de que una determinada unidad $i$ en el dominio $d$ obtenga el valor $1$. Bajo este escenario, el $\theta_{di}$ con una función de enlace logit se define como: 

$$
logit(\theta_{di}) = \log \left(\frac{\theta_{di}}{1-\theta_{di}}\right) = \boldsymbol{x}_{di}^{T}\boldsymbol{\beta} + u_{d}
$$
con $i=1,\cdots,N_d$, $d=1,\cdots,D$, $\boldsymbol{\beta}$  un vector de parámetros de efecto fijo, y $u_d$ el efecto aleatorio especifico del área para el dominio $d$ con $u_d \sim N\left(0,\sigma^2_u \right)$.  $u_d$ son independiente y $y_{di}\mid u_d \sim Bernoulli(\theta_{di})$ con $E(y_{di}\mid u_d)=\theta_{di}$ y $Var(y_{di}\mid u_d)=\sigma_{di}^2=\theta_{di}(1-\theta_{di})$. Además,  $\boldsymbol{x}_{di}^T$ representa el vector $p\times 1$ de valores de $p$ variables auxiliares. Entonces, $\theta_{di}$ se puede escribir como 

$$
\theta_{di} = \frac{\exp(\boldsymbol{x}_{di}^T\boldsymbol{\beta} + u_{d})}{1+ \exp(\boldsymbol{x}_{di}^T\boldsymbol{\beta} + u_{d})}
$$
De está forma podemos definir distribuciones previas 

$$
\begin{eqnarray*}
\beta_k & \sim   & N(0, 10000)\\
\sigma^2_u &\sim & IG(0.0001,0.0001)
\end{eqnarray*}
$$
El modelo se debe estimar para cada una de las dimensiones. 
  
#### Obejtivo {-}


Estimar la proporción de personas que presentan la $k-$ésima carencia, es decir, 

$$ 
P_d = \frac{\sum_{U_d}q_{di}}{N_d}
$$

donde $q_{di}$ toma el valor de 1 cuando la $i-$ésima persona presenta Privación Multidimensional y el valor de 0 en caso contrario. 

Note que, 

$$
\begin{equation*}
\bar{Y}_d = P_d =  \frac{\sum_{s_d}q_{di} + \sum_{s^c_d}q_{di}}{N_d} 
\end{equation*}
$$

Ahora, el estimador de $P$ esta dado por: 

$$
\hat{P}_d = \frac{\sum_{s_d}q_{di} + \sum_{s^c_d}\hat{q}_{di}}{N_d}
$$

donde

$$
\hat{q}_{di} = \frac{1}{8}\sum_{k=1}^{8}\hat{y}_{di}^{k}
$$

$$\hat{y}_{di}^{k}=E_{\mathscr{M}}\left(y_{di}^{k}\mid\boldsymbol{x}_{d},\boldsymbol{\beta}\right)$$,

con $\mathscr{M}$  la medida de probabilidad inducida por el modelamiento. 
De esta forma se tiene que, 

$$
\hat{P}_d = \frac{\sum_{U_{d}}\hat{q}_{di}}{N_d}
$$


  
### Procesamiento del modelo en `R`. 
El proceso inicia con el cargue de las librerías. 


```r
library(patchwork)
library(lme4)
library(tidyverse)
library(rstan)
library(rstanarm)
library(magrittr)
```

Los datos de la encuesta y el censo han sido preparados previamente, la información sobre la cual realizaremos la predicción corresponde a Colombia en el 2019 


```r
encuesta_H <- readRDS("Recursos/Día4/Sesion3/Data/encuesta_ipm.rds") 
statelevel_predictors_df <-
  readRDS("Recursos/Día4/Sesion3/Data/statelevel_predictors_df_dam2.rds") %>% 
   mutate_at(.vars = c("luces_nocturnas",
                      "cubrimiento_cultivo",
                      "cubrimiento_urbano",
                      "modificacion_humana",
                      "accesibilidad_hospitales",
                      "accesibilidad_hosp_caminado"),
            function(x) as.numeric(scale(x)))

byAgrega <- c("dam", "dam2", "area", "sexo","anoest", "edad" )
```




```r
names_H <- grep(pattern = "nbi", names(encuesta_H),value = TRUE)

encuesta_df <- map(setNames(names_H,names_H),
    function(y){
  encuesta_H$temp <- encuesta_H[[y]]
  encuesta_H %>% 
  group_by_at(all_of(byAgrega)) %>%
  summarise(n = n(),
            yno = sum(temp),
            ysi = n - yno, .groups = "drop") %>% 
    inner_join(statelevel_predictors_df)
})
```

#### Privación en material de construcción de la vivienda {-} 

<table class="table table-striped lightable-classic" style="width: auto !important; margin-left: auto; margin-right: auto; font-family: Arial Narrow; width: auto !important; margin-left: auto; margin-right: auto;">
<caption>(\#tab:unnamed-chunk-4)nbi_matviv</caption>
 <thead>
  <tr>
   <th style="text-align:left;"> dam </th>
   <th style="text-align:left;"> dam2 </th>
   <th style="text-align:left;"> area </th>
   <th style="text-align:left;"> sexo </th>
   <th style="text-align:left;"> anoest </th>
   <th style="text-align:left;"> edad </th>
   <th style="text-align:right;"> n </th>
   <th style="text-align:right;"> yno </th>
   <th style="text-align:right;"> ysi </th>
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
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> 652 </td>
   <td style="text-align:right;"> 27 </td>
   <td style="text-align:right;"> 625 </td>
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
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> 624 </td>
   <td style="text-align:right;"> 24 </td>
   <td style="text-align:right;"> 600 </td>
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
   <td style="text-align:left;"> 32 </td>
   <td style="text-align:left;"> 03201 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> 575 </td>
   <td style="text-align:right;"> 22 </td>
   <td style="text-align:right;"> 553 </td>
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
   <td style="text-align:left;"> 103201 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 32 </td>
   <td style="text-align:left;"> 03201 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> 547 </td>
   <td style="text-align:right;"> 39 </td>
   <td style="text-align:right;"> 508 </td>
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
   <td style="text-align:left;"> 103201 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 25 </td>
   <td style="text-align:left;"> 02501 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> 505 </td>
   <td style="text-align:right;"> 25 </td>
   <td style="text-align:right;"> 480 </td>
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
   <td style="text-align:left;"> 012501 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 25 </td>
   <td style="text-align:left;"> 02501 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> 447 </td>
   <td style="text-align:right;"> 15 </td>
   <td style="text-align:right;"> 432 </td>
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
   <td style="text-align:left;"> 012501 </td>
  </tr>
</tbody>
</table>

#### Hacinamiento {-}

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
   <th style="text-align:right;"> yno </th>
   <th style="text-align:right;"> ysi </th>
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
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> 652 </td>
   <td style="text-align:right;"> 221 </td>
   <td style="text-align:right;"> 431 </td>
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
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> 624 </td>
   <td style="text-align:right;"> 207 </td>
   <td style="text-align:right;"> 417 </td>
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
   <td style="text-align:left;"> 32 </td>
   <td style="text-align:left;"> 03201 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> 575 </td>
   <td style="text-align:right;"> 181 </td>
   <td style="text-align:right;"> 394 </td>
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
   <td style="text-align:left;"> 103201 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 32 </td>
   <td style="text-align:left;"> 03201 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> 547 </td>
   <td style="text-align:right;"> 167 </td>
   <td style="text-align:right;"> 380 </td>
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
   <td style="text-align:left;"> 103201 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 25 </td>
   <td style="text-align:left;"> 02501 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> 505 </td>
   <td style="text-align:right;"> 101 </td>
   <td style="text-align:right;"> 404 </td>
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
   <td style="text-align:left;"> 012501 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 25 </td>
   <td style="text-align:left;"> 02501 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> 447 </td>
   <td style="text-align:right;"> 89 </td>
   <td style="text-align:right;"> 358 </td>
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
   <td style="text-align:left;"> 012501 </td>
  </tr>
</tbody>
</table>

### Definiendo el modelo multinivel.

Para cada dimensión que compone el H se ajusta el siguiente modelo mostrado en el script. En este código se incluye el uso de la función `future_map` que permite procesar en paralelo cada modelo O puede compilar cada por separado.   


```r
library(furrr)
library(rstanarm)
plan(multisession, workers = 4)

fit <- future_map(encuesta_df, function(xdat){
stan_glmer(
  cbind(yno, ysi) ~ (1 | dam2) +
    (1 | dam) +
    (1|edad) +
    area +
    (1|anoest) +
    sexo + 
    tasa_desocupacion +
    luces_nocturnas +
    modificacion_humana,
  family = binomial(link = "logit"),
  data = xdat,
  cores = 7,
  chains = 4,
  iter = 300
)}, 
.progress = TRUE)

saveRDS(object = fit, "Recursos/Día4/Sesion3/Data/fits_H.rds")
```

Terminado la compilación de los modelos después de realizar validaciones sobre esto, pasamos hacer las predicciones en el censo. 

### Proceso de estimación y predicción

Los modelos fueron compilados de manera separada, por tanto, disponemos de un objeto `.rds` por cada dimensión del H 


```r
fit_agua <-
  readRDS(file = "Recursos/Día4/Sesion3/Data/fits_bayes_nbi_agua.rds")
fit_combustible <-
  readRDS(file = "Recursos/Día4/Sesion3/Data/fits_bayes_nbi_combus.rds")
fit_techo <-
  readRDS(file = "Recursos/Día4/Sesion3/Data/fits_bayes_nbi_techo.rds")
fit_energia <-
  readRDS(file = "Recursos/Día4/Sesion3/Data/fits_bayes_nbi_elect.rds")
fit_hacinamiento <-
  readRDS(file = "Recursos/Día4/Sesion3/Data/fits_bayes_nbi_hacina.rds")
fit_paredes <-
  readRDS(file = "Recursos/Día4/Sesion3/Data/fits_bayes_nbi_pared.rds")
fit_material <-
  readRDS(file = "Recursos/Día4/Sesion3/Data/fits_bayes_nbi_matviv.rds")
fit_saneamiento <-
  readRDS(file = "Recursos/Día4/Sesion3/Data/fits_bayes_nbi_saneamiento.rds")
```

Ahora, debemos leer la información del censo  y crear los **post-estrato **

```r
censo_H <- readRDS("Recursos/Día4/Sesion3/Data/censo_mrp_dam2.rds") 

poststrat_df <- censo_H %>%
  group_by_at(byAgrega) %>%
  summarise(n = sum(n), .groups = "drop") %>% 
  arrange(desc(n))
tba(head(poststrat_df))
```

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
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 32 </td>
   <td style="text-align:left;"> 03201 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> 78858 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 32 </td>
   <td style="text-align:left;"> 03201 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> 77566 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> 76098 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> 76002 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 25 </td>
   <td style="text-align:left;"> 02501 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> 52770 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 25 </td>
   <td style="text-align:left;"> 02501 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:right;"> 51227 </td>
  </tr>
</tbody>
</table>

Para realizar la predicción en el censo debemos incluir la información auxiliar 


```r
poststrat_df <- inner_join(poststrat_df, statelevel_predictors_df)
dim(poststrat_df)
```

```
## [1] 14120    36
```


Para cada uno de los modelos anteriores debe tener las predicciones, para ejemplificar el proceso tomaremos el departamento de la Guajira de Colombia 

-   Privación de acceso al agua potable. 


```r
temp <- poststrat_df 
epred_mat_agua <- posterior_epred(
  fit_agua,
  newdata = temp,
  type = "response",
  allow.new.levels = TRUE
)
```

-   Privación de acceso al combustible para cocinar.


```r
epred_mat_combustible <-
  posterior_epred(
    fit_combustible,
    newdata = temp,
    type = "response",
    allow.new.levels = TRUE
  )
```

-   Privación en material de los techo.


```r
epred_mat_techo <-
  posterior_epred(
    fit_techo,
    newdata = temp,
    type = "response",
    allow.new.levels = TRUE
  )
```

-   Acceso al servicio energía eléctrica.


```r
epred_mat_energia <-
  posterior_epred(
    fit_energia,
    newdata = temp,
    type = "response",
    allow.new.levels = TRUE
  )
```

-    Hacinamiento en el hogar.


```r
epred_mat_hacinamiento <-
  posterior_epred(
    fit_hacinamiento,
    newdata = temp,
    type = "response",
    allow.new.levels = TRUE
  )
```

-   Privación el material de las paredes.


```r
epred_mat_paredes <-
  posterior_epred(
    fit_paredes,
    newdata = temp,
    type = "response",
    allow.new.levels = TRUE
  )
```

-   Privación en material de construcción de la vivienda


```r
epred_mat_material <-
  posterior_epred(
    fit_material,
    newdata = temp,
    type = "response",
    allow.new.levels = TRUE
  )
```

-   Privación en saneamiento.


```r
epred_mat_saneamiento <-
  posterior_epred(
    fit_saneamiento,
    newdata = temp,
    type = "response",
    allow.new.levels = TRUE
  )
```



Los resultados anteriores se deben procesarse en términos de carencia (1) y  no carencia (0) para la $k-esima$ dimensión . 

-    Privación de acceso al agua potable. 



```r
epred_mat_agua_dummy <-
  rbinom(n = nrow(epred_mat_agua) * ncol(epred_mat_agua) , 1,
         epred_mat_agua)

epred_mat_agua_dummy <- matrix(
  epred_mat_agua_dummy,
  nrow = nrow(epred_mat_agua),
  ncol = ncol(epred_mat_agua)
)
```

-   Privación de acceso al combustible para cocinar.



```r
epred_mat_combustible_dummy <-
  rbinom(n = nrow(epred_mat_combustible) * ncol(epred_mat_combustible) ,
         1,
         epred_mat_combustible)

epred_mat_combustible_dummy <- matrix(
  epred_mat_combustible_dummy,
  nrow = nrow(epred_mat_combustible),
  ncol = ncol(epred_mat_combustible)
)
```

-    Acceso al servicio energía eléctrica 



```r
epred_mat_energia_dummy <-
  rbinom(n = nrow(epred_mat_energia) * ncol(epred_mat_energia) ,
         1,
         epred_mat_energia)

epred_mat_energia_dummy <- matrix(
  epred_mat_energia_dummy,
  nrow = nrow(epred_mat_energia),
  ncol = ncol(epred_mat_energia)
)
```

-   Hacinamiento en el hogar.



```r
epred_mat_hacinamiento_dummy <-
  rbinom(
    n = nrow(epred_mat_hacinamiento) * ncol(epred_mat_hacinamiento) ,
    1,
    epred_mat_hacinamiento
  )

epred_mat_hacinamiento_dummy <-
  matrix(
    epred_mat_hacinamiento_dummy,
    nrow = nrow(epred_mat_hacinamiento),
    ncol = ncol(epred_mat_hacinamiento)
  )
```

-   Privación el material de las paredes.



```r
epred_mat_paredes_dummy <-
  rbinom(n = nrow(epred_mat_paredes) * ncol(epred_mat_paredes) ,
         1,
         epred_mat_paredes)

epred_mat_paredes_dummy <- matrix(
  epred_mat_paredes_dummy,
  nrow = nrow(epred_mat_paredes),
  ncol = ncol(epred_mat_paredes)
)
```

-   Privación en material de construcción de la vivienda 



```r
epred_mat_material_dummy <-
  rbinom(n = nrow(epred_mat_material) * ncol(epred_mat_material) ,
         1,
         epred_mat_material)

epred_mat_material_dummy <- matrix(
  epred_mat_material_dummy,
  nrow = nrow(epred_mat_material),
  ncol = ncol(epred_mat_material)
)
```

-   Privación en saneamiento. 



```r
epred_mat_saneamiento_dummy <-
  rbinom(n = nrow(epred_mat_saneamiento) * ncol(epred_mat_saneamiento) ,
         1,
         epred_mat_saneamiento)

epred_mat_saneamiento_dummy <- matrix(
  epred_mat_saneamiento_dummy,
  nrow = nrow(epred_mat_saneamiento),
  ncol = ncol(epred_mat_saneamiento)
)
```

-   Privación en material de los techo. 



```r
epred_mat_techo_dummy <-
  rbinom(n = nrow(epred_mat_techo) * ncol(epred_mat_techo) ,
         1,
         epred_mat_techo)

epred_mat_techo_dummy <- matrix(
  epred_mat_techo_dummy,
  nrow = nrow(epred_mat_techo),
  ncol = ncol(epred_mat_techo)
)
```





Con las variables dummy creadas es posible estimar el H 


```r
epred_mat_H <- (1/8) * (
  epred_mat_material_dummy +
    epred_mat_hacinamiento_dummy +
    epred_mat_agua_dummy +
    epred_mat_saneamiento_dummy +
    epred_mat_energia_dummy +
    epred_mat_paredes_dummy +
    epred_mat_combustible_dummy + 
    epred_mat_techo_dummy)
```

Ahora, debemos dicotomizar la variable nuevamente. 


```r
epred_mat_H[epred_mat_H <= 0.4] <- 0
epred_mat_H[epred_mat_H != 0] <- 1
```




Finalmente realizamos el calculo del H así: 

```r
mean(colSums(t(epred_mat_H)*poststrat_df$n)/sum(poststrat_df$n))
```

```
## [1] 0.008795743
```
También es posible utilizar la función `Aux_Agregado` para las estimaciones. 

Para obtener el resultado por municipio procedemos así: 

```r
source("Recursos/Día4/Sesion3/0Recursos/funciones_mrp.R")
mrp_estimate_dam2 <-
   Aux_Agregado(poststrat = temp,
                epredmat = epred_mat_H,
                byMap = "dam2")
tba(mrp_estimate_dam2 %>% head(10))
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
   <td style="text-align:right;"> 0.0011 </td>
   <td style="text-align:right;"> 0.0056 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00201 </td>
   <td style="text-align:right;"> 0.0010 </td>
   <td style="text-align:right;"> 0.0037 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00202 </td>
   <td style="text-align:right;"> 0.0010 </td>
   <td style="text-align:right;"> 0.0039 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00203 </td>
   <td style="text-align:right;"> 0.0143 </td>
   <td style="text-align:right;"> 0.0136 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00204 </td>
   <td style="text-align:right;"> 0.0006 </td>
   <td style="text-align:right;"> 0.0034 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00205 </td>
   <td style="text-align:right;"> 0.0002 </td>
   <td style="text-align:right;"> 0.0018 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00206 </td>
   <td style="text-align:right;"> 0.0060 </td>
   <td style="text-align:right;"> 0.0117 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00207 </td>
   <td style="text-align:right;"> 0.0072 </td>
   <td style="text-align:right;"> 0.0187 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00208 </td>
   <td style="text-align:right;"> 0.0015 </td>
   <td style="text-align:right;"> 0.0054 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00209 </td>
   <td style="text-align:right;"> 0.0024 </td>
   <td style="text-align:right;"> 0.0067 </td>
  </tr>
</tbody>
</table>


El siguiente paso es realizar el mapa de los resultados 


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
```

Los resultados nacionales son mostrados en el mapa. 


```r
brks_ing <- c(0,0.05,0.1,0.15, 0.20 ,0.3, 1)
maps3 <- tm_shape(ShapeSAE %>%
                    left_join(mrp_estimate_dam2,  by = "dam2"))

Mapa_ing3 <-
  maps3 + tm_polygons(
    "mrp_estimate",
    breaks = brks_ing,
    title = "H",
    palette = "YlOrRd",
    colorNA = "white"
  ) 

tmap_save(
  Mapa_ing3,
  "Recursos/Día4/Sesion3/Data/DOM_H.jpeg",
  width = 2000,
  height = 1500,
  asp = 0
)

Mapa_ing3
```


<img src="Recursos/Día4/Sesion3/Data/DOM_H.jpeg" width="900px" height="600px" style="display: block; margin: auto;" />

Los resultado para cada componente puede ser mapeado de forma similar. 

Para obtener el resultado por municipio procedemos así: 



<table class="table table-striped lightable-classic" style="width: auto !important; margin-left: auto; margin-right: auto; font-family: Arial Narrow; width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> Indicador </th>
   <th style="text-align:left;"> dam2 </th>
   <th style="text-align:right;"> mrp_estimate </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Material </td>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:right;"> 0.0466 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Material </td>
   <td style="text-align:left;"> 00201 </td>
   <td style="text-align:right;"> 0.0337 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Material </td>
   <td style="text-align:left;"> 00202 </td>
   <td style="text-align:right;"> 0.0333 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Material </td>
   <td style="text-align:left;"> 00203 </td>
   <td style="text-align:right;"> 0.0562 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Material </td>
   <td style="text-align:left;"> 00204 </td>
   <td style="text-align:right;"> 0.0193 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Material </td>
   <td style="text-align:left;"> 00205 </td>
   <td style="text-align:right;"> 0.0148 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Material </td>
   <td style="text-align:left;"> 00206 </td>
   <td style="text-align:right;"> 0.0885 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Material </td>
   <td style="text-align:left;"> 00207 </td>
   <td style="text-align:right;"> 0.0746 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Material </td>
   <td style="text-align:left;"> 00208 </td>
   <td style="text-align:right;"> 0.0884 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Material </td>
   <td style="text-align:left;"> 00209 </td>
   <td style="text-align:right;"> 0.0430 </td>
  </tr>
</tbody>
</table>




<img src="Recursos/Día4/Sesion3/Data/NBI_DOM.png" width="900px" height="600px" style="display: block; margin: auto;" />
