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

### Seguridad del Kernel

Una medida que se puede implementar para la seguridad del kernel, es el `Safe Boot`, una caracteristica de seguridad que se encuentran en los sistemas `UEFI` (Unified Extensible Firmware Interface). Esto requiere que exista una cade de confianza, desde lo primero que carga el hardware (el codigo de firmware), hasta lo ultimo que carga el sistema operativo como parte del kernel `los modulos`.

Si el `Safe Boot` se encuentra habilitado, el kernel y todos los modulos del kernel tiene que estar firmados con una clave privada y autenticados con la clave publica correspondiente. Si no estan firmados y autenticados, el sistema no podra treminar el proceso de arranque. De igual manera cuando el sistema este en funcionamiento, tampoco se podran cargar modulos que no esten firmados y autenticados.

![Img5](/img/img5.png)
> Error al cargar el modulo al kernel, dado que el modulo no se encuentra firmado y autenticado, y el Safe Boot se encuentra activado

## Desafio 2

### Como empiezan los programas y los modulos

Un programa generalmente comienza con una funcion `main()`, ejecuta instrucciones y termina al completar esas instrucciones. Los modulos de kernel funcionan de forma un poco diferente. Un modulo siempre comienza con `init_module` o con la fucnion que se especifique en la llamada `module_init`. Esta es la funcion de entrada para modulos, le dice al kernel que funcionalidad proporcional el modulo y configura el kernel para ejecutar las funciones del modulos cuando sean necesarias. Una vez que hace esto, la funcion de netrada regresa y el modulo no hace nada hasta que el kernel quiera hacer algo con el codigo que proporciona el modulo.

Todos los modulos finalizan llamando a `cleanup_module` o la funcion que especifique con la llamada `module_exit`. Esta es la funcion de salida de los modulos, deshace cualquier funcion de entrada que se haya realizado. Da de baja la funcionalidad que registro la funcion de entrada.

### Funciones disponibles de un programa y un modulo

Los programas al ser ejecutados en el espacio de usuarios, tienen acceso a diversas funciones proporcionadas por el sistemas operativos a traves de `system calls` y bibliotecas:
* Gestion de archivos: abrir (`open()`), cerrar (`close()`), leer (`read()`) y escribir (`write()`) archivos.
* Gestion de procesos: `fork()`, `exec()`, `wait()`, etc
* Gestion de memoria: `malloc()`, `free()`, `calloc()`, etc
* Gestion de red: `socket()`, `bind()`, `listen()` `accept()`, etc

Se compilo un codigo `C` que imprime `Hello, World!` con la flag `-Wall`, la flag sirve para imprimir advertencias durante el proceso de compilacion, por ejemplo: funcion sin declaracion previa, funcion sin `return`, funciones sin uso, etc.

![Img6](/img/img6.png)
![Img7](/img/img7.png)

Luego se ejecuto con `strace -tt` y `strace -c`.

`strace -tt` da una vision detallada de cada `system call`, por ejemplo `mmap()` es la `system call` que se utiliza para mapear una region de la memoria, `close(3)` otra `syscall` que cierra el file descriptor 3 y al final de todo `write()` que imprime el mensaje de `Hello, World!` y luego termina el programa

`strace -c` proporciona un resumen de las `syscall` mas importantes, con el % de tiempo de ejecucion, por ejemplo `execve()` ocupo el `58.40%` del tiempo total `0.000512seg` de la ejecucion total:

![Img8](/img/img8.png)

El archivo `/proc/kallsyms` es un archivo virtual proporcionado por el kernel, que contiene la tabla de simbolos utilizada por el kernel, que incluye nombres de funciones y nombres de variables. Estos simbolos son esenciales para comprender el funcionamiento interno del kernel.

![Img9](/img/img9.png)

El archivo `/proc/kallsyms` tiene el siguente formato:

| Direccion del Simbolo | Tipo de Simbolo | Nombre del Simbolo |
|-----------------|----------------------|----------------------

Los tipos que se pueden ver son:
* `d`: datos de un modulo
* `t`: codigo de un modulo
* `r`: datos de lectura de un modulo
* `b`: datos no inicializados de un modulo

### Espacio de usuario vs espacio de kernel

## Anexo

https://tldp.org/LDP/lkmpg/2.4/html/x437.html
