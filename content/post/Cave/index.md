+++
author = "Darlez.Sec"
title = "Reto CTF You're in a cave, Nivel insane plataforma TryHackMe."
date = "2019-03-11"
description = "Un simple Post para explicar la Metodologia que aplique en este Reto CTF"
tags = [
"Metodologias",
"My Mem0ri3s",
]
categories = [
"Metodologias",
"My Mem0ri3s",
]
series = ["Themes Guide"]
aliases = ["migrate-from-jekyl"]
image = "plantilla.png"
+++
# Reto CTF You're in a cave Nivel Insane, Plataforna TryHackMe.

![You're in a cave.](cave.png)

`Ctf You're in a cave, Nivel Insane Plataforma TryHackMe.`

`Metodología: Ctf dónde se ve envenenamiento de un archivo que se ejecuta como un proceso de sistema, le hemos agregado una revshell a ese proceso y al matar el proceso nos lo ha reiniciado y logramos obtener una rshell con root, después de eso se tuvo que salir de un contendor, que contaba con privilegios que introducen problemas de seguridad, dando acceso completo al host; debido a que se genera una carencia de seguridad por la eliminación de las restricciones de Seccomp, AppArmor y del propio sistema Linux. De modo que usado un exploit que su función es que ejecuta código através de el archivo "realse_agent" para crear un controlador secundario cgroup, especificando el archivo "release_agent" y activar el "release_agent" elimina todos los procesos en el cgroup, añadiendo a esto una reverse shell como payload para poder tener un acceso más completo a la maquina host.`
