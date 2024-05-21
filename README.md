# Trabajo Practico N°4 - Sistemas de Computacion - Modulos de Kernel
## Desafio 1

### Checkinstall

`checkinstall` es una herramienta utilizado en Sistemas Operativos basadoe en `Unix/Linux` para simplificar la instalacion de programas desde su codigo fuente. Su funcion principal es crear paquetes instalables que se integren con el sistema de gestion de paquetes del Sistema Operativo, esto no solo permite una facil instalacion sino que tambien, una facil eliminacion del paquete.

### Ejemplo de Hello World

Se comienza con un codigo en `C` simple:
```c
#include <stdio.h>

int main() {
    printf("Hello, World!\n");
    return 0;
}
```
Solo realiza una impresion de un `Hello, World!`

El `Makefile`:
```
all:
	gcc -o hello_world hello_world.c

install:
	install -m 0755 hello_world /usr/local/bin/hello

clean:
	rm -f hello_world hello-world_1.0-1_amd64.deb description-pak
```
El `Makefile` hace 2 cosas, por un lado crea el objeto ejecutable `hello_world` y luego agrega el path `/usr/local/bin/` para el ejecutabe y nombrandolo `hello`, ademas de establecer los permisos `-m 0755` `r/w/x` para el propietario y `r/x` para otros usuarios:

![Imagen1](/img/img1.png)


Y el `build.sh`, que contiene la instruccion `checkinstall` que crea el paquete para que pueda ser gestionado por el sistema de gestion de paquetes:
```bash
sudo checkinstall --pkgname=hello_world --pkgversion=1.0 --pkgrelease=1 --default
```
> `--pkgname=hello_world` indica el nombre del paquete a instalar

![Imagen2](/img/img2.png)
![Imagen3](/img/img3.png)

Una vez que se termino de instalar el paquete `hello`, nos idica que podemos desinstalarlo facilmente usando
```bash
dpkg -r hello-world
```
Si vamos al directorio `/usr/local/bin/` podemos ver que se instalo correctamente y al ejecutarlo imprime `Hello, World`

![Imagen4](/img/img4.png)


## Desafio 2


