# Día 2 - Sesión 3- Modelo de Fay Herriot - Estimación de la pobreza 




El modelo de Fay Herriot, propuesto por Fay y Herriot (1979), es un modelo estadístico de área y es el más comúnmente utilizado, cabe tener en cuenta, que dentro de la metodología de estimación en áreas pequeñas, los modelos de área son los de mayor aplicación, ya que lo más factible es no contar con la información a nivel de individuo, pero si encontrar no solo los datos a nivel de área, sino también información auxiliar asociada a estos datos. Este modelo lineal mixto, fue el primero en incluir efectos aleatorios a nivel de área, lo que implica que la mayoría de la información que se introduce al modelo corresponde a agregaciaciones usualmente, departamentos, regiones, provincias, municipios entre otros, donde las estimaciones que se logran con el modelo se obtienen sobre estas agregaciones o subpoblaciones.


-   El modelo FH enlaza indicadores de las áreas $\theta_d$, $d = 1, \cdots , D$, asumiendo que varían respeto a un vector de $p$ covariables, $\boldsymbol{x}_d$ , de forma constante. El modelo esta dado por la ecuación

$$
\theta_d = \boldsymbol{x}^{T}_{d}\boldsymbol{\beta} + u_d ,\ \ \ \ \  d = 1, \cdots , D
$$ 

- $u_d$ es el término de error, o el efecto aleatorio, diferente para cada área dado por

$$
\begin{eqnarray*}
u_{d} & \stackrel{iid}{\sim} & \left(0,\sigma_{u}^{2}\right)
\end{eqnarray*}
$$

-   Sin embargo, los verdaderos valores de los indicadores $\theta_d$ no son observables. Entonces, usamos el estimador directo $\hat{\theta}^{DIR}_d$ para $\theta_d$ , lo que conlleva un error debido al muestro.

-   $\hat{\theta}^{DIR}_d$ todavía se considera insesgado bajo el diseño muestral.

-   Podemos definir, entonces, 

$$
\hat{\theta}^{DIR}_d = \theta_d + e_d, \ \ \ \ \ \ d = 1, \cdots , D 
$$ 
    
donde $e_d$ es el error debido al muestreo, $e_{d} \stackrel{ind}{\sim} \left(0,\sigma^2\right)$

-   Dichas varianzas $\sigma^2_d = var_{\mathscr{P}}\left(\hat{\theta}^{DIR}_d\mid\theta_d\right)$, $d = 1,\cdots,D$ se estiman con los microdatos de la encuesta.

-   Por tanto, el modelo se hace, $$
    \hat{\theta}^{DIR}_d = \boldsymbol{x}^{T}_{d}\boldsymbol{\beta} + u_d + e_d, \ \ \ \ \ \ d = 1, \cdots , D
    $$

-   El BLUP (best linear unbiased predictor) bajo el modelo FH de $\theta_d$ viene dado por

$$
    \begin{eqnarray*}
    \tilde{\theta}_{d}^{FH} & = & \boldsymbol{x}^{T}_{d}\tilde{\boldsymbol{\beta}}+\tilde{u}_{d}
    \end{eqnarray*}
$$

-   Si sustituimos $\tilde{u}_d = \gamma_d\left(\hat{\theta}^{DIR}_d - \boldsymbol{x}^{T}_{d}\tilde{\boldsymbol{\beta}} \right)$ en el BLUP bajo el modelo FH, obtenemos $$
    \begin{eqnarray*}
    \tilde{\theta}_{d}^{FH} & = & \gamma_d\hat{\theta}^{DIR}_{d}+(1-\gamma_d)\boldsymbol{x}^{T}_{d}\tilde{\boldsymbol{\beta}}
    \end{eqnarray*}
    $$ siendo $\gamma_d=\frac{\sigma^2_u}{\sigma^2_u + \sigma^2_d}$.

-   Habitualmente, no sabemos el verdadero valor de $\sigma^2_u$ efectos aleatorios $u_d$.

-   Sea $\hat{\sigma}^2_u$ un estimador consistente para $\sigma^2_u$. Entonces, obtenemos el BLUP empírico (empirical BLUP, EBLUP) de $\theta_d$ ,

$$
    \begin{eqnarray*}
    \tilde{\theta}_{d}^{FH} & = & \hat{\gamma_d}\hat{\theta}^{DIR}_{d}+(1-\hat{\gamma_d})\boldsymbol{x}^{T}_{d}\hat{\boldsymbol{\beta}}
    \end{eqnarray*}
$$

donde $\hat{\gamma_d}=\frac{\hat{\sigma}^2_u}{\hat{\sigma}^2_u + \sigma^2_d}$.

### Modelo de área para la estimación de la pobreza {-}


El modelo bayesiano estaría definido como:  

$$
\begin{eqnarray*}
\hat{Y}_d\mid\theta_d,\sigma_d^2 & \sim & N\left(\theta_d,\sigma_d^2\right)\\
\theta_d & = & \boldsymbol{x}^{T}_{d}\boldsymbol{\beta}+u_d
\end{eqnarray*}
$$

donde $u_d \sim N(0 , \sigma^2_u)$ y $\hat{Y}_d$ es la estimación directa de la pobreza en el $d-ésimo$ dominio. 

Las distribuciones previas para $\boldsymbol{\beta}$ y $\sigma^2_u$

$$
\begin{eqnarray*}
\beta_p & \sim   & N(0, 10000)\\
\sigma^2_u &\sim & IG(0.0001, 0.0001)
\end{eqnarray*}
$$

## Procedimiento de estimación

Este código utiliza las librerías `tidyverse` y `magrittr` para procesamiento y analizar datos.

La función `readRDS()` es utilizada para cargar un archivo de datos en formato RDS, que contiene las estimaciones directas y la varianza suvizada para la proporción de personas en condición de pobreza correspondientes al año 2018. Luego, se utiliza el operador `%>%` de la librería `magrittr` para encadenar la selección de las columnas de interés, que corresponden a los nombres `dam2`, `nd`, `pobreza`, `vardir` y `hat_var`.


```r
library(tidyverse)
library(magrittr)

base_FH <- readRDS("Recursos/Día2/Sesion3/Data/base_FH_2018.rds") %>% 
  select(dam2, nd,  pobreza, vardir, hat_var)
```

Lectura de las covariables, las cuales son obtenidas previamente. Dado la diferencia entre las escalas de las variables  es necesario hacer un ajuste a estas. 


```r
statelevel_predictors_df <- readRDS("Recursos/Día2/Sesion3/Data/statelevel_predictors_df_dam2.rds") %>% 
    mutate_at(.vars = c("luces_nocturnas",
                      "cubrimiento_cultivo",
                      "cubrimiento_urbano",
                      "modificacion_humana",
                      "accesibilidad_hospitales",
                      "accesibilidad_hosp_caminado"),
            function(x) as.numeric(scale(x)))
```

Ahora, se realiza una unión completa (`full_join`) entre el conjunto de datos `base_FH` y los predictores `statelevel_predictors_df` utilizando la variable `dam2` como clave de unión.

Se utiliza la función tba() para imprimir las primeras 10 filas y 8 columnas del conjunto de datos resultante de la unión anterior.

La unión completa (`full_join`) combina los datos de ambos conjuntos, manteniendo todas las filas de ambos, y llenando con valores faltantes (NA) en caso de no encontrar coincidencias en la variable de unión (dam2 en este caso).

La función `tba()` imprime una tabla en formato HTML en la consola de R que muestra las primeras 10 filas y 8 columnas del conjunto de datos resultante de la unión.


```r
base_FH <- full_join(base_FH, statelevel_predictors_df, by = "dam2" )
tba(base_FH[1:10,1:8])
```

<table class="table table-striped lightable-classic" style="width: auto !important; margin-left: auto; margin-right: auto; font-family: Arial Narrow; width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> dam2 </th>
   <th style="text-align:right;"> nd </th>
   <th style="text-align:right;"> pobreza </th>
   <th style="text-align:right;"> vardir </th>
   <th style="text-align:right;"> hat_var </th>
   <th style="text-align:right;"> modificacion_humana </th>
   <th style="text-align:right;"> accesibilidad_hospitales </th>
   <th style="text-align:right;"> accesibilidad_hosp_caminado </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:right;"> 6796 </td>
   <td style="text-align:right;"> 0.2225 </td>
   <td style="text-align:right;"> 0.0004 </td>
   <td style="text-align:right;"> 0.0004 </td>
   <td style="text-align:right;"> 3.6127 </td>
   <td style="text-align:right;"> -1.1835 </td>
   <td style="text-align:right;"> -1.5653 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00201 </td>
   <td style="text-align:right;"> 531 </td>
   <td style="text-align:right;"> 0.1822 </td>
   <td style="text-align:right;"> 0.0004 </td>
   <td style="text-align:right;"> 0.0039 </td>
   <td style="text-align:right;"> -0.0553 </td>
   <td style="text-align:right;"> 0.4449 </td>
   <td style="text-align:right;"> 0.2100 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00206 </td>
   <td style="text-align:right;"> 230 </td>
   <td style="text-align:right;"> 0.3366 </td>
   <td style="text-align:right;"> 0.0031 </td>
   <td style="text-align:right;"> 0.0041 </td>
   <td style="text-align:right;"> 0.5157 </td>
   <td style="text-align:right;"> -0.1468 </td>
   <td style="text-align:right;"> -0.1811 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00301 </td>
   <td style="text-align:right;"> 666 </td>
   <td style="text-align:right;"> 0.4266 </td>
   <td style="text-align:right;"> 0.0043 </td>
   <td style="text-align:right;"> 0.0050 </td>
   <td style="text-align:right;"> 0.1364 </td>
   <td style="text-align:right;"> 0.5744 </td>
   <td style="text-align:right;"> 1.1660 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00302 </td>
   <td style="text-align:right;"> 261 </td>
   <td style="text-align:right;"> 0.4461 </td>
   <td style="text-align:right;"> 0.0014 </td>
   <td style="text-align:right;"> 0.0029 </td>
   <td style="text-align:right;"> -0.5103 </td>
   <td style="text-align:right;"> 0.2531 </td>
   <td style="text-align:right;"> 1.0880 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00303 </td>
   <td style="text-align:right;"> 566 </td>
   <td style="text-align:right;"> 0.5587 </td>
   <td style="text-align:right;"> 0.0142 </td>
   <td style="text-align:right;"> 0.0075 </td>
   <td style="text-align:right;"> -0.6591 </td>
   <td style="text-align:right;"> 0.6249 </td>
   <td style="text-align:right;"> 1.2229 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00304 </td>
   <td style="text-align:right;"> 412 </td>
   <td style="text-align:right;"> 0.5406 </td>
   <td style="text-align:right;"> 0.0116 </td>
   <td style="text-align:right;"> 0.0042 </td>
   <td style="text-align:right;"> -0.5573 </td>
   <td style="text-align:right;"> 1.4586 </td>
   <td style="text-align:right;"> 2.7337 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00401 </td>
   <td style="text-align:right;"> 1219 </td>
   <td style="text-align:right;"> 0.3359 </td>
   <td style="text-align:right;"> 0.0010 </td>
   <td style="text-align:right;"> 0.0042 </td>
   <td style="text-align:right;"> 0.3979 </td>
   <td style="text-align:right;"> -0.0833 </td>
   <td style="text-align:right;"> -0.4490 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00402 </td>
   <td style="text-align:right;"> 172 </td>
   <td style="text-align:right;"> 0.1496 </td>
   <td style="text-align:right;"> 0.0007 </td>
   <td style="text-align:right;"> 0.0047 </td>
   <td style="text-align:right;"> -0.3661 </td>
   <td style="text-align:right;"> -0.0114 </td>
   <td style="text-align:right;"> -0.2863 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00403 </td>
   <td style="text-align:right;"> 309 </td>
   <td style="text-align:right;"> 0.4644 </td>
   <td style="text-align:right;"> 0.0015 </td>
   <td style="text-align:right;"> 0.0031 </td>
   <td style="text-align:right;"> -1.0446 </td>
   <td style="text-align:right;"> 0.4542 </td>
   <td style="text-align:right;"> 0.5702 </td>
  </tr>
