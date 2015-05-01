# Práctica 3:

Para configurar nginx una vez instalado entramos en 
```/etc/nginx/conf.d/default.conf ```

Una vez hecho eso, tenemos que añadir al principio del fichero:

```
upstream apaches 
{
	server 192.168.1.100;
	server 192.168.1.101;
}
```
Ahora en el archivo de antes, borramos todo el contenido de server {} y añadimos dentro lo siguiente:

```
server{
	listen 80;
	server_name m3lb;
	access_log /var/log/nginx/m3lb.access.log;
	error_log /var/log/nginx/m3lb.error.log;
	root /var/www/;
	location /
		{
			proxy_pass http://apaches;
			proxy_set_header Host $host;
			proxy_set_header X-Real-IP $remote_addr;
			proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
			proxy_http_version 1.1;
			proxy_set_header Connection "";
		}
}
```

Hacemos ```
sudo service nginx restart
``` y ya tenemos los cambios aplicados.

Ahora probaremos si funciona el balanceo, haciendo curl desde la máquina A a la dirección del balanceador nos devuelve aleatoriamente las dos máquinas:

![](http://)

Para ponderar la carga, el apartado server del archivo de configuración tiene que quedar asi:

```
upstream apaches
{
	server 192.168.1.100 weight=2;
	server 192.168.1.101 weight=1;
}
```

Aquí podemos ver como balancea la carga con pesos distintos:

![](http://)

Ahora pasamos a configurar Haproxy, para ello editamos el archivo ```
/etc/haproxy/haproxy.cfg
``` para que quede de la forma:

![](http://)

Una vez hecho esto paramos el servicio nginx mediante el comando ```
sudo service nginx stop
``` y ejecutamos el comando ```
sudo /usr/sbin/haproxy -f /etc/haproxy/haproxy.cfg
``` para activar el haproxy


Ahora probamos si funciona el balanceo; como podemos ver va mostrando la página de ambos servidores una vez cada uno.

![](http://)