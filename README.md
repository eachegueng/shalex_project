# Front-end Compilador — Lenguaje Shalex (`.sa`)

Proyecto del curso de **Compiladores** que implementa el Front-end completo (Análisis Léxico, Análisis Sintáctico, Árbol de Sintaxis Abstracta - AST, Tabla de Símbolos y Recuperación de Errores) para el lenguaje **Shalex (`.sa`)**, así como la generación de código traducido e indentado hacia el lenguaje intermedio **Urio (`.urio`)**.

---

## Integrantes del Equipo

| Nombre Completo | Carné |
| :--- | :---: |
| Sharon Anelisse Marroquín Hernández | 2400058 |
| Eddy Alexander Cheguén García | 2400059 |

---

## Arquitectura del Compilador

```text
  [ Archivo fuente (.sa) ]
             │
             ▼  (Flex - lexer.l)
  [ Analizador Léxico / Tokens ]
             │
             ▼  (Bison - parser.y)
  [ Analizador Sintáctico + AST + Tabla de Símbolos ]
             │
             ▼  (Generador de Código Indentado)
  [ Archivo de Salida (salida.urio) ]
```

---

## Estructura del Proyecto

* **`lexer.l`**: Especificación del analizador léxico en **Flex**. Reconoce tokens, identificadores, números enteros, números flotantes, cadenas de texto, expresiones booleanas y comentarios simples y multilínea.
* **`parser.y`**: Especificación sintáctica en **Bison**. Define las reglas gramaticales, la precedencia de operadores, la construcción del Árbol de Sintaxis Abstracta (AST), la gestión de la tabla de símbolos, la recuperación de errores sin detener el proceso y la exportación formateada hacia `.urio`.
* **`valido.sa` / `valido2.sa` / `valido3.sa`**: Archivos de pruebas con sintaxis válida para probar las distintas estructuras del lenguaje.
* **`invalido.sa`**: Archivo de prueba para demostrar la recuperación de errores cuando se ingresan sentencias incorrectas.
* **`salida.urio`**: Archivo traducido generado automáticamente al ejecutar el compilador.

---

## Diccionario del Lenguaje Shalex (`.sa`)

El lenguaje toma como referencia visual y temática la franquicia de *Super Mario Bros*:

| Categoría | Palabras Reservadas en `.sa` | Equivalencia en `.urio` | Descripción |
| :--- | :---: | :---: | :--- |
| **Entero** | `coin` | `entero` | Declaración de variables enteras (`int`). |
| **Decimal** | `star` | `real` | Declaración de variables decimales (`float`). |
| **Texto / Cadena** | `player` | `crear` | Declaración de cadenas de texto (`string`). |
| **Literales Booleanos** | `true` / `false` | `true` / `false` | Valore booleanos literales. |
| **Condicional Si** | `mushroom (...) [...]` | `si (...) [...]` | Estructura condicional principal (`if`). |
| **Condicional Sino Si**| `oneUp (...) [...]` | `sino si (...) [...]` | Condicional secundario (`else if`). |
| **Condicional Sino** | `poison [...]` | `sino [...]` | Bloque condicional por defecto (`else`). |
| **Bucle Mientras** | `warp (...) [...]` | `mientras (...) [...]` | Bucle de repetición condicional (`while`). |
| **Bucle Para** | `level (...) [...]` | `mientras (...) [...]` | Bucle por conteo desglosado (`for`). |
| **Lectura** | `box(variable)?` | `leer(variable);` | Captura entrada de usuario en consola. |
| **Impresión** | `flag(expresion)?` | `mostrar(expresion);` | Muestra salida en consola. |
| **Retorno / Salida** | `pipe variable?` | `mostrar(variable);` | Retorna o emite el valor de una variable. |
| **Fin de instrucción** | `?` | `;` | Símbolo delimitador de final de sentencia. |
| **Agrupación / Alcance** | `[` `]` | `[` `]` | Delimitadores de bloques de código. |
| **Precedencia** | `(` `)` | `(` `)` | Agrupación en expresiones matemáticas. |
| **Comentario 1 línea** | `[?] ...` | `// ...` | Comentarios de una sola línea. |
| **Comentario Bloque** | `[??] ... [??]` | `/* ... */` | Comentarios multilínea. |

---

## Características Técnicas Destacadas

1. **Precedencia de Operadores y Paréntesis:**
   * La gramática evalúa la multiplicación (`*`) y división (`/`) con mayor prioridad que la suma (`+`) y resta (`-`).
   * Admite paréntesis de cualquier nivel de anidamiento (ejemplo: `((estrellas + 10) / 2)`), manteniendo los nodos de agrupación (`NODE_PAREN`) en la estructura del AST para su preservación gráfica y semántica.

2. **Recuperación ante Errores Sintácticos:**
   * Implementación de la regla especial `error '?'` junto con la macro `yyerrok` en Bison.
   * Si el compilador detecta una falla en una línea, muestra un mensaje explícito con el número de línea (`yylineno`), omite la sentencia defectuosa y continúa analizando el resto del archivo para generar `salida.urio` con las instrucciones válidas rescatadas.

3. **Indentación Automática del Archivo Resultante:**
   * La rutina de recorrido del AST (`generar_codigo_ulorio`) añade sangría automática (4 espacios por nivel de anidamiento) en los bloques `si`, `sino` y `mientras`, generando un código `.urio` perfectamente legible y estilizado.

---

## Requisitos Previos

Asegúrate de contar con los siguientes compiladores y generadores instalados en tu entorno (macOS / Linux / WSL):

* **GCC / CC** (Compilador de C compatible con estándar C11)
* **Flex** (Fast Lexical Analyzer Generator)
* **Bison** (GNU Parser Generator)

---

## Compilación y Ejecución

Abre una terminal en el directorio raíz del proyecto y ejecuta la siguiente secuencia de comandos:

```bash
# 1. Generar el analizador sintáctico y su encabezado (.tab.h) con Bison
bison -d -v -o parser.tab.c parser.y

# 2. Generar el analizador léxico con Flex
flex -o lex.yy.c lexer.l

# 3. Compilar el programa binario con CC/GCC
cc -Wall -pedantic -std=c11 parser.tab.c lex.yy.c -o programa

# 4. Probar la ejecución con un archivo sintácticamente válido
./programa valido.sa

# 5. Probar la recuperación de errores con un archivo que contenga fallas
./programa invalido.sa
```

---

## Ejemplo de Entrada y Salida

### Entrada (`valido.sa`)
```text
[?] Ejemplo de código en Shalex
player Personaje = 'Peach'?
coin estrellas = 20?
star tiempo = 45.5?

box(estrellas)?

mushroom (estrellas >= 20) [
    tiempo = (tiempo - 5.5) * 2?
] poison [
    estrellas = 100 - (25 * 2)?
]

flag(tiempo)?
```

### Salida Generada (`salida.urio`)
```text
// Ejemplo de código en Shalex

crear Personaje = 'Peach';
entero estrellas = 20;
real tiempo = 45.50;

leer(estrellas);

si (estrellas >= 20) [
    tiempo = (tiempo - 5.50) * 2;
] sino [
    estrellas = 100 - (25 * 2);
]

mostrar(tiempo);
```