</tbody>
</table>

```r
# View(base_FH)
```

## Preparando los insumos para `STAN`

  1.    Dividir la base de datos en dominios observados y no observados.
    
  Dominios observados.
    

```r
data_dir <- base_FH %>% filter(!is.na(pobreza))
```

  Dominios NO observados.
    

```r
data_syn <-
  base_FH %>% anti_join(data_dir %>% select(dam2))
tba(data_syn[1:10,1:8])
```

<table class="table table-striped lightable-classic" style="width: auto !important; margin-left: auto; margin-right: auto; font-family: Arial Narrow; width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> dam2 </th>
   <th style="text-align:right;"> nd </th>
   <th style="text-align:right;"> pobreza </th>
   <th style="text-align:right;"> vardir </th>
   <th style="text-align:right;"> hat_var </th>
   <th style="text-align:right;"> modificacion_humana </th>
   <th style="text-align:right;"> accesibilidad_hospitales </th>
   <th style="text-align:right;"> accesibilidad_hosp_caminado </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 00202 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> -0.3758 </td>
   <td style="text-align:right;"> 0.0000 </td>
   <td style="text-align:right;"> 0.1482 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00203 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> -0.9259 </td>
   <td style="text-align:right;"> 0.5732 </td>
   <td style="text-align:right;"> -0.1402 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00204 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> -1.3166 </td>
   <td style="text-align:right;"> 1.1111 </td>
   <td style="text-align:right;"> 0.4438 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00205 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> -0.7474 </td>
   <td style="text-align:right;"> 2.1155 </td>
   <td style="text-align:right;"> 1.2271 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00207 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> 1.7368 </td>
   <td style="text-align:right;"> -0.7648 </td>
   <td style="text-align:right;"> -0.4861 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00208 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> -0.5942 </td>
   <td style="text-align:right;"> 0.3212 </td>
   <td style="text-align:right;"> -0.1697 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00209 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> -1.5280 </td>
   <td style="text-align:right;"> 3.0192 </td>
   <td style="text-align:right;"> 1.9428 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00210 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> -1.0038 </td>
   <td style="text-align:right;"> 0.5778 </td>
   <td style="text-align:right;"> 0.2678 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00305 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> -0.8480 </td>
   <td style="text-align:right;"> 1.5047 </td>
   <td style="text-align:right;"> 3.2004 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00404 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> -0.5678 </td>
   <td style="text-align:right;"> 1.0735 </td>
   <td style="text-align:right;"> 0.9856 </td>
  </tr>
</tbody>
</table>

  2.    Definir matriz de efectos fijos.
  
  Define un modelo lineal utilizando la función `formula()`, que incluye varias variables predictoras, como la edad, la etnia, la tasa de desocupación, entre otras.

  Utiliza la función `model.matrix()` para generar matrices de diseño (`Xdat` y `Xs`) a partir de los datos observados (`data_dir`) y no observados (`data_syn`) para utilizar en la construcción de modelos de regresión. La función `model.matrix()` convierte las variables categóricas en variables binarias (dummy), de manera que puedan ser utilizadas. 
  

```r
formula_mod  <- formula(~ sexo2 + 
                         anoest2 +
                         anoest3 +
                         anoest4 + 
                         edad2 +
                         edad3  +
                         edad4  +
                         edad5 +
                         tasa_desocupacion +
                         luces_nocturnas +
                         cubrimiento_cultivo +
                         alfabeta)
## Dominios observados
Xdat <- model.matrix(formula_mod, data = data_dir)

## Dominios no observados
Xs <- model.matrix(formula_mod, data = data_syn)
```

Ahora, se utiliza la función `setdiff()` para identificar las columnas de `Xdat` que no están presentes en $X_s$, es decir, las variables que no se encuentran en los datos no observados. A continuación, se crea una matriz temporal (`temp`) con ceros para las columnas faltantes de $X_s$, y se agregan estas columnas a $X_s$ utilizando `cbind()`. El resultado final es una matriz `Xs` con las mismas variables que `Xdat`, lo que asegura que se puedan realizar comparaciones adecuadas entre los datos observados y no observados en la construcción de modelos de regresión. En general, este código es útil para preparar los datos para su posterior análisis y asegurar que los modelos de regresión sean adecuados para su uso.


```r
temp <- setdiff(colnames(Xdat),colnames(Xs))

temp <- matrix(
  0,
  nrow = nrow(Xs),
  ncol = length(temp),
  dimnames = list(1:nrow(Xs), temp)
)

Xs <- cbind(Xs,temp)[,colnames(Xdat)]
```


  3.    Creando lista de parámetros para `STAN`


```r
sample_data <- list(
  N1 = nrow(Xdat),   # Observados.
  N2 = nrow(Xs),   # NO Observados.
  p  = ncol(Xdat),       # Número de regresores.
  X  = as.matrix(Xdat),  # Covariables Observados.
  Xs = as.matrix(Xs),    # Covariables NO Observados
  y  = as.numeric(data_dir$pobreza), # Estimación directa
  sigma_e = sqrt(data_dir$hat_var)   # Error de estimación
)
```

Rutina implementada en `STAN`


```r
data {
  int<lower=0> N1;   // number of data items
  int<lower=0> N2;   // number of data items for prediction
  int<lower=0> p;   // number of predictors
  matrix[N1, p] X;   // predictor matrix
  matrix[N2, p] Xs;   // predictor matrix
  vector[N1] y;      // predictor matrix 
  vector[N1] sigma_e; // known variances
}

parameters {
  vector[p] beta;       // coefficients for predictors
  real<lower=0> sigma2_u;
  vector[N1] u;
}

transformed parameters{
  vector[N1] theta;
  vector[N1] thetaSyn;
  vector[N1] thetaFH;
  vector[N1] gammaj;
  real<lower=0> sigma_u;
  thetaSyn = X * beta;
  theta = thetaSyn + u;
  sigma_u = sqrt(sigma2_u);
  gammaj =  to_vector(sigma_u ./ (sigma_u + sigma_e));
  thetaFH = (gammaj) .* y + (1-gammaj).*thetaSyn; 
}

model {
  // likelihood
  y ~ normal(theta, sigma_e); 
  // priors
  beta ~ normal(0, 100);
  u ~ normal(0, sigma_u);
  sigma2_u ~ inv_gamma(0.0001, 0.0001);
}

generated quantities{
  vector[N2] y_pred;
  for(j in 1:N2) {
    y_pred[j] = normal_rng(Xs[j] * beta, sigma_u);
  }
}
```

 4. Compilando el modelo en `STAN`.
A continuación mostramos la forma de compilar el código de `STAN` desde R.  

En este código se utiliza la librería `rstan` para ajustar un modelo bayesiano utilizando el archivo `17FH_normal.stan` que contiene el modelo escrito en el lenguaje de modelado probabilístico Stan.

En primer lugar, se utiliza la función `stan()` para ajustar el modelo a los datos de `sample_data`. Los argumentos que se pasan a `stan()` incluyen el archivo que contiene el modelo (`fit_FH_normal`), los datos (`sample_data`), y los argumentos para controlar el proceso de ajuste del modelo, como el número de iteraciones para el período de calentamiento (`warmup`) y el período de muestreo (`iter`), y el número de núcleos de la CPU para utilizar en el proceso de ajuste (`cores`).

Además, se utiliza la función `parallel::detectCores()` para detectar automáticamente el número de núcleos disponibles en la CPU, y se establece la opción `mc.cores` para aprovechar el número máximo de núcleos disponibles para el ajuste del modelo.

El resultado del ajuste del modelo es almacenado en `model_FH_normal`, que contiene una muestra de la distribución posterior del modelo, la cual puede ser utilizada para realizar inferencias sobre los parámetros del modelo y las predicciones. En general, este código es útil para ajustar modelos bayesianos utilizando Stan y realizar inferencias posteriores.


```r
library(rstan)
fit_FH_normal <- "Recursos/Día2/Sesion3/Data/modelosStan/17FH_normal.stan"
options(mc.cores = parallel::detectCores())
model_FH_normal <- stan(
  file = fit_FH_normal,  
  data = sample_data,   
  verbose = FALSE,
  warmup = 500,         
  iter = 1000,            
  cores = 4              
)
saveRDS(object = model_FH_normal,
        file = "Recursos/Día2/Sesion3/Data/model_FH_normal.rds")
```

Leer el modelo 

```r
model_FH_normal<- readRDS("Recursos/Día2/Sesion3/Data/model_FH_normal.rds")
```

### Resultados del modelo para los dominios observados. 

En este código, se cargan las librerías `bayesplot`, `posterior` y `patchwork`, que se utilizan para realizar gráficos y visualizaciones de los resultados del modelo.

A continuación, se utiliza la función `as.array()` y `as_draws_matrix()` para extraer las muestras de la distribución posterior del parámetro `theta` del modelo, y se seleccionan aleatoriamente 100 filas de estas muestras utilizando la función `sample()`, lo que resulta en la matriz `y_pred2.`

Finalmente, se utiliza la función `ppc_dens_overlay()` de `bayesplot` para graficar una comparación entre la distribución empírica de la variable observada pobreza en los datos (`data_dir$pobreza`) y las distribuciones predictivas posteriores simuladas para la misma variable (`y_pred2`). La función `ppc_dens_overlay()` produce un gráfico de densidad para ambas distribuciones, lo que permite visualizar cómo se comparan.


```r
library(bayesplot)
library(posterior)
library(patchwork)
y_pred_B <- as.array(model_FH_normal, pars = "theta") %>% 
  as_draws_matrix()
rowsrandom <- sample(nrow(y_pred_B), 100)
y_pred2 <- y_pred_B[rowsrandom, ]
ppc_dens_overlay(y = as.numeric(data_dir$pobreza), y_pred2)
```


<img src="Recursos/Día2/Sesion3/0Recursos/FH1.png" width="200%" />

Análisis gráfico de la convergencia de las cadenas de $\sigma^2_V$. 


```r
posterior_sigma2_u <- as.array(model_FH_normal, pars = "sigma2_u")
(mcmc_dens_chains(posterior_sigma2_u) +
    mcmc_areas(posterior_sigma2_u) ) / 
  mcmc_trace(posterior_sigma2_u)
#traceplot(model_FH_normal, pars = "sigma2_u", inc_warmup = TRUE)
```


<img src="Recursos/Día2/Sesion3/0Recursos/FH2.png" width="200%" />

