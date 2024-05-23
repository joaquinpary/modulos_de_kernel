# Trabajo Practico N°4 - Sistemas de Computacion - Modulos de Kernel

## Desafio 2

### Espacio de usuario vs espacio de kernel
El kernel se encarga de administrar el acceso a los recursos, por ejemplo de un disco duro o una memoria. Generalmente los programas compiten por los mismos recursos, es el trabajo del kernel mantener el orden y asegurar que los usuarios no accedan a recursos de forma indiscriminada. Para gestionar esto las CPUs pueden operar en diferentes modos, ofreciendo niveles variables de control del sistema para cada modo.

En la arquitectura Intel 80386 tenemos cuatro modos, conocidos como anillos. Por otro lado, Unix usa solo dos de estos anillos: el anillo 0 "modo supervisor", donde todas las acciones son permitidas y el anillo 3 también llamado "modo usuario".

De esta forma el kernel actúa como intermediario entre las aplicaciones en modo usuario y los recursos del sistema en modo supervisor, garantizando así la seguridad y estabilidad del sistema.


### Espacio de Datos y Espacio de Código para Usuario y Kernel
La gestión de memoria es fundamental en los sistemas operativos y se divide en dos espacios principales: el espacio de usuario y el espacio del kernel. Si se quieren escribir módulos de kernel de forma segura es fundamental conocer estos conceptos.

#### Espacio de Usuario
Cuando se crea un proceso, el kernel asigna una porción de memoria física para su código, variables, stack, y heap. Este espacio de memoria virtual es único para cada proceso, evitando que unos interfieran con otros. Por ejemplo, una dirección como `0xbffff978` en diferentes procesos apunta a diferentes ubicaciones físicas.

#### Espacio del Kernel
El kernel tiene su propio espacio de memoria, reservado y protegido. Los módulos del kernel comparten este espacio y no tienen uno propio. Esto significa que errores en los módulos pueden afectar gravemente al kernel:
* Fallos de Segmentación (Segfaults): Un segfault en un módulo del kernel puede derribar el sistema completo.
* Errores de Desbordamiento: Un error, como un off-by-one, puede sobrescribir datos críticos del kernel, causando inestabilidad o fallos del sistema.

#### Riesgos Asociados

|                       | Fallos de Segmentación      | Errores de Desbordamiento                                            |
| --------------------- | --------------------------- | -------------------------------------------------------------------- |
| En espacio de usuario | termina el proceso afectado | puede ejecutar código arbitrario                                     |
| En espacio del kernel | puede afectar al sistema    | puede modificar datos críticos, afectando la estabilidad del sistema |


La separación entre el espacio de usuario y el del kernel se vuelve crucial para la seguridad del sistema. Los errores de gestión de memoria en los módulos del kernel pueden tener consecuencias graves. 

### Drivers
Los drivers son módulos de software que permiten al sistema operativo comunicarse y controlar el hardware. Proporcionan una interfaz para interactuar con dispositivos determinados, facilitando que los programas de usuario accedan al hardware sin tener que conocer los detalles técnicos del mismo.
En sistemas Unix y Linux, cada pieza de hardware está representada por un archivo especial ubicado en el directorio `/dev`. Estos archivos, conocidos como archivos de dispositivo, sirven como interfaces para interactuar con el hardware. Por ejemplo, un controlador de tarjeta de sonido puede conectar el archivo de dispositivo `/dev/sound` con una tarjeta de sonido específica, permitiendo a los programas de usuario reproducir sonido a través de este archivo sin saber los detalles del hardware subyacente.

#### Contenido de /dev
El directorio `/dev` contiene archivos que representan dispositivos de hardware. Estos archivos pueden ser de dos tipos principales:
* Dispositivos de bloque: Estos dispositivos manejan datos en bloques. Incluyen discos duros y unidades SSD por ejemplo. Los dispositivos de bloque tienen un búfer para gestionar las solicitudes de lectura y escritura de manera eficiente. Se identifican con una `b` al comienzo de la salida del comando `ls -l`.
Ejemplo:

```bash
brw-rw----  1 root  disk  3, 1 Jul  5  2000 /dev/hda1
```

* Dispositivos de carácter: Estos dispositivos manejan datos carácter por carácter, sin utilizar un búfer intermedio. Ejemplos comunes son teclados, mouse y puertos seriales. Se identifican con una `c` al comienzo de la salida del comando `ls -l`.
Ejemplo:

```bash
crw-rw----  1 root  dial 4, 64 Feb 18 23:34 /dev/ttyS0
```







