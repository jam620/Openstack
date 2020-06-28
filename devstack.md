# Instalar Openstack en Ubuntu con Devstack

Compartiendo con unos colegas surge un proyecto de cloud que tenía en mente para unas clases, implementar una nube privada, la mayoría de los profesionales de tecnología que conocemos les viene a la mente el uso de  proveedores grandes como  Azure, AWS o Google, sin embargo para una entidad educativa pública, no sé cuenta con el presupuesto para adquirir estos servicios. 

Sabemos de la existencia de múltiples herramientas que nos permiten desplegar un modelo IAAS, infraestructura como servicio, el cliente obtiene recursos como servidores, aplicaciones y redes administradas en demanda por el proveedor de servicios.

Algunos de los proyectos que evaluamos fueron los proyectos:

* https://www.proxmox.com/en/
* https://www.citrix.com/
* https://www.vmware.com/products/esxi-and-esx.html

Finalmente decidimos usar **proxmox** pero por razones fuera del alcance de este demostración, como precio de licencias, cantidad de servidores físicos a disposición, entre otras.

OpenStack es una solución de cloud computing del tipo IaaS de código abierto. Su misión es proveer una solución flexible tanto para nubes públicas como privadas, sean estas de cualquier tamaño, y para esto se consideran dos requerimientos básicos: las nubes deben ser simples de implementar y masivamente escalables.

Existen múltiples instalaciones formas de instalación e implementación de openstack

