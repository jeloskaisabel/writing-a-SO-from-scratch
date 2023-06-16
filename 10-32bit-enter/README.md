**Objetivo: Ingresar al modo protegido de 32 bits y probar el código desarrollado anteriormente**

Para saltar al modo de 32 bits:

1. Deshabilitar interrupciones
2. Cargar GDT
3. Establecer un bit en el registro de control de la CPU `cr0`
4. Vaciar la canalización de la CPU emitiendo un salto lejano cuidadosamente diseñado
5. Actualizar todos los registros de segmento
6. Actualizar la pila
7. Llamada a una etiqueta conocida que contiene el primer código útil en 32 bits

Encapsularemos este proceso en el archivo `32bit-switch.asm`. 

Después de ingresar al modo de 32 bits, llamaremos a `BEGIN_PM`, que es el punto de entrada para nuestro código útil real (por ejemplo, código del kernel, etc.). Podemos leer el código en `32bit-main.asm`. Compilamos y ejecutamos este último archivo y veremos los dos mensajes en la pantalla.
