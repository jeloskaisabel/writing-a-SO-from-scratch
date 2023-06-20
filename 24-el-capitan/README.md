# Documentación del Trabajo

## Objetivo: Actualizar nuestro sistema de compilación a El Capitan

Actualización del compilador cruzado
------------------------------------

```
export CC=/usr/local/bin/gcc-5
export LD=/usr/local/bin/gcc-5
```

Necesitaremos volver a compilar binutils y nuestro gcc compilado cruzado. Exporta los destinos y el prefijo:

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
curl -O http://ftp.gnu.org/gnu/binutils/binutils-2.24.tar.gz # Si el enlace devuelve error 404, busca una versión más reciente
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
curl -O http://mirror.bbln.org/gcc/releases/gcc-4.9.1/gcc-4.9.1.tar.bz2
tar xf gcc-4.9.1.tar.bz2
mkdir gcc-build
cd gcc-build
../gcc-4.9.1/configure --target=$TARGET --prefix="$PREFIX" --disable-nls --disable-libssp --enable-languages=c --without-headers
make all-gcc 
make all-target-libgcc 
make install-gcc 
make install-target-libgcc 
```

Ahora escribirimos `make` en la carpeta de esta lección y verificamos que todo se compile correctamente.
