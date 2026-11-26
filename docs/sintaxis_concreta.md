# Sintaxis concreta y gramática

*Historia: TECH-06 · Responsable: Orlando Medina Guzmán · Sprint 1*

## 1. Qué es la sintaxis concreta

La sintaxis concreta es la forma en que una persona escribe el lenguaje: qué palabras usa, en qué orden y con qué reglas. Es lo que se ve en el archivo `.park`. Cómo guarda el programa esos datos por dentro es otro tema, y eso es la sintaxis abstracta.

## 2. Cómo es un archivo .park

- Es un archivo de texto plano con extensión `.park`.
- Cada instrucción va en su propia línea.
- Empieza con `ESTACIONAMIENTO` y un nombre, y termina con `FIN_ESTACIONAMIENTO`.
- Entre esas dos líneas van las instrucciones, en cualquier orden.
- Hay un solo estacionamiento por archivo. Después de `FIN_ESTACIONAMIENTO` no puede haber instrucciones.

```text
ESTACIONAMIENTO CENTRO
TARIFA_HORA 20
TOLERANCIA 15
DESCUENTO ESTUDIANTE 20
MAXIMO_DIARIO 150
FIN_ESTACIONAMIENTO
```

## 3. Palabras clave

| Instrucción | Valores | Obligatoria | Se puede repetir | Ejemplo | Historia |
|---|---|---|---|---|---|
| `ESTACIONAMIENTO` | nombre (una o más palabras) | Sí | No | `ESTACIONAMIENTO CENTRO` | HU-01 |
| `TARIFA_HORA` | importe mayor que 0 | Sí | No | `TARIFA_HORA 20` | HU-02 |
| `TOLERANCIA` | minutos enteros, 0 o más | No (vale 0) | No | `TOLERANCIA 15` | HU-03 |
| `MAXIMO_DIARIO` | importe mayor que 0 | No | No | `MAXIMO_DIARIO 150` | HU-08 |
| `DESCUENTO` | categoría y porcentaje de 0 a 100 | No | Sí, una vez por categoría | `DESCUENTO ESTUDIANTE 20` | HU-07 |
| `HORARIO` | hora de inicio y de fin (HH:MM) | No | No | `HORARIO 08:00 22:00` | HU-09 |
| `TARIFA_VEHICULO` | categoría e importe mayor que 0 | No | Sí, una vez por categoría | `TARIFA_VEHICULO MOTO 10` | HU-11 |
| `RESTRINGIR` | categoría de vehículo | No | Sí, una vez por categoría | `RESTRINGIR CAMION` | HU-12 |
| `FIN_ESTACIONAMIENTO` | ninguno | Sí | No | `FIN_ESTACIONAMIENTO` | HU-01 |

## 4. Reglas de escritura

- **Mayúsculas:** las palabras clave se escriben en mayúsculas. `tarifa_hora 20` no es válido.
- **Espacios:** se separan los valores con uno o más espacios. Se permiten espacios al inicio y al final de la línea.
- **Números:** solo dígitos y un punto decimal, por ejemplo `20` o `12.5`. No se usa `$` ni coma.
- **Categorías:** letras mayúsculas sin acentos, números y guion bajo, por ejemplo `ESTUDIANTE` o `ADULTO_MAYOR`.
- **Horas:** formato `HH:MM` de 24 horas, entre `00:00` y `23:59`.
- **Comentarios:** `#` inicia un comentario que llega hasta el final de la línea.
- **Líneas vacías:** se ignoran.
- **Orden:** entre el encabezado y `FIN_ESTACIONAMIENTO` las instrucciones pueden ir en cualquier orden.

## 5. Gramática en EBNF

La gramática solo describe cómo se escribe cada línea. Que un valor sea mayor que cero o que una tarifa no se repita lo revisa el validador, que está explicado en la sintaxis abstracta. Por eso `TARIFA_HORA -5` cumple la gramática (es un número) pero el validador lo rechaza.

