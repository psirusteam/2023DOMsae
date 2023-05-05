## Estimación de la pobreza a partir del ingreso



## Proceso de estimación y predicción


```r
source("Recursos/Día4/Sesion1/0Recursos/funciones_mrp.R")
fit <- readRDS("Recursos/Día4/Sesion1/Data/fit_ingresos.rds")
```


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
epred_mat <- posterior_epred(fit, newdata = poststrat_df, type = "response")
dim(epred_mat)
dim(poststrat_df)
```
Como el interés es realizar comparaciones entre los países de la región se presenta la estimación del ingreso medio en términos de lineas de pobreza. Para esto procedemos así:

-   Obteniendo las lineas de pobreza por cada post-estrato


```r
(
  lp <-
    readRDS("Recursos/Día4/Sesion1/Data/encuestaDOM21N1.rds") %>% distinct(area_ee, lp, li) %>%
    mutate(
      area = ifelse(
        haven::as_factor(area_ee, levels = "values") == 1 , "1", "0"),
      area_ee = NULL
    )
) %>%
  tba()
```

<table class="table table-striped lightable-classic" style="width: auto !important; margin-left: auto; margin-right: auto; font-family: Arial Narrow; width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:right;"> lp </th>
   <th style="text-align:right;"> li </th>
   <th style="text-align:left;"> area </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 5622.81 </td>
   <td style="text-align:right;"> 3159.09 </td>
   <td style="text-align:left;"> 1 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 4876.69 </td>
   <td style="text-align:right;"> 3061.23 </td>
   <td style="text-align:left;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 5710.40 </td>
   <td style="text-align:right;"> 3193.03 </td>
   <td style="text-align:left;"> 1 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 4949.12 </td>
   <td style="text-align:right;"> 3094.12 </td>
   <td style="text-align:left;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 5844.03 </td>
   <td style="text-align:right;"> 3291.64 </td>
   <td style="text-align:left;"> 1 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 5070.47 </td>
   <td style="text-align:right;"> 3189.68 </td>
   <td style="text-align:left;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 5973.59 </td>
   <td style="text-align:right;"> 3377.04 </td>
   <td style="text-align:left;"> 1 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 5185.77 </td>
   <td style="text-align:right;"> 3272.42 </td>
   <td style="text-align:left;"> 0 </td>
  </tr>
</tbody>
</table>

-   Ingreso en términos de lineas de pobreza.


```r
lp %<>% group_by(area) %>% summarise(lp = mean(lp),li = mean(li)) 
lp <- inner_join(poststrat_df,lp,by = "area") %>% select(lp)
epred_mat_pobreza_lp <- (exp(epred_mat)-1) <= lp$lp
epred_mat_pobreza_li <- (exp(epred_mat)-1) <= lp$li
```

## Estimación de la pobreza





```r
n_filtered <- poststrat_df$n
mrp_estimates <- epred_mat_pobreza_lp %*% n_filtered / sum(n_filtered)

(temp_ing <- data.frame(
  mrp_estimate = mean(mrp_estimates),
  mrp_estimate_se = sd(mrp_estimates)
) ) %>% tba()
```

<table class="table table-striped lightable-classic" style="width: auto !important; margin-left: auto; margin-right: auto; font-family: Arial Narrow; width: auto !important; margin-left: auto; margin-right: auto;">
<tbody>
  <tr>

  </tr>
</tbody>
</table>


### Estimación para el dam == "01".

Es importante siempre conservar el orden de la base, dado que relación entre la predicción y el censo en uno a uno.


```r
temp <- poststrat_df %>%  mutate(Posi = 1:n())
temp <- filter(temp, dam == "01") %>% select(n, Posi)
n_filtered <- temp$n
temp_epred_mat <- epred_mat_pobreza_lp[, temp$Posi]

## Estimando el CME
mrp_estimates <- temp_epred_mat %*% n_filtered / sum(n_filtered)

