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
|Servicios/versiones visibles | Tenía muy vivibles servicios que ahora están obsoletos y vulnerables.| | | 
| Respuesta al ping | | | | 



| Aspecto | Antes | Contramedida aplicada | Después |
| :--- | :--- | :--- | :--- | 
| **Puertos abiertos** | Múltiples puertos expuestos (~36 en `netstat`, ~23+ detectados por `nmap`, incluyendo servicios heredados y Tomcat). | Desactivación del super-servidor `xinetd`, parada de servicios innecesarios (`tomcat5.5`) y retirada del arranque automático (`update-rc.d -f remove`). | Reducción drástica de la superficie de ataque, manteniendo únicamente los servicios esenciales para la gestión (ej. SSH). |
| **Servicios/versiones visibles** | Alta visibilidad de servicios obsoletos y vulnerables (ej. `rexec` en puerto 512, conector AJP de Tomcat en puerto 8009, UnrealIRCd, etc.) detectados por escaneos. | Cierre de demonios vulnerables y desactivación de sus scripts de inicio para evitar su reactivación tras reinicios. | Servicios heredados inaccesibles y versiones obsoletas ocultas a los escaneos externos. |
| **Respuesta al ping** | La máquina respondía con normalidad a las solicitudes ICMP Echo Request lanzadas desde la máquina atacante (Torrent-Auditor). | Aplicación de la regla de cortafuegos en la cadena de entrada: <br>`sudo iptables -A INPUT -p icmp --icmp-type echo-request -j DROP` | Pérdida del 100% de paquetes (`100% packet loss` / `Request timeout`), haciendo la máquina invisible a sondeos ICMP básicos. |