```ebnf
(* Archivo .park de ParkLogic, versión 0.1 *)

archivo         = { linea_vacia }, encabezado, { instruccion | linea_vacia }, cierre,
                  { linea_vacia } ;

encabezado      = [ espacio ], "ESTACIONAMIENTO", espacio, nombre, fin_linea ;
cierre          = [ espacio ], "FIN_ESTACIONAMIENTO", fin_linea ;

instruccion     = [ espacio ],
                  ( tarifa | tolerancia | maximo | descuento | horario
                  | tarifa_vehiculo | restriccion ),
                  fin_linea ;

tarifa          = "TARIFA_HORA", espacio, numero ;
tolerancia      = "TOLERANCIA", espacio, entero ;
maximo          = "MAXIMO_DIARIO", espacio, numero ;
descuento       = "DESCUENTO", espacio, categoria, espacio, numero ;
horario         = "HORARIO", espacio, hora, espacio, hora ;
tarifa_vehiculo = "TARIFA_VEHICULO", espacio, categoria, espacio, numero ;
restriccion     = "RESTRINGIR", espacio, categoria ;

nombre          = palabra, { espacio, palabra } ;
palabra         = caracter_nombre, { caracter_nombre } ;
caracter_nombre = letra | digito | "_" | "-" ;
categoria       = ( letra_mayuscula | digito ), { letra_mayuscula | digito | "_" } ;
hora            = digito, digito, ":", digito, digito ;
numero          = [ "-" ], digito, { digito }, [ ".", digito, { digito } ] ;
entero          = [ "-" ], digito, { digito } ;

fin_linea       = [ espacio ], [ comentario ], salto_de_linea ;
linea_vacia     = [ espacio ], [ comentario ], salto_de_linea ;
comentario      = "#", { cualquier_caracter_menos_salto } ;
espacio         = ( " " | tabulador ), { " " | tabulador } ;

letra_mayuscula = "A" | "B" | ... | "Z" ;      (* sin Ñ ni acentos en las categorías *)
letra           = letra_mayuscula | "a" | ... | "z" ;
digito          = "0" | "1" | ... | "9" ;
```

## 6. Ejemplos válidos

### centro_basico.park

Ejemplo base del proyecto.

```text
ESTACIONAMIENTO CENTRO
TARIFA_HORA 20
TOLERANCIA 15
DESCUENTO ESTUDIANTE 20
MAXIMO_DIARIO 150
FIN_ESTACIONAMIENTO
```

### completo.park

Usa todas las instrucciones, con un comentario.

```text
# Estacionamiento con todas las instrucciones
ESTACIONAMIENTO UNIVERSIDAD
TARIFA_HORA 20
TOLERANCIA 10
MAXIMO_DIARIO 150
DESCUENTO ESTUDIANTE 20
DESCUENTO PROFESOR 10
HORARIO 07:00 21:00
TARIFA_VEHICULO MOTO 10
TARIFA_VEHICULO AUTO 20
RESTRINGIR CAMION
FIN_ESTACIONAMIENTO
```

## 7. Ejemplos inválidos

Estos tres archivos incumplen la gramática. Están en la carpeta `ejemplos/invalidos/`.

### 01_clave_desconocida.park

Usa TARIFAX, una instrucción que no existe.

```text
ESTACIONAMIENTO CENTRO
TARIFA_HORA 20
TARIFAX 20
FIN_ESTACIONAMIENTO
```

### 03_sin_fin.park

Falta cerrar con FIN_ESTACIONAMIENTO.

```text
ESTACIONAMIENTO CENTRO
TARIFA_HORA 20
```

### 04_valor_no_numerico.park

La tarifa está escrita con letras en vez de número.

```text
ESTACIONAMIENTO CENTRO
TARIFA_HORA veinte
FIN_ESTACIONAMIENTO
```
