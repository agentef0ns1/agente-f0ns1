# Creating the Dataset

A good dataset is essential for training or guiding an autonomous agent.

---

## Types of Data

Useful data includes:

- Prompt → Command pairs  
- Prompt → Multi‑step plans  
- Error handling examples  
- Reporting and summarization examples  

---

## Dataset Format

A common format is **JSONL** (one JSON object per line):
```json
{"prompt": "Scan target 10.0.0.5", "response": "nmap -sV 10.0.0.5"}
{"prompt": "Enumerate HTTP headers on 10.0.0.5:80", "response": "curl -I http://10.0.0.5"}
```
Remember, you can create the dataset manually or you can ask an LLM to generate it for you with different iterations, flags, and types of cases to enumerate or analyze, based on categories:
- Aggressive
- Silent
- Fast
- Complete
- Using scripts
- To specific ports
- Analyzing vulnerabilities, etc.

**Keep in mind that this will be the knowledge of your model; the richer it is, the better results will be obtained after training.**

I'm going to provided a custom dataset for the course, bust you should improve it for better results:

```json
{"instruction": "Ejecuta escaneo agresivo autorizado en <ip>", "output": "nmap -A <ip>"}
{"instruction": "Define timing T4 en escaneo de <ip>", "output": "nmap -T4 <ip>"}
{"instruction": "Ejecuta escaneo agresivo autorizado en <ip>", "output": "nmap -A <ip>"}
{"instruction": "Escaneo rápido con detección de versión en <ip>", "output": "nmap -F -sV <ip>"}
{"instruction": "Realiza escaneo SYN con detección de versión y OS en <ip> usando timing T4", "output": "nmap -sS -sV -O -T4 <ip>"}
{"instruction": "Escaneo agresivo sin resolución DNS guardando salida completa en <ip>", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -A -n -oA reporte_30_1 <ip>"}
{"instruction": "Escaneo rápido detectando versión y mostrando solo abiertos en <ip>", "output": "nmap -F -sV --open <ip>"}
{"instruction": "Escaneo agresivo sin ping previo en entorno de laboratorio", "output": "nmap -A -Pn <ip>"}
{"instruction": "Escaneo rápido sin ping y sin resolución DNS", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -F -Pn -n <ip>"}
{"instruction": "Escaneo SYN rápido con min-rate 3500 y sin ping", "output": "nmap -sS --min-rate 3500 -Pn <ip>"}
{"instruction": "Realiza un escaneo agresivo en entorno de laboratorio autorizado", "output": "nmap -A <ip>"}
{"instruction": "Escaneo agresivo sin DNS y sin ping previo", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -A -n -Pn <ip>"}
{"instruction": "Escaneo agresivo con salida en todos los formatos", "output": "nmap -A -oA reporte_agresivo_41_21 <ip>"}
{"instruction": "Escaneo agresivo con límite de reintentos a 2 y host-timeout 60s", "output": "nmap -A --max-retries 2 --host-timeout 60s <ip>"}
{"instruction": "Escaneo agresivo sin DNS y guardando salida XML", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -A -n -oX agresivo_41_24.xml <ip>"}
{"instruction": "Escaneo agresivo con scripts safe adicionales", "output": "nmap -A --script=safe <ip>"}
{"instruction": "Escaneo agresivo con min-rate 2500 en entorno controlado", "output": "nmap -A --min-rate 2500 <ip>"}
{"instruction": "Escaneo agresivo con timing T4 y sin resolución DNS", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -A -T4 -n <ip>"}
{"instruction": "Escaneo IPv6 rápido con min-rate 3000", "output": "nmap -6 --min-rate 3000 2001:db8:40::4"}
{"instruction": "Realiza escaneo rápido de puertos comunes en <ip>", "output": "nmap -F <ip>"}
{"instruction": "Realiza escaneo rápido de puertos comunes en <ip>", "output": "nmap -F <ip>"}
{"instruction": "Ejecuta un escaneo TCP rápido con min-rate 3000 y mostrando solo puertos abiertos", "output": "nmap -sT --min-rate 3000 --open <ip>"}
{"instruction": "Escaneo rápido de puertos comunes con detección de versión y sin DNS", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -F -sV -n <ip>"}
{"instruction": "Haz un escaneo rápido de los puertos más comunes en este servidor de laboratorio", "output": "nmap -F <ip>"}
{"instruction": "Ejecuta un escaneo SYN con timing T4 y mostrando solo puertos abiertos", "output": "nmap -sS -T4 --open <ip>"}
{"instruction": "Escaneo UDP rápido con min-rate 2000 sobre los puertos 53 y 123", "output": "nmap -sU --min-rate 2000 -p 53,123 <ip>"}
{"instruction": "Escaneo SYN rápido sin DNS y mostrando solo puertos abiertos", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -sS -T4 -n --open <ip>"}
{"instruction": "Escaneo rápido de puertos comunes sin DNS en entorno de pruebas", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -F -n <ip>"}
{"instruction": "Escaneo rápido de puertos comunes forzando resolución DNS", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -F -R <ip>"}
{"instruction": "Ejecuta un escaneo rápido con min-rate 3000 sobre puertos TCP comunes", "output": "nmap -sT -F --min-rate 3000 <ip>"}
{"instruction": "Escaneo agresivo sin ping y mostrando solo puertos abiertos", "output": "nmap -A -Pn --open <ip>"}
{"instruction": "Escaneo del puerto 21 con scripts ftp-anon y ftp-banner", "output": "nmap --script=ftp-anon,ftp-banner,ftp-syst,ftp-vuln-cve2010-4221,ftp-anon,ftp-banner -p 21 <ip>"}
{"instruction": "Escaneo rápido con detección de versión y mostrando solo puertos abiertos", "output": "nmap -F -sV --open <ip>"}
{"instruction": "Escaneo todos los puertos con timing T5 y límite de reintentos en <ip>", "output": "nmap -p- -T5 --max-retries 1 <ip>"}
{"instruction": "Escaneo TCP completo con timing T5 y límite de reintentos 1", "output": "nmap -sT -p- -T5 --max-retries 1 <ip>"}
{"instruction": "Escanea subred <ip>/<mask> con timing T4 y salida en todos los formatos", "output": "nmap -T4 -oA reporte_subred_30 <ip>"}
{"instruction": "Escaneo agresivo excluyendo host <ip> en subred <ip>/<mask>", "output": "nmap -A <ip> --exclude <ip>"}
{"instruction": "Escaneo rápido de subred con min-rate 6000 para descubrimiento de hosts", "output": "nmap --script=discovery,default -sn --min-rate 6000 <ip>"}
{"instruction": "Escaneo agresivo sobre subred de laboratorio <ip>/<mask>", "output": "nmap -A <ip>"}
{"instruction": "Escaneo SYN sobre subred <ip>/<mask> con timing T4", "output": "nmap -sS -T4 <ip>"}
{"instruction": "Escaneo rápido de subred con min-rate 5000", "output": "nmap -sn --min-rate 5000 <ip>"}
{"instruction": "Detecta versiones de servicios en <ip>", "output": "nmap -sV <ip>"}
{"instruction": "Ejecuta scripts NSE por defecto en <ip>", "output": "nmap -sC <ip>"}
{"instruction": "Ejecuta scripts safe NSE en <ip>", "output": "nmap --script=safe <ip>"}
{"instruction": "Detecta versiones de servicios en <ip>", "output": "nmap -sV <ip>"}
{"instruction": "Ejecuta scripts NSE por defecto en <ip>", "output": "nmap -sC <ip>"}
{"instruction": "Ejecuta scripts safe NSE en <ip>", "output": "nmap --script=safe <ip>"}
{"instruction": "Enumera servicios y versiones en el host autorizado <ip>", "output": "nmap --script=discovery,default -sV <ip>"}
{"instruction": "Enumera servicios en <ip> desactivando resolución DNS", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -sV -n <ip>"}
{"instruction": "Enumera servicios en <ip> forzando resolución DNS inversa", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -sV -R <ip>"}
{"instruction": "Ejecuta scripts safe y detecta servicios en <ip>", "output": "nmap -sV --script=safe <ip>"}
{"instruction": "Ejecuta scripts por defecto junto con detección de versión en <ip>", "output": "nmap -sC -sV <ip>"}
{"instruction": "Detecta sistema operativo y servicios en <ip> sin DNS", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -O -sV -n <ip>"}
{"instruction": "Escanea hosts definidos en archivo inventario.txt detectando servicios", "output": "nmap -sV -iL inventario.txt"}
{"instruction": "Ejecuta scripts NSE categoría default en <ip>", "output": "nmap --script=default <ip>"}
{"instruction": "Ejecuta scripts de categoría discovery en <ip>", "output": "nmap --script=discovery <ip>"}
{"instruction": "Ejecuta scripts default y safe junto con detección de versión en <ip>", "output": "nmap -sV --script=default,safe <ip>"}
{"instruction": "Escaneo IPv6 detectando versión y guardando salida XML", "output": "nmap -6 -sV -oX ipv6_30.xml 2001:db8::30"}
{"instruction": "Escanea hosts definidos en archivo activos.txt detectando servicios y sin DNS", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -sV -n -iL activos.txt"}
{"instruction": "Escaneo SYN detectando versión y limitando RTT máximo en <ip>", "output": "nmap -sS -sV --max-rtt-timeout 150ms <ip>"}
{"instruction": "Escaneo TCP connect detectando versión y ejecutando scripts safe en <ip>", "output": "nmap -sT -sV --script=safe <ip>"}
{"instruction": "Escaneo completo detectando OS, versión y ejecutando scripts discovery sin DNS", "output": "nmap -O -sV --script=dns-recursion,dns-nsid,dns-brute,discovery -n <ip>"}
{"instruction": "Escaneo sin ping detectando versiones y ejecutando scripts safe", "output": "nmap -Pn -sV --script=safe <ip>"}
{"instruction": "Escaneo sin ping con scripts default y min-rate 2500", "output": "nmap -Pn --script=default --min-rate 2500 <ip>"}
{"instruction": "Escaneo UDP completo detectando versiones y sin resolución DNS", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -sU -sV -n <ip>"}
{"instruction": "Ejecuta scripts NSE por defecto y detección de versión en este host", "output": "nmap -sV -sC <ip>"}
{"instruction": "Lanza scripts de categoría safe junto con detección de versión", "output": "nmap -sV --script=safe <ip>"}
{"instruction": "Lanza scripts de categoría discovery y detección de versión sin usar DNS", "output": "nmap -sV --script=dns-recursion,dns-nsid,dns-brute,discovery -n <ip>"}
{"instruction": "Ejecuta scripts default y safe simultáneamente con detección de versión", "output": "nmap -sV --script=default,safe <ip>"}
{"instruction": "Escaneo con scripts default sin resolución DNS", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute,default -n <ip>"}
{"instruction": "Escaneo con scripts DNS y detección de versión forzando DNS", "output": "nmap -sV --script=dns-recursion,dns-nsid,dns-brute,dns-* -R <ip>"}
{"instruction": "Escaneo sin ping con scripts default y detección de versión", "output": "nmap -sV -sC -Pn <ip>"}
{"instruction": "Escaneo con scripts default y min-rate 2500", "output": "nmap --script=default --min-rate 2500 <ip>"}
{"instruction": "Escaneo IPv6 con scripts default y salida XML", "output": "nmap -6 --script=default -oX ipv6_default_40.xml 2001:db8:40::3"}
{"instruction": "Escaneo IPv6 con scripts safe en entorno de laboratorio", "output": "nmap -6 --script=safe 2001:db8:40::8"}
{"instruction": "Escaneo TCP connect con scripts safe y sin ping", "output": "nmap -sT --script=safe -Pn <ip>"}
{"instruction": "Escaneo SYN con scripts discovery y sin resolución DNS", "output": "nmap -sS --script=dns-recursion,dns-nsid,dns-brute,discovery -n <ip>"}
{"instruction": "Escaneo UDP sin ping con scripts default y salida normal en archivo", "output": "nmap -sU -Pn --script=default -oN udp_default_52_10.txt <ip>"}
{"instruction": "Detecta versión detallada de servicio SSH en <ip> puerto 2222", "output": "nmap --script=ssh-hostkey,ssh-auth-methods,ssh2-enum-algos -sV -p 2222 <ip>"}
{"instruction": "Ejecuta script NSE http-title en servidor web <ip>", "output": "nmap --script=http-title,http-enum,http-headers,http-methods,http-robots.txt,http-title -p 80 <ip>"}
{"instruction": "Ejecuta scripts NSE relacionados con HTTP en <ip>", "output": "nmap --script=http-title,http-enum,http-headers,http-methods,http-robots.txt,http-* -p 80 <ip>"}
{"instruction": "Ejecuta script smb-os-discovery en servidor SMB <ip>", "output": "nmap --script=smb-os-discovery,smb-enum-shares,smb-enum-users,smb-vuln-ms17-010,smb-os-discovery -p 445 <ip>"}
{"instruction": "Enumera recursos compartidos SMB en <ip>", "output": "nmap --script=smb-os-discovery,smb-enum-shares,smb-enum-users,smb-vuln-ms17-010,smb-enum-shares -p 445 <ip>"}
{"instruction": "Enumera banner FTP en <ip>", "output": "nmap --script=ftp-anon,ftp-banner,ftp-syst,ftp-vuln-cve2010-4221,ftp-banner -p 21 <ip>"}
{"instruction": "Ejecuta script dns-recursion en servidor DNS <ip>", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute,dns-recursion -p 53 <ip>"}
{"instruction": "Enumera servicio MySQL en puerto 3306 del host <ip>", "output": "nmap --script=mysql-info,mysql-enum,mysql-databases,mysql-users -sV -p 3306 <ip>"}
{"instruction": "Enumera servicio PostgreSQL en puerto 5432 en <ip>", "output": "nmap --script=discovery,default -sV -p 5432 <ip>"}
{"instruction": "Ejecuta script mysql-info en servidor MySQL <ip>", "output": "nmap --script=mysql-info,mysql-enum,mysql-databases,mysql-users,mysql-info -p 3306 <ip>"}
{"instruction": "Ejecuta script ssh-hostkey en servidor SSH <ip>", "output": "nmap --script=ssh-hostkey,ssh-auth-methods,ssh2-enum-algos,ssh-hostkey -p 22 <ip>"}
{"instruction": "Detecta servicios en puertos 8000 al 8100 en <ip>", "output": "nmap -sV -p 8000-8100 <ip>"}
{"instruction": "Enumera servicios web en puerto 8080 ejecutando scripts http y detección de versión en <ip>", "output": "nmap -sV --script=http-title,http-enum,http-headers,http-methods,http-robots.txt,http-* -p 8080 <ip>"}
{"instruction": "Escanea puertos 21,22,25,80 detectando versiones y mostrando solo abiertos en <ip>", "output": "nmap -sV -p 21,22,25,80 --open <ip>"}
{"instruction": "Escanea UDP puerto 53 ejecutando script dns-recursion en <ip>", "output": "nmap -sU -p 53 --script=dns-recursion,dns-nsid,dns-brute,dns-recursion <ip>"}
{"instruction": "Escanea servicio SMB puerto 445 detectando versión y ejecutando smb-enum-shares en <ip>", "output": "nmap -sV -p 445 --script=smb-os-discovery,smb-enum-shares,smb-enum-users,smb-vuln-ms17-010,smb-enum-shares <ip>"}
{"instruction": "Escanea MySQL puerto 3306 con script mysql-info y salida normal en archivo", "output": "nmap -p 3306 --script=mysql-info,mysql-enum,mysql-databases,mysql-users,mysql-info -oN mysql_30_2.txt <ip>"}
{"instruction": "Escaneo UDP puertos 161 y 162 detectando versión en <ip>", "output": "nmap -sU -sV -p 161,162 <ip>"}
{"instruction": "Ejecuta detección de versión y script http-headers en el puerto 8081 sin usar DNS", "output": "nmap -sV --script=http-title,http-enum,http-headers,http-methods,http-robots.txt,http-headers -p 8081 -n <ip>"}
{"instruction": "Escaneo UDP específico al puerto 500 con script ike-version", "output": "nmap -sU -p 500 --script=ike-version <ip>"}
{"instruction": "Ejecuta scripts DNS y detección de versión en el puerto 53 forzando resolución DNS", "output": "nmap -sV --script=dns-recursion,dns-nsid,dns-brute,dns-* -p 53 -R <ip>"}
{"instruction": "Ejecuta script smb-enum-users en el puerto 445 sin realizar descubrimiento previo", "output": "nmap -Pn --script=smb-os-discovery,smb-enum-shares,smb-enum-users,smb-vuln-ms17-010,smb-enum-users -p 445 <ip>"}
{"instruction": "Escaneo TCP a puertos 8080-8090 con script http-title y timing T3", "output": "nmap -sT -p 8080-8090 --script=http-title,http-enum,http-headers,http-methods,http-robots.txt,http-title -T3 <ip>"}
{"instruction": "Ejecuta scripts http-* sobre el puerto 80 con detección de versión", "output": "nmap -sV --script=http-title,http-enum,http-headers,http-methods,http-robots.txt,http-* -p 80 <ip>"}
{"instruction": "Escaneo del puerto 445 con script smb-os-discovery", "output": "nmap --script=smb-os-discovery,smb-enum-shares,smb-enum-users,smb-vuln-ms17-010,smb-os-discovery -p 445 <ip>"}
{"instruction": "Enumera recursos compartidos SMB usando script smb-enum-shares", "output": "nmap --script=smb-os-discovery,smb-enum-shares,smb-enum-users,smb-vuln-ms17-010,smb-enum-shares -p 445 <ip>"}
{"instruction": "Ejecuta script ssh-hostkey en el puerto 22 con detección de versión", "output": "nmap -sV --script=ssh-hostkey,ssh-auth-methods,ssh2-enum-algos,ssh-hostkey -p 22 <ip>"}
{"instruction": "Escaneo del puerto 21 con script ftp-banner en entorno de laboratorio", "output": "nmap --script=ftp-anon,ftp-banner,ftp-syst,ftp-vuln-cve2010-4221,ftp-banner -p 21 <ip>"}
{"instruction": "Escaneo con scripts NSE http-title y http-headers sobre el puerto 8080", "output": "nmap --script=http-title,http-enum,http-headers,http-methods,http-robots.txt,http-title,http-headers -p 8080 <ip>"}
{"instruction": "Escaneo del puerto 53 UDP con script dns-recursion y DNS inverso", "output": "nmap -sU --script=dns-recursion,dns-nsid,dns-brute,dns-recursion -p 53 -R <ip>"}
{"instruction": "Escaneo del puerto 3306 con script mysql-info y detección de versión", "output": "nmap -sV --script=mysql-info,mysql-enum,mysql-databases,mysql-users,mysql-info -p 3306 <ip>"}
{"instruction": "Escaneo del puerto 22 con script ssh-hostkey y sin DNS", "output": "nmap --script=ssh-hostkey,ssh-auth-methods,ssh2-enum-algos,ssh-hostkey -p 22 -n <ip>"}
{"instruction": "Escaneo del puerto 80 con scripts http-title y http-server-header", "output": "nmap --script=http-title,http-enum,http-headers,http-methods,http-robots.txt,http-title,http-server-header -p 80 <ip>"}
{"instruction": "Escaneo del puerto 445 con scripts smb-enum-shares y smb-os-discovery", "output": "nmap --script=smb-os-discovery,smb-enum-shares,smb-enum-users,smb-vuln-ms17-010,smb-enum-shares,smb-os-discovery -p 445 <ip>"}
{"instruction": "Escaneo del puerto 25 con scripts smtp-commands y detección de versión", "output": "nmap -sV --script=smtp-commands,smtp-enum-users,smtp-ntlm-info,smtp-commands -p 25 <ip>"}
{"instruction": "Escaneo del puerto 110 con script pop3-capabilities", "output": "nmap --script=pop3-capabilities -p 110 <ip>"}
{"instruction": "Escaneo del puerto 143 con script imap-capabilities", "output": "nmap --script=imap-capabilities -p 143 <ip>"}
{"instruction": "Escaneo del puerto 389 con script ldap-rootdse", "output": "nmap --script=ldap-rootdse,ldap-search,ldap-rootdse -p 389 <ip>"}
{"instruction": "Escanea todos los puertos TCP y detecta versiones en <ip>", "output": "nmap -p- -sV <ip>"}
{"instruction": "Escanea todos los puertos TCP detectando versión y guardando salida XML en <ip>", "output": "nmap -p- -sV -oX reporte_30_1.xml <ip>"}
{"instruction": "Escanea todos los puertos TCP con detección OS, versión y scripts default en <ip>", "output": "nmap -p- -O -sV -sC <ip>"}
{"instruction": "Escaneo TCP completo con detección de OS, servicios y scripts default", "output": "nmap -p- -O -sV -sC <ip>"}
{"instruction": "Escanea subred interna <ip>/<mask> detectando versiones", "output": "nmap -sV <ip>"}
{"instruction": "Escanea subred <ip>/<mask> detectando versiones sin DNS", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -sV -n <ip>"}
{"instruction": "Ejecuta scripts de categoría discovery y detecta versiones en subred sin DNS", "output": "nmap -sV --script=dns-recursion,dns-nsid,dns-brute,discovery -n <ip>"}
{"instruction": "Escaneo de subred <ip>/<mask> detectando versiones de servicios", "output": "nmap -sV <ip>"}
{"instruction": "Escaneo de subred con scripts discovery y sin DNS", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute,discovery -n <ip>"}
{"instruction": "Realiza un escaneo básico autorizado sobre el host <ip>", "output": "nmap <ip>"}
{"instruction": "Ejecuta un escaneo SYN en laboratorio contra <ip>", "output": "nmap -sS <ip>"}
{"instruction": "Realiza un escaneo TCP connect en <ip> sin privilegios root", "output": "nmap -sT <ip>"}
{"instruction": "Escanea servicios UDP en el servidor <ip>", "output": "nmap -sU <ip>"}
{"instruction": "Identifica el sistema operativo del host <ip>", "output": "nmap -O <ip>"}
{"instruction": "Desactiva resolución DNS durante escaneo en <ip>", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -n <ip>"}
{"instruction": "Activa verbosidad en escaneo del host <ip>", "output": "nmap -v <ip>"}
{"instruction": "Guarda resultados normales del escaneo <ip>", "output": "nmap -oN resultado_10_1.txt <ip>"}
{"instruction": "Guarda resultados XML del host <ip>", "output": "nmap -oX resultado_10_1.xml <ip>"}
{"instruction": "Limita reintentos a 2 en <ip>", "output": "nmap --max-retries 2 <ip>"}
{"instruction": "Escanea ignorando descubrimiento previo en <ip>", "output": "nmap --script=discovery,default -Pn <ip>"}
{"instruction": "Realiza un escaneo básico autorizado sobre el host <ip>", "output": "nmap <ip>"}
{"instruction": "Ejecuta un escaneo SYN en laboratorio contra <ip>", "output": "nmap -sS <ip>"}
{"instruction": "Realiza un escaneo TCP connect en <ip> sin privilegios root", "output": "nmap -sT <ip>"}
{"instruction": "Escanea servicios UDP en el servidor <ip>", "output": "nmap -sU <ip>"}
{"instruction": "Identifica el sistema operativo del host <ip>", "output": "nmap -O <ip>"}
{"instruction": "Desactiva resolución DNS durante escaneo en <ip>", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -n <ip>"}
{"instruction": "Activa verbosidad en escaneo del host <ip>", "output": "nmap -v <ip>"}
{"instruction": "Guarda resultados normales del escaneo <ip>", "output": "nmap -oN resultado_10_2.txt <ip>"}
{"instruction": "Guarda resultados XML del host <ip>", "output": "nmap -oX resultado_10_2.xml <ip>"}
{"instruction": "Define timing T3 en escaneo de <ip>", "output": "nmap -T3 <ip>"}
{"instruction": "Limita reintentos a 3 en <ip>", "output": "nmap --max-retries 3 <ip>"}
{"instruction": "Escanea ignorando descubrimiento previo en <ip>", "output": "nmap --script=discovery,default -Pn <ip>"}
{"instruction": "Guarda resultados completos en todos los formatos para <ip>", "output": "nmap -oA reporte_20_1 <ip>"}
{"instruction": "Escaneo FIN en entorno autorizado <ip>", "output": "nmap -sF <ip>"}
{"instruction": "Escaneo NULL en entorno controlado <ip>", "output": "nmap -sN <ip>"}
{"instruction": "Escaneo Xmas en laboratorio <ip>", "output": "nmap -sX <ip>"}
{"instruction": "Define tiempo máximo por host 60 segundos en <ip>", "output": "nmap --host-timeout 60s <ip>"}
{"instruction": "Limita RTT máximo a 200ms en <ip>", "output": "nmap --max-rtt-timeout 200ms <ip>"}
{"instruction": "Define interfaz eth0 para escaneo <ip>", "output": "nmap -e eth0 <ip>"}
{"instruction": "Escanea IPv6 autorizado 2001:db8::20", "output": "nmap -6 2001:db8::20"}
{"instruction": "Activa depuración nivel 1 en <ip>", "output": "nmap -d <ip>"}
{"instruction": "Escaneo SYN con límite de reintentos y host-timeout en <ip>", "output": "nmap -sS --max-retries 2 --host-timeout 45s <ip>"}
{"instruction": "Escaneo FIN con detección de versión en laboratorio <ip>", "output": "nmap -sF -sV <ip>"}
{"instruction": "Escaneo NULL con resolución DNS activada en <ip>", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -sN -R <ip>"}
{"instruction": "Realiza un escaneo UDP básico sobre este host de laboratorio", "output": "nmap -sU <ip>"}
{"instruction": "Escaneo UDP sin ping previo y sin resolución DNS", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -sU -Pn -n <ip>"}
{"instruction": "Escaneo UDP con timing T2 y sin resolución DNS en entorno de pruebas", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -sU -T2 -n <ip>"}
{"instruction": "Escaneo con detección de OS y servicios desactivando resolución DNS", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -O -sV -n <ip>"}
{"instruction": "Escaneo TCP connect sin DNS y sin ping previo", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -sT -n -Pn <ip>"}
{"instruction": "Realiza un escaneo forzando resolución DNS inversa sobre este host", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -R <ip>"}
{"instruction": "Escaneo SYN con detección de versión y DNS inverso activado", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -sS -sV -R <ip>"}
{"instruction": "Escaneo TCP connect con detección de versión y DNS inverso", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -sT -sV -R <ip>"}
{"instruction": "Escaneo con detección de OS y servicios forzando resolución DNS", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -O -sV -R <ip>"}
{"instruction": "Realiza un escaneo sin ping previo sobre este host de laboratorio", "output": "nmap -Pn <ip>"}
{"instruction": "Escaneo SYN sin ping y con detección de versión", "output": "nmap -sS -sV -Pn <ip>"}
{"instruction": "Escaneo SYN sin ping con límite de reintentos a 2", "output": "nmap -sS -Pn --max-retries 2 <ip>"}
{"instruction": "Escaneo UDP sin ping con min-rate 1500 y sin DNS", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -sU -Pn --min-rate 1500 -n <ip>"}
{"instruction": "Escaneo TCP connect con detección de versión y min-rate 2000", "output": "nmap -sT -sV --min-rate 2000 <ip>"}
{"instruction": "Escaneo FIN sobre este host de pruebas", "output": "nmap -sF <ip>"}
{"instruction": "Escaneo NULL en entorno controlado", "output": "nmap -sN <ip>"}
{"instruction": "Escaneo Xmas sobre este servidor de laboratorio", "output": "nmap -sX <ip>"}
{"instruction": "Escaneo FIN con detección de versión", "output": "nmap -sF -sV <ip>"}
{"instruction": "Escaneo NULL sin ping previo y sin DNS", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -sN -Pn -n <ip>"}
{"instruction": "Escaneo ACK con timing T3 y límite de reintentos 1", "output": "nmap -sA -T3 --max-retries 1 <ip>"}
{"instruction": "Escaneo IPv6 básico sobre este host de laboratorio", "output": "nmap -6 2001:db8:40::1"}
{"instruction": "Escaneo IPv6 con detección de versión", "output": "nmap -6 -sV 2001:db8:40::2"}
{"instruction": "Escaneo IPv6 sin ping previo y sin DNS", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -6 -Pn -n 2001:db8:40::5"}
{"instruction": "Escaneo IPv6 con detección de OS y servicios", "output": "nmap -6 -O -sV 2001:db8:40::6"}
{"instruction": "Escaneo IPv6 con timing T3 y sin ping", "output": "nmap -6 -T3 -Pn 2001:db8:40::10"}
{"instruction": "Escaneo SYN con detección de versión y límite de RTT máximo a 150ms", "output": "nmap -sS -sV --max-rtt-timeout 150ms <ip>"}
{"instruction": "Escanea únicamente los puertos 22 y 80 en <ip>", "output": "nmap -p 22,80 <ip>"}
{"instruction": "Muestra solo puertos abiertos en <ip>", "output": "nmap --open <ip>"}
{"instruction": "Escanea únicamente los puertos 22 y 80 en <ip>", "output": "nmap -p 22,80 <ip>"}
{"instruction": "Muestra solo puertos abiertos en <ip>", "output": "nmap --open <ip>"}
{"instruction": "Escanea servicio web en puerto 8080 del host <ip>", "output": "nmap --script=http-title,http-enum,http-headers -p 8080 <ip>"}
{"instruction": "Escanea servidor DNS en puerto 53 UDP en <ip>", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -sU -p 53 <ip>"}
{"instruction": "Define puerto fuente 53 en escaneo <ip>", "output": "nmap --source-port 53 <ip>"}
{"instruction": "Escanea SSH puerto 22 ejecutando ssh-hostkey y detección de versión en <ip>", "output": "nmap -sV -p 22 --script=ssh-hostkey,ssh-auth-methods,ssh2-enum-algos,ssh-hostkey <ip>"}
{"instruction": "Escaneo SYN especificando interfaz eth0 y puerto fuente 53 en <ip>", "output": "nmap -sS -e eth0 --source-port 53 <ip>"}
{"instruction": "Escaneo UDP sin ping y con min-rate 1500 sobre los puertos 123 y 161", "output": "nmap -sU -Pn --min-rate 1500 -p 123,161 <ip>"}
{"instruction": "Escanea solo el puerto 443 por TCP sin usar resolución DNS", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -sT -p 443 -n <ip>"}
{"instruction": "Comprueba qué servicios TCP hay entre los puertos 1000 y 2000", "output": "nmap -sT -p 1000-2000 <ip>"}
{"instruction": "Realiza un escaneo SYN solo a los puertos 21,22,80 y 443", "output": "nmap -sS -p 21,22,80,443 <ip>"}
{"instruction": "Ejecuta un escaneo SYN sin ping previo sobre los puertos 1 al 1024", "output": "nmap -sS -Pn -p 1-1024 <ip>"}
{"instruction": "Escaneo UDP a los puertos 53,67,123 y 161 con detección de versión", "output": "nmap -sU -sV -p 53,67,123,161 <ip>"}
{"instruction": "Comprueba solo el puerto 161 UDP para servicios SNMP", "output": "nmap --script=snmp-info,snmp-interfaces,snmp-sysdescr -sU -p 161 <ip>"}
{"instruction": "Realiza un escaneo UDP sin ping y mostrando solo puertos abiertos", "output": "nmap -sU -Pn --open <ip>"}
{"instruction": "Haz un escaneo UDP sin DNS a los puertos 53 y 161", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -sU -p 53,161 -n <ip>"}
{"instruction": "Escaneo SYN a puertos 80 y 443 con DNS inverso", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -sS -p 80,443 -R <ip>"}
{"instruction": "Escaneo UDP a puertos 53 y 5353 con DNS inverso", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -sU -p 53,5353 -R <ip>"}
{"instruction": "Escaneo TCP connect sin ping y mostrando solo puertos abiertos", "output": "nmap -sT -Pn --open <ip>"}
{"instruction": "Escaneo UDP sin ping sobre los puertos 53,123 y 161", "output": "nmap -sU -Pn -p 53,123,161 <ip>"}
{"instruction": "Escaneo SYN con min-rate 5000 y mostrando solo puertos abiertos", "output": "nmap -sS --min-rate 5000 --open <ip>"}
{"instruction": "Escaneo UDP con min-rate 2000 sobre puertos 53 y 161", "output": "nmap -sU --min-rate 2000 -p 53,161 <ip>"}
{"instruction": "Escaneo SYN a puertos 80,443 y 8080 con min-rate 3000", "output": "nmap -sS -p 80,443,8080 --min-rate 3000 <ip>"}
{"instruction": "Escaneo ACK mostrando solo puertos abiertos", "output": "nmap -sA --open <ip>"}
{"instruction": "Escaneo FIN sobre puertos 80,443 y 8080", "output": "nmap -sF -p 80,443,8080 <ip>"}
{"instruction": "Escaneo IPv6 sobre puertos 22,80 y 443", "output": "nmap -6 -p 22,80,443 2001:db8:40::7"}
{"instruction": "Escaneo del puerto 5432 con detección de versión en entorno de laboratorio", "output": "nmap -sV -p 5432 <ip>"}
{"instruction": "Escaneo UDP de los puertos 161 y 162 con detección de versión", "output": "nmap -sU -sV -p 161,162 <ip>"}
{"instruction": "Escaneo SYN especificando interfaz eth0 y puerto fuente 53", "output": "nmap -sS -e eth0 --source-port 53 <ip>"}
{"instruction": "Escanea todos los puertos TCP en <ip>", "output": "nmap -p- <ip>"}
{"instruction": "Escanea todos los puertos TCP en <ip>", "output": "nmap -p- <ip>"}
{"instruction": "Escaneo TCP completo con min-rate 2000 y sin resolución DNS", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -sT -p- --min-rate 2000 -n <ip>"}
{"instruction": "Realiza un escaneo TCP connect con detección de versión sobre todos los puertos en entorno controlado", "output": "nmap -sT -sV -p- <ip>"}
{"instruction": "Escaneo TCP completo con timing T3 y sin resolución DNS en entorno de pruebas", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -sT -p- -T3 -n <ip>"}
{"instruction": "Escaneo UDP completo con detección de versión y guardando salida XML", "output": "nmap -sU -sV -p- -oX udp_completo_40_20.xml <ip>"}
{"instruction": "Realiza un escaneo sin resolución DNS sobre todos los puertos TCP", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -sT -p- -n <ip>"}
{"instruction": "Escaneo UDP completo sin ping y guardando salida XML", "output": "nmap -sU -p- -Pn -oX udp_sin_ping_40_58.xml <ip>"}
{"instruction": "Escaneo TCP completo con min-rate 4000 y sin DNS", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -sT -p- --min-rate 4000 -n <ip>"}
{"instruction": "Escaneo TCP completo con timing T2 y límite de reintentos 4", "output": "nmap -sT -p- -T2 --max-retries 4 <ip>"}
{"instruction": "Escaneo IPv6 completo con salida en todos los formatos", "output": "nmap -6 -p- -oA ipv6_completo_40 2001:db8:40::9"}
{"instruction": "Escaneo UDP completo con timing T3 y sin DNS", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -sU -p- -T3 -n <ip>"}
{"instruction": "Realiza descubrimiento de hosts en subred <ip>/<mask> sin resolver DNS", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -sn -n <ip>"}
{"instruction": "Escanea subred <ip>/<mask> excluyendo <ip>", "output": "nmap <ip> --exclude <ip>"}
{"instruction": "Descubre hosts en <ip>/<mask> excluyendo <ip> y <ip>", "output": "nmap --script=discovery,default -sn <ip> --exclude <ip>,<ip>"}
{"instruction": "Escaneo de subred sin DNS para descubrimiento de hosts", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -sn -n <ip>"}
{"instruction": "Escaneo de subred con resolución DNS inversa para todos los hosts", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -sn -R <ip>"}
{"instruction": "Escaneo de subred sin ping previo para descubrimiento de hosts", "output": "nmap --script=discovery,default -sn -Pn <ip>"}
{"instruction": "Descubre hosts activos en la subred <ip>/<mask> sin usar DNS", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -sn -n <ip>"}
{"instruction": "Escaneo de subred excluyendo el host <ip>", "output": "nmap <ip> --exclude <ip>"}
{"instruction": "Escaneo de subred con salida en todos los formatos", "output": "nmap -oA reporte_subred_50 <ip>"}
{"instruction": "Escaneo de subred con detección de OS y servicios sin DNS", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -O -sV -n <ip>"}
{"instruction": "Escanea rango 8000-8100 con detección de versión y timing T3 en <ip>", "output": "nmap -sV -p 8000-8100 -T3 <ip>"}
{"instruction": "Escaneo UDP con detección de versión sobre el rango de puertos 500-600", "output": "nmap -sU -sV -p 500-600 <ip>"}
{"instruction": "Escaneo SYN sin DNS sobre el rango de puertos 8000-8100", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -sS -p 8000-8100 -n <ip>"}
{"instruction": "Escaneo UDP sobre subred <ip>/<mask> para puertos 53 y 161", "output": "nmap -sU -p 53,161 <ip>"}
{"instruction": "Escaneo de subred sin ping previo y mostrando solo puertos abiertos", "output": "nmap -sS -Pn --open <ip>"}
{"instruction": "Escaneo ACK para análisis de firewall en laboratorio <ip>", "output": "nmap -sA <ip>"}
{"instruction": "Fragmenta paquetes en entorno laboratorio <ip>", "output": "nmap -f <ip>"}
{"instruction": "Escaneo Xmas fragmentando paquetes en entorno controlado <ip>", "output": "nmap -sX -f <ip>"}
{"instruction": "Realiza un escaneo ACK para análisis de firewall en laboratorio", "output": "nmap -sA <ip>"}
{"instruction": "Escaneo Xmas fragmentando paquetes en entorno de pruebas", "output": "nmap -sX -f <ip>"}
{"instruction": "Escaneo ACK para análisis de firewall mostrando solo puertos abiertos en <ip>", "output": "nmap -sA --open <ip>"}
{"instruction": "Escaneo TCP mostrando solo puertos abiertos y guardando salida normal en archivo", "output": "nmap -sT --open -oN tcp_abiertos_40_10.txt <ip>"}
{"instruction": "Escaneo UDP completo sin DNS y guardando salida normal en archivo", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -sU -p- -n -oN udp_sin_dns_40_38.txt <ip>"}
{"instruction": "Escaneo lento SYN con timing T0 y límite de reintentos a 1", "output": "nmap -sS -T0 --max-retries 1 <ip>"}
{"instruction": "Realiza un escaneo muy lento con timing T0 y límite de reintentos 3", "output": "nmap -T0 --max-retries 3 <ip>"}
{"instruction": "Escaneo UDP lento con timing T1 y sin ping", "output": "nmap -sU -T1 -Pn <ip>"}
{"instruction": "Escaneo TCP connect lento con timing T2 y sin DNS", "output": "nmap --script=dns-recursion,dns-nsid,dns-brute -sT -T2 -n <ip>"}
{"instruction": "Escaneo con scripts safe usando timing T1", "output": "nmap --script=safe -T1 <ip>"}
{"instruction": "Escaneo SYN con host-timeout de 90 segundos y timing T1", "output": "nmap -sS --host-timeout 90s -T1 <ip>"}
{"instruction": "Escaneo UDP con host-timeout de 120 segundos y timing T0", "output": "nmap -sU --host-timeout 120s -T0 <ip>"}
{"instruction": "Escaneo con detección de OS y servicios usando timing T1", "output": "nmap -O -sV -T1 <ip>"}
{"instruction": "Realiza un escaneo UDP lento sobre los puertos 53,67 y 161 con timing T1", "output": "nmap -sU -p 53,67,161 -T1 <ip>"}
{"instruction": "Escaneo TCP connect lento con timing T1 para todos los puertos", "output": "nmap -sT -p- -T1 <ip>"}
{"instruction": "Haz un escaneo UDP lento con timing T0 sobre todos los puertos", "output": "nmap -sU -p- -T0 <ip>"}
{"instruction": "Escaneo SYN lento con timing T1 sobre todos los puertos", "output": "nmap -sS -p- -T1 <ip>"}
{"instruction": "Escaneo lento de subred con timing T1 para descubrimiento de hosts", "output": "nmap --script=discovery,default -sn -T1 <ip>"}
```

---

[back](./)
