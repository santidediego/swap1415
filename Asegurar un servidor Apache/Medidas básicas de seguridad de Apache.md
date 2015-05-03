#Medidas básicas de seguridad de Apache
##Estructura de los principales archivos que forman Apache

/**etc/apache2/apache2.conf**: el archivo raíz es el que incluye a los demás. No se debe modificar este archivo.

**mods-enabled/*.load** y **mods-enabled/*.conf**: la finalidad de estos archivos es la carga y configuración de los módulos de Apache.

**httpd.conf**: directivas aplicables a todos los servidores web.

**ports.conf**: define en qué puertos “escuchará” Apache.

**conf.d/**: directorio que contiene archivos de configuración para cada funcionalidad de apache (charset, php, security, etc.)

**sites-enabled/**: directorio que contiene los archivos de configuración de cada virtual host

###Advertencia:
En distribuciones Ubuntu, el archivo **httpd.conf** no existe, y por tanto el archivo que hay que editar es el **apache2.conf**.

##Medidas básicas de seguridad

###Esconder el número de versión de Apache y otra información importante
Es importante esconder este tipo de información para evitar que un atacante pueda utilizarla con malos fines. Para ello, tenemos que entrar en el archivo httpd.conf y añadir las sentencias:
```
ServerSignature Off  	
ServerTokens Prod
```
Con la primera sentencia le decimos que oculte la firma del servidor.

Con la segunda le decimos lo que escribirá Apache en la cabecera de respuesta HTTP del servidor; con esta configuración escribirá Server:Apache

###Asegurarse que archivos fuera de la raíz web no sean accesibles
Tenemos que hacer que Apache no sea capaz de acceder a ningún archivo que no vaya a necesitar. En este, caso vamos a evitar que Apache acceda a archivos que se encuentren fuera de su directorio

```
<Directory />

        Order Deny, Allow
        Deny from all
        Options None
        AllowOverride None
</Directory>
```
        
```
<Directory /var/www>
         Order Allow,Deny
         Allow from all
</Directory>
```
###Permisos sobre los ficheros
Ningún usuario debe tener acceso a los ficheros alojados en el servidor, ya que podría instalar código malicioso. Para ello ejecutamos la orden:
`chown -R root:root /usr/share/apache2`
`chmod -R o-rwx /usr/share/apache2`

Con este comando estamos cambiando el usuario propietario a root y quitando todos los permisos al resto de usuarios.

Además, vamos a eliminar los permisos de lectura sobre archivos de configuración y logs de apache. Para ello ejecutamos los comandos:
`chmod -R go-r /etc/apache2`
`chmod -R go-r /var/log/apache2`

###Desactivar la exploración de directorios y las inclusiones del lado del servidor

Dentro de las etiquetas <Directory /> </Directory> ponemos la sentencia:
`Options None`

###No permitir a Apache utilizar enlaces simbólicos
Con esta medida evitamos que un atacante con permisos de escritura dentro del directorio pueda crear enlaces a archivos fuera del directorio. Tenemos que escribir la sentencia:
`Options -FollowSymLinks`

###Desactivar los módulos no necesarios
En muchas ocasiones Apache viene instalado con módulos que no necesitaremos. Estos módulos se encuentran dentro del archivo httpd.conf y vienen antecedidos por la sentencia LoadModule. Solo tenemos que desactivarlos comentando la línea con un # al principio.

###Disminuir el valor de Timeout
Disminuyendo el valor del timeout dificultamos un ataque de denegación de servicios. Por defecto suele estar en 300 seg, podemos ponerlo más corto añadiendo
		`Timeout tiempo_que_queramos`

###Ajustar el tamaño de los pedidos
Esta medida también ayuda a prevenir ataques de denegación de servicios. Tenemos que alterar el parámetro `LimitRequestBody`. Por defecto está configurado para no tener límite, nosotros podemos ajustar ese número dependiendo de nuestras necesidades.

###Limitar el acceso a archivos por extensión

Podemos utilizar expresiones regulares para limitar el acceso a determinados archivos. Aquí ponemos unos ejemplos:
- Denegar el acceso a los ficheros .htaccess .htpasswd

            <Files ~ “^\.ht”>
                Order allow,deny
                Deny from all
            </Files>

- Evitar que se publiquen los archivos de copia de seguridad

        <FilesMatch “(\.bak$|\.BAK$)”>
                Order Allow,Deny
                Deny from all
        </FilesMatch>

- Denegar el acceso a todos los directorios CVS (utilizados en sistemas de control de versiones de código fuente):

        <DirectoryMatch /CVS/>
            Order Allow,Deny
            Deny from all
        </DirectoryMatch>
