
**Objetivo: crear un kernel simple y un sector de arranque capaz de arrancarlo**

Kernel
----------

Nuestro kernel C simplemente imprimirá una 'X' en la esquina superior izquierda de la pantalla. Abrimos `kernel.c`.

En el código tenemos una función que nos obligará a crear una rutina de entrada al kernel que no apunta al byte 0x0 en nuestro kernel, sino a una etiqueta real que sabemos que lo inicia. En nuestro caso, la función `main()`.

`i386-elf-gcc -ffreestanding -c kernel.c -o kernel.o`

Esa rutina está codificada en `kernel_entry.asm`. Para compilar este archivo, en lugar de generar un binario, generaremos un archivo en formato `elf` que se vinculará con `kernel.o`

`nasm kernel_entry.asm -f elf -o kernel_entry.o`


el enlazador
----------

Un enlazador es una herramienta muy poderosa y solo comenzamos a beneficiarnos de ella.

Para vincular ambos archivos de objetos en un solo núcleo binario y resolver las referencias de etiquetas,

`i386-elf-ld -o kernel.bin -Ttext 0x1000 kernel_entry.o kernel.o --oformato binario`

y observamos como nuestro kernel se colocará no en `0x0` en la memoria, sino en `0x1000`. El sector de arranque también necesitará conocer esta dirección.


Bootsector

--------------
Abrimos  `bootsect.asm` y examine el código y compilamos con `nasm bootsect.asm -f bin -o bootsect.bin`

-----------------------

Vincularlos todo en un solo archivo:

`cat bootsect.bin kernel.bin > os-image.bin`


Makefile
--------

Como último paso, ordenaremos el proceso de compilación con un Makefile. Abrimos el script `Makefile`.
