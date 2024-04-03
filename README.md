# dnssec

<img style="float:left" alt="Infraestructura DNS" src="https://github.com/hackingyseguridad/dnssec/blob/master/dns.png">

Simple script para hacer consultas DNS DNSSEC

# dig nist.gov @8.8.8.8 +dnssec

DNS Seguros:

DNS sobre TLS (DoT)

RFC7858 especificó DNS-over-TLS como un protocolo de seguimiento de estándares en mayo de 2016 con una asignación de puerto de 853 de IANA. Hay un trabajo activo en esta área .

# kdig nist.gov @9.9.9.9 +tls-ca +tls-host=dns.quad9.net

https://www.knot-dns.cz/docs/2.6/html/man_kdig.html

Instalar kdig; $apt-get install knot-dnsutils

https://dnslookup.org/hackingyseguridad.com/

DNS sobre HTTP (DoH)

RFC8484 especifica DNS sobre HTTPS como protocolo de seguimiento de estándares en octubre de 2018.  

Hay varias implementaciones. Con DoH es posible mezclar tráfico DNS y HTTP en la misma conexión del puerto 443 y dificultar el bloqueo del DNS cifrado. 

DNS sobre DTLS

RFC8094  especificó DNS-over-DTLS como un Estándar Experimental en febrero de 2017. Según nuestro conocimiento, no hay implementaciones de DNS-over-DTLS planificadas o en progreso.

Un problema con DNS sobre DTLS es que aún debe truncar las respuestas de DNS si el tamaño de la respuesta es demasiado grande (al igual que lo hace UDP) y, por lo tanto, no puede ser una solución independiente para la privacidad sin un mecanismo de respaldo (como DNS-over- TLS) también está disponible

DNSCrypt

DNSCrypt  es un método para autenticar las comunicaciones entre un cliente DNS y un sistema de resolución de DNS que existe desde 2011. 

Previene la falsificación de DNS. 
Utiliza firmas criptográficas para verificar que las respuestas se originen en el sistema de resolución de DNS elegido y que no se hayan manipulado (los mensajes aún se envían a través de UDP). 
Como efecto secundario, proporciona mayor privacidad porque el contenido del mensaje DNS está cifrado.  
Es una especificación abierta pero ha  no  sido estandarizado por la IETF. 
Existen múltiples implementaciones y un conjunto de servidores DNSCrypt disponibles.
OpenDNS ofrece DNSCrypt 

DNS sobre HTTPS (proxy)

Hay implementaciones disponibles (por ejemplo, de BII ) de proxies que canalizarán DNS sobre HTTPS .

Un nuevo grupo de trabajo se formó en septiembre de 2017 por el IETF: DNS-over-HTTPS (DOH)

DNS sobre QUIC

En abril de 2017, se envió un borrador al Grupo de trabajo de IETF QUIC sobre DNS sobre QUIC.

DNSCurve

DNSCurve  fue desarrollado en 2010 con el cifrado del resolutor para las comunicaciones autorizadas en mente. No fue estandarizado por el IETF.

# ATAQUES A LOS DNS

PARTE 1

1º.-	Nuevos  ataques a los DNS;  DoH y DoT, por https 
https://raw.githubusercontent.com/hackingyseguridad/dnssec/master/doh_test.sh

2º.-	Nuevos ataques: KeyTrap DNSSEC (CVE-2023-50387) permitió que un solo paquete DNS denegara el servicio al agotar los recursos de la CPU de las máquinas que ejecutaban servicios DNS validados por DNSSEC, como los proporcionados por Google y Cloudflare.
Afecta a los validadores DNS, que son los servidores encargados de verificar la autenticidad de las respuestas DNS para garantizar la seguridad e integridad del sistema de nombres de dominio. Se envia una consulta  que incluye un nombre de dominio ficticio y una gran cantidad de claves y firmas DNSSEC falsas. El validador DNS intenta verificar la respuesta a la consulta. Para ello, debe probar cada combinación posible de claves y firmas, lo que puede ser un proceso computacionalmente costoso. claves y firmas falsas en la consulta obliga al validador a consumir una gran cantidad de recursos CPU. https://kb.isc.org/docs/cve-2023-50387 . La solución es gestionar redundancia con otros servidores validadores.

