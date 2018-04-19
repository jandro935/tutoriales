# Como instalar Laravel Homestead de forma global

## Descripción

Laravel Homestead proporciona un entorno de desarrollo local completo para empezar
 a trabajar con Laravel sin necesidad de tener que instalar todo lo necesario
 manualmente (servidor, bases de datos, etc). A grandes rasgos, se trata de una
máquina virtualizada desde la que se puede trabajar a través de una conexión SSH.

>:warning: Las siguientes instrucciones son para equipos con Windows 10
instalado. Es posible que se deba habilitar la virtualización de hardware (VT-x). Si se está
utilizando Hyper-V en un sistema UEFI, también puede necesitar deshabilitar Hyper-V para acceder a VT-x.
[Más información](https://laravel.com/docs/5.6/homestead "Documentación de Laravel Homestead").

**0. Descargar e instalar Git y Composer.**

Todo desarrollador de PHP moderno debería tener estas dos herramientas instaladas
en su equipo, pero si no es así, debes instalarlas.

- [Descargar](https://git-scm.com/downloads "Descargar Git") e instalar Git.
- [Descargar](https://getcomposer.org/download "Descargar Composer") e instalar Composer.

**1. Descargar e instalar VirtualBox.**

Laravel Homestead permite trabajar con varios programas de virtualización (VirtualBox, VMWare, Parallels o Hyper-V). En este ejemplo utilizaremos [VirtualBox](https://www.virtualbox.org/wiki/Downloads "Descargar VirtualBox").

**2. Descargar e instalar Vagrant.**

Vagrant es una herramienta para la creación y configuración de entornos de desarrollo virtualizados.
[Descargamos](https://www.vagrantup.com/downloads.html "Descargar Vagrant") e instalamos.

**3. Añadir Vagrant box.**

Abrir el terminal de Git (o el similar que se utilice) como administrador en una
carpeta de nuestro equipo en la que queramos tener instalado Homestead
(Por ejemplo: C:\Users\USER_NAME\) y ejecutar:

```bash
vagrant box add laravel/homestead
```

**4. Clonar el repositorio de Homestead.**

Una vez finalizado el paso 3, debemos clonar el repositorio de Homestead:

```bash
git clone https://github.com/laravel/homestead.git Homestead
```

Nos dirigimos a la carpeta Homestead y ejecutamos:

```bash
cd Homestead
bash init.sh
```

Se debe seleccionar como provider VirtualBox (opción 3).

Principalmente, este script nos generará el archivo Homestead.yaml, el cual contiene la configuración básica de nuestro entorno de desarrollo. Este archivo se encontrará en C:\Users\USER_NAME\Homestead (si se ha seguido el directorio de
este tutorial).

**5. Generar claves SSH.**

Es necesaria una llave SSH. Para saber si se tienen o no, dirígete a C:\Users\USER_NAME\ y comprueba si tienes una carpeta
llamada **.ssh** (se debe habilitar la vista de elementos ocultos en Windows). Si existe, entra en el directorio y comprueba
si existen 2 archivos llamados **id_rsa** y **id_rsa.pub**.

Si la carpeta **.ssh** no existe se debe generar ejecutando:

```bash
ssh-keygen -t rsa -C "tu_email@ejemplo.com"
```

**6. Crear proyecto Laravel e instalar dependencias de Composer**

Nos dirigimos a la carpeta en la que deseemos tener nuestro proyecto Laravel
(c:/Proyectos/Workspace en mi caso) y ejecutamos:

```bash
composer create-project laravel/laravel proyecto
```

Esto nos creará una carpeta llamada proyecto con los archivos por defecto de Laravel.
Con ello trabajaremos con la última versión disponible, pero si deseamos otra distinta,
podemos indicarla en este comando.

```bash
cd proyecto
composer install
```

Con esto instalaremos todas las dependencias declaradas en el archivo **composer.json.**

**7. Edición del archivo Homestead.yaml** :fire:

Dentro del directorio C:\Users\USER_NAME\Homestead abrir el archivo **Homestead.yaml**.
Debería tener un aspecto como el siguiente:

```file
— -
ip: “192.168.10.10”
memory: 2048
cpus: 1
provider: virtualbox

authorize: ~/.ssh/id_rsa.pub

keys:
    — ~/.ssh/id_rsa

folders:
    — map: ~/Code
    to: /home/vagrant/Code

sites:
    — map: homestead.app
    to: /home/vagrant/Code/Laravel/public

databases:
    — homestead

# blackfire:
# — id: foo
# token: bar
# client-id: foo
# client-token: bar

# ports:
# — send: 50000
# to: 5000
# — send: 7777
# to: 777
# protocol: udp
```

Veamos cada uno de los bloques del fichero:

```file
ip: “192.168.10.10”
memory: 2048
cpus: 1
provider: virtualbox
```

- **ip:** dirección IP desde la cual Homestead escuchará (puede ser modificada).
- **memory:** memoria **máxima** que consumirá la máquina virtual.
- **cpus:** número de cpus a utilizar.
- **provider:** programa de virtualización.

---

```file
authorize: ~/.ssh/id_rsa.pub

keys:
    — ~/.ssh/id_rsa
 ```

- **authorize:** ruta para la llave id_rsa.pub.
- **keys:** ruta para llave id_rsa.

```file
authorize: c:/Users/USER_NAME/.ssh/id_rsa.pub

keys:
    — c:/Users/USER_NAME/.ssh/id_rsa
```

:exclamation: No olvidar que la ruta tiene la letra de la unidad en minúscula y con
barra inclinada (/), no con barra invertida.

---

```file
folders:
    - map: c:/Proyectos/Workspace/proyecto
      to: /home/vagrant/code
```

En esta parte se establece la ruta de nuestro equipo que estará mapeada con la ruta
de la máquina virtual. Es decir, el repositorio que hemos clonado en nuestro equipo
local tendrá un "espejo" en la máquina virtual. Si por ejemplo borramos una carpeta
o un fichero en ese directorio, desaparecerá en la máquina también.

En mi caso he mapeado el directorio c:/Proyectos/Workspace/proyecto con /home/vagrant/code,
el cual viene creado por defecto.

---

```file
sites:
    - map: proyecto.local
      to: /home/vagrant/code/proyecto/public
```

Para poder trabajar más cómodamente es muy recomendable crearse un virtual host.
En este caso yo he creado **proyecto.local**. Estas líneas simplemente establecen
la unión entre este virtual host y la carpeta del proyecto.

:exclamation: No olvidar que los proyectos Laravel deben apuntar a la carpeta public, no
a la raíz del proyecto.

:pencil2: Se pueden crear tantos virtual hosts como proyectos se tengan.

---

```file
databases:
    - proyecto
```

Esto creará una base de datos con nombre **proyecto**.

**8. Crear Virtual Host en Windows.**

Para terminar la configuración de nuestro virtual host, debemos declararlo en el
**fichero hosts de Windows (C:\Windows\System32\drivers\etc)**. Dentro del mismo
añadimos al final lo siguiente:

```file
192.168.10.10   proyecto.local
```

:exclamation: Se debe tener cuidado con la extensión de dominio que se asigna
al Virtual Host, ya que a partir de Chrome v.63 algunas extensiones de dominio
requieren HTTPS, como .dev.

**9. Levantar máquina virtual.**

Dentro de la carpeta de Homestead (C:\Users\USER_NAME\Homestead en mi caso),
ejecutamos:

```bash
vagrant up
```

**10. Generar .env y key.**

Una vez levantada la máquina virtual, accedemos a ella y a la carpeta del proyecto:

```bash
vagrant ssh
cd code/proyecto
```

Ahí copiamos el archivo **.env.example** y lo renombramos a **.env**. En este
fichero están las claves que utiliza la aplicación.

Si se cambian otros parámetros, como el nombre de la base de datos, el usuario de
acceso a ella o la contraseña, se deben cambiar también.

Lo único que nos falta para poder levantar el proyecto es generar nuestra clave.
Para ello ejecutamos:

```bash
php artisan key:generate
```

Una vez finalizado, y si todo va bien, al introducir la URL **http://proyecto.local**
veremos la página de inicio del proyecto. :clap:

:boom: Existen varias formas de instalar **Laravel Homestead**. Como digo al principio
del tutorial, esta es la forma global de hacerlo en Windows 10. Instalarlo de forma
global quiere decir que podemos tener uno o varios proyectos bajo el mismo entorno
de Homestead.
