# Laboratorio fases 1 y 2 — Estudio Torren

## B. Antes y después:
* Empecé lanzando desde Torrent-Auditor lo siguiente:

  sudo nmap -sV 192.168.10.10 -oN antes.txt

* 192.168.10.10 es la IP de la máquina vulnerable.

* Al abrir el archivo antes.txt, veo que hay como 23 puertos abiertos.

* En Torrent-vulnerable lancé el:

  sudo netstat -tulpn

* lo cual nos arrojó 35 puertos abiertos. Hay puertos con la IP 127.0.0.1 esa la que no sale al exterior por lo que nmap no lo vió. 

* Las clasificamos ya que solo nos teníamos que quedar con 2 puertos abiertos. 80 y 22.


# Empezamos con un puerto ejmpl. puerto 512:

 ¿Como averiguar su informaión?

A simple vista ya sabemos el puerto que es 512 y el protocolo es tcp.
busquemos su nombre de servicio ya que xinetd es un portero.

*  podemos verlo escribiendo: grep -w 512/tcp /etc/services

* De esta manera nos devuleve el nombre del servicio que es exec.

* Tambien usando: sudo grep -v '^#' /etc/inetd.conf  ---> todos los programas que tienen como portero a xinet estan dentro.

* vemos que exec se encuentra dentro y que su programa ejecutable es:


/usr/sbin/in.rexecd

* Ahora veamos el paquete usando:

dpkg -S /usr/sbin/in.rexecd

* ahí dentro vemos que el paquete se llama:

 rsh-server

* Pero a la hora de intentar apagar ese servicio "in.rexecd" no era el que lo gestionaba si no xinet.
  sudo ls -l /proc/[PID]/exe
* se ve que se ejecuta desde /usr/sbin/xinetd
* por lo que fue más fácil apagar al portero :
  sudo /etc/init.d/xinetd stop
  * usando el comando  which (nombre) vemos de donde viene
* comprobamos que todos los que llevaban a xinetd de portero desaparecieron junto a sus puertos.

# parar un programa que arranca a la hora de encender la máquina.
* Usemos a  jsvc, aquí tuve que buscar su script para pagarlo ya que arranca al iniciarse por lo cual está en la ruta de /etc/init.d/
* sudo grep -rn "jsvc" /etc/init.d/  lo cual me devolvió --> /etc/init.d/tomcat5.5:26:DAEMON=/usr/bin/jsvc (ese es el script)
    sudo /etc/init.d/tomcat5.5 stop   ---> así lo paramos
pero quería que no vuelva a arrancar al inicio,
entonces este comando nos ayuda a que no ---> sudo update-rc.d -f tomcat5.5 remove

así ya no molesta.

 * Algunos no me daban el PID ni el nombre por lo que usaba el comando de
   grep (puerto) /etc/services

* Por último se bloquea el ping con sudo iptables -A INPUT -p icmp --icmp-type echo-request -j DROP
 
| Puerto / Proto | Nombre Servicio  | Programa Ejecutable  | Paquete (Debian) | ¿Es necesario?  | Decisión |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 512/tcp| exec| /usr/sbin/in.rexecd |  rsh-server |No | apagarlo |
|513/tcp | login|/usr/sbin/in.rlogind | rsh-server| No |apagarlo| 
| 2049/tcp| nsf| /usr/sbin/rpc.nfsd| nfs-kernel-server| No | Apagarlo |
| 514/tcp| shell|/usr/sbin/in.rshd |rsh-server | No | apagarlo |
|4604/tcp |rmiregistry | /usr/bin/rmiregistry| java-gcj-compat-headless |No | apagarlo|
| 8009/tcp | jsvc | /usr/bin/jsvc | jsvc | No | apagarlo |
| 6697/tcp | Xtightvnc (supuestamente unrealircd) |  /usr/bin/Xtightvnc | tightvncserver |No|apagarlo|
| 5900/tcp | Xtightvnc (supuestamente unrealircd) |  /usr/bin/Xtightvnc | tightvncserver |No|apagarlo|
| 3306/tcp | mysqld | /usr/sbin/mysqld |mysql-server / mysql-client | No | apagarlo|
|1099/tcp |rmiregistry | /usr/bin/rmiregistry| java-gcj-compat-headless |No | apagarlo|
| 6667/tcp | xtighttvnc (supuestamente unrealircd) |  /usr/bin/Xtightvnc | tightvncserver |No|apagarlo|
| 139/tcp | smbd | /usr/sbin/smbd | samba | No | apagarlo |
| 111/tcp | portmap | /sbin/portmap | portmap| No | apagarlo |
| 6000/tcp | Xtightvnc (supuestamente unrealircd) |  /usr/bin/Xtightvnc | tightvncserver |No|apagarlo|
|80/tcp | apache2 |  /usr/sbin/apache2 | apache2.2-common| Si | mantenerlo|
|22/ tcp6 | ss |  /usr/sbin/sshd | openssh-server | Si| mantenerlo|
| 21/tcp | ftp (xinetd) | /usr/sbin/vsftpd | vsftpd | No | apagarlo |
| 23/tcp | telnet (xinetd) | /usr/sbin/in.telnetd | telnetd | No | apagarlo |
| 25/tcp | smtp | /usr/sbin/master (Postfix) | postfix | No | apagarlo |
| 53/tcp, udp | dns | /usr/sbin/named | bind9 | No | apagarlo |
| 69/udp | tftp (xinetd) | /usr/sbin/in.tftpd | tftpd-hpa | No | apagarlo |
| 137, 138/udp | netbios-ns/dgm | /usr/sbin/nmbd | samba | No | apagarlo |
| 445/tcp | microsoft-ds (smb) | /usr/sbin/smbd | samba | No | apagarlo |
| 1524/tcp | ingreslock (backdoor) | /bin/bash (xinetd) | metasploitable-backdoor | No | apagarlo |
| 2121/tcp | proftpd | /usr/sbin/proftpd | proftpd-basic | No | apagarlo |
| 3632/tcp | distccd | /usr/bin/distccd | distcc | No | apagarlo |
| 5432/tcp, tcp6 | postgresql | /usr/lib/postgresql/.../postgres | postgresql | No | apagarlo |
| 8180/tcp | tomcat (http) | /usr/bin/jsvc | jsvc / tomcat5.5 | No | apagarlo |
| 8787/tcp | ruby web service | /usr/bin/ruby | ruby | No | apagarlo |
| 111/tcp, udp | rpcbind / portmap | /sbin/portmap | portmap | No | apagarlo |
| 2049/tcp, udp | nfs | - | nfs-kernel-server | No | Apagarlo |
| 46373/tcp, 42213/udp | rpc.mountd | /usr/sbin/rpc.mountd | nfs-common | No | apagarlo |
| 60492/tcp, 941/udp | rpc.statd | /usr/sbin/rpc.statd | nfs-common | No | apagarlo |


