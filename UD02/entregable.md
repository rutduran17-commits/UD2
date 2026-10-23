# Apuntes y Guía de Comandos para Examen

## 1. Configuración Inicial y Teclado

* **Terminal y Emulación:**
  * PuTTY / Emulación de terminal: `vt100`

* **Cambiar mapa de teclado a español:**
  ```bash
  sudo loadkeys es
  ```

* **Ajuste SSH (`/etc/init.d/ssh`):**
  Desactivar la resolución DNS inversa para acelerar las conexiones SSH (`UseDNS no`).

* **Buscar puerto de un servicio en `/etc/services`:**
  ```bash
  grep -i "nombre_servicio" /etc/services
  ```

---

## 2. Configuración de Red IP

### Asignación Temporal (en memoria)

* **Añadir IP a una interfaz:**
  ```bash
  sudo ip addr add 192.168.10.11/24 dev enp0s3
  ```

* **Activar la interfaz:**
  ```bash
  sudo ip link set enp0s3 up
  ```

### Asignación Permanente (Ubuntu moderno - Netplan)

1. Editar la configuración de Netplan:
   ```bash
   sudo nano /etc/netplan/50-cloud-init.yaml
   ```

2. Configurar el archivo:
   ```yaml
   network:
     version: 2
     ethernets:
       enp0s3:
         dhcp4: false
         addresses:
           - 192.168.10.10/24
   ```

3. Aplicar los cambios:
   ```bash
   sudo netplan apply
   ```

### Asignación Permanente (Metasploitable / Debian Antiguo)

1. Editar interfaces de red:
   ```bash
   sudo nano /etc/network/interfaces
   ```

2. Reiniciar el servicio de red:
   ```bash
   sudo /etc/init.d/networking restart
   ```

---

## 3. Escaneo y Auditoría de Redes (Nmap)

* **Escanear IP y detectar versión de servicios:**
  ```bash
  sudo nmap -sV <IP> -oN fichero.txt
  ```

* **Escanear TODOS los puertos (1-65535):**
  ```bash
  sudo nmap -sV -p- 192.168.100.10 -oN fichero.txt
  ```

* **Comprobación del informe:**
  * `pwd`: Ver directorio actual.
  * `ls -l antes.txt`: Verificar la existencia del fichero.
  * `cat antes.txt` o `less antes.txt`: Ver el contenido (`Ctrl + Z` detiene `less`).

---

## 4. Identificación y Análisis de Servicios Abiertos

### Ver puertos y procesos activos

* **Mostrar puertos en escucha con PID y nombre de programa:**
  ```bash
  sudo netstat -tulpn
  ```

  *Cabecera típica de resultado:*
  ```text
  Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
  tcp        0      0 0.0.0.0:512             0.0.0.0:*               LISTEN      4469/xinetd
  ```

* **Ver todas las conexiones y puertos (alternativa):**
  ```bash
  netstat -antp
  ```

### Casos Prácticos de Identificación de Servicios

#### Caso 1: Servicio bajo un superdominio / portero (`xinetd` / `inetd`)

Si en `netstat` aparece `xinetd` asociando a un puerto (ej. `512/tcp`):

1. **Obtener el nombre del servicio a partir del puerto:**
   ```bash
   grep -w "512/tcp" /etc/services
   # Devuelve el servicio: 'exec'
   ```

2. **Consultar la configuración de `inetd` / `xinetd`:**
   ```bash
   sudo grep -v '^#' /etc/inetd.conf
   # Muestra el ejecutable real: /usr/sbin/in.rexecd
   ```

3. **Localizar el paquete Debian instalado:**
   ```bash
   dpkg -S /usr/sbin/in.rexecd
   # Muestra el paquete asociado: rsh-server
   ```

#### Caso 2: El servicio no está en `inetd.conf`

1. **Buscar el nombre del servicio por su puerto:**
   ```bash
   grep -w "2049/tcp" /etc/services
   # Devuelve: 'nfs'
   ```

2. **Si no aparece en `inetd.conf`, buscar paquetes instalados relacionados:**
   ```bash
   dpkg -l | grep nfs
   ```

#### Caso 3: Servicio independiente (Sin portero `xinetd`)

1. **Para consultar la ruta del binario/programa ejecutable:**
   ```bash
   which rmiregistry
   ```

2. **Para consultar qué paquete instaló el programa:**
   ```bash
   dpkg -S /ruta/al/ejecutable
   ```

#### Caso 4: Proceso ambiguo o no identificado directamente

1. **Localizar la ruta exacta del ejecutable mediante su PID (ej. PID `4620`):**
   ```bash
   ls -l /proc/4620/exe
   ```

2. **Consultar el paquete del binario real obtenido (ejemplo: `Xtightvnc`):**
   ```bash
   dpkg -S /usr/bin/Xtightvnc
   ```

> **Nota:** Si `/proc/<PID>/exe` no devuelve nada o falla, ejecuta `netstat -antp` para verificar si el proceso se reinició y cambió de PID.

---

## 5. Apagado y Deshabilitación de Servicios

### Apagar `xinetd`

Para detener todos los servicios gestionados por el superservidor `xinetd`:
```bash
sudo /etc/init.d/xinetd stop
```

### Localizar y detener un servicio por su binario

1. **Buscar el script de arranque en `/etc/init.d/` asociado a un proceso (ej. `jsvc`):**
   ```bash
   sudo grep -rn "jsvc" /etc/init.d/
   # Ejemplo de respuesta: /etc/init.d/tomcat5.5:26:DAEMON=/usr/bin/jsvc
   ```

2. **Detener el servicio identificado:**
   ```bash
   sudo /etc/init.d/tomcat5.5 stop
   ```

3. **Evitar que vuelva a arrancar al inicio del sistema:**
   ```bash
   sudo update-rc.d -f tomcat5.5 remove
   ```

---

## 6. Firewall y Contramedidas (`iptables`)

* **Bloquear las respuestas a peticiones ICMP (Ping):**
  ```bash
  sudo iptables -A INPUT -p icmp --icmp-type echo-request -j DROP
  ```

---

## 7. Gestión de Paquetes (`apt`) y Repositorios

* **Contar paquetes actualizables omitiendo avisos:**
  ```bash
  apt list --upgradable 2>/dev/null | wc -l
  ```

* **Repositorios y Claves:**
  * Ruta del archivo de fuentes: `/etc/apt/sources.list` *(obsoleto en versiones recientes de Ubuntu)*.
  * Ruta de almacenamiento de claves GPG oficiales: `/usr/share/keyrings/` (ejemplo: `ubuntu-archive-keyring.gpg`).

* **Ver detalles de políticas e instalación de un paquete:**
  ```bash
  apt policy curl
  ```
