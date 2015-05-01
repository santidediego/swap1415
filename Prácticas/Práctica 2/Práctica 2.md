<H1>Práctica 2</H1>

Primero instalamos rsync con ```
sudo apt-get install rsync
``` pero vemos que ya está instalada por defecto.

Escribimos la orden ```
sudo chown santiago:santiago -R html
``` dentro de /var/www para poder cambiar el usuario de root a santiago, y esto lo hacemos en ambas máquinas

Después hacemos la orden:
```rsync -avz -e ssh santiago@192.168.1.100:/var/www/html/ /var/www/html/```
Nos queda :
![](http://)
Haciendo ```
ls-la /var/www/html
```  podemos ver que el directorio se ha copiado correctamente en la máquina 2.

Ahora vamos a configurar ssh para poder conectarnos automaticamente sin contraseña, para ello escribimos: ssh-keygen -t dsa y nos queda algo como lo siguiente:
![](http://)
El campo enter passphrase lo dejamos en blanco en este caso. La consola nos devuelve donde se ha guardado la identificación y la clave pública.

Nos hemos desplazado a nuestra carpeta y hemos ejecutado el comando:

```ssh-copy-id -i .ssh/id_dsa.pub santiago@192.168.1.100```

Ya hemos configurado ssh para poder conectarnos a la otra máquina sin tener que autenticarnos cada vez. Lo comprobamos haciendo ```ssh santiago@192.168.1.100``` y metiendo la contraseña del otro equipo y ya nos encontramos dentro de máquina 1.

Ahora vamos a utilizar el archivo crontab para programar que se active el rsync cada hora para mantener actualizado el directorio. Para ello ejecutamos:
```sudo nano /etc/crontab.```

Una vez dentro tenemos que añadir la línea:
0 * * * * santiago rsync -avz -e ssh ```santiago@192.168.1.100:/var/www/html/ /var/www/html/```

De esta forma hacemos que se ejecute una vez al principio de cada hora. El archivo crontab queda de la siguiente manera:
![](http://)

