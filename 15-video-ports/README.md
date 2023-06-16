**Objetivo: aprender a usar los puertos de datos de la tarjeta VGA**

Usaremos C para comunicarnos con dispositivos a través de registros y puertos de E/S.

Abrimos `drivers/ports.c` y examinamos la sintaxis del ensamblador C en línea. Tiene algunas diferencias, como el orden de los operandos de origen y destino, y la sintaxis para asignar variables a los operandos.

En este ejemplo, examinaremos los puertos de E/S que asignan la posición del cursor de la pantalla. Específicamente, consultaremos el puerto `0x3d4` con el valor `14` para solicitar el byte alto de posición del cursor, y el mismo puerto con `15` para el byte bajo.

Cuando se consulta este puerto, guarda el resultado en el puerto `0x3d5`

También usamos `gdb` para inspeccionar el valor de las variables C, ya que todavía no podemos imprimirlas en la pantalla. Para hacerlo, establcemos un punto de interrupción para una línea específica, `breakpoint kernel.c:21` y use el comando `print` para examinar las variables.

Finalmente, usaremos la posición del cursor consultada para escribir un carácter en esa ubicación.
