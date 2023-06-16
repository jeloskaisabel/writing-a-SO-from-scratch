**Objetivo: desplazar la pantalla cuando el texto llegue al final**

En `drivers/screen.c` y tenemos en cuenta que en la parte inferior de `print_char` hay una nueva sección (línea 84) que comprueba si el desplazamiento actual supera el tamaño de la pantalla y desplaza el texto.

El desplazamiento real es manejado por una nueva función, `memory_copy`. Es una versión más simple del `memcpy` estándar, pero lo nombramos de manera diferente para evitar colisiones de espacios de nombres, al menos por ahora. Abra `kernel/util.c` para ver su implementación.

Para ayudar a visualizar el desplazamiento, también implementaremos una función para convertir números enteros en texto, `int_to_ascii`. Nuevamente, es una implementación rápida del estándar `itoa`. Los números enteros que tienen dos dígitos o más, se imprimen al revés.

Finalmente, abrimos `kernel/kernel.c`. Inicialmente, cada línea muestra su número de línea. Puede establecer un punto de interrupción en la línea 14 para confirmar esto. Luego, los siguientes `kprint`s obligan al núcleo a desplazarse hacia abajo.