Como método de validación se comparan las diferentes elementos de la estimación del modelo de FH obtenidos en `STAN`


```r
theta <-   summary(model_FH_normal,pars =  "theta")$summary %>%
  data.frame()
thetaSyn <-   summary(model_FH_normal,pars =  "thetaSyn")$summary %>%
  data.frame()
theta_FH <-   summary(model_FH_normal,pars =  "thetaFH")$summary %>%
  data.frame()

data_dir %<>% mutate(
            thetadir = pobreza,
            theta_pred = theta$mean,
            thetaSyn = thetaSyn$mean,
            thetaFH = theta_FH$mean,
            theta_pred_EE = theta$sd,
            Cv_theta_pred = theta_pred_EE/theta_pred
            ) 
# saveRDS(data_dir,"Recursos/Día2/Sesion3/0Recursos/data_dir.rds")
# Estimación predicción del modelo vs ecuación ponderada de FH 
p11 <- ggplot(data_dir, aes(x = theta_pred, y = thetaFH)) +
  geom_point() + 
  geom_abline(slope = 1,intercept = 0, colour = "red") +
  theme_bw(10) 

# Estimación con la ecuación ponderada de FH Vs estimación sintética
p12 <- ggplot(data_dir, aes(x = thetaSyn, y = thetaFH)) +
  geom_point() + 
  geom_abline(slope = 1,intercept = 0, colour = "red") +
  theme_bw(10) 

# Estimación con la ecuación ponderada de FH Vs estimación directa

p21 <- ggplot(data_dir, aes(x = thetadir, y = thetaFH)) +
  geom_point() + 
  geom_abline(slope = 1,intercept = 0, colour = "red") +
  theme_bw(10) 

# Estimación directa Vs estimación sintética

p22 <- ggplot(data_dir, aes(x = thetadir, y = thetaSyn)) +
  geom_point() + 
  geom_abline(slope = 1,intercept = 0, colour = "red") +
  theme_bw(10) 

(p11+p12)/(p21+p22)
```

<img src="Recursos/Día2/Sesion3/0Recursos/FH3.png" width="200%" />


Estimación del FH de la pobreza en los dominios NO observados. 


```r
theta_syn_pred <- summary(model_FH_normal,pars =  "y_pred")$summary %>%
  data.frame()

data_syn <- data_syn %>% 
  mutate(
    theta_pred = theta_syn_pred$mean,
    thetaSyn = theta_pred,
    thetaFH = theta_pred,
    theta_pred_EE = theta_syn_pred$sd,
    Cv_theta_pred = theta_pred_EE/theta_pred)

#saveRDS(data_syn,"Recursos/Día2/Sesion3/0Recursos/data_syn.rds")
tba(data_syn %>% slice(1:10) %>%
      select(dam2:hat_var,theta_pred:Cv_theta_pred))
```

<table class="table table-striped lightable-classic" style="width: auto !important; margin-left: auto; margin-right: auto; font-family: Arial Narrow; width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> dam2 </th>
   <th style="text-align:right;"> nd </th>
   <th style="text-align:right;"> pobreza </th>
   <th style="text-align:right;"> vardir </th>
   <th style="text-align:right;"> hat_var </th>
   <th style="text-align:right;"> theta_pred </th>
   <th style="text-align:right;"> thetaSyn </th>
   <th style="text-align:right;"> thetaFH </th>
   <th style="text-align:right;"> theta_pred_EE </th>
   <th style="text-align:right;"> Cv_theta_pred </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 00202 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> 0.2939 </td>
   <td style="text-align:right;"> 0.2939 </td>
   <td style="text-align:right;"> 0.2939 </td>
   <td style="text-align:right;"> 0.0934 </td>
   <td style="text-align:right;"> 0.3177 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00203 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> 0.2816 </td>
   <td style="text-align:right;"> 0.2816 </td>
   <td style="text-align:right;"> 0.2816 </td>
   <td style="text-align:right;"> 0.0881 </td>
   <td style="text-align:right;"> 0.3131 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00204 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> 0.2930 </td>
   <td style="text-align:right;"> 0.2930 </td>
   <td style="text-align:right;"> 0.2930 </td>
   <td style="text-align:right;"> 0.0916 </td>
   <td style="text-align:right;"> 0.3128 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00205 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> 0.1812 </td>
   <td style="text-align:right;"> 0.1812 </td>
   <td style="text-align:right;"> 0.1812 </td>
   <td style="text-align:right;"> 0.0977 </td>
   <td style="text-align:right;"> 0.5392 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00207 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> 0.3500 </td>
   <td style="text-align:right;"> 0.3500 </td>
   <td style="text-align:right;"> 0.3500 </td>
   <td style="text-align:right;"> 0.0921 </td>
   <td style="text-align:right;"> 0.2632 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00208 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> 0.2330 </td>
   <td style="text-align:right;"> 0.2330 </td>
   <td style="text-align:right;"> 0.2330 </td>
   <td style="text-align:right;"> 0.1001 </td>
   <td style="text-align:right;"> 0.4294 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00209 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> 0.2275 </td>
   <td style="text-align:right;"> 0.2275 </td>
   <td style="text-align:right;"> 0.2275 </td>
   <td style="text-align:right;"> 0.1214 </td>
   <td style="text-align:right;"> 0.5338 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00210 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> 0.2784 </td>
   <td style="text-align:right;"> 0.2784 </td>
   <td style="text-align:right;"> 0.2784 </td>
   <td style="text-align:right;"> 0.1013 </td>
   <td style="text-align:right;"> 0.3639 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00305 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> 0.4831 </td>
   <td style="text-align:right;"> 0.4831 </td>
   <td style="text-align:right;"> 0.4831 </td>
   <td style="text-align:right;"> 0.0975 </td>
   <td style="text-align:right;"> 0.2019 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00404 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> 0.4305 </td>
   <td style="text-align:right;"> 0.4305 </td>
   <td style="text-align:right;"> 0.4305 </td>
   <td style="text-align:right;"> 0.1088 </td>
   <td style="text-align:right;"> 0.2527 </td>
  </tr>
</tbody>
</table>



consolidando las bases de estimaciones para dominios observados y NO observados. 


```r
estimacionesPre <- bind_rows(data_dir, data_syn) %>% 
  select(dam2, theta_pred)
```


## Proceso de Benchmark 

1. Del censo extraer el total de personas por DAM2 



```r
total_pp <- readRDS(file = "Recursos/Día2/Sesion3/Data/total_personas_dam2.rds")

N_dam_pp <- total_pp %>%   group_by(region) %>%  
            mutate(region_pp = sum(pp_dam2) ) 

tba(N_dam_pp %>% slice(1:20))
```

