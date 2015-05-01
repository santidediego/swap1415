#Mod-evasive
Mod evasive se utiliza para evitar ataques de denegación de servicio o ataques DoS. Es una solución que puede ser eficaz frente a un ataque de pequeña magnitud, pero ante un ataque grande, no serviría demasiado.

El funcionamiento es muy sencillo, simplemente realiza una acción cuando una determinada IP intenta acceder a un recurso más de n veces m segundos. La acción por defecto consiste en bloquear dicha IP devolviendo un mensaje de error.

##Instalación
Ejecutamos el comando: `sudo apt-get install libapache2-mod-evasive`

Como podemos ver resulta muy complicado instalarlo.
##Configuración
Ahora tenemos que crear un directorio para los logs, eso lo hacemos mediante el comando: `sudo mkdir /var/log/mod_evasive`

Le cambiamos los permisos mediante el comando `sudo chown www-data:www-data /var/log/mod_evasive/`

Ahora vamos a modificar el archivo de configuración, podemos acceder a él en la ruta
`/etc/apache2/mods-available/evasive.conf`

Ahora dentro del archivo que estamos modificando podemos ver que tiene varios datos por defecto, nosotros abajo proponemos una configuración (por jugar un poco con el archivo de configuración), aunque se puede dejar la que viene por defecto:

```
<ifmodule mod_evasive20.c>

DOSHashTableSize 2000

DOSPageCount  5

DOSSiteCount  50

DOSPageInterval 2

DOSSiteInterval  5

DOSBlockingPeriod  30

DOSLogDir   /var/log/mod_evasive

DOSEmailNotify  santidediego@hotmail.com

DOSWhitelist   127.0.0.1

</ifmodule>
```

Pero ¿Para qué sirve cad cosa?
- **DOSHashTableSize**: Cuanto mayor sea el número que pongamos mayor rendimiento, pero también aumenta el consumo de memoria necesario.
- **DOSPageCount**: Indica el umbral máximo de peticiones permitidas. Si una determinada IP lo excede, se añade a la lista de bloqueos.
- **DOSSiteCount**: Cuenta cuántas peticiones de cualquier tipo puede hacer un cliente dentro del intervalo definido en DOSSiteInterval. Si se excede dicho valor, el cliente queda añadido a la lista de bloqueos.
- **DOSPageInterval**: Intervalo, en segundos, para el umbral de petición de páginas.
- **DOSSiteInterval**: El intervalo, en segundos, para el umbral de petición de objetos de cualquier tipo.
- **DOSBlockingPeriod**: Determina el tiempo que una IP queda bloqueada una vez ha entrado en la lista de bloqueos.
- **DOSLogDir**: Especifica la ruta donde se guardarán los logs, como podemos ver es el directorio anterior.
- **DOSEmailNotify**: Especifica el email donde nos mandarán los avisos.
- **DOSWhiteliest**: Especifica una IP de confianza, por defecto es el localhost.
- **DOSSystemCommand**: Este no aparece en nuestra configuración, pero si en la que viene por defecto. Indica el comando por defecto que se ejecuta cuando una ip es bloqueada

Una vez hecho esto, tenemos que habilitar el módulo; para ello recordemos lo visto en la parte de "Principios básicos de seguridad". No obstante, lo hacemos otra vez para recordar, ejecutamos los comandos:

`sudo a2enmod evasive.load`

`sudo /etc/init.d/apache2 restart`

`service apache2 restart`

Probablemente veamos que el módulo ya se activa solo una vez instalado, en este caso los comandos anteriores están de más pero nunca viene mal saberlo.