* [Packstack](https://www.golinuxcloud.com/openstack-installation-packstack-centos-linux-7/ "Packstack")
* [MASS](https://ubuntu.com/blog/introduction-deploying-openstack-on-maas-1-9-with-juju "MAAS")
* [Devstack](https://docs.openstack.org/devstack/latest/guides/multinode-lab.html "Devstack" )

Al igual que podemos tener un instalación multinodos o singlenode, la imagen a continuación es un ejemplo de multinode, para este articulo usaremos todo en un solo servidor (**singlenode**)

<img src="/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/multinode.png" alt="multinode" style="zoom:50%;" />

Fuente: https://github.com/bilelmsekni/OpenStack-Folsom-Install-guide/blob/master/OpenStack_Folsom_Install_Guide_WebVersion.rst#25-others

Devstack instalara todos los componentes necesarios para **openstack** 

![openstack-software-diagram](/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/openstack-software-diagram.png)

Fuente: http://vmartinezdelacruz.com/en-pocas-palabras-como-funciona-openstack/

* Horizon – Dashboard: panel web para el manejo de instancias y volúmenes. Este servicio es realmente una aplicación web desarrollada en django que permite comunicarse con las diferentes APIs de OpenStack de una forma sencilla. OpenStack Dashboard es fundamental para usuarios noveles y en general para realizar acciones sencillas sobre las instancias.
* Nova – Compute Service: es el controlador de la estructura básica del Cloud. Es el encargado de iniciar las instancias (máquinas virtuales) de los usuarios y grupos. También es el servicio encargado de la gestión de la red virtual para cada instancia o para las múltiples instancias que formen parte de un proyecto (tenant).
* Glance – Image Service: proporciona un catálogo y un repositorio de imágenes de discos virtuales. Muy utilizado por Nova y de forma casi exclusiva, aunque es un servicio técnicamente opcional, cualquier infraestructura de cloud de un tamaño considerable lo necesita.
* Neutron – Network Service: provee redes virtuales como servicio entre dispositivos administrados por otros servicios de OpenStack, como puede ser una máquina virtual de Nova. Permite a los usuarios crear sus propias redes y luego vincularlas con los dispositivos que deseen.
* Keystone – Identity Service: es un servicio usado para la autenticación entre el resto de componentes. Este servicio utiliza un sistema de autenticación basado en tokens y se incorporó en la versión 2012.1 de OpenStack.
* Cinder – Block Storage Service:  De forma complementaria al almacenamiento de objetos que realiza swift, este componente de nombre Cinder es el encargado del almacenamiento de bloques, que se utilizan en las instancias de OpenStack, es equivalente al servicio de pago Elastic Block Storage (EBS) de Amazon.

###### Requerimientos

* Ubuntu server 18.04  estamors utilizando un vps de [Vultr.com](https://www.vultr.com/?ref=8403796-6G "vultr.com")
* Memoria mínima de 4GB utilizaremos **16GB**
* Al menos 2 vCPU usaremos **6vCPU**
* Almacenamiento de 10GB usaremos **320GB**

1. ###### Instalación Devstack 

   Debemos crear un usuario que no utilice contraseña para la prueba de devstack

   ```shell
   useradd -s /bin/bash -d /opt/stack -m stack
   echo "stack ALL=(ALL) NOPASSWD: ALL" | sudo tee /etc/sudoers.d/stack
   ```

   ![2](/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/2.png)

   Nos logueamos como el usuario **stack** para realizar la instalación

   <img src="/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/3.png" alt="3" style="zoom:50%;" />

   Procedemos a actualizar y vamos a clonar el repositorio de devstack

   ```shell
   apt update
   git clone https://git.openstack.org/openstack-dev/devstack
   ```

   ![4](/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/4.png)

   Vamos a copiar un archivo de configurar 

   ```shell
   cp samples/local.conf ./local.conf
   ```

   ![5](/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/5.png)

   Modificamos los parámetros siguientes con nuestros datos

   ```shell
   nano local.conf
   ```

   ![6](/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/6.png)

   También se debe modificar el parámetro **HOST_IP** con el valor del ip del servidor

   ![7](/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/7.png)

   Procedemos a realizar la instalación de openstack, esto proceso de instalación va a demorar un tiempo dependiendo del servidor y nuestra conexión. Recordar que debemos comprobar los permisos de ejecución del script **stack.sh**

   ```shell
   ./stack.sh
   ```

   <img src="/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/8.png" alt="8" style="zoom: 33%;" />

   Cuando se termina la instalación de openstack nos debe arrojar el siguiente mensaje

   <img src="/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/9.png" alt="9" style="zoom:50%;" />

2. ###### Configuración Openstack

   Accedemos al url indicado http://ip/dashboard y colocamos nuestras credenciales

   <img src="/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/10.png" alt="10" style="zoom:50%;" />

   Vamos entrar al panel de administración

   <img src="/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/11.png" alt="11" style="zoom:50%;" />

   Crearemos una imagen que nos permita crear una instancia de ubuntu server, debemos habilitar el uso de la **cli** de opentsack 

   ```shell
   source openrc admin admin
   ```

   ![12](/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/12.png)

   Procedemos a descargar la imagen en la ruta **~/devstack/files**

   ```shell
    curl -O http://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.img
   ```

   ![13](/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/13.png)

   La imagen debe tener un formato especifico para que pueda ser utilizada por openstack

   ```shell
   openstack image create "ubuntu" --file  bionic-server-cloudimg-amd64.img --disk-format qcow2 --container-format bare --public
   ```

   En el panel de administración con nuestro usuario admin podemos ver las imagen que acabamos de crear

   ![14](/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/14.png)

   

3. ###### Configuración Grupos de Seguridad

   De igual manera que en los proveedores más reconocidos contamos con una sección donde configuramos las políticas de seguridad. Navegue a "**Red"** y luego a "**Grupos de seguridad**" en el menú de la izquierda, como se ve en la imagen a continuación. Haga clic en "**administrar reglas**". Luego haga clic en "**Grupo de seguridad default**", porque queremos que todas las máquinas virtuales tengan las mismas reglas de firewall.

   ![15](/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/15.png)

   ![16](/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/16.png)

   Agregaremos las reglas de ping y ssh

   <img src="/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/17.png" alt="17" style="zoom: 25%;" />

   ​	<img src="/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/18.png" alt="18" style="zoom: 33%;" />

   

   4. ###### Configuración de red

   El globo es la red pública para las máquinas virtuales. La nube es la red interna para las máquinas virtuales. Las dos redes deben estar conectadas entre sí mediante un enrutador, lo hará en el siguiente paso.

   Navegue a "**Red**" y luego a "**Topología de red**" en el menú de la izquierda.

   ![19](/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/19.png)

   Luego tendrá que crear y configurar un **routers**, para hacerlo, navegue a **Red** y luego a **Router** en el menú de la izquierda. 

   <img src="/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/20.png" alt="20" style="zoom: 25%;" />

   Vamos a crear un  Router con el nombre "R1", hacemos clic en el botón "**Crear router**"

   <img src="/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/21.png" alt="21" style="zoom: 25%;" />

   

   <img src="/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/22.png" alt="22" style="zoom: 25%;" />

   

   Haga clic en el router (R1), y luego vamos a interfaces para añadirlas

   <img src="/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/23.png" alt="23" style="zoom: 25%;" />

   Seleccionamos la interfaz compartida 

   <img src="/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/24.png" alt="24" style="zoom:25%;" />

   

   Observaremos la interfaz que acabamos de añadir

   <img src="/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/25.png" alt="25" style="zoom:25%;" />

   ​	

   Ahora nuestra red necesita otra puerta de enlace para "**alcanzar**" el mundo exterior o Internet, para eso utilizamos el adaptador de red externo.

   <img src="/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/26.png" alt="26" style="zoom: 25%;" />

​			

<img src="/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/27.png" alt="27" style="zoom:25%;" />



​		Regresamos a "**Red**" y luego a "**Topología de red**" en el menú de la izquierda. Confirme que las         redes **internal** y **public** están conectados a través del enrutador "R1".

<img src="/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/28.png" alt="28" style="zoom:25%;" />

5. ###### Crear instancias 

   Navegamos a la sección de instancias, en el menú de la izquierda debemos ir a la sección **Compute** y luego a instancias y hacemos click en **Lanzar instancia**
   
   ![29](/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/29.png) 
   
   Crearemos nuestra primera instancia de ubuntu server que descargamos en el **punto 2** 
   
   <img src="/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/30.png" alt="30" style="zoom:25%;" />
   
   Luego, debe elegir la imagen y sí, elegimos la opción "**Eliminar volumen en la instancia Eliminar**". Y seleccione la imagen **Ubuntu**,, luego haga clic en el botón "**Siguiente**".
   
   <img src="/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/31.png" alt="31" style="zoom:25%;" />
   
   A continuación, debe elegir el **sabor** de la instancia, un sabor determina cuántos núcleos, RAM y cuánto espacio en el disco duro tendrá la máquina virtual. En este ejemplo usaremos "**ds512M**". Luego haga clic en el botón "Siguiente"
   
   ![32](/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/32.png)
   
   
   
   En esta ventana seleccionamos la red privada y luego hacemos clic en "Siguiente".
   
   <img src="/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/33.png" alt="33" style="zoom:25%;" />
   
   
   
   En los **Grupos de seguridad** dejamos la opción **default**
   
   <img src="/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/34.png" alt="34" style="zoom:25%;" />
   
   Podemos añadir nuestras llaves ssh en la sección **Key Pair**
   
   <img src="/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/35.png" alt="35" style="zoom:25%;" />
   
   
   
   La ventana de configuración permite utilizar [cloudinit](https://cloudinit.readthedocs.io/en/latest/ "cloudinit") para personalizar nuestra imagen en este caso definimos una contraseña diferente a la que trae por defecto y por último procedemos a **Ejecutar Instancia**
   
   <img src="/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/36.png" alt="36" style="zoom:25%;" />
   
   
   
   La máquina virtual se creará en el servidor con el sabor y se iniciará. Si el estado cambia a "**Corriendo**", puede hacer clic en el nombre de la máquina virtual y luego abrir la consola, debería ver la consola como un monitor virtual. Como se ve en la imagen a continuación.
   
   ![38](/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/38.png)
   
   Openstack funciona con direcciones IP flotantes que se pueden comparar con direcciones IP externas. Para asignar una dirección IP flotante a una máquina virtual, haga clic en asociar IP flotante como se muestra en la imagen a continuación.
   
   <img src="/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/39.png" alt="39" style="zoom:25%;" />
   
   
   
   En nuestro caso debemos primero añadir las direcciones flotantes
   
   <img src="/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/40.png" alt="40" style="zoom:25%;" />
   
   Asignamos el ip flotante con la red **pública**
   
   <img src="/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/41.png" alt="41" style="zoom:25%;" />
   
   Ahora si podemos asociar una ip flotante a la instancia de la máquina virtual
   
   <img src="/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/42.png" alt="42" style="zoom:25%;" />
   
   Vemos que la dirección IP pública 172.24.4.48 se asigna a ubuntu-test.
   
   <img src="/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/43.png" alt="43" style="zoom:25%;" />
   
   Si observamos las interfaz de red de la máquina virtual a la cual instalamos openstack podemos comprobar que se genera un **bridge** para conectar con las máquinas virtuales
   
   <img src="/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/44.png" alt="44" style="zoom:25%;" />
   
   Le podemos llegar haciendo una prueba de ping sencilla
   
   ![45](/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/45.png)
   
   Accedemos a nuestra instancia por ssh
   
   <img src="/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/46.png" alt="46" style="zoom:25%;" />
   
   Comprobamos la salida a internet con otro ping a google
   
   <img src="/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/48.png" alt="48" style="zoom:25%;" />
   
   Existe otra manera de entrar a través de la consola de openstack, haciendo click al nombre de la instancia
   
   <img src="/Users/jam620/Documents/Personal/Backtrack-academy/Openstack/50.png" alt="50" style="zoom:25%;" />
   
   
   
   Recapitulando openstack es una herramienta muy interesante,  para esta prueba con devstack no da muchos problemas, sin embargo la instalación mulinode de manera manual como se recomienda realizar su implementación, más que difícil es tediosa, por ende por cuestiones de tiempo y limitante de hardware decidimos utilizar proxmox. 

###### Referencias

* http://aprendiendoopenstack.blogspot.com/
* http://vmartinezdelacruz.com/en-pocas-palabras-como-funciona-openstack/
* https://www.reddit.com/r/openstack/comments/dq63fk/instruction_manual_how_to_install_openstack_in/
* https://varunsaklani.wordpress.com/2019/09/17/installing-openstack-using-devstack-on-ubuntu-18-04/
* https://www.theurbanpenguin.com/installing-devstack-on-ubuntu-16-04/
* http://ajayaa.github.io/neutron-multinode-devstack/