<table class="table table-striped lightable-classic" style="width: auto !important; margin-left: auto; margin-right: auto; font-family: Arial Narrow; width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> region </th>
   <th style="text-align:left;"> nombre_region </th>
   <th style="text-align:left;"> dam </th>
   <th style="text-align:left;"> dam2 </th>
   <th style="text-align:left;"> id_municipio </th>
   <th style="text-align:right;"> pp_dam2 </th>
   <th style="text-align:right;"> region_pp </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> Región Cibao Norte </td>
   <td style="text-align:left;"> 09 </td>
   <td style="text-align:left;"> 00901 </td>
   <td style="text-align:left;"> 010901 </td>
   <td style="text-align:right;"> 179829 </td>
   <td style="text-align:right;"> 1516957 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> Región Cibao Norte </td>
   <td style="text-align:left;"> 09 </td>
   <td style="text-align:left;"> 00902 </td>
   <td style="text-align:left;"> 010902 </td>
   <td style="text-align:right;"> 6911 </td>
   <td style="text-align:right;"> 1516957 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> Región Cibao Norte </td>
   <td style="text-align:left;"> 09 </td>
   <td style="text-align:left;"> 00903 </td>
   <td style="text-align:left;"> 010903 </td>
   <td style="text-align:right;"> 37378 </td>
   <td style="text-align:right;"> 1516957 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> Región Cibao Norte </td>
   <td style="text-align:left;"> 09 </td>
   <td style="text-align:left;"> 00904 </td>
   <td style="text-align:left;"> 010904 </td>
   <td style="text-align:right;"> 7820 </td>
   <td style="text-align:right;"> 1516957 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> Región Cibao Norte </td>
   <td style="text-align:left;"> 18 </td>
   <td style="text-align:left;"> 01801 </td>
   <td style="text-align:left;"> 011801 </td>
   <td style="text-align:right;"> 158756 </td>
   <td style="text-align:right;"> 1516957 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> Región Cibao Norte </td>
   <td style="text-align:left;"> 18 </td>
   <td style="text-align:left;"> 01802 </td>
   <td style="text-align:left;"> 011802 </td>
   <td style="text-align:right;"> 18868 </td>
   <td style="text-align:right;"> 1516957 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> Región Cibao Norte </td>
   <td style="text-align:left;"> 18 </td>
   <td style="text-align:left;"> 01803 </td>
   <td style="text-align:left;"> 011803 </td>
   <td style="text-align:right;"> 6333 </td>
   <td style="text-align:right;"> 1516957 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> Región Cibao Norte </td>
   <td style="text-align:left;"> 18 </td>
   <td style="text-align:left;"> 01804 </td>
   <td style="text-align:left;"> 011804 </td>
   <td style="text-align:right;"> 22058 </td>
   <td style="text-align:right;"> 1516957 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> Región Cibao Norte </td>
   <td style="text-align:left;"> 18 </td>
   <td style="text-align:left;"> 01805 </td>
   <td style="text-align:left;"> 011805 </td>
   <td style="text-align:right;"> 12639 </td>
   <td style="text-align:right;"> 1516957 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> Región Cibao Norte </td>
   <td style="text-align:left;"> 18 </td>
   <td style="text-align:left;"> 01806 </td>
   <td style="text-align:left;"> 011806 </td>
   <td style="text-align:right;"> 16464 </td>
   <td style="text-align:right;"> 1516957 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> Región Cibao Norte </td>
   <td style="text-align:left;"> 18 </td>
   <td style="text-align:left;"> 01807 </td>
   <td style="text-align:left;"> 011807 </td>
   <td style="text-align:right;"> 49593 </td>
   <td style="text-align:right;"> 1516957 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> Región Cibao Norte </td>
   <td style="text-align:left;"> 18 </td>
   <td style="text-align:left;"> 01808 </td>
   <td style="text-align:left;"> 011808 </td>
   <td style="text-align:right;"> 17169 </td>
   <td style="text-align:right;"> 1516957 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> Región Cibao Norte </td>
   <td style="text-align:left;"> 18 </td>
   <td style="text-align:left;"> 01809 </td>
   <td style="text-align:left;"> 011809 </td>
   <td style="text-align:right;"> 19717 </td>
   <td style="text-align:right;"> 1516957 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> Región Cibao Norte </td>
   <td style="text-align:left;"> 25 </td>
   <td style="text-align:left;"> 02501 </td>
   <td style="text-align:left;"> 012501 </td>
   <td style="text-align:right;"> 691262 </td>
   <td style="text-align:right;"> 1516957 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> Región Cibao Norte </td>
   <td style="text-align:left;"> 25 </td>
   <td style="text-align:left;"> 02502 </td>
   <td style="text-align:left;"> 012502 </td>
   <td style="text-align:right;"> 42092 </td>
   <td style="text-align:right;"> 1516957 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> Región Cibao Norte </td>
   <td style="text-align:left;"> 25 </td>
   <td style="text-align:left;"> 02503 </td>
   <td style="text-align:left;"> 012503 </td>
   <td style="text-align:right;"> 16993 </td>
   <td style="text-align:right;"> 1516957 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> Región Cibao Norte </td>
   <td style="text-align:left;"> 25 </td>
   <td style="text-align:left;"> 02504 </td>
   <td style="text-align:left;"> 012504 </td>
   <td style="text-align:right;"> 25539 </td>
   <td style="text-align:right;"> 1516957 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> Región Cibao Norte </td>
   <td style="text-align:left;"> 25 </td>
   <td style="text-align:left;"> 02505 </td>
   <td style="text-align:left;"> 012505 </td>
   <td style="text-align:right;"> 38628 </td>
   <td style="text-align:right;"> 1516957 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> Región Cibao Norte </td>
   <td style="text-align:left;"> 25 </td>
   <td style="text-align:left;"> 02506 </td>
   <td style="text-align:left;"> 012506 </td>
   <td style="text-align:right;"> 51695 </td>
   <td style="text-align:right;"> 1516957 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> Región Cibao Norte </td>
   <td style="text-align:left;"> 25 </td>
   <td style="text-align:left;"> 02507 </td>
   <td style="text-align:left;"> 012507 </td>
   <td style="text-align:right;"> 37349 </td>
   <td style="text-align:right;"> 1516957 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> Región Cibao Sur </td>
   <td style="text-align:left;"> 13 </td>
   <td style="text-align:left;"> 01301 </td>
   <td style="text-align:left;"> 021301 </td>
   <td style="text-align:right;"> 248089 </td>
   <td style="text-align:right;"> 710821 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> Región Cibao Sur </td>
   <td style="text-align:left;"> 13 </td>
   <td style="text-align:left;"> 01302 </td>
   <td style="text-align:left;"> 021302 </td>
   <td style="text-align:right;"> 59052 </td>
   <td style="text-align:right;"> 710821 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> Región Cibao Sur </td>
   <td style="text-align:left;"> 13 </td>
   <td style="text-align:left;"> 01303 </td>
   <td style="text-align:left;"> 021303 </td>
   <td style="text-align:right;"> 56803 </td>
   <td style="text-align:right;"> 710821 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> Región Cibao Sur </td>
   <td style="text-align:left;"> 13 </td>
   <td style="text-align:left;"> 01304 </td>
   <td style="text-align:left;"> 021304 </td>
   <td style="text-align:right;"> 30261 </td>
   <td style="text-align:right;"> 710821 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> Región Cibao Sur </td>
   <td style="text-align:left;"> 24 </td>
   <td style="text-align:left;"> 02401 </td>
   <td style="text-align:left;"> 022401 </td>
   <td style="text-align:right;"> 76554 </td>
   <td style="text-align:right;"> 710821 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> Región Cibao Sur </td>
   <td style="text-align:left;"> 24 </td>
   <td style="text-align:left;"> 02402 </td>
   <td style="text-align:left;"> 022402 </td>
   <td style="text-align:right;"> 13759 </td>
   <td style="text-align:right;"> 710821 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> Región Cibao Sur </td>
   <td style="text-align:left;"> 24 </td>
   <td style="text-align:left;"> 02403 </td>
   <td style="text-align:left;"> 022403 </td>
   <td style="text-align:right;"> 22117 </td>
   <td style="text-align:right;"> 710821 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> Región Cibao Sur </td>
   <td style="text-align:left;"> 24 </td>
   <td style="text-align:left;"> 02404 </td>
   <td style="text-align:left;"> 022404 </td>
   <td style="text-align:right;"> 38962 </td>
   <td style="text-align:right;"> 710821 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> Región Cibao Sur </td>
   <td style="text-align:left;"> 28 </td>
   <td style="text-align:left;"> 02801 </td>
   <td style="text-align:left;"> 022801 </td>
   <td style="text-align:right;"> 125338 </td>
   <td style="text-align:right;"> 710821 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> Región Cibao Sur </td>
   <td style="text-align:left;"> 28 </td>
   <td style="text-align:left;"> 02802 </td>
   <td style="text-align:left;"> 022802 </td>
   <td style="text-align:right;"> 18952 </td>
   <td style="text-align:right;"> 710821 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> Región Cibao Sur </td>
   <td style="text-align:left;"> 28 </td>
   <td style="text-align:left;"> 02803 </td>
   <td style="text-align:left;"> 022803 </td>
   <td style="text-align:right;"> 20934 </td>
   <td style="text-align:right;"> 710821 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:left;"> Región Cibao Nordeste </td>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> 00601 </td>
   <td style="text-align:left;"> 030601 </td>
   <td style="text-align:right;"> 188118 </td>
   <td style="text-align:right;"> 624186 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:left;"> Región Cibao Nordeste </td>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> 00602 </td>
   <td style="text-align:left;"> 030602 </td>
   <td style="text-align:right;"> 14062 </td>
   <td style="text-align:right;"> 624186 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:left;"> Región Cibao Nordeste </td>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> 00603 </td>
   <td style="text-align:left;"> 030603 </td>
   <td style="text-align:right;"> 15709 </td>
   <td style="text-align:right;"> 624186 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:left;"> Región Cibao Nordeste </td>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> 00604 </td>
   <td style="text-align:left;"> 030604 </td>
   <td style="text-align:right;"> 17864 </td>
   <td style="text-align:right;"> 624186 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:left;"> Región Cibao Nordeste </td>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> 00605 </td>
   <td style="text-align:left;"> 030605 </td>
   <td style="text-align:right;"> 33663 </td>
   <td style="text-align:right;"> 624186 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:left;"> Región Cibao Nordeste </td>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> 00606 </td>
   <td style="text-align:left;"> 030606 </td>
   <td style="text-align:right;"> 14661 </td>
   <td style="text-align:right;"> 624186 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:left;"> Región Cibao Nordeste </td>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> 00607 </td>
   <td style="text-align:left;"> 030607 </td>
   <td style="text-align:right;"> 5497 </td>
   <td style="text-align:right;"> 624186 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:left;"> Región Cibao Nordeste </td>
   <td style="text-align:left;"> 14 </td>
   <td style="text-align:left;"> 01401 </td>
   <td style="text-align:left;"> 031401 </td>
   <td style="text-align:right;"> 76993 </td>
   <td style="text-align:right;"> 624186 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:left;"> Región Cibao Nordeste </td>
   <td style="text-align:left;"> 14 </td>
   <td style="text-align:left;"> 01402 </td>
   <td style="text-align:left;"> 031402 </td>
   <td style="text-align:right;"> 24524 </td>
   <td style="text-align:right;"> 624186 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:left;"> Región Cibao Nordeste </td>
   <td style="text-align:left;"> 14 </td>
   <td style="text-align:left;"> 01403 </td>
   <td style="text-align:left;"> 031403 </td>
   <td style="text-align:right;"> 24240 </td>
   <td style="text-align:right;"> 624186 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:left;"> Región Cibao Nordeste </td>
   <td style="text-align:left;"> 14 </td>
   <td style="text-align:left;"> 01404 </td>
   <td style="text-align:left;"> 031404 </td>
   <td style="text-align:right;"> 15168 </td>
   <td style="text-align:right;"> 624186 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:left;"> Región Cibao Nordeste </td>
   <td style="text-align:left;"> 19 </td>
   <td style="text-align:left;"> 01901 </td>
   <td style="text-align:left;"> 031901 </td>
   <td style="text-align:right;"> 39557 </td>
   <td style="text-align:right;"> 624186 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:left;"> Región Cibao Nordeste </td>
   <td style="text-align:left;"> 19 </td>
   <td style="text-align:left;"> 01902 </td>
   <td style="text-align:left;"> 031902 </td>
   <td style="text-align:right;"> 27765 </td>
   <td style="text-align:right;"> 624186 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:left;"> Región Cibao Nordeste </td>
   <td style="text-align:left;"> 19 </td>
   <td style="text-align:left;"> 01903 </td>
   <td style="text-align:left;"> 031903 </td>
   <td style="text-align:right;"> 24871 </td>
   <td style="text-align:right;"> 624186 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:left;"> Región Cibao Nordeste </td>
   <td style="text-align:left;"> 20 </td>
   <td style="text-align:left;"> 02001 </td>
   <td style="text-align:left;"> 032001 </td>
   <td style="text-align:right;"> 58156 </td>
   <td style="text-align:right;"> 624186 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:left;"> Región Cibao Nordeste </td>
   <td style="text-align:left;"> 20 </td>
   <td style="text-align:left;"> 02002 </td>
   <td style="text-align:left;"> 032002 </td>
   <td style="text-align:right;"> 24509 </td>
   <td style="text-align:right;"> 624186 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:left;"> Región Cibao Nordeste </td>
   <td style="text-align:left;"> 20 </td>
   <td style="text-align:left;"> 02003 </td>
   <td style="text-align:left;"> 032003 </td>
   <td style="text-align:right;"> 18829 </td>
   <td style="text-align:right;"> 624186 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:left;"> Región Cibao Noroeste </td>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:left;"> 00501 </td>
   <td style="text-align:left;"> 040501 </td>
   <td style="text-align:right;"> 28071 </td>
   <td style="text-align:right;"> 394068 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:left;"> Región Cibao Noroeste </td>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:left;"> 00502 </td>
   <td style="text-align:left;"> 040502 </td>
   <td style="text-align:right;"> 15624 </td>
   <td style="text-align:right;"> 394068 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:left;"> Región Cibao Noroeste </td>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:left;"> 00503 </td>
   <td style="text-align:left;"> 040503 </td>
   <td style="text-align:right;"> 6951 </td>
   <td style="text-align:right;"> 394068 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:left;"> Región Cibao Noroeste </td>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:left;"> 00504 </td>
   <td style="text-align:left;"> 040504 </td>
   <td style="text-align:right;"> 7274 </td>
   <td style="text-align:right;"> 394068 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:left;"> Región Cibao Noroeste </td>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:left;"> 00505 </td>
   <td style="text-align:left;"> 040505 </td>
   <td style="text-align:right;"> 6035 </td>
   <td style="text-align:right;"> 394068 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:left;"> Región Cibao Noroeste </td>
   <td style="text-align:left;"> 15 </td>
   <td style="text-align:left;"> 01501 </td>
   <td style="text-align:left;"> 041501 </td>
   <td style="text-align:right;"> 24644 </td>
   <td style="text-align:right;"> 394068 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:left;"> Región Cibao Noroeste </td>
   <td style="text-align:left;"> 15 </td>
   <td style="text-align:left;"> 01502 </td>
   <td style="text-align:left;"> 041502 </td>
   <td style="text-align:right;"> 14921 </td>
   <td style="text-align:right;"> 394068 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:left;"> Región Cibao Noroeste </td>
   <td style="text-align:left;"> 15 </td>
   <td style="text-align:left;"> 01503 </td>
   <td style="text-align:left;"> 041503 </td>
   <td style="text-align:right;"> 35923 </td>
   <td style="text-align:right;"> 394068 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:left;"> Región Cibao Noroeste </td>
   <td style="text-align:left;"> 15 </td>
   <td style="text-align:left;"> 01504 </td>
   <td style="text-align:left;"> 041504 </td>
   <td style="text-align:right;"> 10559 </td>
   <td style="text-align:right;"> 394068 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:left;"> Región Cibao Noroeste </td>
   <td style="text-align:left;"> 15 </td>
   <td style="text-align:left;"> 01505 </td>
   <td style="text-align:left;"> 041505 </td>
   <td style="text-align:right;"> 9136 </td>
   <td style="text-align:right;"> 394068 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:left;"> Región Cibao Noroeste </td>
   <td style="text-align:left;"> 15 </td>
   <td style="text-align:left;"> 01506 </td>
   <td style="text-align:left;"> 041506 </td>
   <td style="text-align:right;"> 14424 </td>
   <td style="text-align:right;"> 394068 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:left;"> Región Cibao Noroeste </td>
   <td style="text-align:left;"> 26 </td>
   <td style="text-align:left;"> 02601 </td>
   <td style="text-align:left;"> 042601 </td>
   <td style="text-align:right;"> 34540 </td>
   <td style="text-align:right;"> 394068 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:left;"> Región Cibao Noroeste </td>
   <td style="text-align:left;"> 26 </td>
   <td style="text-align:left;"> 02602 </td>
   <td style="text-align:left;"> 042602 </td>
   <td style="text-align:right;"> 11183 </td>
   <td style="text-align:right;"> 394068 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:left;"> Región Cibao Noroeste </td>
   <td style="text-align:left;"> 26 </td>
   <td style="text-align:left;"> 02603 </td>
   <td style="text-align:left;"> 042603 </td>
   <td style="text-align:right;"> 11753 </td>
   <td style="text-align:right;"> 394068 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:left;"> Región Cibao Noroeste </td>
   <td style="text-align:left;"> 27 </td>
   <td style="text-align:left;"> 02701 </td>
   <td style="text-align:left;"> 042701 </td>
   <td style="text-align:right;"> 76863 </td>
   <td style="text-align:right;"> 394068 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:left;"> Región Cibao Noroeste </td>
   <td style="text-align:left;"> 27 </td>
   <td style="text-align:left;"> 02702 </td>
   <td style="text-align:left;"> 042702 </td>
   <td style="text-align:right;"> 62205 </td>
   <td style="text-align:right;"> 394068 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:left;"> Región Cibao Noroeste </td>
   <td style="text-align:left;"> 27 </td>
   <td style="text-align:left;"> 02703 </td>
   <td style="text-align:left;"> 042703 </td>
   <td style="text-align:right;"> 23962 </td>
   <td style="text-align:right;"> 394068 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:left;"> Región Valdesia </td>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> 00201 </td>
   <td style="text-align:left;"> 050201 </td>
   <td style="text-align:right;"> 91345 </td>
   <td style="text-align:right;"> 1028129 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:left;"> Región Valdesia </td>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> 00202 </td>
   <td style="text-align:left;"> 050202 </td>
   <td style="text-align:right;"> 11243 </td>
   <td style="text-align:right;"> 1028129 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:left;"> Región Valdesia </td>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> 00203 </td>
   <td style="text-align:left;"> 050203 </td>
   <td style="text-align:right;"> 17620 </td>
   <td style="text-align:right;"> 1028129 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:left;"> Región Valdesia </td>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> 00204 </td>
   <td style="text-align:left;"> 050204 </td>
   <td style="text-align:right;"> 20041 </td>
   <td style="text-align:right;"> 1028129 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:left;"> Región Valdesia </td>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> 00205 </td>
   <td style="text-align:left;"> 050205 </td>
   <td style="text-align:right;"> 15257 </td>
   <td style="text-align:right;"> 1028129 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:left;"> Región Valdesia </td>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> 00206 </td>
   <td style="text-align:left;"> 050206 </td>
   <td style="text-align:right;"> 19020 </td>
   <td style="text-align:right;"> 1028129 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:left;"> Región Valdesia </td>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> 00207 </td>
   <td style="text-align:left;"> 050207 </td>
   <td style="text-align:right;"> 11235 </td>
   <td style="text-align:right;"> 1028129 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:left;"> Región Valdesia </td>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> 00208 </td>
   <td style="text-align:left;"> 050208 </td>
   <td style="text-align:right;"> 17647 </td>
   <td style="text-align:right;"> 1028129 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:left;"> Región Valdesia </td>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> 00209 </td>
   <td style="text-align:left;"> 050209 </td>
   <td style="text-align:right;"> 5263 </td>
   <td style="text-align:right;"> 1028129 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:left;"> Región Valdesia </td>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> 00210 </td>
   <td style="text-align:left;"> 050210 </td>
   <td style="text-align:right;"> 5640 </td>
   <td style="text-align:right;"> 1028129 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:left;"> Región Valdesia </td>
   <td style="text-align:left;"> 17 </td>
   <td style="text-align:left;"> 01701 </td>
   <td style="text-align:left;"> 051701 </td>
   <td style="text-align:right;"> 157316 </td>
   <td style="text-align:right;"> 1028129 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:left;"> Región Valdesia </td>
   <td style="text-align:left;"> 17 </td>
   <td style="text-align:left;"> 01702 </td>
   <td style="text-align:left;"> 051702 </td>
   <td style="text-align:right;"> 27028 </td>
   <td style="text-align:right;"> 1028129 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:left;"> Región Valdesia </td>
   <td style="text-align:left;"> 21 </td>
   <td style="text-align:left;"> 02101 </td>
   <td style="text-align:left;"> 052101 </td>
   <td style="text-align:right;"> 232769 </td>
   <td style="text-align:right;"> 1028129 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:left;"> Región Valdesia </td>
   <td style="text-align:left;"> 21 </td>
   <td style="text-align:left;"> 02102 </td>
   <td style="text-align:left;"> 052102 </td>
   <td style="text-align:right;"> 15466 </td>
   <td style="text-align:right;"> 1028129 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:left;"> Región Valdesia </td>
   <td style="text-align:left;"> 21 </td>
   <td style="text-align:left;"> 02103 </td>
   <td style="text-align:left;"> 052103 </td>
   <td style="text-align:right;"> 124193 </td>
   <td style="text-align:right;"> 1028129 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:left;"> Región Valdesia </td>
   <td style="text-align:left;"> 21 </td>
   <td style="text-align:left;"> 02104 </td>
   <td style="text-align:left;"> 052104 </td>
   <td style="text-align:right;"> 31057 </td>
   <td style="text-align:right;"> 1028129 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:left;"> Región Valdesia </td>
   <td style="text-align:left;"> 21 </td>
   <td style="text-align:left;"> 02105 </td>
   <td style="text-align:left;"> 052105 </td>
   <td style="text-align:right;"> 84312 </td>
   <td style="text-align:right;"> 1028129 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:left;"> Región Valdesia </td>
   <td style="text-align:left;"> 21 </td>
   <td style="text-align:left;"> 02106 </td>
   <td style="text-align:left;"> 052106 </td>
   <td style="text-align:right;"> 42325 </td>
   <td style="text-align:right;"> 1028129 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:left;"> Región Valdesia </td>
   <td style="text-align:left;"> 21 </td>
   <td style="text-align:left;"> 02107 </td>
   <td style="text-align:left;"> 052107 </td>
   <td style="text-align:right;"> 30268 </td>
   <td style="text-align:right;"> 1028129 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:left;"> Región Valdesia </td>
   <td style="text-align:left;"> 21 </td>
   <td style="text-align:left;"> 02108 </td>
   <td style="text-align:left;"> 052108 </td>
   <td style="text-align:right;"> 9540 </td>
   <td style="text-align:right;"> 1028129 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> Región Enriquillo </td>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:left;"> 00301 </td>
   <td style="text-align:left;"> 060301 </td>
   <td style="text-align:right;"> 36511 </td>
   <td style="text-align:right;"> 368594 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> Región Enriquillo </td>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:left;"> 00302 </td>
   <td style="text-align:left;"> 060302 </td>
   <td style="text-align:right;"> 15702 </td>
   <td style="text-align:right;"> 368594 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> Región Enriquillo </td>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:left;"> 00303 </td>
   <td style="text-align:left;"> 060303 </td>
   <td style="text-align:right;"> 26772 </td>
   <td style="text-align:right;"> 368594 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> Región Enriquillo </td>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:left;"> 00304 </td>
   <td style="text-align:left;"> 060304 </td>
   <td style="text-align:right;"> 10619 </td>
   <td style="text-align:right;"> 368594 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> Región Enriquillo </td>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:left;"> 00305 </td>
   <td style="text-align:left;"> 060305 </td>
   <td style="text-align:right;"> 7709 </td>
   <td style="text-align:right;"> 368594 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> Región Enriquillo </td>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:left;"> 00401 </td>
   <td style="text-align:left;"> 060401 </td>
   <td style="text-align:right;"> 83619 </td>
   <td style="text-align:right;"> 368594 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> Región Enriquillo </td>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:left;"> 00402 </td>
   <td style="text-align:left;"> 060402 </td>
   <td style="text-align:right;"> 14823 </td>
   <td style="text-align:right;"> 368594 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> Región Enriquillo </td>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:left;"> 00403 </td>
   <td style="text-align:left;"> 060403 </td>
   <td style="text-align:right;"> 13164 </td>
   <td style="text-align:right;"> 368594 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> Región Enriquillo </td>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:left;"> 00404 </td>
   <td style="text-align:left;"> 060404 </td>
   <td style="text-align:right;"> 15390 </td>
   <td style="text-align:right;"> 368594 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> Región Enriquillo </td>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:left;"> 00405 </td>
   <td style="text-align:left;"> 060405 </td>
   <td style="text-align:right;"> 21605 </td>
   <td style="text-align:right;"> 368594 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> Región Enriquillo </td>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:left;"> 00406 </td>
   <td style="text-align:left;"> 060406 </td>
   <td style="text-align:right;"> 3970 </td>
   <td style="text-align:right;"> 368594 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> Región Enriquillo </td>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:left;"> 00407 </td>
   <td style="text-align:left;"> 060407 </td>
   <td style="text-align:right;"> 9112 </td>
   <td style="text-align:right;"> 368594 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> Región Enriquillo </td>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:left;"> 00408 </td>
   <td style="text-align:left;"> 060408 </td>
   <td style="text-align:right;"> 8042 </td>
   <td style="text-align:right;"> 368594 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> Región Enriquillo </td>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:left;"> 00409 </td>
   <td style="text-align:left;"> 060409 </td>
   <td style="text-align:right;"> 4703 </td>
   <td style="text-align:right;"> 368594 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> Región Enriquillo </td>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:left;"> 00410 </td>
   <td style="text-align:left;"> 060410 </td>
   <td style="text-align:right;"> 8186 </td>
   <td style="text-align:right;"> 368594 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> Región Enriquillo </td>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:left;"> 00411 </td>
   <td style="text-align:left;"> 060411 </td>
   <td style="text-align:right;"> 4491 </td>
   <td style="text-align:right;"> 368594 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> Región Enriquillo </td>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:left;"> 01001 </td>
   <td style="text-align:left;"> 061001 </td>
   <td style="text-align:right;"> 16510 </td>
   <td style="text-align:right;"> 368594 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> Región Enriquillo </td>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:left;"> 01002 </td>
   <td style="text-align:left;"> 061002 </td>
   <td style="text-align:right;"> 12029 </td>
   <td style="text-align:right;"> 368594 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> Región Enriquillo </td>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:left;"> 01003 </td>
   <td style="text-align:left;"> 061003 </td>
   <td style="text-align:right;"> 8310 </td>
   <td style="text-align:right;"> 368594 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> Región Enriquillo </td>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:left;"> 01004 </td>
   <td style="text-align:left;"> 061004 </td>
   <td style="text-align:right;"> 5668 </td>
   <td style="text-align:right;"> 368594 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 07 </td>
   <td style="text-align:left;"> Región EL Valle </td>
   <td style="text-align:left;"> 07 </td>
   <td style="text-align:left;"> 00701 </td>
   <td style="text-align:left;"> 070701 </td>
   <td style="text-align:right;"> 25924 </td>
   <td style="text-align:right;"> 295362 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 07 </td>
   <td style="text-align:left;"> Región EL Valle </td>
   <td style="text-align:left;"> 07 </td>
   <td style="text-align:left;"> 00702 </td>
   <td style="text-align:left;"> 070702 </td>
   <td style="text-align:right;"> 6533 </td>
   <td style="text-align:right;"> 295362 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 07 </td>
   <td style="text-align:left;"> Región EL Valle </td>
   <td style="text-align:left;"> 07 </td>
   <td style="text-align:left;"> 00703 </td>
   <td style="text-align:left;"> 070703 </td>
   <td style="text-align:right;"> 8344 </td>
   <td style="text-align:right;"> 295362 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 07 </td>
   <td style="text-align:left;"> Región EL Valle </td>
   <td style="text-align:left;"> 07 </td>
   <td style="text-align:left;"> 00704 </td>
   <td style="text-align:left;"> 070704 </td>
   <td style="text-align:right;"> 10587 </td>
   <td style="text-align:right;"> 295362 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 07 </td>
   <td style="text-align:left;"> Región EL Valle </td>
   <td style="text-align:left;"> 07 </td>
   <td style="text-align:left;"> 00705 </td>
   <td style="text-align:left;"> 070705 </td>
   <td style="text-align:right;"> 7281 </td>
   <td style="text-align:right;"> 295362 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 07 </td>
   <td style="text-align:left;"> Región EL Valle </td>
   <td style="text-align:left;"> 07 </td>
   <td style="text-align:left;"> 00706 </td>
   <td style="text-align:left;"> 070706 </td>
   <td style="text-align:right;"> 4360 </td>
   <td style="text-align:right;"> 295362 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 07 </td>
   <td style="text-align:left;"> Región EL Valle </td>
   <td style="text-align:left;"> 22 </td>
   <td style="text-align:left;"> 02201 </td>
   <td style="text-align:left;"> 072201 </td>
   <td style="text-align:right;"> 132177 </td>
   <td style="text-align:right;"> 295362 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 07 </td>
   <td style="text-align:left;"> Región EL Valle </td>
   <td style="text-align:left;"> 22 </td>
   <td style="text-align:left;"> 02202 </td>
   <td style="text-align:left;"> 072202 </td>
   <td style="text-align:right;"> 9685 </td>
   <td style="text-align:right;"> 295362 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 07 </td>
   <td style="text-align:left;"> Región EL Valle </td>
   <td style="text-align:left;"> 22 </td>
   <td style="text-align:left;"> 02203 </td>
   <td style="text-align:left;"> 072203 </td>
   <td style="text-align:right;"> 20843 </td>
   <td style="text-align:right;"> 295362 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 07 </td>
   <td style="text-align:left;"> Región EL Valle </td>
   <td style="text-align:left;"> 22 </td>
   <td style="text-align:left;"> 02204 </td>
   <td style="text-align:left;"> 072204 </td>
   <td style="text-align:right;"> 13062 </td>
   <td style="text-align:right;"> 295362 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 07 </td>
   <td style="text-align:left;"> Región EL Valle </td>
   <td style="text-align:left;"> 22 </td>
   <td style="text-align:left;"> 02205 </td>
   <td style="text-align:left;"> 072205 </td>
   <td style="text-align:right;"> 44163 </td>
   <td style="text-align:right;"> 295362 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 07 </td>
   <td style="text-align:left;"> Región EL Valle </td>
   <td style="text-align:left;"> 22 </td>
   <td style="text-align:left;"> 02206 </td>
   <td style="text-align:left;"> 072206 </td>
   <td style="text-align:right;"> 12403 </td>
   <td style="text-align:right;"> 295362 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 08 </td>
   <td style="text-align:left;"> Región Yuma </td>
   <td style="text-align:left;"> 08 </td>
   <td style="text-align:left;"> 00801 </td>
   <td style="text-align:left;"> 080801 </td>
   <td style="text-align:right;"> 66867 </td>
   <td style="text-align:right;"> 606323 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 08 </td>
   <td style="text-align:left;"> Región Yuma </td>
   <td style="text-align:left;"> 08 </td>
   <td style="text-align:left;"> 00802 </td>
   <td style="text-align:left;"> 080802 </td>
   <td style="text-align:right;"> 20813 </td>
   <td style="text-align:right;"> 606323 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 08 </td>
   <td style="text-align:left;"> Región Yuma </td>
   <td style="text-align:left;"> 11 </td>
   <td style="text-align:left;"> 01101 </td>
   <td style="text-align:left;"> 081101 </td>
   <td style="text-align:right;"> 251243 </td>
   <td style="text-align:right;"> 606323 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 08 </td>
   <td style="text-align:left;"> Región Yuma </td>
   <td style="text-align:left;"> 11 </td>
   <td style="text-align:left;"> 01102 </td>
   <td style="text-align:left;"> 081102 </td>
   <td style="text-align:right;"> 21967 </td>
   <td style="text-align:right;"> 606323 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 08 </td>
   <td style="text-align:left;"> Región Yuma </td>
   <td style="text-align:left;"> 12 </td>
   <td style="text-align:left;"> 01201 </td>
   <td style="text-align:left;"> 081201 </td>
   <td style="text-align:right;"> 139671 </td>
   <td style="text-align:right;"> 606323 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 08 </td>
   <td style="text-align:left;"> Región Yuma </td>
   <td style="text-align:left;"> 12 </td>
   <td style="text-align:left;"> 01202 </td>
   <td style="text-align:left;"> 081202 </td>
   <td style="text-align:right;"> 16558 </td>
   <td style="text-align:right;"> 606323 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 08 </td>
   <td style="text-align:left;"> Región Yuma </td>
   <td style="text-align:left;"> 12 </td>
   <td style="text-align:left;"> 01203 </td>
   <td style="text-align:left;"> 081203 </td>
   <td style="text-align:right;"> 89204 </td>
   <td style="text-align:right;"> 606323 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 09 </td>
   <td style="text-align:left;"> Región Higuamo </td>
   <td style="text-align:left;"> 23 </td>
   <td style="text-align:left;"> 02301 </td>
   <td style="text-align:left;"> 092301 </td>
   <td style="text-align:right;"> 195307 </td>
   <td style="text-align:right;"> 561431 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 09 </td>
   <td style="text-align:left;"> Región Higuamo </td>
   <td style="text-align:left;"> 23 </td>
   <td style="text-align:left;"> 02302 </td>
   <td style="text-align:left;"> 092302 </td>
   <td style="text-align:right;"> 22573 </td>
   <td style="text-align:right;"> 561431 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 09 </td>
   <td style="text-align:left;"> Región Higuamo </td>
   <td style="text-align:left;"> 23 </td>
   <td style="text-align:left;"> 02303 </td>
   <td style="text-align:left;"> 092303 </td>
   <td style="text-align:right;"> 8901 </td>
   <td style="text-align:right;"> 561431 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 09 </td>
   <td style="text-align:left;"> Región Higuamo </td>
   <td style="text-align:left;"> 23 </td>
   <td style="text-align:left;"> 02304 </td>
   <td style="text-align:left;"> 092304 </td>
   <td style="text-align:right;"> 30051 </td>
   <td style="text-align:right;"> 561431 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 09 </td>
   <td style="text-align:left;"> Región Higuamo </td>
   <td style="text-align:left;"> 23 </td>
   <td style="text-align:left;"> 02305 </td>
   <td style="text-align:left;"> 092305 </td>
   <td style="text-align:right;"> 19034 </td>
   <td style="text-align:right;"> 561431 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 09 </td>
   <td style="text-align:left;"> Región Higuamo </td>
   <td style="text-align:left;"> 23 </td>
   <td style="text-align:left;"> 02306 </td>
   <td style="text-align:left;"> 092306 </td>
   <td style="text-align:right;"> 14592 </td>
   <td style="text-align:right;"> 561431 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 09 </td>
   <td style="text-align:left;"> Región Higuamo </td>
   <td style="text-align:left;"> 29 </td>
   <td style="text-align:left;"> 02901 </td>
   <td style="text-align:left;"> 092901 </td>
   <td style="text-align:right;"> 46723 </td>
   <td style="text-align:right;"> 561431 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 09 </td>
   <td style="text-align:left;"> Región Higuamo </td>
   <td style="text-align:left;"> 29 </td>
   <td style="text-align:left;"> 02902 </td>
   <td style="text-align:left;"> 092902 </td>
   <td style="text-align:right;"> 31889 </td>
   <td style="text-align:right;"> 561431 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 09 </td>
   <td style="text-align:left;"> Región Higuamo </td>
   <td style="text-align:left;"> 29 </td>
   <td style="text-align:left;"> 02903 </td>
   <td style="text-align:left;"> 092903 </td>
   <td style="text-align:right;"> 31096 </td>
   <td style="text-align:right;"> 561431 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 09 </td>
   <td style="text-align:left;"> Región Higuamo </td>
   <td style="text-align:left;"> 29 </td>
   <td style="text-align:left;"> 02904 </td>
   <td style="text-align:left;"> 092904 </td>
   <td style="text-align:right;"> 55348 </td>
   <td style="text-align:right;"> 561431 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 09 </td>
   <td style="text-align:left;"> Región Higuamo </td>
   <td style="text-align:left;"> 29 </td>
   <td style="text-align:left;"> 02905 </td>
   <td style="text-align:left;"> 092905 </td>
   <td style="text-align:right;"> 20900 </td>
   <td style="text-align:right;"> 561431 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 09 </td>
   <td style="text-align:left;"> Región Higuamo </td>
   <td style="text-align:left;"> 30 </td>
   <td style="text-align:left;"> 03001 </td>
   <td style="text-align:left;"> 093001 </td>
   <td style="text-align:right;"> 61517 </td>
   <td style="text-align:right;"> 561431 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 09 </td>
   <td style="text-align:left;"> Región Higuamo </td>
   <td style="text-align:left;"> 30 </td>
   <td style="text-align:left;"> 03002 </td>
   <td style="text-align:left;"> 093002 </td>
   <td style="text-align:right;"> 16272 </td>
   <td style="text-align:right;"> 561431 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 09 </td>
   <td style="text-align:left;"> Región Higuamo </td>
   <td style="text-align:left;"> 30 </td>
   <td style="text-align:left;"> 03003 </td>
   <td style="text-align:left;"> 093003 </td>
   <td style="text-align:right;"> 7228 </td>
   <td style="text-align:right;"> 561431 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:left;"> Región Ozama </td>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:left;"> 100101 </td>
   <td style="text-align:right;"> 965040 </td>
   <td style="text-align:right;"> 3339410 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:left;"> Región Ozama </td>
   <td style="text-align:left;"> 32 </td>
   <td style="text-align:left;"> 03201 </td>
   <td style="text-align:left;"> 103201 </td>
   <td style="text-align:right;"> 948885 </td>
   <td style="text-align:right;"> 3339410 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:left;"> Región Ozama </td>
   <td style="text-align:left;"> 32 </td>
   <td style="text-align:left;"> 03202 </td>
   <td style="text-align:left;"> 103202 </td>
   <td style="text-align:right;"> 363321 </td>
   <td style="text-align:right;"> 3339410 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:left;"> Región Ozama </td>
   <td style="text-align:left;"> 32 </td>
   <td style="text-align:left;"> 03203 </td>
   <td style="text-align:left;"> 103203 </td>
   <td style="text-align:right;"> 529390 </td>
   <td style="text-align:right;"> 3339410 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:left;"> Región Ozama </td>
   <td style="text-align:left;"> 32 </td>
   <td style="text-align:left;"> 03204 </td>
   <td style="text-align:left;"> 103204 </td>
   <td style="text-align:right;"> 142019 </td>
   <td style="text-align:right;"> 3339410 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:left;"> Región Ozama </td>
   <td style="text-align:left;"> 32 </td>
   <td style="text-align:left;"> 03205 </td>
   <td style="text-align:left;"> 103205 </td>
   <td style="text-align:right;"> 43963 </td>
   <td style="text-align:right;"> 3339410 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:left;"> Región Ozama </td>
   <td style="text-align:left;"> 32 </td>
   <td style="text-align:left;"> 03206 </td>
   <td style="text-align:left;"> 103206 </td>
   <td style="text-align:right;"> 272776 </td>
   <td style="text-align:right;"> 3339410 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:left;"> Región Ozama </td>
   <td style="text-align:left;"> 32 </td>
   <td style="text-align:left;"> 03207 </td>
   <td style="text-align:left;"> 103207 </td>
   <td style="text-align:right;"> 74016 </td>
   <td style="text-align:right;"> 3339410 </td>
  </tr>
