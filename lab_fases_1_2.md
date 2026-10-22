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
