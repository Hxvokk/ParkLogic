# Investigación de referencias y lenguajes similares

*Historia: TECH-05 · Responsable: Eduardo Borquez Navarrete · Sprint 1*

## 1. Para qué se hizo esta investigación

ParkLogic es un lenguaje propio para escribir reglas de cobro en un archivo de texto. Antes de decidir cómo se escribe, revisamos cómo resuelven algo parecido otros lenguajes y formatos que ya existen. La idea era ver qué podíamos tomar de cada uno y qué no nos servía.

## 2. Referencias revisadas

### 2.1 SQL

SQL es el lenguaje estándar para consultar bases de datos y está definido en la norma ISO/IEC 9075. Es **declarativo**: uno escribe qué quiere obtener y no los pasos para conseguirlo. Sus instrucciones usan palabras clave en mayúsculas, como `SELECT` o `WHERE`, y se leen casi como una frase.

- **Lo que tomamos:** el estilo declarativo (decir qué regla aplica, no cómo calcularla) y las palabras clave en mayúsculas.
- **Lo que no tomamos:** las consultas, las uniones y las expresiones complejas. ParkLogic solo necesita declarar valores.

### 2.2 JSON Schema

JSON Schema sirve para describir cómo debe ser un documento JSON y comprobar si lo cumple. Indica el tipo de cada dato, cuáles son obligatorios y sus límites, por ejemplo un mínimo o un máximo.

- **Lo que tomamos:** separar la lectura del archivo de su validación. Primero se lee el archivo y después se revisan tipos y rangos. También tomamos la idea de datos obligatorios y opcionales, y de mensajes que dicen qué regla falló.
- **Lo que no tomamos:** la sintaxis de JSON (llaves y comillas), porque queremos que una persona sin experiencia pueda escribir las reglas.

### 2.3 ANTLR

ANTLR es una herramienta que genera analizadores (parsers) a partir de una gramática. La gramática se escribe aparte del código y tiene reglas para reconocer las palabras (léxico) y la estructura (sintaxis).

- **Lo que tomamos:** la idea de escribir primero la gramática como un documento aparte y que el programa se base en ella.
- **Lo que no tomamos:** la herramienta en sí. Para la v0.1 el lenguaje es pequeño y, como el equipo tiene poca experiencia programando, el lector se escribirá a mano en Python. Más adelante se podría reconsiderar.

### 2.4 EBNF

EBNF (Extended Backus-Naur Form, norma ISO/IEC 14977) es una notación para escribir gramáticas. Con ella se define de forma exacta cómo se escribe un lenguaje. No es un lenguaje para ejecutar, solo para describir. La usamos en la sintaxis concreta de ParkLogic.

## 3. Comparación

| Referencia | Para qué sirve | Cómo se escribe | Qué aplicamos en ParkLogic |
|---|---|---|---|
| SQL | Consultar bases de datos | Palabras clave en mayúsculas, estilo declarativo | Mayúsculas y estilo declarativo |
| JSON Schema | Validar la estructura de datos | Documento JSON con tipos, mínimos y obligatorios | Validación separada, obligatorios y rangos |
| ANTLR | Generar analizadores desde una gramática | Archivo de gramática con reglas | Gramática escrita aparte del código |
| EBNF | Describir gramáticas | Reglas con comas, llaves, corchetes y barras | Notación de la gramática de ParkLogic |

## 4. Conclusiones que usamos en el diseño

1. ParkLogic será declarativo: el archivo dice qué reglas hay y el programa se encarga de calcular.
2. Las palabras clave van en mayúsculas y cada instrucción ocupa una línea. Se permiten comentarios con `#` y líneas vacías para que los archivos sean fáciles de leer.
3. La gramática se documenta en EBNF antes de programar nada.
4. Leer el archivo y validarlo son dos pasos distintos.

El lenguaje es propio. Las palabras clave (`TARIFA_HORA`, `TOLERANCIA`, `MAXIMO_DIARIO`, etc.) las diseñamos nosotros y solo nos inspiramos en las ideas de las referencias de arriba.

## 5. Fuentes

- ISO/IEC 9075, *Database languages SQL*. Estándar del lenguaje SQL.
- JSON Schema. *Understanding JSON Schema*. https://json-schema.org/understanding-json-schema/
- ANTLR. Sitio oficial. https://www.antlr.org/
- Parr, T. (2013). *The Definitive ANTLR 4 Reference*. Pragmatic Bookshelf.
- ISO/IEC 14977:1996, *Extended BNF*. Notación EBNF.
- Fowler, M. (2010). *Domain-Specific Languages*. Addison-Wesley.

Fecha de consulta de las fuentes en línea: 24 y 25 de noviembre de 2026.
