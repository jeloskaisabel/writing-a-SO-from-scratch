**Objetivo: hacer que nuestro sector de arranque previamente silencioso imprima algún texto**

Mejoraremos un poco nuestro sector de arranque de bucle infinito e imprimiremos algo en la pantalla. Levantaremos una interrupción para esto.

En este ejemplo, vamos a escribir cada carácter de la palabra "Hola" en el registro `al` (parte inferior de `ax`), los bytes `0x0e` en `ah` (la parte superior de `ax`) y aumentar la interrupción `0x10`, que es una interrupción general para los servicios de video.

`0x0e` en `ah` le dice a la interrupción de video que la función real que queremos ejecutar es 'escribir el contenido de `al` en modo tty'.

Estableceremos el modo tty solo una vez, aunque en el mundo real no podemos estar seguros de que los contenidos de `ah` sean constantes. Es posible que algún otro proceso se ejecute en la CPU no se limpie correctamente y deje datos basura en `ah`.

Para este ejemplo, no necesitamos ocuparnos de eso, ya que somos lo único que se ejecuta en la CPU.

Nuestro nuevo sector de arranque:
```nasm
mov ah, 0x0e ; modo tty
mov al, 'H'
int 0x10
mov al, 'e'
int 0x10
mov al, 'l'
int 0x10
int 0x10 ; 'l' seguirá en al
mov al, 'o'
int 0x10

jmp $ ; saltar a la dirección actual = bucle infinito

times 510 - ($-$$) db 0
dw 0xaa55 
```

Podemos examinar los datos binarios con: `xxd file.bin`

`nasm -fbin boot_sect_hello.asm -o boot_sect_hello.bin`

`qemu boot_sect_hello.bin`

Su sector de arranque dirá 'Hola' y se colgará en un bucle infinito
