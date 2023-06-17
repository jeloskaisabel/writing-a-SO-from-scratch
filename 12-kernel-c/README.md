**Objetivo: Convertir código Assembler a lenhuaje C**


Compilar
-------

Comenzamos a escribir un programa simple que contenga una función, `función.c`. Abra el archivo y examínelo.

Para compilar código independiente del sistema, necesitamos el indicador `ffreestanding`, así que entonces compilamos `function.c` de esta manera:

`i386-elf-gcc -ffreestanding -c function.c -o function.o`

Examinamos el código máquina generado por el compilador:

`i386-elf-objdump -d función.o`

----

Finalmente, para producir un archivo binario, usaremos el enlazador. Una parte importante de este paso es aprender cómo los lenguajes de alto nivel llaman a las etiquetas de función. Para este ejemplo, colocamos el desplazamiento en `0x0` y usaremos el formato `binario` que genera código de máquina sin etiquetas ni metadatos.

`i386-elf-ld -o function.bin -Ttext 0x0 --oformat binary function.o`

Ahora examinamos ambos archivos "binarios", `function.o` y `function.bin` usando `xxd`. Observamos que el archivo `.bin` es código de máquina, mientras que el archivo `.o` tiene mucha información de depuración, etiquetas, etc.

descompilar
---------

Examinamos el código máquina.

`ndisasm -b 32 función.bin`

