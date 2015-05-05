#Encriptación de ficheros con htaccess y htpasswd
En esta sección vamos a aprender a encriptar ficheros para que no puedan ser vistos por otros usuarios que no tengan la clave.

Primero de todo, recordemos una directiva que incluimos en "Principios básicos de seguridad" que tenía que ver con evitar el acceso a ficheros .htaccess y .htpasswd .Ahora veremos porqué es importante proteger estos archivos, ya que son los protagonistas de la encriptación de ficheros.

Imaginemos que queremos proteger el directorio `/var/www/html/secret` .Vamos a explicar como podemos hacerlo:

Primero de todo instalar todo lo necesario para utilizar el htpasswd. Escribimos `sudo apt-get install apache2-utils`

Una vez hecho esto, tenemos que crear un fichero `.htaccess` en la ubicación que queramos proteger. Para ello, escribimos: `sudo nano /var/www/html/secret/.htaccess`

Dentro del fichero tenemos que escribir lo siguiente:

```
AuthUserFile <ruta>
AuthName <lo_que_queramos_que_salga_al_solicitar_ingreso>
AuthType Basic
require valid-user
```
Por poner un ejemplo, podríamos poner:
```
AuthUserFile /home/santiago/.htpasswd
AuthName "Introduzca el usuario y la clave de acceso"
AuthType Basic
require valid-user
```
Ahora tenemos que crear el fichero `.htpasswd`. Para ello vamos a la ruta que hemos especificado arriba y lo creamos, en nuestro caso escribimos: `sudo nano /home/santiago/.htpasswd`

Mi recomendación personal es utilizar `sudo nano` para que el fichero además sea propiedad de root; de esta forma además estará protegido contra modificaciones involuntarias.

Una vez creado, ejecutamos:
`htpasswd .htpasswd santiago`

Nos pedirá que le pongamos un password, se lo introducimos y ya tenemos nuestro archivo de claves listo. Además este archivo está encriptado en md5 por lo que aunque accedamos a él no podremos ver la clave en texto plano, algo realmente importante.

##Posibles problemas
Es probable que habiendo hecho todo lo anterior, no funcione. En ese caso puede que sea por la directova AllowOverride. Recordemos en "Principios básicos de seguridad" para qué servía esta directiva; para que otros usuarios no puedan acceder al directorio. Para que funciones, vamos al archivp **apache2.conf** (en Ubuntu Server) y añadimos la siguiente directiva:
```
<Directory /var/www/html/secret>
  AllowOverride AuthConfig
</Directory>
```
Más información sobre la directiva `AllowOverride` en http://httpd.apache.org/docs/2.2/mod/core.html#allowoverride

##Un método más rápido
Una vez hemos comprendido el funcionamiento general de los ficheros .htaccess y .htpasswd, estamos en posición de presentar un método más rápido para proteger un directorio con usuario y contraseña. Para ello, tenemos que ir al archivo **apache2.conf** y añadir una directiva. Presentamos las directivas para nuestro caso particular, el lector puede adaptarlas sin mayor problema con lo leído anteriormente a sus propias necesidades.

```
<Directory /var/www/html/secret>
    AuthUserFile /home/santiago/.htpasswd
    AuthName "Introduzca el usuario y la clave de acceso"
    AuthType Basic
    require valid-user
</Directory>
```
Una vez añadidas estas directivas, ya solo tenemos que crear un fichero .htpasswd utilizando el comando visto anteriormente y ya tenemos nuestro directorio protegido. Con este método resulta más cómodo proteger varios directorios, ya que solo tenemos que añadir tantos directorios como queramos al archivo **apache2.conf** en vez de tener que copiar y pegar varias veces el fichero .htaccess.

##Si todo ha ido bien
Si todo ha funcionado, cuando accedamos al directorio especificado, nos aparecerá una pantalla de login similar a la siguiente:

![](http://)
