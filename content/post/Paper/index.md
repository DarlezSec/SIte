+++
author = "Darlez.Sec"
title = "Reto CTF Paper Nivel Easy, Plataforma HackTheBox."
date = "2022-05-19"
description = "Metodologia que aplico en la CTF Paper de la plataforma HackTheBox."
tags = [
"Mem0ri3s",
"HackTheBox",
"Metodologias",
]
categories = [
"Mem0ri3s",
"HackTheBox",
"Metodologias",
]
series = ["Themes Guide"]
aliases = ["migrate-from-jekyl"]
image = "https://byte-mind.net/wp-content/uploads/2022/02/paper-750x410.jpg"
+++

#### CTF Paper Nivel Easy, Plataforma HackTheBox.

`Metodología: dónde se encuentra un vhost con burpsuite llamado office.paper en el encabezado de respuesta X-Backend-Server, Lograron encontrar la versión del WordPress y usaron un exploit para la versión de WordPress 5.2.3, Esta vulnerabilidad podría permitir una unauthenticated uservista privada o draft postsdebido a un problema dentro de WP_Query. Después de eso encontramos otro vhost dónde encontraron que  En el chat hay recyclops botque ayuda al usuario a listar el directorio de ventas list command con file command del que podemos ver el contenido dentro del archivo, encontraron un archivo navegando entre Directorios llamando .env estos archivos generalmente se usan para almacenar secrets, y ahí estaba la contraseña y el usuario para la sesión por SSH, corrieron el script de LinPEAS Y vemos que esta máquina es vulnerable a CVE-2021-3560e so, lo Polkit or Pwnkitque permite unprivileged user llamar a métodos privilegiados usando DBus y así lograr obtener root.`
