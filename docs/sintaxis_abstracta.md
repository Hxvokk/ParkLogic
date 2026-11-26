# Sintaxis abstracta y estructura de instrucciones

*Historia: TECH-07 · Responsable: Edgar Gael Reyes Quevedo · Sprint 1*

## 1. Qué es la sintaxis abstracta

La sintaxis abstracta es la forma en que el programa guarda lo que leyó, sin importar cómo estaba escrito. A Python le da igual si había espacios de más, comentarios o líneas vacías. Lo que le importa es: hay un estacionamiento con este nombre, esta tarifa, esta tolerancia, y así. Esa estructura interna es la que usan después el validador y la calculadora.

## 2. Recorrido de un archivo

1. El **lector** abre el archivo `.park` y convierte cada línea con contenido en una `Instruccion`, guardando su número de línea.
2. El **validador** revisa cada instrucción (palabra conocida, tipo de valor, rangos, repetidas, obligatorias) y junta los errores.
3. Si no hay errores, el validador arma un objeto `Estacionamiento` con todos los datos.
4. La **calculadora** usa el `Estacionamiento` junto con la `Consulta` (minutos, cliente, vehículo) para obtener el cobro.

## 3. Componentes

### 3.1 Instruccion (una línea del archivo)

| Campo | Tipo | Qué guarda | Ejemplo |
|---|---|---|---|
| clave | texto | La palabra clave | `"TARIFA_HORA"` |
| argumentos | lista de textos | Los valores que siguen a la palabra clave | `["20"]` |
| linea | entero | Número de línea en el archivo | `2` |

### 3.2 Estacionamiento (la configuración completa)

| Componente | Tipo | Obligatorio | Valores permitidos | Por defecto |
|---|---|---|---|---|
| nombre | texto | Sí | No vacío | - |
| tarifa_hora | Decimal | Sí | Mayor que 0 | - |
| tolerancia | entero | No | 0 o más | 0 |
| maximo_diario | Decimal o nada | No | Mayor que 0 | Sin máximo |
| descuentos | diccionario categoría → porcentaje | No | Porcentaje entre 0 y 100 | Vacío |
| horario | pareja de horas o nada | No | HH:MM entre 00:00 y 23:59 | Sin horario |
| tarifas_vehiculo | diccionario categoría → importe | No | Importe mayor que 0 | Vacío |
| restricciones | conjunto de categorías | No | Categorías válidas | Vacío |

### 3.3 Consulta (lo que pide la persona al ejecutar)

| Componente | Tipo | Obligatorio | Valores permitidos | Por defecto |
|---|---|---|---|---|
| minutos | entero | Sí | 0 a 1440 | - |
| cliente | texto | No | Categoría válida | GENERAL |
| vehiculo | texto o nada | No | Categoría válida | Sin vehículo |

## 4. Ejemplo de representación

Con el archivo `centro_basico.park`:

```text
ESTACIONAMIENTO CENTRO
TARIFA_HORA 20
TOLERANCIA 15
DESCUENTO ESTUDIANTE 20
MAXIMO_DIARIO 150
FIN_ESTACIONAMIENTO
```

El programa lo representa así:

```text
Estacionamiento(
    nombre="CENTRO", tarifa_hora=Decimal("20"), tolerancia=15,
    maximo_diario=Decimal("150"), descuentos={"ESTUDIANTE": Decimal("20")},
    horario=None, tarifas_vehiculo={}, restricciones=set())
```

## 5. Valores permitidos y reglas obligatorias

Estas son las reglas que debe cumplir un archivo para generar un `Estacionamiento`. Se usa `Decimal` y no `float` porque con dinero el `float` puede dar errores de redondeo.

| Regla | Aplica a |
|---|---|
| El archivo empieza con ESTACIONAMIENTO y un nombre, y termina con FIN_ESTACIONAMIENTO | Estructura |
| No hay instrucciones después de FIN_ESTACIONAMIENTO | Estructura |
| Solo existen las instrucciones del lenguaje | Todas |
| Cada instrucción trae la cantidad de valores que necesita | Todas |
| TARIFA_HORA es obligatoria y mayor que 0 | TARIFA_HORA |
| MAXIMO_DIARIO es mayor que 0 | MAXIMO_DIARIO |
| TOLERANCIA es un entero de 0 o más | TOLERANCIA |
| El porcentaje de DESCUENTO está entre 0 y 100 | DESCUENTO |
| El importe de TARIFA_VEHICULO es mayor que 0 | TARIFA_VEHICULO |
| Las horas tienen formato HH:MM, entre 00:00 y 23:59 | HORARIO |
| Las categorías van en mayúsculas, números o guion bajo | DESCUENTO, TARIFA_VEHICULO, RESTRINGIR |
| TARIFA_HORA, TOLERANCIA, MAXIMO_DIARIO y HORARIO no se repiten; DESCUENTO, TARIFA_VEHICULO y RESTRINGIR solo una vez por categoría | Repetidas |
