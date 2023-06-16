**Objetivo: terminar la implementación de interrupciones y el temporizador de la CPU**

Cuando arranca la CPU, el PIC asigna las IRQ 0-7 a INT 0x8-0xF y las IRQ 8-15 a INT 0x70-0x77. Esto entra en conflicto con los ISR que programamos en la última lección. Dado que programamos los ISR 0-31, es estándar reasignar los IRQ a los ISR 32-47.

Los PIC se comunican a través de puertos de E/S (consulte la lección 15). El PIC maestro tiene comando 0x20 y datos 0x21, mientras que el esclavo tiene comando 0xA0 y datos 0xA1.

El código para reasignar los PIC es extraño e incluye algunas máscaras. De lo contrario, solo mire `cpu/isr.c`, nuevo código después de configurar las puertas IDT para los ISR. Después de eso, agregamos las puertas IDT para IRQ.

Ahora saltamos al ensamblador, en `interrupt.asm`. La primera tarea es agregar definiciones globales para los símbolos IRQ que acabamos de usar en el código C. Mire al final de las declaraciones `globales`.

Luego, agregue los controladores de IRQ. Mismo `interrupt.asm`, en la parte inferior. Observamos cómo saltan a un nuevo código auxiliar común: `irq_common_stub` 

Luego creamos este `irq_common_stub` que es muy similar al ISR. Se encuentra en la parte superior de `interrupt.asm`, y también define un nuevo `[extern irq_handler]`

Ahora volvamos al código C, para escribir `irq_handler()` en `isr.c`. Envía algunos EOI a los PIC y llama al controlador apropiado, que se almacena en una matriz llamada `interrupt_handlers` y se define en la parte superior del archivo. Las nuevas estructuras se definen en `isr.h`. También usaremos una función simple para registrar los controladores de interrupción.


No hay cambios en `kernel.c`, por lo que no hay nada nuevo para ejecutar y ver.
