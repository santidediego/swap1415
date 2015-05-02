#Probando la configuración de mod-evasive
En este apartado vamos a ver si realmente el módulo que hemos instalado puede evitar los ataques de denegación de servicio. Que mejor para probarlo que simular uno.

Primero vamos a presentar el escenario que hemos montado:

- Tenemos una máquina virtual donde hemos configurado el mod-evasive en el servidor Apache. Esta máquina tiene la dirección IP privada 192.168.1.100
- Tenemos otra máquina virtual situada en la misma red que la primera con dirección IP 192.168.1.103, que además está equipada con Siege.

Una vez presentado el escenario, vamos a lanzar una batería de peticiones HTTP a la máquina con evasive instalado. Para ello, simulamos un ataque con concurrencia 1, para simular a un único usuario lanzando un ataque DoS. Ejecutamos:
`siege -b -t60s -c1 -v 192.168.1.100/index.html`

Nos encontramos con la sorpresa de que la consola nos devuelve lo siguiente:

![](https://github.com/santidediego/swap1415/blob/master/Trabajo%20de%20la%20asignatura/AtaqueDoS.png)

Fijémonos en el error: aparece un error 403, esto quiere decir que el servidor está rechazando las peticiones HTTP. Además de aparecer la línea en morado, en vez de en su color original.

