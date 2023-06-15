**Creando un archivo que el BIOS interprete como un disco de arranque**

------

Cuando la computadora arranca, el BIOS no sabe cómo cargar el sistema operativo, por lo que delega esa tarea al sector de arranque. Por lo tanto, el boot debe colocarse en una ubicación estándar conocida. Esa ubicación es el primer sector del disco (cilindro 0, cabezal 0, sector 0) y ocupa 512 bytes.
Para asegurarse de que el "disco sea de arranque", la BIOS comprueba que los bytes 511 y 512 del supuesto sector de arranque sean bytes `0xAA55`.
Este es el sector de arranque más simple que existe:

```
e9 fd ff 00 00 00 00 00 00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
[ 29 more lines with sixteen zero-bytes each ]
00 00 00 00 00 00 00 00 00 00 00 00 00 00 55 aa
```

Básicamente es todo ceros, terminando con el valor de 16 bits `0xAA55`. 

Los tres primeros bytes realizan un salto infinito

Boot de arranque sencillo
-------------------------

Podemos escribir los 512 bytes anteriores con un editor binario o simplemente escribir un código ensamblador muy simple:

```nasm
; Loop infinito (e9 fd ff)
loop:
    jmp loop 

; Rellenar con 510 ceros menos el tamaño del código anterior
times 510-($-$$) db 0
dw 0xaa55 
```

Para compilar:
`nasm -f bin boot_sect_simple.asm -o boot_sect_simple.bin`

`qemu boot_sect_simple.bin`

> En algunos sistemas, es posible que debamos ejecutar `qemu-system-x86_64 boot_sect_simple.bin` Si esto da un error de SDL, tenemos que pasar las banderas --nographic and/or --curses .

Ahora observamos una ventana que dice "Arrancando desde el disco duro...".
