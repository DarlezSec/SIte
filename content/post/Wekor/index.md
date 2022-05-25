+++
author = "DarlezSec"
title = "Reto CTF Wekor Nivel Medium, Plataforma TryHackMe."
date = "2022-05-25"
description = "Metodologia que aplico en la CTF Wekor de la plataforma TryHackMe. "
tags = [
"Mem0ri3s",
"Meotodologias",
]
categories = [
"Mem0ri3s",
"Metodologias",
]
series = ["Themes Guide"]
aliases = ["migrate-from-jekyl"]
image = "https://tryhackme-images.s3.amazonaws.com/room-icons/e27d2e539f0105c9a45a7892234229c5.jpeg"
+++

### CTF Wekor Nivel Medium, Plataforma TryHackMe.

![](ko.png)

#### Metodología: Ctf Dónde se ven temas de explotación a SQL y WordPress,  Comenzamos interceptado el tráfico de la pagina web con BurpSuite y logramos encontrar una versión Vulnerable a SQL Injection y obtuvimos la credenciales para poder loguearnos en el WordPress y hacer una Reverse Shell al sistema, Enumeramos servicios Memcached para lograr obtener las credenciales del primer usuario y obtener la primera flag, había un archivo llamado "bitcoin" y se podia ejecutar como sudo así que lo cambiamos de directorio lo remplazamos con bash y obtuvimos la raiz y así la bandera root.

