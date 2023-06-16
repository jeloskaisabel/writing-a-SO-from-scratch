**Objetivo: Programación en Assembler**

Strings
-------

Se definen las cadenas como bytes, pero hay que terminarlas con un byte nulo para poder determinar su final.

```nasm
mystring:
    db 'Hello, World', 0
```

Assembler convierte el texto entre comillas a ASCII, mientras que ese cero solitario se pasará como byte `0x00` (byte nulo)


Estructuras de Control
------------------

Los saltos del ensamblador están definidos por el resultado de la instrucción *anterior*. Por ejemplo:

```nasm
cmp ax, 4      ; si ax = 4
je ax_is_four  ; salta a ax_is_four
jmp else       ; sino salta a else
jmp endif      ; finalmente, reanudar el flujo normal
ax_is_four:
    .....
    jmp endif

else:
    .....
    jmp endif  
endif:
```

Llamando a funciones
-----------------

Llamar a una función es solo un salto a una etiqueta.

Hay dos pasos para trabajar con parámetros:

1. El programador sabe que comparten un registro específico o dirección de memoria
2. Escribir un poco más de código y hacer que las llamadas a funciones sean genéricas y sin efectos secundarios.

El paso 1 es fácil. Por ejemplo, establecemos que usaremos `al` (en realidad, `ax`) para los parámetros.

```nasm
mov al, 'X'
jmp print
endprint:

...

print:
    mov ah, 0x0e  ; tty code
    int 0x10      ; Asumimos que 'al' ya tiene el caracter
    jmp endprint  ; esta etiqueta también está preestablecida
```

Facilmente se puede observar que este enfoque se convertirá en código espagueti. La función `print` actual solo volverá a `endprint`. 

La solución correcta ofrece dos mejoras:

- Guardaremos la dirección de devolución para que pueda variar
- Guardaremos los registros actuales para permitir que las subfunciones los modifiquen sin efectos secundarios.

Para almacenar la dirección de retorno, la CPU nos ayudará. En lugar de usar un par de `jmp` para llamar a las subrutinas, usamos `call` y `ret`.

Para guardar los datos del registro, también hay un comando especial que usa la pila: `pusha` y `popa`, que empuja todos los registros a la pila automáticamente y los recupera después.


Incluyendo archivos externos
------------------------

La sintaxis es
```nasm
%include "file.asm"
```


Impresión de valores hexadecimales
-------------------

En la próxima lección, comenzaremos a leer desde el disco, por lo que necesitamos alguna forma de asegurarnos de que estamos leyendo los datos correctos. El archivo `boot_sect_print_hex.asm` extiende `boot_sect_print.asm` para imprimir bytes hexadecimales, no solo caracteres ASCII.


-----

El archivo `boot_sect_print.asm` es la subrutina que obtendrá `%include`d en el archivo principal. Utiliza un bucle para imprimir bytes en la pantalla. También incluye una función para imprimir una nueva línea. El familiar `'\n'` es en realidad dos bytes, el carácter de nueva línea `0x0A` y un retorno de carro `0x0D`. 

Como se indicó anteriormente, `boot_sect_print_hex.asm` permite la impresión de bytes.

El archivo principal `boot_sect_main.asm` carga un par de cadenas y bytes, llama a `print` y `print_hex` y se cuelga.
