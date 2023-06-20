**Objetivo: Pausar y organizar un poco nuestro código. Luego aprenda a depurar el kernel con gdb**

Una vez construido el Kernel y antes de avanzar vamos a organizar el código en carpetas, crear un Makefile escalable para código futuro y pensar en una estrategia.

La mayoría de los archivos creados tienen enlaces simbólicos de lecciones anteriores, por lo que si tenemos que cambiarlos en algún momento, será una mejor idea eliminar el enlace simbólico y crear un nuevo archivo.

Además, usaremos principalmente C para codificar, aprovecharemos la capacidad de qemu para abrir una conexión con gdb. Primero, instalamos un 'gdb' de compilación cruzada ya que OSX usa 'lldb' que no es compatible con el formato de archivo ELF.

```sh
cd /tmp/src
curl -O http://ftp.rediris.es/mirror/GNU/gdb/gdb-7.8.tar.gz
tar xf gdb-7.8.tar.gz
mkdir gdb-build
cd gdb-build
export PREFIX="/usr/local/i386elfgcc"
export TARGET=i386-elf
../gdb-7.8/configure --target="$TARGET" --prefix="$PREFIX" --program-prefix=i386-elf-
make
make install
```

El objetivo de Makefile `make debug`. Este objetivo usa compilaciones `kernel.elf`, que es un archivo de objeto (no binario) con todos los símbolos que generamos en el kernel, gracias al indicador `-g` en gcc. Por favor, examínelo con `xxd` y verá algunas cadenas. En realidad, la forma correcta de examinar las cadenas en un archivo de objeto es `strings kernel.elf`

Podemos aprovechar esta característica de qemu. Escribiendo `make debug` y, en el shell de gdb:

- Configurar un punto de interrupción en `kernel.c:main()`: `b main`
- Ejecutar el sistema operativo: `continuar`
- Ejecutar dos pasos en el código: `siguiente` y luego `siguiente`.
- En la memoria de video: `print *video_memory`. Está la 'L' de "Landed in
   Modo protegido de 32 bits"
- `video_memory` apunta a la dirección correcta: `print video_memory`
- `siguiente` 


*El Kernel será monolítico, ya que son más fáciles de diseñar e implementar.
