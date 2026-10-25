# Laboratorio 3 – Diseño y Validación de Gramáticas

**Asignatura:** Teoría de la Computación – INFO1148
**Carrera:** Ingeniería Civil en Informática
**Docente:** Marcos Lévano
**Integrantes:** Nataly Huaiquinao, Diego Sanhueza

Este repositorio contiene los archivos de **JFLAP** usados en el Laboratorio 3. En ellos se ingresó la Gramática Libre de Contexto (GLC) diseñada para el lenguaje de programación simplificado del laboratorio, y se usaron para generar los árboles de derivación y validar las cadenas del informe.

---

## Archivos

| Archivo | Contenido | Símbolo inicial |
|---|---|---|
| `Gramatica_Lab3.jff` | Gramática base (producciones P1 a P16) | `P` (programa) |
| `Gramatica_Lab3_instruccion.jff` | Gramática base, con las producciones de `I` al inicio | `I` (instrucción) |
| `Gramatica_Lab3_extension.jff` | Gramática base + extensión condicional (P1 a P25) | `P` (programa) |
| `Gramatica_Lab3_extension_instruccion.jff` | Gramática extendida, con las producciones de `I` al inicio | `I` (instrucción) |

JFLAP toma como símbolo inicial el lado izquierdo de la **primera producción**. Por eso hay dos versiones de cada gramática:
- **Las que parten en `P`** sirven para probar programas completos (`Begin … End`).
- **Las que parten en `I`** sirven para probar instrucciones sueltas, como `z = x + y #`, que son las cadenas de las preguntas 3 y 4.

---

## Equivalencia de símbolos

JFLAP solo admite variables de **una letra mayúscula** y trata cada carácter como un terminal. Por eso cada símbolo de la gramática del informe se representa con una letra:

| JFLAP | Gramática | JFLAP | Gramática |
|---|---|---|---|
| `P` | ⟨programa⟩ | `b` | `Begin` |
| `L` | ⟨lista_instrucciones⟩ | `e` | `End` |
| `I` | ⟨instruccion⟩ | `p` | `Programa` |
| `A` | ⟨asignacion⟩ | `s` | `salida` |
| `E` | ⟨expresion⟩ | `i` | `id` (identificador) |
| `O` | ⟨operando⟩ | `n` | `num` (número) |
| `R` | ⟨operador⟩ | `c` | `si` |
| `S` | ⟨salida⟩ | `t` | `entonces` |
| `C` | ⟨condicional⟩ | `f` | `fin_si` |
| `K` | ⟨condicion⟩ | | |
| `Q` | ⟨op_relacional⟩ | | |

Los símbolos `= + - * / % #` y `> < >= <= == !=` se mantienen igual.

---

## Gramática

### Gramática base (P1 a P16)

```
P1      ⟨programa⟩            → Begin Programa id ⟨lista_instrucciones⟩ End
P2, P3  ⟨lista_instrucciones⟩ → ⟨instruccion⟩ ⟨lista_instrucciones⟩ | ⟨instruccion⟩
P4, P5  ⟨instruccion⟩         → ⟨asignacion⟩ | ⟨salida⟩
P6      ⟨asignacion⟩          → id = ⟨expresion⟩ #
P7, P8  ⟨expresion⟩           → ⟨operando⟩ | ⟨operando⟩ ⟨operador⟩ ⟨operando⟩
P9, P10 ⟨operando⟩            → id | num
P11–P15 ⟨operador⟩            → + | - | * | / | %
P16     ⟨salida⟩              → salida id #
```

En JFLAP:

```
P → bpiLe        A → i=E#        R → +
L → IL           E → O           R → -
L → I            E → ORO         R → *
I → A            O → i           R → /
I → S            O → n           R → %
                                 S → si#
```

### Extensión: condicional (P17 a P25)

```
P17      ⟨instruccion⟩   → ⟨condicional⟩
P18      ⟨condicional⟩   → si ⟨condicion⟩ entonces ⟨lista_instrucciones⟩ fin_si
P19      ⟨condicion⟩     → ⟨operando⟩ ⟨op_relacional⟩ ⟨operando⟩
P20–P25  ⟨op_relacional⟩ → > | < | >= | <= | == | !=
```

En JFLAP:

```
I → C       C → cKtLf       K → OQO       Q → >  |  <  |  >=  |  <=  |  ==  |  !=
```

---

## Cómo ejecutarlo

1. Instalar **Java** (por ejemplo, desde [adoptium.net](https://adoptium.net)).
2. Descargar **JFLAP 7.1** desde [jflap.org](https://www.jflap.org).
3. Abrir JFLAP. En Windows, desde PowerShell, en la carpeta donde está el `.jar`:
   ```powershell
   java -jar .\JFLAP7.1.jar
   ```
4. Ir a **File → Open** y elegir uno de los archivos `.jff` de este repositorio.
5. Ir a **Input → Brute Force Parse**, escribir la cadena en el campo *Input* (sin espacios) y presionar **Start**.
6. Para ver el árbol, elegir la vista **Noninverted Tree** y presionar **Step** hasta que el árbol esté completo. La vista **Derivation Table** muestra la derivación paso a paso.

---

## Cadenas de prueba

### Pregunta 3 – Derivaciones y árbol
Archivo: `Gramatica_Lab3_instruccion.jff`

| Cadena original | En JFLAP | Resultado |
|---|---|---|
| `z = x + y #` | `i=i+i#` | Aceptada |
| `salida z #` | `si#` | Aceptada |
| `x = 20 #` | `i=n#` | Aceptada |
| `z = x * y #` | `i=i*i#` | Aceptada |
| `salida temporal #` | `si#` | Aceptada |

### Pregunta 4 – Validación y detección de errores
Archivo: `Gramatica_Lab3_instruccion.jff` (casos 1 a 7) y `Gramatica_Lab3.jff` (caso 8)

| Caso | Cadena original | En JFLAP | Resultado | Error |
|---|---|---|---|---|
| 1 | `x = 10 #` | `i=n#` | Aceptada | – |
| 2 | `z=x+y#` | `i=i+i#` | Aceptada | – |
| 3 | `salida z #` | `si#` | Aceptada | – |
| 4 | `x 10 #` | `in#` | Rechazada | Falta `=` |
| 5 | `z=+xy#` | `i=+ii#` | Rechazada | Operador en posición incorrecta |
| 6 | `salida #` | `s#` | Rechazada | Falta el identificador |
| 7 | `x = 20` | `i=n` | Rechazada | Falta `#` |
| 8 | `Begin Programa prueba x = 5 # salida x # End` | `bpii=n#si#e` | Aceptada | – |

### Extensión – Condicional
Archivo: `Gramatica_Lab3_extension_instruccion.jff`

| Cadena original | En JFLAP | Resultado | Error |
|---|---|---|---|
| `si x > 10 entonces salida x # fin_si` | `ci>ntsi#f` | Aceptada | – |
| `si x > 10 entonces salida x #` | `ci>ntsi#` | Rechazada | Falta `fin_si` |

---

## Notas

- Los identificadores y los números se representan con los terminales genéricos `i` (`id`) y `n` (`num`), porque forman conjuntos infinitos y su forma la reconoce el analizador léxico, no la gramática.
- Los espacios y los comentarios (`/* … */`) se descartan en el análisis léxico, por eso las cadenas se escriben sin espacios en JFLAP.
- La explicación completa de la gramática, las derivaciones y la validación de cadenas está en el informe del laboratorio.
