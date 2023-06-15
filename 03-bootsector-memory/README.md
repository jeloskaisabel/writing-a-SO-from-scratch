**Organización de la memoria de la computadora**

En la página 14 [del documento](
http://www.cs.bham.ac.uk/~exr/lectures/opsys/10_11/lectures/os-dev.pdf)<sup>1</sup>
podemos observar el diseño de la memoria.
Ahora veremos dónde se almacena el sector de arranque

Sabemos que el BIOS lo ubica en `0x7C00`, sin embargo debemos hacer las siguientes consideraciones las cuales se ven a través de ejemplos.

Imprimiremos una X en pantalla. Probaremos 4 estrategias diferentes y veremos cuáles funcionan y por qué.
**Open the file `boot_sect_memory.asm`**

Primero, definiremos la X como dato, con una etiqueta:
```nasm
the_secret:
    db "X"
```

Luego intentaremos acceder a `the_secret` de diferentes formas:

1. `mov al, the_secret`
2. `mov al, [the_secret]`
3. `mov al, the_secret + 0x7C00`
4. `mov al, 2d + 0x7C00`, donde `2d` es la posición real del byte 'X' en el binario

Compilamos y ejecutamos el código. Observamos `1[2¢3X4X`, donde los bytes que siguen a 1 y 2 son aleatorios.

Si agregamos o elimina instrucciones, hay que calcular el nuevo desplazamiento de la X contando los bytes y reemplazar `0x2d` con el nuevo.


El desplazamiento global
-----------------

Ahora, dado que compensar `0x7c00` en todas partes es muy inconveniente, los ensambladores nos permiten definir un "desplazamiento global" para cada ubicación de memoria, con el comando `org`:

```nasm
[org 0x7c00]
```

Abriendo **open `boot_sect_memory_org.asm`** observaremos la forma canónica de imprimir datos con el sector de arranque, que ahora es intento 2. Compilando el código y ejecútandolo, y observamos como afecta el comando `org` a cada solución anterior.

En los comentarios del archivo tenemos una explicación completa de los cambios con y sin `org`