</tbody>
</table>

2. Obtener las estimaciones directa por región o el nivel de agregación en el cual la encuesta es representativa. 

En este código, se lee un archivo RDS de una encuesta y se utilizan las funciones `transmute()` y `paste0()` para seleccionar y transformar las variables de interés.

En primer lugar, se crea una variable `dam` que corresponde al identificador de la división administrativa mayor de la encuesta. A continuación, se utiliza la columna `dam_ee` para crear una variable `dam`, se selecciona la variable `dam2` que corresponde al identificador de la división administrativa municipal de segundo nivel (subdivisión del departamento) de la encuesta.

Luego, se crea una variable `wkx` que corresponde al peso de la observación en la encuesta, y una variable `upm` que corresponde al identificador del segmento muestral en la encuesta.

La variable `estrato` se crea utilizando la función `paste0()`, que concatena los valores de `dam` y `area_ee` (una variable que indica el área geográfica en la que se encuentra la vivienda de la encuesta).

Finalmente, se crea una variable `pobreza` que toma el valor 1 si el ingreso de la vivienda es menor que un umbral lp, y 0 en caso contrario.


```r
encuesta <- readRDS("Recursos/Día2/Sesion3/Data/encuestaDOM21N1.rds") %>% 
  transmute(
    dam = haven::as_factor(dam_ee,levels = "values"),
    dam = str_pad(dam,width = 2,pad = "0"),
    dam2,
    wkx = `_fep`, 
    upm = `_upm`,
    estrato =`_estrato`,
    pobreza = ifelse(ingcorte < lp, 1 , 0)) %>% 
  inner_join(N_dam_pp %>% select(region,dam2) )
```

