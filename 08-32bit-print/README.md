**Objetivo: Imprimir en la pantalla cuando está en modo protegido de 32 bits**

El modo de 32 bits nos permite usar registros de 32 bits y direccionamiento de memoria, memoria protegida, memoria virtual y otras ventajas, pero perderemos las interrupciones del BIOS y necesitaremos codificar la GDT.

Escribiremos una nueva rutina de cadena de impresión que funciona en modo de 32 bits, donde no tenemos interrupciones de BIOS, manipulando directamente la memoria de video VGA en lugar de llamar a `int 0x10`. La memoria VGA comienza en la dirección `0xb8000` y tiene un modo de texto que es útil para evitar manipular píxeles directos.

La fórmula para acceder a un carácter específico en la cuadrícula de 80x25 es:

`0xb8000 + 2 * (row * 80 + col)`

Es decir, cada caracter usa 2 bytes (uno para el ASCII, otro para el color y tal), y vemos que la estructura de la memoria concatena filas.

En `32bit-print.asm` se tiene el código. Siempre imprimirá la cadena en la parte superior izquierda de la pantalla, pero pronto escribiremos rutinas de nivel superior para reemplazarla.
