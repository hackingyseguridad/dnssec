# Infraestructura DNS:

El servidor de nombres de dominio (DNS) funciona traduciendo nombres de dominio fqdn (como "www.hackingyseguridad.com") en direcciones IP numéricas que las máquinas pueden entender (como "185.66.41.219") y viversa de IP a fqdn con la resolucion inversa.

La infraestructura del DNS se compone de varios elementos:
1. Servidores DNS:
IP VIP del bajanceador y servidores DNS recursivos: Servidor DNS autoritativo: Almacena la información de zona para un dominio específico, incluyendo la relación entre nombres de dominio y direcciones IP.

2. Jerarquía de nombres de dominio:
Dominio de nivel superior (TLD): Es la parte final del nombre de dominio, como ".com" o ".es". Dominio de segundo nivel (SLD): Es la parte del nombre de dominio que precede al TLD, como "hackingyseguridad" en "www.hackingyseguridad.com". Nombre de host: Es la parte del nombre de dominio que identifica un equipo específico dentro de un dominio, como "www" en "www.hackingyseguridad.com".

3. Proceso de resolución de nombres:
<img style="float:left" alt="Infraestructura DNS" src="https://github.com/hackingyseguridad/dnssec/blob/master/dns.png">
(1) Un dispositivo escribe un nombre de dominio en su navegador web, o envia una solicitud desde consola con dig, el dispositivo envía una solicitud al servidor DNS resolver con recursion (2). El servidor DNS recursivo busca la dirección IP del dominio en su granja de DNS caché (3) o contacta a traves de los DNS root (4) con el servidor DNS TLD (5) y autoritativo (6)., si hay cifrado comprueba con la  PKI (7)  El servidor DNS autoritativo responde con la dirección IP del dominio. El servidor (2) DNS resolver recursivo envía la dirección IP al dispositivo origen (1)

Las consultas y repsuestas DNS por defecto va sin cifrar, por lo que cualquiera, o si pasa por una red inseguroa, con tecnicas de MiTM Man in The Middle, puede inteceptar y espiar las consultas y respuestas, asi como modidicar las repuestas o intentar reenviar respuestas manipuladas que apunten a otro IP distinta, al utilizar protclo UDP, no esta orientado a conexion no hay un sincronismo como en TCP.  Ademas UDP, si el proveedor de internet no tiene implementadas medidas BCP31, es facil de modificar y supantar en la IP origen en las cabeceras de los paquetes UDP. El DNS autoritativo utiliza ademas de UDP,  TCP para poder gestionar respuestas de  tamaño mayor a 512 Bytes, lo que tambien ocurre con DNSSEC, donde se permite tamaños de respuestas grandes ,de 4096 Bytes,

Para evitar la suplantacion de la IP origen, la interceptación y el envenenamiento de respuestas con resputas DNS moficiadas, existen varios modos DNS con cifrado,  más seguros.

# 1.- DNS Seguros:

# DNSSEC

Simple script para hacer consultas DNS DNSSEC

dig nist.gov @8.8.8.8 +dnssec

# DNS sobre TLS (DoT)

RFC7858 especificó DNS-over-TLS como un protocolo de seguimiento de estándares en mayo de 2016 con una asignación de puerto de 853 de IANA. Hay un trabajo activo en esta área .

kdig nist.gov @9.9.9.9 +tls-ca +tls-host=dns.quad9.net

https://www.knot-dns.cz/docs/2.6/html/man_kdig.html

Instalar kdig; $apt-get install knot-dnsutils

https://dnslookup.org/hackingyseguridad.com/

# DNS sobre HTTP (DoH) 

RFC8484 especifica DNS sobre HTTPS como protocolo de seguimiento de estándares en octubre de 2018.  

Hay varias implementaciones. Con DoH es posible mezclar tráfico DNS y HTTP en la misma conexión del puerto 443 y dificultar el bloqueo del DNS cifrado. 

https://raw.githubusercontent.com/hackingyseguridad/dnssec/master/doh_test.sh

# DNS sobre DTLS

RFC8094  especificó DNS-over-DTLS como un Estándar Experimental en febrero de 2017. Según nuestro conocimiento, no hay implementaciones de DNS-over-DTLS planificadas o en progreso.

Un problema con DNS sobre DTLS es que aún debe truncar las respuestas de DNS si el tamaño de la respuesta es demasiado grande (al igual que lo hace UDP) y, por lo tanto, no puede ser una solución independiente para la privacidad sin un mecanismo de respaldo (como DNS-over- TLS) también está disponible

