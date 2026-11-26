# Semántica y reglas de ejecución

*Historia: TECH-08 · Responsable: Abisaid Guadalupe Angulo Munguia · Sprint 1*

## 1. Qué es la semántica

La sintaxis dice cómo se escribe el archivo. La semántica dice qué significa lo que se escribió y qué hace el programa con eso. En ParkLogic, la semántica define cómo se calcula el cobro a partir de las reglas y de los datos de la estancia.

## 2. Qué significa cada instrucción

| Instrucción | Significado | Efecto en el cálculo |
|---|---|---|
| `ESTACIONAMIENTO nombre` | Le pone nombre a la configuración. | Solo se muestra en el resultado. |
| `TARIFA_HORA x` | Precio en pesos por cada hora o fracción de hora. | Es la base del costo. |
| `TOLERANCIA m` | Minutos que no se cobran. | Si la duración es menor o igual a m, el costo es 0. Si se pasa, se cobra desde el minuto 0. |
| `MAXIMO_DIARIO x` | Lo máximo que se cobra por una estancia. | El costo base nunca pasa de x. |
| `DESCUENTO cat p` | Porcentaje que se descuenta a los clientes de esa categoría. | Se aplica sobre el costo base ya limitado por el máximo. |
| `HORARIO a b` | Documenta cuándo opera el estacionamiento. | En la v0.1 no cambia el cobro. Solo se muestra. |
| `TARIFA_VEHICULO cat x` | Tarifa por hora para un tipo de vehículo. | Reemplaza a TARIFA_HORA para ese vehículo. |
| `RESTRINGIR cat` | Ese tipo de vehículo no puede entrar. | No se calcula cobro, se muestra que el acceso no está autorizado. |
| `FIN_ESTACIONAMIENTO` | Cierra la configuración. | Ninguno. |

## 3. Datos de entrada

Además del archivo, la persona da los datos de la estancia al ejecutar el programa:

```text
python main.py ejemplos/validos/centro_basico.park --minutos 180 --cliente ESTUDIANTE
```

- `--minutos` es obligatorio: entero de 0 a 1440.
- `--cliente` es opcional: categoría del cliente. Si no se da, es GENERAL.
- `--vehiculo` es opcional: categoría del vehículo.
- La versión inicial solo cubre estancias de hasta 24 horas (1440 minutos).

## 4. Orden de cálculo

1. **Validar.** Se revisa el archivo y los datos de entrada. Si hay errores, se muestran y el programa termina.
2. **Restricción.** Si el vehículo está en `RESTRINGIR`, se muestra "Acceso no autorizado" y se termina.
3. **Tarifa.** Se usa la de `TARIFA_VEHICULO` si el vehículo tiene una. Si no, se usa `TARIFA_HORA`.
4. **Tolerancia.** Si los minutos son menores o iguales a la tolerancia, el costo base es 0 y el total es 0.
5. **Horas cobradas.** Si se pasó la tolerancia, se cobra la hora iniciada: `horas = minutos / 60` redondeado hacia arriba. El conteo empieza desde el minuto 0, no desde que termina la tolerancia.
6. **Costo base.** `costo base = horas × tarifa`.
7. **Máximo.** Si hay `MAXIMO_DIARIO`, el costo base queda en el menor entre el costo base y el máximo.
8. **Descuento.** Se busca el porcentaje del cliente. Si no hay, es 0. `descuento = costo base × porcentaje / 100`.
9. **Total.** `total = costo base − descuento`.
10. **Redondeo.** Los importes se redondean a dos decimales, y la mitad se redondea hacia arriba.

El máximo se aplica **antes** del descuento. Así, el descuento siempre se calcula sobre lo que realmente se cobraría sin descuento.

## 5. Ejemplo resuelto

### Estudiante, 180 minutos (archivo centro_basico.park)

```text
tarifa 20, tolerancia 15, máximo 150, descuento ESTUDIANTE 20
180 min > 15 min de tolerancia, se cobra
horas = 180 / 60 = 3
costo base = 3 x 20 = 60
máximo: menor entre 60 y 150 = 60
descuento = 60 x 20 / 100 = 12
total = 60 - 12 = 48
```

## 6. Casos con resultado esperado

Son los resultados que debe dar el programa según las reglas de arriba. Los importes están en pesos mexicanos.

| # | Configuración | Minutos | Datos | Resultado esperado |
|---|---|---|---|---|
| 1 | Tarifa 20, sin tolerancia | 120 | - | $40.00 |
| 2 | Tarifa 20, sin tolerancia | 90 | - | $40.00 (la fracción cuenta como hora completa) |
| 3 | Tarifa 20, tolerancia 15 | 10 | - | $0.00 |
| 4 | Tarifa 20, descuento ESTUDIANTE 20 | 180 | cliente ESTUDIANTE | base $60.00, descuento $12.00, total $48.00 |
| 5 | Tarifa 20, máximo 150 | 600 | - | $150.00 |
