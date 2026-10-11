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

![I1](./Prints_proyecto/print_1.PNG)

``` python
print(df_restaurantes.dtypes)

print(df_menus.dtypes)
```
- *resultado*

![I1](./Prints_proyecto/print_2.PNG)

``` python
print(df_restaurantes.isnull().sum())
```
- *resultado*

![I1](./Prints_proyecto/print_3.PNG)

``` python
print(df_menus.isnull().sum())
```
- *resultado*

![I1](./Prints_proyecto/print_4.PNG)

## Parte 2 - Ingeniería de Características y Limpieza (Transform)
Para este apartado, realizaremos limpieza y cambios en nuestros dataframes trabajados previamente.

En df_restaurantes:
1. Transformaremos la columna rango_de_precios, que contiene símbolos ($, $$, etc), con un diccionario para que la lectura de resultados sea mas entendible:
``` python
df_restaurantes["price_range"] = df_restaurantes["price_range"].replace({
    "$": "Económico",
    "$$": "Moderadamente caro",
    "$$$": "Caro",
    "$$$$": "Muy caro"
})
```
Por lo que luego del cambio se veria asi:
``` python
df_restaurantes["price_range"].value_counts(dropna=False)
```
- *resultado* 

![I1](./Prints_proyecto/print_5.PNG)

2. En la columna dirección_completa, que contiene informacion de la calle, ciudad, estado y código postal separados por comas, realizaremos un split para crear tres nuevas columnas independientes: calle, ciudad y estado_zip.
``` python
df_restaurantes[["calle", "ciudad", "estado_zip"]] = (
    df_restaurantes["full_address"]
    .str.split(",", expand=True, n=2)
)
```
Por lo que luego del cambio se veria asi:
``` python
df_restaurantes[
    ["full_address", "calle", "ciudad", "estado_zip"]
].head(10)
```
- *resultado*

![I1](./Prints_proyecto/print_6.PNG)

3. Eliminaremos los restaurantes que no tengan puntaje (score nulo) o cuyo puntaje sea 0 y el estandar de la columna category será a minúsculas.
``` python
df_restaurantes = df_restaurantes[
    (df_restaurantes["score"].notna()) &
    (df_restaurantes["score"] != 0)
]
```
``` python
df_restaurantes["category"] = df_restaurantes["category"].str.lower()
```
Por lo que luego del cambio se veria asi:
``` python
df_restaurantes["category"].head()
```
- *resultado*

![I1](./Prints_proyecto/print_7.PNG)

En df_menus:
1. La columna price viene como texto (ej. "15,99 USD"). Por lo que usaremos una funcion para eliminar el texto " USD", reemplazar la coma por un punto, y castear el resultado a formato decimal (float).
``` python
df_menus["price"] = (
    df_menus["price"]
    .str.replace(" USD", "", regex=False)
    .str.replace(",", ".", regex=False)
    .astype(float)
)
```
Por lo que luego del cambio se veria asi:
``` python
df_menus["price"].head(10)
```
- *resultado*

![I1](./Prints_proyecto/print_8.PNG)

2. Eliminaremos los ítems del menú con precio igual a 0.0 o nulos.
``` python
df_menus = df_menus[
    (df_menus["price"].notna()) &
    (df_menus["price"] != 0.0)
]
```
### Optimización y Cruce (Control de Memoria RAM)
* Para no colapsar la memoria del servidor, filtraremos en df_restaurantes, para quedarnos únicamente con aquellos que tengan más de 100 calificaciones (ratings).
``` python
df_restaurantes = df_restaurantes[
    (df_restaurantes["ratings"].notna()) &
    (df_restaurantes["ratings"] > 100)
]
```
* Realizamos un INNER JOIN entre el df_restaurantes filtrado y df_menus
utilizando el ID del restaurante y guardamos este cruce en un DataFrame llamado df_master.
``` python
df_master = pd.merge(
    df_restaurantes,
    df_menus,
    left_on="id",
    right_on="restaurant_id",
    how="inner"
)
```
- *resultado*

![I1](./Prints_proyecto/print_9.PNG)



















