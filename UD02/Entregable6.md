A1. Anotad qué versión de sistema operativo y kernel tenéis
* versión del sistema: 24.04
* kernel: 7.0.0-31-generic


A2. Pedid al sistema la lista de paquetes disponibles y qué se puede actualizar:  
  <img width="966" height="530" alt="image" src="https://github.com/user-attachments/assets/04f653aa-0c83-4af5-8fd0-972fa84dd240" />

* son 226 paquetes pendientes por instalar

  A3. Simulad la actualización sin aplicarla:
  
  A4. ¿Y si el sistema ya no recibe parches?
| Sistema| 	Fecha de fin de soporte | ¿Recibe parches hoy?|
  | :--- | :--- | :--- |
  |Torrent-Auditor| Ends in 2 years and 7 months(31 May 2029) | Si|
   |Torrent-Vulnerable (Ubuntu 8.04)|(09 May 2013) | No |


 Pregunta A: En la oferta de empleo de Torrent (dossier de la UD02) decían que trabajaban con "Ubuntu 8.04". Aunque pusieran todas las contraseñas bien, ¿qué problema sigue sin tener solución?
* El problema siempre será de que el SO está obsoleto desde el 2013. Las vulnerabilidades e el kernel, software serán imposibles de controlar.
  
Investiga el fichero donde están configurados los repositorios
<img width="747" height="362" alt="image" src="https://github.com/user-attachments/assets/172d81bc-ced4-423f-a80d-5cb8aa638169" />

* Types: deb ---> Indica que el repositorio proporciona paquetes binarios precompilados listos para descargar e instalar directamente.
* URIs: [http://archive.ubuntu.com/ubuntu/](http://archive.ubuntu.com/ubuntu/)   ---> es la dirección del servidor espejo, el oficial de ubuntu en donde descargan los paquetes de software.
* Suites: noble noble-updates noble-security   ---> Indica las ramas o versiones específicas de la distribución que se van a consultar:
  * noble: La versión base de Ubuntu 24.04.
  * noble-updates: Los paquetes que han recibido actualizaciones de mantenimiento o correcciones posteriores al lanzamiento.
  * noble-security: Los parches críticos de seguridad publicados para proteger el sistema.
* Components: main restricted universe multiverse ---> Define las categorías de software a las que se permite acceder:
  * main -->  Software oficial libre y gratuito soportado por Canonical.
  * restricted  -->  Controladores privativos para hardware (como gráficas o Wi-Fi).
  * universe --> Software de código abierto mantenido por la comunidad.
  * multiverse -->  Software con restricciones legales o de licencias.
* Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg   ---> es la ruta a la clave criptográfica oficial que el sistema utiliza para verificar y garantizar los paquetes descarhgados son auténticos, legítimos y que no han sido manipulados.

B2. apt solo instala paquetes firmados por claves en las que confía. Mirad dónde están esas claves:
ls /etc/apt/trusted.gpg.d/ /usr/share/keyrings/ 2>/dev/null
*  ¿qué pasaría si un atacante introduce una clave falsa en /usr/share/keyrings/?
  Eso se consigue con sudo, tendría que tener esos permisos de administrador, por lo que si añade una clave falsa y lo asocia a un repositorio malvado controlado por él, lo que haría es firmar los paquetes malvados engañando a apt que son actualizaciones oficiales, al ejecutar una actualización, esta se instalará  el software malvado sin que te salen alertas o alarmas de seguridad.
*  ¿y si intercepta o reemplaza la clave oficial de Ubuntu ubuntu-archive-keyrings.gpg?
* El sistema te bloqueará las actualizaciones oficiales, cuando apt intente descargar los parches reales de los servidores oficiales de ubuntu, esta comprobará la firma con la clave falsa, y al no coincidir, las bloqueará todas esas actualizaciones de seguridad.

 B3. Información de un paquete concreto.
 curl
 * versión instalada: none
 * versión candidata: 8.5.0-2ubuntu10.16
 * repositorio de origen: http://es.archive.ubuntu.com/ubuntu   http://es.archive.ubuntu.com/ubuntu noble-updates/main
 *de esas dos direcciones se obtienen los paquetes. 
  
  
