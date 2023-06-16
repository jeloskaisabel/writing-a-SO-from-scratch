**Objetivo: Direccionar la memoria con segmentación en modo real de 16 bits**

La segmentación significa que puede especificar una compensación para todos los datos a los que hace referencia.

Esto se hace usando registros especiales: `cs`, `ds`, `ss` y `es`, para Código, Datos, Pila y Extra (es decir, definido por el usuario)

Son *implícitamente* utilizados por la CPU, por lo que una vez que establezca algún valor para, por ejemplo para, `ds`, entonces todo su acceso a la memoria se compensará con `ds`. 

Además, para calcular la dirección real, no solo unimos las dos direcciones, sino que las *superponemos*: `segmento << 4 + dirección`. Por ejemplo, si `ds` es `0x4d`, entonces `[0x20]` en realidad se refiere a `0x4d0 + 0x20 = 0x4f0`
