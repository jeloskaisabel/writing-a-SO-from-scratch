## Objetivo

El objetivo de esta sección es lograr la escritura de cadenas de texto en la pantalla. A través de la implementación de código específico, seremos capaces de mostrar información en el monitor. 

## Estructura de archivos

El proyecto se compone de los siguientes archivos:

- `drivers/screen.h`: Contiene la definición de constantes para el controlador de la tarjeta VGA y tres funciones públicas: una para borrar la pantalla y dos para escribir cadenas de texto. Destaca la función `kprint`, abreviatura de "kernel imprimir".

- `drivers/screen.c`: Este archivo inicia con la declaración de funciones auxiliares privadas que se utilizan para asistir a la API del kernel en la impresión de texto. Entre estas funciones se encuentran `get` y `set_cursor_offset()`, rutinas de acceso al puerto de E/S aprendidas en lecciones anteriores. Además, se encuentra la rutina `print_char()`, encargada de manipular directamente la memoria de video para imprimir caracteres en la pantalla. Por último, se incluyen tres pequeñas funciones auxiliares para transformar filas y columnas en compensaciones y viceversa.

## Funciones principales

### Función `kprint_at`

La función `kprint_at` se utiliza para imprimir una cadena de texto en la pantalla en la posición actual del cursor. Se puede llamar a esta función con los valores `-1` para `col` y `row`.

El proceso de impresión se realiza de la siguiente manera:
1. Se establecen tres variables para representar la columna, la fila y el desplazamiento del cursor.
2. Se itera a través de un puntero `char*` y se llama a la función `print_char()` con las coordenadas actuales.
3. Cabe destacar que la función `print_char` devuelve el desplazamiento de la siguiente posición del cursor, el cual es reutilizado en la siguiente iteración del bucle.

La función `kprint` es básicamente un contenedor para la función `kprint_at`.

### Función `print_char`

Al igual que `kprint_at`, la función `print_char` permite que las columnas y filas sean `-1`. En caso de que sean `-1`, la función recupera la posición actual del cursor del hardware utilizando las rutinas definidas en el archivo `ports.c`.

La función `print_char` también gestiona los saltos de línea. En ese caso, se coloca el desplazamiento del cursor en la columna 0 de la siguiente fila.

Es importante tener en cuenta que las celdas VGA están compuestas por dos bytes: uno para el carácter en sí y otro para el atributo.

## Archivo `núcleo.c`

En el archivo `núcleo.c`, se implementa la capacidad del kernel para imprimir cadenas de texto. Se realiza una serie de pruebas para verificar el correcto posicionamiento de los caracteres, abarcando múltiples líneas, saltos de línea y, finalmente, intentando escribir fuera de los límites de la pantalla. Se recomienda analizar el comportamiento y los resultados obtenidos en estas pruebas.