# DNSCrypt

DNSCrypt  es un método para autenticar las comunicaciones entre un cliente DNS y un sistema de resolución de DNS que existe desde 2011. 

Previene la falsificación de DNS. 
Utiliza firmas criptográficas para verificar que las respuestas se originen en el sistema de resolución de DNS elegido y que no se hayan manipulado (los mensajes aún se envían a través de UDP). 
Como efecto secundario, proporciona mayor privacidad porque el contenido del mensaje DNS está cifrado.  
Es una especificación abierta pero ha  no  sido estandarizado por la IETF. 
Existen múltiples implementaciones y un conjunto de servidores DNSCrypt disponibles.
OpenDNS ofrece DNSCrypt 

# DNS sobre HTTPS (proxy)

Hay implementaciones disponibles (por ejemplo, de BII ) de proxies que canalizarán DNS sobre HTTPS .

Un nuevo grupo de trabajo se formó en septiembre de 2017 por el IETF: DNS-over-HTTPS (DOH)

# DNS sobre QUIC

En abril de 2017, se envió un borrador al Grupo de trabajo de IETF QUIC sobre DNS sobre QUIC.

# DNSCurve

DNSCurve  fue desarrollado en 2010 con el cifrado del resolutor para las comunicaciones autorizadas en mente. No fue estandarizado por el IETF.

# 2.- ATAQUES A LOS DNS

PARTE 1

1º.-	Nuevos  ataques a los DNS;  DoH y DoT, por https son las mismas que a las API
https://raw.githubusercontent.com/hackingyseguridad/dnssec/master/doh_test.sh

2º.-	Nuevos ataques: KeyTrap DNSSEC (CVE-2023-50387) permitió que un solo paquete DNS denegara el servicio al agotar los recursos de la CPU de las máquinas que ejecutaban servicios DNS validados por DNSSEC, como los proporcionados por Google y Cloudflare.
Afecta a los validadores DNS, que son los servidores encargados de verificar la autenticidad de las respuestas DNS para garantizar la seguridad e integridad del sistema de nombres de dominio. Se envia una consulta  que incluye un nombre de dominio ficticio y una gran cantidad de claves y firmas DNSSEC falsas. El validador DNS intenta verificar la respuesta a la consulta. Para ello, debe probar cada combinación posible de claves y firmas, lo que puede ser un proceso computacionalmente costoso. claves y firmas falsas en la consulta obliga al validador a consumir una gran cantidad de recursos CPU. https://kb.isc.org/docs/cve-2023-50387 . La solución es gestionar redundancia con otros servidores validadores.

3º.- 	Nuevos ataques : NSEC3-encloser DNSSEC CVE-2023-50868 ), también se presentó como un riesgo de agotamiento de la CPU. afecta las implementaciones de DNSSEC en versiones del software BIND 9.x anteriores a la 9.11.37. Una consulta puede causar un consumo excesivo de CPU en el resolvedor DNS y hacer que vaya mas lento o que se bloquee. La solución es actualizar.

4º.-	Nuevos ataues  CPE basado en botnet, por número de solicitudes. Ataque de subdominio aleatorio, ataque a los DNS autoritativos. tambien llamado ataque de bloqueo de dominio.  https://github.com/hackingyseguridad/watertorture son ataques que consisten en enviar consultas a DNS con peticiones de dominios, subdominios/ fqdn que no existen: Una inundación de baja velocidad con 'prueba de inexistencia' puede agotar la CPU del resolver DNS, en algunas implementaciones; Paper pdf : https://arxiv.org/pdf/2403.15233.pdf

5º.- 	Ataques de inundación de paquetes a los DNS, Ataques de denegación de servicio DDoS/Dos a los DNS
https://github.com/hackingyseguridad/udpflood

PARTE 2

6º.- 	Ataque de amplificación de DNS, servicios/puertos UDP que permiten respuestas amplificadas  BAF y reflexión con suplantación de origen. Ataques DNSSec, tamaño de ventana y respuesta

<img style="float:left" alt="Ataque DDoS DNS reflexion y amplificacion" src="https://github.com/hackingyseguridad/dnssec/blob/master/amplificacion.png">

7º.-	Ataque de redirección de DNS

8º.-    Ataque de túnel DNS, exfiltración de datos en pequeños fragmentos, a través en los paquetes UDP/53 de solicitudes DNS

9º.- 	Ataque de dominio fantasma

10º.- 	Ataque de suplantación de DNS




