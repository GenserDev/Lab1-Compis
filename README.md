# Laboratorio 1 - Compiladores

## Análisis de la gramática ANTLR y del Driver

### Video explicativo (Analisis y pruebas de casos validos e invalidos)

Link: https://youtu.be/JmUeV0BQWSk 

### ANTLR


ANTLR es una herramienta que, a partir de la descripción de un lenguaje escrita en un archivo `.g4`, genera automáticamente el código de un **lexer** (que agrupa el texto en tokens, como números, identificadores u operadores) y un **parser** (que valida que esos tokens estén en un orden permitido por el lenguaje, construyendo un árbol de sintaxis).

Un archivo `.g4` tiene las siguientes secciones: 
- Primero la declaración de la gramática (`grammar MiniLang;`), cuyo nombre debe coincidir con el del archivo; 
- Segundo las reglas del **parser**, escritas en minúscula (`prog`, `stat`, `expr`), que definen la estructura del lenguaje; 
- Tercero las reglas del **lexer**, escritas en MAYÚSCULA (`ID`, `INT`, `NEWLINE`, `WS`), que definen cómo se arma cada token a partir de caracteres.

Dentro de las reglas se usan símbolos con significado propio: 
- `|` indica alternativas ("esto o esto otro")
- `+` indica "uno o más" (por ejemplo `stat+` o `[a-zA-Z]+`)
- El texto entre comillas simples (`'='`, `'('`) es un literal exacto
- `[a-zA-Z]` es una clase de caracteres, igual que en expresiones regulares. 

Utilizar `#` en ANTLR sirve para etiquetar cada alternativa de una regla (por ejemplo `# assign`, `# MulDiv`), de modo que ANTLR genere un contexto independiente para cada caso en vez de uno genérico para toda la regla; esto es lo que después permite distinguir cada caso al trabajar con Visitors o Listeners. 

La acción `-> skip`, usada en la regla `WS`, le indica al lexer que reconozca ese texto (espacios y tabulaciones) pero lo descarte sin mandarlo al parser. 

Por último, el orden de las alternativas en `expr` no es arbitrario: como `MulDiv` se escribe antes que `AddSub`, ANTLR le da mayor precedencia a la multiplicación/división sobre la suma/resta, igual que en matemáticas.

### Driver

`Driver.py` es el programa que conecta las piezas generadas por ANTLR. Toma el archivo de entrada con `FileStream`, lo pasa al `MiniLangLexer` para convertirlo en tokens, esos tokens se guardan en orden en un `CommonTokenStream`, y ese stream se le entrega al `MiniLangParser`, que finalmente ejecuta `parser.prog()` para validar todo el archivo desde la regla inicial de la gramática. 

El script nunca hace `print` de nada, así que si el archivo es válido no se ve ninguna salida en consola, sin embargo, si algo no cumple la gramática, ANTLR imprime automáticamente el error indicando línea, columna y qué esperaba encontrar.
