# Integración y Normalización de Datos de Ventas

## Descripción

Este proyecto tiene como objetivo simular un escenario en el que los datos de ventas de una empresa provienen de diferentes fuentes y formatos.

Para realizar el ejercicio se utilizaron dos archivos:

- `transacciones.csv`: contiene las transacciones realizadas, indicando el producto, la cantidad vendida y la fecha.
- `productos.xlsx`: contiene el catálogo de productos con su nombre y precio unitario.

Mediante Python y la librería Pandas se realiza la carga, limpieza, transformación e integración de ambos archivos para obtener un único dataset con la información completa de las ventas.

## Estructura del proyecto

El repositorio contiene los siguientes archivos:

- `transacciones.csv`: datos de las transacciones.
- `productos.xlsx`: catálogo de productos.
- `ventas_normalizadas.parquet`: resultado final del procesamiento.
- `README.md`: documentación del proyecto.

## Proceso realizado

### 1. Carga de datos

Los archivos CSV y Excel son cargados utilizando Pandas.

```python
transacciones = pd.read_csv("transacciones.csv")
productos = pd.read_excel("productos.xlsx", engine="openpyxl")
```

### 2. Transformación

Se verifican los tipos de datos y se convierte la columna `fecha` al formato `datetime`.

```python
transacciones["fecha"] = pd.to_datetime(transacciones["fecha"])
```

También se verifica que `id_producto` tenga un tipo de dato compatible en ambos datasets.

### 3. Tratamiento de valores nulos

Se buscan posibles valores nulos utilizando:

```python
transacciones.isnull().sum()
productos.isnull().sum()
```

En las transacciones, los registros que no contienen una cantidad válida son eliminados, ya que no es posible determinar cuántas unidades fueron vendidas.

### 4. Integración de los datos

Los dos datasets se combinan mediante un `merge`, utilizando `id_producto` como clave común.

```python
ventas = pd.merge(
    transacciones,
    productos,
    on="id_producto",
    how="left"
)
```

De esta manera, cada transacción queda asociada con el nombre y el precio del producto correspondiente.

### 5. Cálculo del total de venta

Se crea una nueva columna llamada `total_venta`, multiplicando la cantidad vendida por el precio unitario.

```python
ventas["total_venta"] = (
    ventas["cantidad"] * ventas["precio_unitario"]
)
```

La fórmula utilizada es:

**total_venta = cantidad × precio_unitario**

### 6. Exportación

Finalmente, el dataset procesado se exporta en formato Parquet.

```python
ventas.to_parquet(
    "ventas_normalizadas.parquet",
    index=False
)
```

El formato Parquet permite almacenar los datos de manera columnar y eficiente, conservando además los tipos de datos utilizados.

## Tecnologías utilizadas

- Python
- Pandas
- OpenPyXL
- PyArrow
- CSV
- Microsoft Excel
- Apache Parquet

## Instalación de dependencias

Para ejecutar el proyecto es necesario instalar las siguientes librerías:

```bash
pip install pandas openpyxl pyarrow
```

## Resultado

Como resultado se obtiene el archivo `ventas_normalizadas.parquet`, que contiene las transacciones integradas con la información del catálogo de productos y el total correspondiente a cada venta.