El código está realizando un análisis de datos de encuestas utilizando el paquete `survey` de R. Primero, se crea un objeto `diseno` de diseño de encuestas usando la función `as_survey_design()` del paquete `srvyr`, que incluye los identificadores de la unidad primaria de muestreo (`upm`), los pesos (`wkx`), las estratos (`estrato`) y los datos de la encuesta (encuesta). Posteriormente, se agrupa el objeto `diseno` por la variable "Agregado" y se calcula la media de la variable pobreza con un intervalo de confianza para toda la población utilizando la función `survey_mean()`. El resultado se guarda en el objeto `directoDam` y se muestra en una tabla.


```r
library(survey)
library(srvyr)
options(survey.lonely.psu = "adjust")

diseno <-
  as_survey_design(
    ids = upm,
    weights = wkx,
    strata = estrato,
    nest = TRUE,
    .data = encuesta
  )

directoDam <- diseno %>% 
   group_by(region) %>% 
  summarise(
    theta_dir = survey_mean(pobreza, vartype = c("ci"))
    )
tba(directoDam)
```

<table class="table table-striped lightable-classic" style="width: auto !important; margin-left: auto; margin-right: auto; font-family: Arial Narrow; width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> region </th>
   <th style="text-align:right;"> theta_dir </th>
   <th style="text-align:right;"> theta_dir_low </th>
   <th style="text-align:right;"> theta_dir_upp </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:right;"> 0.1696 </td>
   <td style="text-align:right;"> 0.1472 </td>
   <td style="text-align:right;"> 0.1919 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:right;"> 0.1618 </td>
   <td style="text-align:right;"> 0.1230 </td>
   <td style="text-align:right;"> 0.2007 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:right;"> 0.1808 </td>
   <td style="text-align:right;"> 0.1371 </td>
   <td style="text-align:right;"> 0.2245 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:right;"> 0.1714 </td>
   <td style="text-align:right;"> 0.1269 </td>
   <td style="text-align:right;"> 0.2160 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:right;"> 0.2629 </td>
   <td style="text-align:right;"> 0.2287 </td>
   <td style="text-align:right;"> 0.2972 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:right;"> 0.4221 </td>
   <td style="text-align:right;"> 0.3741 </td>
   <td style="text-align:right;"> 0.4702 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 07 </td>
   <td style="text-align:right;"> 0.2957 </td>
   <td style="text-align:right;"> 0.2316 </td>
   <td style="text-align:right;"> 0.3599 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 08 </td>
   <td style="text-align:right;"> 0.1998 </td>
   <td style="text-align:right;"> 0.1605 </td>
   <td style="text-align:right;"> 0.2392 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 09 </td>
   <td style="text-align:right;"> 0.2258 </td>
   <td style="text-align:right;"> 0.1817 </td>
   <td style="text-align:right;"> 0.2700 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:right;"> 0.2409 </td>
   <td style="text-align:right;"> 0.2213 </td>
   <td style="text-align:right;"> 0.2604 </td>
  </tr>
