# Tabla de Inventario de Servicios y Análisis de Cadena

| Puerto / Proto | Nombre Servicio  | Programa Ejecutable  | Paquete (Debian) | ¿Es necesario?  | Decisión |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 512/tcp| exec| /usr/sbin/in.rexecd |  rsh-server |No | apagarlo |
|513/tcp | login|/usr/sbin/in.rlogind | rsh-server| No |apagarlo| 
| 2049/tcp| nsf| /usr/sbin/rpc.nfsd| nfs-kernel-server| No | Para su casa |
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


En el /usr/sbin/in.rexecd aún que dentro de sudo grep '^#' /etc/inetd.conf salía esa ruta, al hacer un sudo ls -l /proc/(pid del programa)/exe salió la ruta de /usr/sbin/xinetd por lo que a la hora de apagar el programa fue con un sudo nano /etc/init.d/winetd stop y al volver a consultar con el sudo netstat -antp, todos los puertos que tenían a xinetd como portero, desaparecieron, me resultó más fácil apagar al cabecilla que hacerlo uno por uno.


| Aspecto | Antes | Contramedida aplicada | Después |
| :--- | :--- | :--- | :--- | 
| Puertos abiertos | * Con nmap 23 puertos fueron detectados   * Con netstat unos 36| Desactivamos al super servidor que era xinetd y paramos los demás servicios innecesarios junto a retirarlos del arranque automático. | Se fueron todos esos puertos innesesarios dejando solo la 80 y 22.  |
|Servicios/versiones visibles | Tenía muy vivibles servicios que ahora están obsoletos y vulnerables.|  Cierre de los demonios vulnerables y se desactivaron sus  de sus scripts de inicio para evitar su reactivación tras reinicios.| Servicios heredados inaccesibles y versiones obsoletas ocultas a los escaneos externos. | 
| Respuesta al ping |a máquina respondía con normalidad a las solicitudes ICMP Echo Request lanzadas desde la máquina atacante (Torrent-Auditor). | Aplicación de la regla de cortafuegos en la cadena de entrada: <br>`sudo iptables -A INPUT -p icmp --icmp-type echo-request -j DROP` |Pérdida del 100% de paquetes (`100% packet loss` / `Request timeout`), haciendo la máquina invisible a sondeos ICMP básicos. |
 
# Preguntas finales
1. ¿Qué contramedida ha reducido más lo que ve el atacante? ¿Por qué?
2. Si solo ocultáis un banner pero el servicio sigue activo, ¿la vulnerabilidad sigue ahí? Razonad la respuesta.
3. Torrent-Vulnerable usa un sistema operativo sin soporte desde hace años. ¿Puede una contramedida de estas sustituir a actualizarlo? ¿Qué haríais en una empresa real?
4. De todas las contramedidas de las partes A y B, ¿cuáles evitan que el atacante encuentre información y cuáles solo ayudan a detectar que lo están intentando? (Aún no las hemos aplicado todas, pero pensad en cuáles serían de cada tipo.)