(temp_dam01 <- data.frame(
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
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
</tbody>
</table>

El resultado nos indica que la proporción de personas en condición de pobreza en la dam 01 es 0

### Estimación para la dam2 == "00203"


```r
temp <- poststrat_df %>%  mutate(Posi = 1:n())
temp <-
  filter(temp, dam2 == "00203") %>% select(n, Posi)
n_filtered <- temp$n
temp_epred_mat <- epred_mat_pobreza_lp[, temp$Posi]
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
   <td style="text-align:right;"> 0.2487 </td>
   <td style="text-align:right;"> 0.052 </td>
  </tr>
</tbody>
</table>

El resultado nos indica que la proporción de personas en condición de pobreza en la dam2 = 00203 es 0.25 lineas de pobreza

Después de comprender la forma en que se realiza la estimación de los dominios no observados procedemos el uso de la función *Aux_Agregado* que es desarrollada para este fin.


```r
(mrp_estimate_Ingresolp <-
  Aux_Agregado(poststrat = poststrat_df,
             epredmat = epred_mat_pobreza_lp,
             byMap = NULL)
)
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
   <td style="text-align:right;"> 0.034 </td>
   <td style="text-align:right;"> 0.0025 </td>
  </tr>
</tbody>
</table>


De forma similar es posible obtener los resultados para las divisiones administrativas. 


```r
mrp_estimate_dam <-
  Aux_Agregado(poststrat = poststrat_df,
             epredmat = epred_mat_pobreza_lp,
             byMap = "dam")
tba(mrp_estimate_dam %>% head(10))
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
   <td style="text-align:right;"> 0.0000 </td>
   <td style="text-align:right;"> 0.0000 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00201 </td>
   <td style="text-align:right;"> 0.0000 </td>
   <td style="text-align:right;"> 0.0000 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00202 </td>
   <td style="text-align:right;"> 0.1778 </td>
   <td style="text-align:right;"> 0.0704 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00203 </td>
   <td style="text-align:right;"> 0.2487 </td>
   <td style="text-align:right;"> 0.0520 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00204 </td>
   <td style="text-align:right;"> 0.0000 </td>
   <td style="text-align:right;"> 0.0000 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00205 </td>
   <td style="text-align:right;"> 0.2179 </td>
   <td style="text-align:right;"> 0.0650 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00206 </td>
   <td style="text-align:right;"> 0.0967 </td>
   <td style="text-align:right;"> 0.0551 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00207 </td>
   <td style="text-align:right;"> 0.1062 </td>
   <td style="text-align:right;"> 0.1718 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00208 </td>
   <td style="text-align:right;"> 0.0630 </td>
   <td style="text-align:right;"> 0.0395 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00209 </td>
   <td style="text-align:right;"> 0.1465 </td>
   <td style="text-align:right;"> 0.1983 </td>
  </tr>
</tbody>
</table>




```r
mrp_estimate_dam2 <-
  Aux_Agregado(poststrat = poststrat_df,
             epredmat = epred_mat_pobreza_lp,
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
   <td style="text-align:right;"> 0.0000 </td>
   <td style="text-align:right;"> 0.0000 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00201 </td>
   <td style="text-align:right;"> 0.0000 </td>
   <td style="text-align:right;"> 0.0000 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00202 </td>
   <td style="text-align:right;"> 0.1778 </td>
   <td style="text-align:right;"> 0.0704 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00203 </td>
   <td style="text-align:right;"> 0.2487 </td>
   <td style="text-align:right;"> 0.0520 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00204 </td>
   <td style="text-align:right;"> 0.0000 </td>
   <td style="text-align:right;"> 0.0000 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00205 </td>
   <td style="text-align:right;"> 0.2179 </td>
   <td style="text-align:right;"> 0.0650 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00206 </td>
   <td style="text-align:right;"> 0.0967 </td>
   <td style="text-align:right;"> 0.0551 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00207 </td>
   <td style="text-align:right;"> 0.1062 </td>
   <td style="text-align:right;"> 0.1718 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00208 </td>
   <td style="text-align:right;"> 0.0630 </td>
   <td style="text-align:right;"> 0.0395 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 00209 </td>
   <td style="text-align:right;"> 0.1465 </td>
   <td style="text-align:right;"> 0.1983 </td>
  </tr>
</tbody>
</table>


El mapa resultante es el siguiente




<img src="Recursos/Día4/Sesion1/0Recursos/Mapa_DOM_Pobreza.PNG" width="400%" style="display: block; margin: auto;" />

