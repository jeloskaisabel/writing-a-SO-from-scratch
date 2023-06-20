## Objetivo: Configurar la Tabla de Descriptores de Interrupción para manejar interrupciones de la CPU

### Tipos de datos

En primer lugar, definiremos algunos tipos de datos especiales en `cpu/types.h` que nos ayudarán a separar las estructuras de datos para bytes crudos de los caracteres y enteros. Estos tipos de datos se han colocado cuidadosamente en la carpeta `cpu/`, donde ubicaremos el código dependiente de la máquina de ahora en adelante. Sí, el código de arranque aún se encuentra específicamente para x86 en la carpeta `boot/`, pero dejémoslo así por ahora.

Se han realizado cambios en algunos de los archivos existentes para utilizar los nuevos tipos de datos `u8`, `u16` y `u32`.

A partir de ahora, nuestros archivos de encabezado C también tendrán protección contra inclusiones múltiples.

### Interrupciones

Las interrupciones son una de las principales cosas que un kernel necesita manejar. Lo implementaremos ahora, lo más pronto posible, para poder recibir la entrada del teclado en lecciones futuras.

Otros ejemplos de interrupciones son: divisiones por cero, desbordamientos de límites, opcodes inválidos, fallos de página, etc.

Las interrupciones se manejan en un vector con entradas similares a las de la Tabla de Descriptores Globales (GDT) (lección 9). Sin embargo, en lugar de programar la Tabla de Descriptores de Interrupción (IDT) en ensamblador, lo haremos en C.

En `cpu/idt.h`, definimos cómo se almacena una entrada en la IDT llamada `idt_gate` (deben haber 256 de ellas, incluso si algunas son nulas, o la CPU podría entrar en pánico). También definimos la estructura real de la IDT que cargará la BIOS, `idt_register`, que es simplemente una dirección de memoria y un tamaño, similar al registro GDT.

Finalmente, definimos un par de variables para acceder a esas estructuras de datos desde el código en ensamblador.

En `cpu/idt.c`, simplemente llenamos cada estructura con un controlador (handler). Como puedes ver, es cuestión de establecer los valores de la estructura y llamar al comando en ensamblador `lidt`.

### ISRs (Rutinas de Servicio de Interrupción)

Las Rutinas de Servicio de Interrupción (ISRs) se ejecutan cada vez que la CPU detecta una interrupción, que generalmente es un evento crítico.

Escribiremos el código necesario para manejar estas interrupciones, imprimir un mensaje de error y detener la CPU.

En `cpu/isr.h`, definimos manualmente 32 ISRs y las declaramos como `extern` porque se implementarán en lenguaje ensamblador en `cpu/interrupt.asm`.

Antes de pasar al código en ensamblador, echemos un vistazo a `cpu/isr.c`. Como puedes ver, aquí definimos una función para instalar todas las ISRs a la vez, cargar la IDT, una lista de mensajes de error y el controlador de alto nivel, que utiliza `kprint` para mostrar información relevante. Puedes personalizar la función `isr_handler` para imprimir o realizar cualquier acción deseada

.

Ahora, pasemos al nivel inferior, donde conectamos cada `idt_gate` con su controlador de nivel bajo y alto. Abre `cpu/interrupt.asm`. Aquí definimos un código común de ISR de nivel bajo, que básicamente guarda/restaura el estado y llama al código en C, y luego las funciones de ISR en ensamblador reales, a las que se hace referencia en `cpu/isr.h`.

Observemos cómo la estructura `registers_t` representa todos los registros que hemos guardado en `interrupt.asm`.

Eso es básicamente todo. Ahora necesitamos hacer referencia a `cpu/interrupt.asm` desde nuestro archivo Makefile y hacer que el kernel instale las ISRs y active una de ellas. Observemos cómo la CPU no se detiene, a pesar de que sería una buena práctica hacerlo después de algunas interrupciones.
