**Objetivo: implementar un asignador de memoria**

Agregaremos un asignador de memoria del kernel a `libc/mem.c`. Se implementa como un simple puntero a la memoria libre, que sigue creciendo.

La función `kmalloc()` se puede usar para solicitar una página alineada, y también devolverá la dirección física real para su uso posterior.

Cambiaremos `kernel.c` dejando todo el código "shell" allí. Probemos el nuevo `kmalloc()` y comprobemos que nuestra primera página comienza en 0x10000 (como está codificado en `mem.c` ) y `kmalloc()` posteriores producen una nueva dirección que está alineada a 4096 bytes o 0x1000 de la anterior.

Tenga en cuenta que agregamos un nuevo `strings.c:hex_to_ascii()` para una mejor impresión de números hexadecimales.

Otra modificación es cambiar el nombre de `types.c` a `type.c` para mantener la coherencia del lenguaje.

El resto de los archivos no han cambiado.
