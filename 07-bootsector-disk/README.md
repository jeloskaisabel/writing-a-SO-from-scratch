**Objetivo: Permitir que el sector de arranque cargue datos desde el disco para arrancar el kernel**

Nuestro sistema operativo no cabe dentro del sector de arranque de 512 bytes, por lo que necesitamos leer datos de un disco para ejecutar el kernel.

Ahora llamamos a algunas rutinas del BIOS, como lo hicimos para imprimir caracteres en la pantalla.
Para hacerlo, establecemos `al` en `0x02` (y otros registros con el cilindro, cabeza y sector requeridos) y elevamos `int 0x13`

Ahora usaremos el *carry bit*, que es un bit extra presente en cada registro que almacena cuando una operación ha desbordado su actual
capacidad:

```nasm
mov ax, 0xFFFF
add ax, 1 ; ax = 0x0000 y carry = 1
```

No se accede directamente al acarreo, pero otros operadores lo utilizan como estructura de control, como `jc` (saltar si el bit de acarreo está configurado)

El BIOS también establece `al` en el número de sectores leídos, así que siempre compárelo con el número esperado.


Código
----

En `boot_sect_disk.asm` se ve la rutina completa que lee desde el disco.

`boot_sect_main.asm` prepara los parámetros para la lectura del disco y llama a `disk_load`.
Nótese que algunos datos adicionales que en realidad no pertenecen al sector de arranque, ya que están fuera de la marca de 512 bits.

El sector de arranque es en realidad el sector 1 (el primero, los sectores comienzan en 1) del cilindro 0 de la cabeza 0 de hdd 0.

Por lo tanto, cualquier byte después del byte 512 corresponde al sector 2 del cilindro 0 de la cabeza 0 de hdd 0

La rutina principal lo llenará con datos de muestra y luego dejará que el sector de arranque
