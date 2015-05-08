#Práctica 5

Una vez creada la base de datos contactos, ejecutamos la orden `mysql -u root -p`. Una vez hecho esto, escribimos:
`FLUSH TABLES WITH READ LOCK;`
`quit;`

Ahora que ya hemos “bloqueado” la base de datos, procedemos a copiarla en la carpeta personal de la máquina esclavo con:

`mysqldump contactos -u root -p > /home/santiago/contactos.sql.`

Nos lanza un error de falta de permisos. Esto es debido a que tenemos que ser root para ejecutar esta operación. Hacemos sudo su y ejecutamos de nuevo la orden. Ahora desblqueamos las tablas; primero ejecutamos `mysql -u root -p` y una vez dentro, ejecutamos: `UNLOCK TABLES;`  `quit;`

Una vez hecho esto, vamos a copiar los datos salvados en la máquina esclavo en nuestra máquina personal. Esto lo hacemos mediante: 

`scp santiago@192.168.1.100: /home/santiago/contactos.sql /home/santiago/`

Ahora vamos a restaurarla. Para ello, vamos a restaurarla en una base de datos nueva llamada contactos_restaurada. Para ello creamos primero otra base de datos:

`mysql > create database contactos_restaurada;`
`mysql > quit;`

Para importarla, escribimos:

`mysql -u root -p contactos_restaurada < /home/santiago/contactos.sql`

##Replicación de la BD mediante una configuración maestro-esclavo

Configuramos el fichero de configuración siguiendo las directrices del guión de prácticas. Efectivamente nos aparece, una vez terminada la configuración, el parámetro **Seconds_Behind_master: 0**

![](http://)

Ahora vamos a comprobar que efectivamente se actuaizan los datos. Insertamos un dato en el maestro y podemos ver en la siguiente imagen como aparece automáticamente en el esclavo.

![](http://)