## B. Antes y después

| Aspecto | Antes | Contramedida aplicada | Después |
| :--- | :--- | :--- | :--- | 
| Puertos abiertos | * Con nmap 23 puertos fueron detectados   * Con netstat unos 36| Desactivamos al super servidor que era xinetd y paramos los demás servicios innecesarios junto a retirarlos del arranque automático. | Se fueron todos esos puertos innesesarios dejando solo la 80 y 22.  |
|Servicios/versiones visibles | Tenía muy vivibles servicios que ahora están obsoletos y vulnerables.|  Cierre de los demonios vulnerables y se desactivaron sus  de sus scripts de inicio para evitar su reactivación tras reinicios.| Servicios heredados inaccesibles y versiones obsoletas ocultas a los escaneos externos. | 
| Respuesta al ping |a máquina respondía con normalidad a las solicitudes ICMP Echo Request lanzadas desde la máquina atacante (Torrent-Auditor). | Aplicación de la regla de cortafuegos en la cadena de entrada: <br>`sudo iptables -A INPUT -p icmp --icmp-type echo-request -j DROP` |Pérdida del 100% de paquetes (`100% packet loss` / `Request timeout`), haciendo la máquina invisible a sondeos ICMP básicos. |
 
# Preguntas finales
1. ¿Qué contramedida ha reducido más lo que ve el atacante? ¿Por qué?
   * Una de ellas es apagar al portero de xinetd, porque me di ceunta que este controlaba varios programas , al apagarlo, apagué los programas que tenía a su dominio junto       a sus puertos que no eran necesarias y generaban un peligro .
   * al igual  que los las demás programas con sus puertos. El atacante ya no tendrá una lista de puertos vulnerables para escoger. 
3. Si solo ocultáis un banner pero el servicio sigue activo, ¿la vulnerabilidad sigue ahí? Razonad la respuesta.
   * esto es como ocultar un jarrón roto, al ocultar el bannner solo lo quitas a simple vista, si el atacante es pedrito que recién está aprendiendo, seguro no lo ve, pero los hackers que quieran hacer algo en contra de la empresa lo verán.
   * Si, la vulnerabilidad sigue ahí, lo mejor sería apagarlo por completo si representa una menaza.
5. Torrent-Vulnerable usa un sistema operativo sin soporte desde hace años. ¿Puede una contramedida de estas sustituir a actualizarlo? ¿Qué haríais en una empresa real?
   * No, aunque tome contramedidas como cerrar puertos, dar reglas, etc, no termina con el problema ya que eso está desde la raíz, el sistema operativo tiene sus fallos muy fuertes al dejar de recibir su soporte.
   * En una empresa real, doy por hecho de que conocen el peligro de tener un SO obsoleta, pero de igual manera se puede recomendar la actualización o la migración, si se oponen a eso por miedo a tocar algo, se le puede dar metidas temporales. Por último que dería de ser uno de los primeros es jubilar el sistema operativo.
7. De todas las contramedidas de las partes A y B, ¿cuáles evitan que el atacante encuentre información y cuáles solo ayudan a detectar que lo están intentando? (Aún no las hemos aplicado todas, pero pensad en cuáles serían de cada tipo.)
   - las que evitan que un atacante encuentre información son las de desactivar servicios, cerrar los puertos. Con eso conseguimos que no sea tan visible y que se reduzca el ataque.
  
   - Las que detectan son las de monitorización y el análisis de logs, de esa forma ves las anomalías que estas representan lo cuál alerta a los encargados y base a eso toman medidas necesarias.