</tbody>
</table>


3. Realizar el consolidando información obtenida en *1* y *2*.  


```r
temp <- estimacionesPre %>%
  inner_join(N_dam_pp %>% select(region,dam2,pp_dam2,region_pp) ) %>% 
  inner_join(directoDam )

tba(temp %>% slice(1:10))
```

<table class="table table-striped lightable-classic" style="width: auto !important; margin-left: auto; margin-right: auto; font-family: Arial Narrow; width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> dam2 </th>
   <th style="text-align:right;"> theta_pred </th>
   <th style="text-align:left;"> region </th>
   <th style="text-align:right;"> pp_dam2 </th>
   <th style="text-align:right;"> region_pp </th>
   <th style="text-align:right;"> theta_dir </th>
   <th style="text-align:right;"> theta_dir_low </th>
   <th style="text-align:right;"> theta_dir_upp </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:right;"> 0.2204 </td>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:right;"> 965040 </td>
   <td style="text-align:right;"> 3339410 </td>
   <td style="text-align:right;"> 0.2409 </td>
   <td style="text-align:right;"> 0.2213 </td>
   <td style="text-align:right;"> 0.2604 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00201 </td>
   <td style="text-align:right;"> 0.2234 </td>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:right;"> 91345 </td>
   <td style="text-align:right;"> 1028129 </td>
   <td style="text-align:right;"> 0.2629 </td>
   <td style="text-align:right;"> 0.2287 </td>
   <td style="text-align:right;"> 0.2972 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00206 </td>
   <td style="text-align:right;"> 0.3097 </td>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:right;"> 19020 </td>
   <td style="text-align:right;"> 1028129 </td>
   <td style="text-align:right;"> 0.2629 </td>
   <td style="text-align:right;"> 0.2287 </td>
   <td style="text-align:right;"> 0.2972 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00301 </td>
   <td style="text-align:right;"> 0.4158 </td>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:right;"> 36511 </td>
   <td style="text-align:right;"> 368594 </td>
   <td style="text-align:right;"> 0.4221 </td>
   <td style="text-align:right;"> 0.3741 </td>
   <td style="text-align:right;"> 0.4702 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00302 </td>
   <td style="text-align:right;"> 0.4343 </td>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:right;"> 15702 </td>
   <td style="text-align:right;"> 368594 </td>
   <td style="text-align:right;"> 0.4221 </td>
   <td style="text-align:right;"> 0.3741 </td>
   <td style="text-align:right;"> 0.4702 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00303 </td>
   <td style="text-align:right;"> 0.4579 </td>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:right;"> 26772 </td>
   <td style="text-align:right;"> 368594 </td>
   <td style="text-align:right;"> 0.4221 </td>
   <td style="text-align:right;"> 0.3741 </td>
   <td style="text-align:right;"> 0.4702 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00304 </td>
   <td style="text-align:right;"> 0.4922 </td>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:right;"> 10619 </td>
   <td style="text-align:right;"> 368594 </td>
   <td style="text-align:right;"> 0.4221 </td>
   <td style="text-align:right;"> 0.3741 </td>
   <td style="text-align:right;"> 0.4702 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00401 </td>
   <td style="text-align:right;"> 0.3379 </td>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:right;"> 83619 </td>
   <td style="text-align:right;"> 368594 </td>
   <td style="text-align:right;"> 0.4221 </td>
   <td style="text-align:right;"> 0.3741 </td>
   <td style="text-align:right;"> 0.4702 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00402 </td>
   <td style="text-align:right;"> 0.2213 </td>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:right;"> 14823 </td>
   <td style="text-align:right;"> 368594 </td>
   <td style="text-align:right;"> 0.4221 </td>
   <td style="text-align:right;"> 0.3741 </td>
   <td style="text-align:right;"> 0.4702 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00403 </td>
   <td style="text-align:right;"> 0.4380 </td>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:right;"> 13164 </td>
   <td style="text-align:right;"> 368594 </td>
   <td style="text-align:right;"> 0.4221 </td>
   <td style="text-align:right;"> 0.3741 </td>
   <td style="text-align:right;"> 0.4702 </td>
  </tr>
</tbody>
</table>

4. Con la información organizada realizar el calculo de los pesos para el Benchmark


```r
R_dam2 <- temp %>% group_by(region) %>% 
  summarise(
  R_dam_RB = unique(theta_dir) / sum((pp_dam2  / region_pp ) * theta_pred)
) 

tba(R_dam2)
```

