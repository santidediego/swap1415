#Práctica 6: Discos en RAID

Primero de todo, tenemos que ver cuantos discos tenemos instalados en nuestro sistema. Para ello, entramos en la configuración de la máquina virtual y en la pestaña de almacenamiento, podemos ver la siguiente imagen:
![](http://)

Como podemos ver sólo tenemos un disco. Para poder crear un RAID1 o 'espejo', tenemos que crear otros dos, ya que el que tenemos es el del sistema operativo. Los creamos.

Después instalamos el programa con `sudo apt install mdadm`. Nos aparece una ventana preguntandonos que tipo de servidor tenemos. En nuestro caso, marcamos servidor con internet.

Ahora, mediante `sudo fdisk -l` vamos a ver información sobre el sistema de almacenamiento del equipo. Nos aparecen los tres discos que tenemos, nombrados como `/dev/sda` , `/dev/sdb` y `/dev/sdc`

Ejecutamos el comando `sudo mdadm -C /dev/md0 --level=raid1 --raid-devices=2 /dev/sdb /dev/sdc`

Ahora les damos formato con `sudo mkfs /dev/md0`

Creamos el directorio en el que se montará la unidad del RAID mediante `sudo mkdir /datos` y lo montamos con `sudo mount /dev/md0 /datos`

Comprobamos si se ha montado correctamente, para ello ejecutamos `sudo mdadm --detail /dev/md0` Este es el resultado

![](http://)

Efectivamente tenemos el RAID1 creado correctamente.

##Habilitar el montaje automático

Para ello tenemos que editar el archivo `/etc/fstab`. Vemos que tenemos, entre otras cosas, que localizar el UUID del raid, para ello ejecutamos `ls -l /dev/disk/by-uuid/` y nos fijamos en el número que aparece en el `md0` que es nuestro espacio para el RAID. Lo copiamos y entramos al archivo mencionado anteriormente y escribimos la línea:

`UUID=c66ad602-0754-4bc8-ad72-9ebcde13f71a /Datos ext4 defaults 0 0`

Reiniciando el sistema y ejecutando la orden `mount` vemos que lo ha montado automáticamente al inicio, como se puede ver:

![](http://)

##Simulando un fallo por software

Ejecutamos la orden `/proc/mdstat` para ver el estado del RAID. Ahí podemos ver los dos discos que componen nuestro RAID. Ahora ejecutamos el comando:
`sudo mdadm --manage /dev/md127 --fail /dev/sdc`

Decir que el número asociado al md lo podemos encontrar en el archivo `/proc/mdstat` que hemos abierto antes.

Ahora, si volvemos a ver el archivo mdstat, podemos ver que junto al disco `/sdc` aparece una `(F)` que indica que el disco ha fallado.

Eliminamos el disco fallido mediante la orden `sudo mdadm --manage /dev/md127 --remove /dev/sdc`. Si volvemos a ver el archivo `mdstat` podemos ver que el disco fallido ya no está. Ahora añadimos uno nuevo con el mismo nombre mediante la orden:

`sudo mdadm --manage /dev/md127 --add /dev/sdc`

El disco ya ha sido añadido. Si ejecutamos `sudo cat /proc/mdstat` nos encontramos con lo siguiente:

![](http://)

Como podemos ver nos indica que el nuevo disco se está integrando en el RAID. Cuando se complete proceso lo tendremos ya perfectamente integrado.
