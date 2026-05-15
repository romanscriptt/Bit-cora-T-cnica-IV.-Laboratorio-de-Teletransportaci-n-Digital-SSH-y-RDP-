## LICENSES.md - Inventario de Licencias de Software
**Proyecto:** Sistemas Informaticos - 1ªDAM
**Autor:** Álvaro López De San Román
**Fecha:** 15 de mayo de 2026
---
En este docuemnto recogeremos todas la sliciencias de todo nuetsro software utilizado  sobre las infraetsructuras que hemos despelegado en esta Bitacora Tecnica IV,
la finlidad que tiene es de verificar que el uso de cada componente es legal ycompatible en un entono empresarial.
---
## 1. Apache Guacamole

| Campo | Detalle |
| --- | --- |
| **Nombre** | Apache Guacamole |
| **Version Utilizada** | linuxserver/webtop en donde se incluyen clientes de Guacamole integrado |
| **Licencia** | Apache License 2.0 |
| **Tipo** | Open Source - Licencia de estilo permisiva |

**Descripción de la licencia:** La Apache License 2.0 permite el uso, modificación y distribución del software, incluso en entornos comerciales, sin coste de licenciamiento. Obliga únicamente a conservar los avisos de copyright y la atribución al proyecto original.
 
**Fuente oficial:** https://www.apache.org/licenses/LICENSE-2.0  
## 2. OpenSSH
 
| Campo | Detalle |
|---|---|
| **Nombre** | OpenSSH |
| **Versión utilizada** | linuxserver/openssh-server:latest |
| **Licencia** | BSD License (modificada) / MIT License |
| **Tipo** | Open Source — Licencia permisiva |
 
**Descripción de la licencia:** OpenSSH se distribuye bajo una licencia de tipo BSD modificada. Es una de las licencias más permisivas del ecosistema open source: permite el uso comercial y privado sin restricciones, siempre que se mantenga el aviso de copyright original. No exige que el código derivado sea publicado.
 
**Fuente oficial:** https://www.openssh.com/portable.html  
**Texto de la licencia:** https://cvsweb.openbsd.org/src/usr.bin/ssh/LICENCE?rev=HEAD
 
---
 
## 3. Docker Engine / Docker Compose
 
| Campo | Detalle |
|---|---|
| **Nombre** | Docker Engine + Docker Compose |
| **Versión utilizada** | Docker Compose v3.8 |
| **Licencia** | Apache License 2.0 |
| **Tipo** | Open Source — Licencia permisiva |
 
**Descripción de la licencia:** El motor de Docker y Docker Compose se distribuyen bajo Apache License 2.0. Su uso es libre en entornos de producción y desarrollo, incluyendo entornos comerciales. Docker Desktop tiene condiciones adicionales para empresas grandes (>250 empleados), pero el motor CLI es completamente libre.
 
**Fuente oficial:** https://docs.docker.com/engine/  
**Repositorio:** https://github.com/docker/compose/blob/main/LICENSE
 
---
 
## 4. Ubuntu (base de las imágenes de contenedor)
 
| Campo | Detalle |
|---|---|
| **Nombre** | Ubuntu Linux (base de linuxserver/webtop:ubuntu-xfce) |
| **Licencia** | Múltiple — GPL v2, MIT, BSD y otras según paquete |
| **Tipo** | Open Source |
 
**Descripción de la licencia:** Ubuntu se distribuye bajo licencias de software libre, principalmente GPL v2. El núcleo Linux está bajo GPL v2. Canonical permite su uso gratuito en cualquier entorno sin restricciones comerciales para la distribución base.
 
**Fuente oficial:** https://ubuntu.com/legal/open-source-licences
 
---
 
