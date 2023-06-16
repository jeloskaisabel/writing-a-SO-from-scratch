**Objetivo: programar el GDT**

En el modo de 32 bits, la segmentación funciona de manera diferente. Ahora, el desplazamiento se convierte en un índice de un descriptor de segmento (SD) en la GDT. Este descriptor define la dirección base (32 bits), el tamaño (20 bits) y algunos indicadores, como solo lectura, permisos, etc. Para agregar confusión, las estructuras de datos están divididas, así que en el archivo base podemos observar la figura en la página 34.

La forma más sencilla de programar la GDT es definir dos segmentos, uno para código y otro para datos. Estos pueden superponerse, lo que significa que no hay protección de memoria, pero es lo suficientemente bueno para arrancar, se solucionará más tarde con un lenguaje superior.

La primera entrada de la GDT debe ser `0x00` para asegurarse de que el programador no cometió ningún error al administrar las direcciones.

Además, la CPU no puede cargar directamente la dirección GDT, pero requiere una metaestructura llamada "descriptor GDT" con el tamaño (16b) y la dirección (32b) de nuestra GDT real. Se carga con la operación `lgdt`.
