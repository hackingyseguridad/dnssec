# dnssec

dig nist.gov @8.8.8.8 +dnssec

; <<>> DiG 9.11.5-P1-1-Debian <<>> nist.gov @8.8.4.4 +dnssec
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 47833
;; flags: qr rd ra ad; QUERY: 1, ANSWER: 3, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags: do; udp: 512
;; QUESTION SECTION:
;nist.gov.                      IN      A

;; ANSWER SECTION:
nist.gov.               569     IN      A       129.6.13.49
nist.gov.               569     IN      RRSIG   A 7 2 1800 20190113200434 20190106191750 14375 nist.gov. k+Qik5oNcATx1Y3v1IuHW6OR6ELqCbgdG29D18zZnv0Z+nOLqGSG8kZ3 SQapwbfgo71Dx9pks7oYEhP+gY45IPG1Fm183YvcQA4aV+mZzpo7fxBb ZGklFP4xA45bR3RhN1VJPPK9U1+76zD1RcpbQayeW1vJ2odq/kA+6rmi whw=
nist.gov.               569     IN      RRSIG   A 7 2 1800 20190113200434 20190106191750 23480 nist.gov. xGQlKPHmODPhWZKi/vnCZwEop/w8TwulkiO9Eyt9aBNjMKX4qAUk4PaJ RZGhUSA6Cjk8NvDcjbvqpIAUcgsB1ETvG0VQ0/nGaT+fCAVmmuGnf3q7 wCypnsPf4USe8kfbYYE0ILWNVpIIlOol2c9VsxTnZqDrunUxHXkkGsbX h5o=

;; Query time: 3 msec
;; SERVER: 8.8.4.4#53(8.8.4.4)
;; WHEN: lun ene 07 00:45:32 CET 2019
;; MSG SIZE  rcvd: 389


kdig nist.gov @8.8.8.8.8 +tls-ca +tls-host=dns.quad9.net