<table class="table table-striped lightable-classic" style="width: auto !important; margin-left: auto; margin-right: auto; font-family: Arial Narrow; width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> region </th>
   <th style="text-align:right;"> R_dam_RB </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:right;"> 1.0526 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:right;"> 0.9339 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:right;"> 1.1303 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:right;"> 1.0137 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:right;"> 1.0035 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:right;"> 1.0894 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 07 </td>
   <td style="text-align:right;"> 0.9750 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 08 </td>
   <td style="text-align:right;"> 1.0018 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 09 </td>
   <td style="text-align:right;"> 0.8851 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:right;"> 0.9941 </td>
  </tr>
</tbody>
</table>
calculando los pesos para cada dominio.


```r
pesos <- temp %>% 
  mutate(W_i = pp_dam2  / region_pp) %>% 
  select(dam2, W_i)
tba(pesos %>% slice(1:10))
```

<table class="table table-striped lightable-classic" style="width: auto !important; margin-left: auto; margin-right: auto; font-family: Arial Narrow; width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> dam2 </th>
   <th style="text-align:right;"> W_i </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:right;"> 0.2890 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00201 </td>
   <td style="text-align:right;"> 0.0888 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00206 </td>
   <td style="text-align:right;"> 0.0185 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00301 </td>
   <td style="text-align:right;"> 0.0991 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00302 </td>
   <td style="text-align:right;"> 0.0426 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00303 </td>
   <td style="text-align:right;"> 0.0726 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00304 </td>
   <td style="text-align:right;"> 0.0288 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00401 </td>
   <td style="text-align:right;"> 0.2269 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00402 </td>
   <td style="text-align:right;"> 0.0402 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00403 </td>
   <td style="text-align:right;"> 0.0357 </td>
  </tr>
</tbody>
</table>


5. Realizar la estimación FH  Benchmark 

En este proceso, se realiza la adición de una nueva columna denominada `R_dam_RB`, que es obtenida a partir de un objeto denominado `R_dam2`. Posteriormente, se agrega una nueva columna denominada `theta_pred_RBench`, la cual es igual a la multiplicación de `R_dam_RB` y `theta_pred.` Finalmente, se hace un `left_join` con el dataframe pesos, y se seleccionan únicamente las columnas `dam`, `dam2`, `W_i`, `theta_pred` y `theta_pred_RBench` para ser presentadas en una tabla (tba) que muestra únicamente las primeras 10 filas.


```r
estimacionesBench <- estimacionesPre %>% 
  inner_join(N_dam_pp %>% select(region,dam2) )%>%
  left_join(R_dam2, by = c("region")) %>%
  mutate(theta_pred_RBench = R_dam_RB * theta_pred) %>%
  left_join(pesos) %>% 
  select(region, dam2, W_i, theta_pred, theta_pred_RBench)  

  tba(estimacionesBench %>% slice(1:10))
```

<table class="table table-striped lightable-classic" style="width: auto !important; margin-left: auto; margin-right: auto; font-family: Arial Narrow; width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> region </th>
   <th style="text-align:left;"> dam2 </th>
   <th style="text-align:right;"> W_i </th>
   <th style="text-align:right;"> theta_pred </th>
   <th style="text-align:right;"> theta_pred_RBench </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:left;"> 00101 </td>
   <td style="text-align:right;"> 0.2890 </td>
   <td style="text-align:right;"> 0.2204 </td>
   <td style="text-align:right;"> 0.2191 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:left;"> 00201 </td>
   <td style="text-align:right;"> 0.0888 </td>
   <td style="text-align:right;"> 0.2234 </td>
   <td style="text-align:right;"> 0.2242 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:left;"> 00206 </td>
   <td style="text-align:right;"> 0.0185 </td>
   <td style="text-align:right;"> 0.3097 </td>
   <td style="text-align:right;"> 0.3108 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> 00301 </td>
   <td style="text-align:right;"> 0.0991 </td>
   <td style="text-align:right;"> 0.4158 </td>
   <td style="text-align:right;"> 0.4529 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> 00302 </td>
   <td style="text-align:right;"> 0.0426 </td>
   <td style="text-align:right;"> 0.4343 </td>
   <td style="text-align:right;"> 0.4731 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> 00303 </td>
   <td style="text-align:right;"> 0.0726 </td>
   <td style="text-align:right;"> 0.4579 </td>
   <td style="text-align:right;"> 0.4988 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> 00304 </td>
   <td style="text-align:right;"> 0.0288 </td>
   <td style="text-align:right;"> 0.4922 </td>
   <td style="text-align:right;"> 0.5362 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> 00401 </td>
   <td style="text-align:right;"> 0.2269 </td>
   <td style="text-align:right;"> 0.3379 </td>
   <td style="text-align:right;"> 0.3681 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> 00402 </td>
   <td style="text-align:right;"> 0.0402 </td>
   <td style="text-align:right;"> 0.2213 </td>
   <td style="text-align:right;"> 0.2411 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> 00403 </td>
   <td style="text-align:right;"> 0.0357 </td>
   <td style="text-align:right;"> 0.4380 </td>
   <td style="text-align:right;"> 0.4771 </td>
  </tr>
</tbody>
</table>

6. Validación: Estimación FH con Benchmark


```r
estimacionesBench %>% group_by(region) %>%
  summarise(theta_reg_RB = sum(W_i * theta_pred_RBench)) %>%
  left_join(directoDam, by = "region") %>% 
  tba()
```

<table class="table table-striped lightable-classic" style="width: auto !important; margin-left: auto; margin-right: auto; font-family: Arial Narrow; width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> region </th>
   <th style="text-align:right;"> theta_reg_RB </th>
   <th style="text-align:right;"> theta_dir </th>
   <th style="text-align:right;"> theta_dir_low </th>
   <th style="text-align:right;"> theta_dir_upp </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:right;"> 0.1696 </td>
   <td style="text-align:right;"> 0.1696 </td>
   <td style="text-align:right;"> 0.1472 </td>
   <td style="text-align:right;"> 0.1919 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:right;"> 0.1618 </td>
   <td style="text-align:right;"> 0.1618 </td>
   <td style="text-align:right;"> 0.1230 </td>
   <td style="text-align:right;"> 0.2007 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:right;"> 0.1808 </td>
   <td style="text-align:right;"> 0.1808 </td>
   <td style="text-align:right;"> 0.1371 </td>
   <td style="text-align:right;"> 0.2245 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:right;"> 0.1714 </td>
   <td style="text-align:right;"> 0.1714 </td>
   <td style="text-align:right;"> 0.1269 </td>
   <td style="text-align:right;"> 0.2160 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:right;"> 0.2629 </td>
   <td style="text-align:right;"> 0.2629 </td>
   <td style="text-align:right;"> 0.2287 </td>
   <td style="text-align:right;"> 0.2972 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:right;"> 0.4221 </td>
   <td style="text-align:right;"> 0.4221 </td>
   <td style="text-align:right;"> 0.3741 </td>
   <td style="text-align:right;"> 0.4702 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 07 </td>
   <td style="text-align:right;"> 0.2957 </td>
   <td style="text-align:right;"> 0.2957 </td>
   <td style="text-align:right;"> 0.2316 </td>
   <td style="text-align:right;"> 0.3599 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 08 </td>
   <td style="text-align:right;"> 0.1998 </td>
   <td style="text-align:right;"> 0.1998 </td>
   <td style="text-align:right;"> 0.1605 </td>
   <td style="text-align:right;"> 0.2392 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 09 </td>
   <td style="text-align:right;"> 0.2258 </td>
   <td style="text-align:right;"> 0.2258 </td>
   <td style="text-align:right;"> 0.1817 </td>
   <td style="text-align:right;"> 0.2700 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:right;"> 0.2409 </td>
   <td style="text-align:right;"> 0.2409 </td>
   <td style="text-align:right;"> 0.2213 </td>
   <td style="text-align:right;"> 0.2604 </td>
  </tr>
</tbody>
</table>

## Validación de los resultados. 

Este código junta las estimaciones del modelo con pesos de benchmarking con los valores observados y sintéticos, y luego resume las estimaciones combinadas para compararlas con la estimación directa obtenida anteriormente. 


```r
temp <- estimacionesBench %>% left_join(
bind_rows(
data_dir %>% select(dam2, thetaSyn, thetaFH),
data_syn %>% select(dam2, thetaSyn, thetaFH))) %>% 
group_by(region) %>% 
summarise(thetaSyn = sum(W_i * thetaSyn),
          thetaFH = sum(W_i * theta_pred),
          theta_RBench = sum(W_i * theta_pred_RBench)
          ) %>%   
left_join(directoDam, by = "region")  %>% 
mutate(id = 1:n())

temp %<>% gather(key = "Metodo",value = "Estimacion",
                -id, -region, -theta_dir_upp, -theta_dir_low)

ggplot(data = temp, aes(x = id, y = Estimacion, shape = Metodo)) +
  geom_point(aes(color = Metodo), size = 2) +
  geom_line(aes(y = theta_dir_low), linetype  = 2) +
  geom_line(aes(y = theta_dir_upp),  linetype  = 2) +
  theme_bw(10) + 
  labs(y = "", x = "")
```

<img src="04-D2S3_Fay_Herriot_normal_files/figure-html/unnamed-chunk-29-1.svg" width="672" />

## Mapa de pobreza

Este es un bloque de código se cargan varios paquetes (`sp`, `sf`, `tmap`) y realiza algunas operaciones. Primero, realiza una unión (`left_join`) entre las estimaciones de ajustadas por el Benchmarking (`estimacionesBench`) y las estimaciones del modelo  (`data_dir`,  `data_syn`), utilizando la variable `dam2` como clave para la unión. Luego, lee un archivo `Shapefile` que contiene información geoespacial del país. A continuación, crea un mapa temático (`tmap`) utilizando la función `tm_shape()` y agregando capas con la función `tm_polygons()`. El mapa representa una variable `theta_pred_RBench` utilizando una paleta de colores llamada "YlOrRd" y establece los cortes de los intervalos de la variable con la variable `brks_lp.` Finalmente, la función `tm_layout()` establece algunos parámetros de diseño del mapa, como la relación de aspecto (asp).


```r
library(sp)
library(sf)
library(tmap)

estimacionesBench %<>% left_join(
bind_rows(
data_dir %>% select(dam2, theta_pred_EE , Cv_theta_pred),
data_syn %>% select(dam2, theta_pred_EE , Cv_theta_pred)))

## Leer Shapefile del país
ShapeSAE <- read_sf("Recursos/Día2/Sesion3/Shape/DOM_dam2.shp") %>% 
  rename(dam2 = id_dominio) %>% 
  mutate(dam2 = str_pad(
                 string = dam2,
                 width = 5,
                 pad = "0"
               ))


mapa <- tm_shape(ShapeSAE %>%
                   left_join(estimacionesBench,  by = "dam2"))

brks_lp <- c(0,0.1,0.15, 0.2, 0.3, 0.4, 0.6, 1)
tmap_options(check.and.fix = TRUE)
Mapa_lp <-
  mapa + tm_polygons(
    c("theta_pred_RBench"),
    breaks = brks_lp,
    title = "Mapa de pobreza",
    palette = "YlOrRd",
    colorNA = "white"
  ) + tm_layout(asp = 1.5)

Mapa_lp
```


<img src="Recursos/Día2/Sesion3/0Recursos/Mapa_DOM_pobreza_normal.png" width="500px" height="250px" style="display: block; margin: auto;" />