3º.- 	Nuevos ataques : NSEC3-encloser DNSSEC CVE-2023-50868 ), también se presentó como un riesgo de agotamiento de la CPU. afecta las implementaciones de DNSSEC en versiones del software BIND 9.x anteriores a la 9.11.37. Una consulta puede causar un consumo excesivo de CPU en el resolvedor DNS y hacer que vaya mas lento o que se bloquee. La solución es actualizar.

4º.-	Nuevos ataues  CPE basado en botnet, por número de solicitudes. Ataque de subdominio aleatorio, ataque a los DNS autoritativos. tambien llamado ataque de bloqueo de dominio.
Ataque DNS con peticiones a dominios, subdominios/ fqdn que no existen: Una inundación de baja velocidad con 'prueba de inexistencia' puede agotar la CPU del resolver DNS, en algunas implementaciones; Paper pdf : https://arxiv.org/pdf/2403.15233.pdf

5º.- 	Ataques de inundación de paquetes a los DNS, Ataques de denegación de servicio DDoS/Dos a los DNS
https://github.com/hackingyseguridad/udpflood

PARTE 2

6º.- 	Ataque de amplificación de DNS, servicios/puertos UDP que permiten respuestas amplificadas  BAF y reflexión con suplantación de origen. Ataques DNSSec, tamaño de ventana y respuesta

7º.-	Ataque de redirección de DNS

8º.-    Ataque de túnel DNS, exfiltración de datos en pequeños fragmentos, a través en los paquetes UDP/53 de solicitudes DNS

9º.- 	Ataque de dominio fantasma

10º.- 	Ataque de suplantación de DNS




KeyTrap DNSSEC (CVE-2023-50387): Descripción del ataque
KeyTrap es un ataque de denegación de servicio (DoS) que explota una vulnerabilidad en la validación de respuestas DNSSEC. Afecta a los validadores DNS, que son los servidores encargados de verificar la autenticidad de las respuestas DNS para garantizar la seguridad e integridad del sistema de nombres de dominio.

¿Cómo funciona?

El atacante envía una consulta DNS cuidadosamente diseñada al validador. La consulta incluye un nombre de dominio ficticio y una gran cantidad de claves y firmas DNSSEC falsas.
El validador DNS intenta verificar la respuesta a la consulta. Para ello, debe probar cada combinación posible de claves y firmas, lo que puede ser un proceso computacionalmente costoso.
La gran cantidad de claves y firmas falsas en la consulta obliga al validador a consumir una gran cantidad de recursos CPU. Esto puede ralentizar significativamente el validador o incluso hacerlo inoperable, lo que efectivamente niega el servicio a los usuarios legítimos que intentan resolver nombres de dominio.
¿Qué tan grave es el ataque?

KeyTrap es un ataque grave que puede tener un impacto significativo en la disponibilidad y el rendimiento del sistema DNS. Un ataque exitoso puede:

Inhabilitar los servidores DNS, impidiendo que los usuarios accedan a sitios web y otros servicios online.
Degradar el rendimiento del DNS, lo que puede ralentizar la navegación web y otras aplicaciones que dependen del DNS.
Permitir a los atacantes redirigir el tráfico a sitios web fraudulentos, lo que puede exponer a los usuarios a malware o robo de información.
¿Qué se puede hacer para mitigar el ataque?

Los proveedores de software DNS han publicado actualizaciones para sus productos que mitigan el ataque KeyTrap. Se recomienda a los administradores de servidores DNS que apliquen estas actualizaciones lo antes posible.

Además de las actualizaciones, existen otras medidas que se pueden tomar para mitigar el ataque, como:

Deshabilitar la validación DNSSEC para los nombres de dominio que no la necesitan.
Utilizar un validador DNS con caché para reducir la carga computacional de la validación.
Implementar medidas de protección contra ataques DDoS en los servidores DNS.
Recursos adicionales:

CVE-2023-50387: KeyTrap - Extreme CPU consumption in DNSSEC validator: https://kb.isc.org/docs/cve-2023-50387
Remediating new DNSSEC resource exhaustion vulnerabilities: https://blog.cloudflare.com/remediating-new-dnssec-resource-exhaustion-vulnerabilities
KeyTrap attack can take out a DNS server: https://www.kaspersky.com/blog/keytrap-dnssec-vulnerability-dos-attack/50594/




