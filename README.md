# dnssec

# dig nist.gov @8.8.8.8 +dnssec

# kdig nist.gov @9.9.9.9 +tls-ca +tls-host=dns.quad9.net

https://www.knot-dns.cz/docs/2.6/html/man_kdig.html

Instalar kdig; $apt-get install knot-dnsutils

https://dnslookup.org/hackingyseguridad.com/


DNS Seguros:

DNS sobre TLS (DoT)

RFC7858 especificó DNS-over-TLS como un protocolo de seguimiento de estándares en mayo de 2016 con una asignación de puerto de 853 de IANA. Hay un trabajo activo en esta área .

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
