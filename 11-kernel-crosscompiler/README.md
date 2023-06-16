**Objetivo: Crear un entorno de desarrollo para construir el kernel**

Paquetes requeridos
-----------------

Primero, instalamos los paquetes necesarios:
- gmp
- MPFR
- libmpc
- CCG

Sí, necesitaremos `gcc` para construir nuestro `gcc` de compilación cruzada.

Una vez instalado, encuentramos dónde está el gcc empaquetado exportandolo. Por ejemplo:

```
export CC=/usr/local/bin/gcc-4.9
export LD=/usr/local/bin/gcc-4.9
```

Necesitaremos compilar binutils y un gcc de compilación cruzada, y los colocaremos en `/usr/local/i386elfgcc`, así que exportemos algunas rutas ahora.
```
export PREFIX="/usr/local/i386elfgcc"
export TARGET=i386-elf
export PATH="$PREFIX/bin:$PATH"
```

binutils
--------

```sh
mkdir /tmp/src
cd /tmp/src
curl -O http://ftp.gnu.org/gnu/binutils/binutils-2.24.tar.gz # If the link 404's, look for a more recent version
tar xf binutils-2.24.tar.gz
mkdir binutils-build
cd binutils-build
../binutils-2.24/configure --target=$TARGET --enable-interwork --enable-multilib --disable-nls --disable-werror --prefix=$PREFIX 2>&1 | tee configure.log
make all install 2>&1 | tee make.log
```

gcc
---
```sh
cd /tmp/src
curl -O https://ftp.gnu.org/gnu/gcc/gcc-4.9.1/gcc-4.9.1.tar.bz2
tar xf gcc-4.9.1.tar.bz2
mkdir gcc-build
cd gcc-build
../gcc-4.9.1/configure --target=$TARGET --prefix="$PREFIX" --disable-nls --disable-libssp --enable-languages=c --without-headers
make all-gcc 
make all-target-libgcc 
make install-gcc 
make install-target-libgcc 
```

Debemos tener todos los binutils de GNU y el compilador en `/usr/local/i386elfgcc/bin`, con el prefijo `i386-elf-` para evitar colisiones con el compilador y los binutils de su sistema.
