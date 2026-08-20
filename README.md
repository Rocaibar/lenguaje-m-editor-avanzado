## 1. ¿Qué hace exactamente el bloque `let ... in` en lenguaje M? ¿Por qué cada paso puede referenciar al anterior?
En lenguaje M, el bloque `let ... in` define una **secuencia de pasos** donde cada uno genera un valor (generalmente una tabla) que puede ser utilizado por los pasos siguientes.
Cada paso funciona como una **variable inmutable**: una vez creado, no cambia, pero puede ser referenciado por nombre.
Ejemplo conceptual:

let 
Paso1 = ...,
Paso2 = Función(Paso1),
Paso3 = Función(Paso2)
In
Paso3

El valor que se devuelve finalmente es el que aparece después del `in`.
Este diseño permite construir una **cadena de transformaciones**, donde cada paso depende del anterior, manteniendo claridad y orden lógico.
---
## 2. ¿Por qué M es Case Sensitive y qué consecuencia práctica tiene? Dá un ejemplo de un error que esto puede causar.
Lenguaje M distingue entre mayúsculas y minúsculas en:
* ** nombres de funciones (`Text.Trim` ≠ `text.trim`)  
* ** nombres de pasos (`LimpiarEspacios` ≠ `limpiarespacios`)  
* ** valores de texto (`"Prueba"` ≠ `"PRUEBA"`)
Esto significa que cualquier diferencia en el uso de mayúsculas genera errores o resultados incorrectos.
Ejemplo de error típico:

table.transformcolumns(...)

Esto falla porque la función correcta es:

Table.TransformColumns(...)

Consecuencia práctica:
Si un paso se llama `EstandarizarCategoria` y luego se referencia como `estandarizarcategoria`, M no lo encuentra y el script se rompe.
---
## 3. ¿Cuál es la diferencia entre usar `Text.Trim` y `Text.Clean` en M?
* **Text.Trim**: Elimina espacios al inicio y al final del texto. Se usa para limpiar valores con espacios “leading” y “trailing”.
* **Text.Clean**: Elimina caracteres no imprimibles: saltos de línea, tabulaciones, caracteres invisibles.
En esta práctica se usa **Text.Trim** porque el problema del dataset son **espacios sobrantes**, no caracteres invisibles.
---
## 4. ¿Por qué filtraste los registros "PRUEBA" después de estandarizar la categoría y no antes?
Porque M es **case sensitive**. Si filtrás antes de estandarizar, solo eliminarías `"PRUEBA"` en mayúsculas, pero no `"prueba"` o `"Prueba"`.
Después de aplicar:

Text.Proper

todas las variantes quedan convertidas en:

"Prueba"

Recién ahí el filtro funciona correctamente:
each [categoria] <> "Prueba"
Si se filtrara antes, quedarían registros incorrectos y la tabla final no tendría las 5 filas requeridas.
