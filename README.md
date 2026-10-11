# JOSE_VALERIANO-PROYECTO-ETL-DELIVERY
En el presente proyecto de Python vamos a realizar un pipeline ETL completo : extraer los datos, realizar una limpieza profunda, optimizar el tamaño, cargarlos en un motor SQL local (SQLite) y finalmente responder a las preguntas claves (insight) utilizando consultas SQL y gráficos.
## Parte 1 - Extracción y Diagnóstico (Extract)
Vamos a utilizar 2 carpetas con archivos .csv para trabajar la data: 
- restaurants.csv: Contiene dirección completa concatenada, puntajes y rango de precios en símbolos ($).

- restaurant-menus.csv: Contiene el nombre del plato y su precio en texto.

Para ello cargaremos ambas carpetas en dataframes con panda:

``` python
import pandas as pd
df_restaurantes = pd.read_csv("/Workspace/Users/***/JOSE_VALERIANO-PROYECTO-ETL-DELIVERY/PROYECTO_FINAL (1)/restaurants.csv/restaurants.csv")
```

``` python
import pandas as pd
import glob

archivos = glob.glob( "/Workspace/Users/***/JOSE_VALERIANO-PROYECTO-ETL-DELIVERY/PROYECTO_FINAL (1)/restaurant-menus.csv/*.csv")

df_menus = pd.concat([pd.read_csv(f) for f in archivos],
            ignore_index=True
)
```
Con los datos ya cargados, realizaremos un análisis exploratorio (Data Profiling):

``` python
print("Restaurantes:")
print(df_restaurantes.shape)

print("Menus:")
print(df_menus.shape)
```
- *resultado*

![I1](/Workspace/Users/josevalebboy@gmail.com/JOSE_VALERIANO-PROYECTO-ETL-DELIVERY/Prints_proyecto/print_1.PNG)

``` python
print(df_restaurantes.dtypes)

print(df_menus.dtypes)
```
- *resultado*

![I1](/Workspace/Users/josevalebboy@gmail.com/JOSE_VALERIANO-PROYECTO-ETL-DELIVERY/Prints_proyecto/print_2.PNG)

``` python
print(df_restaurantes.isnull().sum())
```
- *resultado*

![I1](/Workspace/Users/josevalebboy@gmail.com/JOSE_VALERIANO-PROYECTO-ETL-DELIVERY/Prints_proyecto/print_3.PNG)

``` python
print(df_menus.isnull().sum())
```
- *resultado*

![I1](/Workspace/Users/josevalebboy@gmail.com/JOSE_VALERIANO-PROYECTO-ETL-DELIVERY/Prints_proyecto/print_4.PNG)





