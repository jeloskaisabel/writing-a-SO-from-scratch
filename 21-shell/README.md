## Objetivo: Limpiar un poco el código e interpretar la entrada del usuario

Primero, limpiaremos un poco el código para prepararlo para futuras lecciones. 

### Limpieza del código

En primer lugar, necesitaremos más funciones de utilidad para manejar cadenas de texto y otras tareas. En un sistema operativo regular, esto se conoce como biblioteca C, o libc.

Actualmente tenemos un archivo `utils.c` que dividiremos en `mem.c` y `string.c`, con sus respectivos archivos de encabezado.

En segundo lugar, crearemos una nueva función `irq_install()` para que el kernel solo necesite realizar una llamada para inicializar todas las IRQs. Esta función es similar a `isr_install()` y se coloca en el mismo archivo `irq.c`. Mientras estamos aquí, deshabilitaremos el `kprint()` en `timer_callback()` para evitar llenar la pantalla con basura, ahora que sabemos que funciona correctamente.

No hay una distinción clara entre `cpu/` y `drivers/`. El único cambio que haremos por ahora es mover `drivers/ports.*` a `cpu/`, ya que claramente es un código dependiente de la CPU. `boot/` también es un código dependiente de la CPU, pero no lo modificaremos hasta que implementemos la secuencia de arranque para una máquina diferente.

Hay más cambios en los interruptores para `CFLAGS` en el `Makefile`, ya que ahora comenzaremos a crear funciones de nivel superior para nuestra biblioteca C y no queremos que el compilador incluya ningún código externo si cometemos un error en una declaración. También agregamos algunas banderas para convertir las advertencias en errores, ya que un error aparentemente menor al convertir punteros puede tener consecuencias más adelante. Esto también nos obligó a modificar algunas declaraciones de punteros misceláneos en nuestro código.

Por último, agregaremos una macro para evitar errores de advertencia en parámetros no utilizados en `libc/function.h`.

### Caracteres del teclado

¿Cómo accedemos a los caracteres escritos?

- Cuando se presiona una tecla, el callback obtiene el código ASCII a través de nuevas matrices que se definen al principio de `keyboard.c`.
- Luego, el callback agrega ese carácter a un búfer, `key_buffer`.
- También se imprime en la pantalla.
- Cuando el sistema operativo desea leer la entrada del usuario, llama a `libc/io.c:readline()`.

`keyboard.c` también interpreta la tecla de retroceso, eliminando el último elemento del búfer de teclas y borrándolo de la pantalla mediante la llamada a `screen.c:kprint_backspace()`. Para ello, tuvimos que modificar un poco `print_char()` para no avanzar el desplazamiento al imprimir una tecla de retroceso.

### Respuesta a la entrada del usuario

El callback del teclado verifica si se presionó la tecla Enter y luego llama al kernel, informándole que el usuario
 ha ingresado algo. Nuestra última función de la biblioteca C es `strcmp()`, que compara dos cadenas y devuelve 0 si son iguales. Si el usuario ingresa "END", detenemos la CPU.

Esta es la shell más básica que existe, pero deberías estar orgulloso porque la implementamos desde cero. ¿Te das cuenta de lo genial que es esto?
