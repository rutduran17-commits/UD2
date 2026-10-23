putty   vt100

/etc/init.d/ssh useDNS no
sudo loadkeys es
********************************************************
- esto para ver el puerto


sudo grep puerto /etc/services
 ******************************************************

para pararlo y editarlo en el mismo

EXAMEN, PARAR UN SERVICIO


primera parte tipo test sin ayuda  32 preguntas

////////////////////////////////////////////////////////////////
como agregarle una ip al ubuntu


- asignar la ip fija osea de momento:

sudo ip addr add 192.168.10.11/24 dev enp0s3 

- así se activ la interfaz:
sudo ip link set enp0s3 up

esto es para añadirle una una ip permanente:

sudo nano /etc/netplan/50-cloud-init.yaml
en  ethernets, buscamos la interfaz, le ponemos a dhcp4: false
y abajo colocamos addresses:
-192.168.10.10/24
Por último le damos un sudo netplan apply


///////////////////////////////////////////////////////////////

en el metasploide funciona el archivo:
sudo nano /etc/network/interfaces
y para reiniciar el servicio usamos un:

sudo /etc/init.d/networking restart

//////////////////////////////////////////////////////////////

usando el comando:
sudo nmap -sV <ip> -oN fichero.txt
si queremos escanear todos los puertos usamos:
sudo nmap -sV -p- 192.168.100.10 -oN fichero.txt

podemos escanear una ip. ahora para ver el archivo creado usamos el 
pwd ( es para saber en donde nos encontramos)
haciendo un ls -l antes.txt vemos que se existe

y con un:

cat antes.txt        o un 
less antes.txt vemos lo que hay dentro
con ctrl + z paramos lo del less

dentro del informe vemos cositas
///////////////////////////////////////////
 
Ahora desglozaremos lo que se ve dentro de ese archivo o haciedno:

sudo netstat -tulpn

este es el cabezero:
Proto Recv-Q Send-Q Local Address         Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:512           0.0.0.0:*               LISTEN      4469/xinetd

Como averiguar su informaión?

a simple vista ya sabemos el puerto que es 512 y el proto que es tcp.
busquemos su nombre de servicio ya que xinetd es un portero.

1. podemos verlo escribiendo: grep -w 512/tcp /etc/services

de esta manera nos devuleve el nombre del servicio que es exec.

Tambien usando: sudo grep -v '^#' /etc/inetd.conf

vemos que exec se encuentra dentro y que su programa ejecutable es:


/usr/sbin/in.rexecd

Ahora veamos el paquete usando:

dpkg -S /usr/sbin/in.rexecd

ahí dentro vemos que el paquete se llama:

rsh-server

Pero a la hora de intentar apagar ese servicio "in.rexecd" no era el que lo gestiona

//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

aquí hay un caso complejin, los que no te dicen directamente el  nombre sel servicio.

primero hacemos un grep -w 2049/tcp /etc/services

nos dará el nombre, en este caso nfs y como no se encuentra en el archivo de sudo grep -v '^#' /etc/inetd.conf nos vamos de frente a ell paquete:
dpkg -l | grep nfs  
eso nos devolverá el paquete

//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

otro caso, si no es un xinetd, ya suele ser el nombre del servicio por lo que no hay un portero por lo tanto el grep -w   no nos vale,
si queremos saber el programa solo ponemos which (nombre)  por ejemplo:

which rmiregistry 

para el paquete:

dpkg -S 
    
/////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

nos hemos topado con un unreali, aquí no nos funciona ninguna de las anteiores por lo que localizaremos la ruta exacta preguntando al sistema con su pid:( el pid ers 4620)
ls -l /proc/4620/exe
tambien usé el whereis unrea

fiémonos mas del ls -l /proc/  / exe, porque aunque salia unreali  no era ese.
dpkg -S /usr/bin/Xtightvns

////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////       

mira, este comando es como el -tulpn:

netstat -antp muestra todo.

si al hacer ells -l /proc/4620/exe  no nos da nada, entonces comprobamos con un netstat, verificamos el mismo puerto y vemos si la pid sigue siendo la misma o cambió.

////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

vamos a apagar esos servicios.

para los xinetd resulta mas fácil apagarlo haciendo:

sudo /etc/init.d/xinetd stop

comprobamos que todos los que llevaban a xinetd de portero desaparecieron junto a sus puertos.


////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////- 
si quiero apagar un programa..., primero busco su script ejecuto:

sudo grep -rn "jsvc" /etc/init.d/ 

nos devolvió: /etc/init.d/tomcat5.5:26:DAEMON=/usr/bin/jsvc
cosa que usamos para pararla haciendo:

sudo /etc/init.d/tomcat5.5 stop


y si quiero que no vuelva a arrancar al inicio le doy un:


sudo update-rc.d -f tomcat5.5 remove


////////////////////////////////////////////////       
 * Algunos no me daban el PID ni el nombre por lo que usaba el comando de
   grep (puerto) /etc/services 





/////////////////////////////

esta es una contramedida, bloquearemos el ping

sudo iptables -A INPUT -p icmp --icmp-type echo-request -j DROP


++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++


Si en el futuro quieres que la terminal te lo diga directamente sin contar a mano, puedes usar el truco de contar las líneas filtrando los mensajes iniciales:

apt list --upgradable 2>/dev/null | wc -l


cat /etc/apt/sources.list   --->  ya no se usa


sobre las claves a la hora de descargar paquetes 

dentro de ---> /usr/share/keyrings/    se guardan las claves oficales de cada paquete


ubuntu-archive-keyring.gpg   ---> clave oficial de ubuntu
**************************************************************************************

apt policy curl
