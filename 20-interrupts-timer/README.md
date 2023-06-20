
## Objetivo: Implementar nuestros primeros controladores IRQ: el temporizador de la CPU y el teclado
### Temporizador

El temporizador es fácil de configurar. Primero, declararemos una función `init_timer()` en `cpu/timer.h` e implementaremos en `cpu/timer.c`. Es solo cuestión de calcular la frecuencia del reloj y enviar los bytes a los puertos correspondientes.

Luego, corregiremos `kernel/utils.c int_to_ascii()` para imprimir los números en el orden correcto. Para eso, necesitamos implementar `reverse()` y `strlen()`.

Finalmente, regresaremos a `kernel/kernel.c` y haremos dos cosas. Habilitaremos las interrupciones nuevamente (¡muy importante!) y luego inicializaremos la interrupción del temporizador.

Ejecutamos `make run`

### Teclado

El teclado es aún más fácil, pero tiene una desventaja. El controlador de interrupciones programable (PIC) no nos envía el código ASCII de la tecla presionada, sino el scancode para los eventos de presionado y liberación de la tecla, por lo que deberemos traducirlos.

En `drivers/keyboard.c`, donde hay dos funciones: el callback y la inicialización que configura el callback de interrupción. Se creó un nuevo archivo `keyboard.h` con las definiciones necesarias.

`keyboard.c` también tiene una tabla extensa para traducir scancodes a teclas ASCII. Por ahora, implementaremos solo un subconjunto simple del teclado en inglés (US).
