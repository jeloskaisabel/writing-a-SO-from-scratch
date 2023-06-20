## Objetivo: Solucionar problemas diversos con nuestro código

1. CFLAGS incorrectos
---------------------

Agregamos `-ffreestanding` al compilar los archivos `.o`, lo cual incluye `kernel_entry.o` y, por lo tanto, `kernel.bin` y `os-image.bin`.

Antes, deshabilitamos libgcc (no libc) mediante el uso de `-nostdlib` y no lo volvimos a habilitar para la vinculación. Dado que esto es complicado, eliminaremos `-nostdlib`.

También se pasó `-nostdinc` a gcc, pero lo necesitaremos para el paso 3, así que lo eliminaremos.

2. Función `main()` en kernel.c
------------------------------

Modifica `kernel/kernel.c` y cambia `main()` por `kernel_main()` ya que gcc reconoce "main" como una palabra clave especial y no queremos interferir con eso.

Cambia `boot/kernel_entry.asm` para que apunte al nuevo nombre correspondientemente.

Para solucionar el mensaje de advertencia `i386-elf-ld: warning: cannot find entry symbol _start; defaulting to 0000000000001000`, agrega `global _start;` y define la etiqueta `_start:` en `boot/kernel_entry.asm`.

3. Tipos de datos reinventados
-----------------------------

Necesitamos incluir `<stdint.h>`, que funciona incluso en `-ffreestanding` (pero requiere bibliotecas estándar), y utilizar esos tipos de datos en lugar de los nuestros, luego eliminarlos en `type.h`.

También eliminamos los guiones bajos alrededor de `__asm__` y `__volatile__` ya que no son necesarios.

4. `kmalloc` mal alineado
-------------------------

En primer lugar, como `kmalloc` utiliza un parámetro de tamaño, utilizaremos el tipo de dato correcto `size_t` en lugar de `u32int_t`. `size_t` debe usarse para todos los parámetros que "cuentan" cosas y no pueden ser negativos. Incluye `<stddef.h>`.

En el futuro, corregiremos nuestro `kmalloc`, convirtiéndolo en un administrador de memoria adecuado y alineando los tipos de datos. Por ahora, siempre devolverá un bloque de memoria nuevo alineado con páginas.

5. Funciones faltantes
----------------------

Implementaremos las funciones faltantes `mem*` en las siguientes lecciones.

6. Controladores de interrupción
--------------------------------

`cli` es redundante, ya que ya establecimos en las entradas de IDT si las interrupciones están habilitadas dentro de un controlador utilizando las marcas `idt_gate_t`.

`sti` también es redundante, ya que `iret` carga el valor de eflags desde la pila,

que contiene un bit que indica si las interrupciones están activadas o desactivadas.
En otras palabras, el controlador de interrupciones restaura automáticamente las interrupciones, estén activadas o no, antes de la interrupción.

En `cpu/isr.h`, `struct registers_t` tiene un par de problemas.
En primer lugar, el supuesto `esp` se cambia a `useless`.
El valor es inútil porque tiene que ver con el contexto de la pila actual, no con lo que se interrumpió.
Luego, renombramos `useresp` a `esp`.

Agregamos `cld` justo antes de `call isr_handler` en `cpu/interrupt.asm` como sugiere la wiki de osdev.

Hay un problema final y importante con `cpu/interrupt.asm`. Los fragmentos comunes crean una instancia de `struct registers` en la pila y luego llaman al controlador de C. Pero eso rompe la ABI, ya que la pila pertenece a la función llamada y pueden modificarla como deseen. Es necesario pasar la estructura como un puntero.

Para lograr esto, edita `cpu/isr.h` y `cpu/isr.c` y cambia `registers_t r` por `registers_t *t`, luego, en lugar de acceder a los campos de la estructura con `.`, accede a los campos del puntero con `->`. Finalmente, en `cpu/interrupt.asm`, agrega un `push esp` antes de llamar tanto a `isr_handler` como a `irq_handler`; recuerda también hacer `pop eax` para limpiar el puntero después.

Ambos callbacks actuales, el temporizador y el teclado, también deben modificarse para usar un puntero a `registers_t`.
