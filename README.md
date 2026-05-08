#  Bitácora Técnica IV — Laboratorio de Teletransportación Digital (SSH y RDP)

**Módulo:** Sistemas Informáticos  
**Alumno:** Álvaro López de San Román  
**GitHub:** [@romanscriptt](https://github.com/romanscriptt)  
**Curso:** 1º DAM — Centro FP Superior Cámara de Comercio de Sevilla  
**Profesor:** Willman Acosta Lugo  
**Resultado de Aprendizaje:** RA6  
---

##  Descripción del laboratorio

El objetivo de esta práctica es aprender a administrar servidores de forma remota, como si estuvieran en un centro de datos en otra ciudad. Para ello levantamos una infraestructura local con **Docker Compose** que simula ese entorno: dos servidores accesibles únicamente por red, sin monitor ni teclado físico.

Lo que vamos a practicar:
- Acceso SSH con autenticación por **clave pública** (sin contraseña)
- **Hardening** del servidor SSH para protegerlo
- Acceso gráfico remoto vía **RDP** y vía **navegador web (Guacamole)**
- Documentación técnica de todo el proceso

---

##  FASE 1 — Preparación del entorno con Docker Compose

### 1.1 Crear la carpeta del proyecto

Abro una terminal (PowerShell o CMD en Windows) y creo la carpeta de trabajo:

```powershell
mkdir SI_Bitacora4_LopezSanRoman
cd SI_Bitacora4_LopezSanRoman
```

### 1.2 Crear el archivo docker-compose.yml

Dentro de esa carpeta creo el archivo `docker-compose.yml` con el siguiente contenido exacto:

```yaml
# Laboratorio de Sistemas Informáticos - Bitácora 4
# Este archivo levanta dos servicios: un servidor SSH y un entorno gráfico vía RDP/Web
version: '3.8'

services:
  # Servidor especializado para prácticas de SSH y seguridad
  servidor_ssh:
    image: linuxserver/openssh-server:latest
    container_name: lab_ssh_servidor
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Madrid
      - USER_NAME=alumno
      - USER_PASSWORD=sistemas_informaticos
      - PASSWORD_ACCESS=true
    ports:
      - "2222:2222" # Mapeamos el puerto 2222 local al 2222 del contenedor
    restart: unless-stopped

  # Servidor con entorno gráfico ligero (XFCE) accesible por RDP y Web
  servidor_rdp:
    image: linuxserver/webtop:ubuntu-xfce
    container_name: lab_rdp_servidor
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Madrid
    ports:
      - "3389:3389" # Puerto estándar de Escritorio Remoto (RDP)
      - "3000:3000" # Puerto para acceso vía navegador (Guacamole)
    shm_size: "1gb" # Importante para evitar cuelgues en el entorno gráfico
    restart: unless-stopped
```

### 1.3 Levantar los contenedores

Desde la misma carpeta ejecuto:

```bash
docker-compose up -d
```

La flag `-d` es para que corra en segundo plano (modo "detached") y me devuelva la terminal.

**Salida esperada:**

```
Creating network "si_bitacora4_lopezSanRoman_default" with the default driver
Creating lab_ssh_servidor ... done
Creating lab_rdp_servidor ... done
```

### 1.4 Verificar que los contenedores están corriendo

```bash
docker ps
```
<img width="1080" height="610" alt="image" src="https://github.com/user-attachments/assets/bfc20b11-c67a-434c-9925-820ac3bdf0c8" />

**Salida esperada:**

```
CONTAINER ID   IMAGE                              PORTS                    NAMES
xxxxxxxxxxxx   linuxserver/openssh-server:latest  0.0.0.0:2222->2222/tcp   lab_ssh_servidor
xxxxxxxxxxxx   linuxserver/webtop:ubuntu-xfce     0.0.0.0:3000->3000/tcp   lab_rdp_servidor
                                                  0.0.0.0:3389->3389/tcp
```

>  Si veo los dos contenedores en estado `Up`, la infraestructura está lista.

>  **Problema que tuve:** Al levantar el compose me dio error en el puerto 3389 porque Windows tenía ocupado ese puerto con su propio servicio de Escritorio Remoto. Lo solucioné abriendo `services.msc`, buscando el servicio **"Remote Desktop Services"** y deteniéndolo temporalmente. Después volví a ejecutar `docker-compose up -d` y funcionó.

---

##  FASE 2 — SSH: Forjando la Llave Maestra

### 2.1 Paso A — Conexión inicial con contraseña

Desde mi máquina anfitriona me conecto al contenedor SSH. El puerto es el 2222 porque así lo mapeamos en el compose:

```bash
ssh alumno@localhost -p 2222
```

Me pide contraseña. Introduzco: `sistemas_informaticos`

**Salida esperada:**
<img width="1096" height="207" alt="image" src="https://github.com/user-attachments/assets/f1a0d564-9066-40cc-bc08-996ec93aaefa" />

```
The authenticity of host '[localhost]:2222' can't be established.
ED25519 key fingerprint is SHA256:xxxxxxxxxxxxxxxxxxxx
Are you sure you want to continue connecting (yes/no)? yes

alumno@localhost's password:
Welcome to OpenSSH Server
alumno@lab_ssh_servidor:~$
```

>  Estoy dentro del servidor. Salgo con `exit` para volver a mi máquina.



---

### 2.2 Paso B — Generación del par de llaves ed25519

**En mi máquina anfitriona** (no dentro del contenedor), genero el par de llaves criptográficas:

```bash
ssh-keygen -t ed25519 -C "alvaro@dam.camara.es"
```

El sistema me pregunta dónde guardar la clave. Pulso **Enter** para usar la ruta por defecto:

```
Enter file in which to save the key (/home/usuario/.ssh/id_ed25519): [Enter]
Enter passphrase (empty for no passphrase): [Enter]
Enter same passphrase again: [Enter]
```

**Salida esperada:**
<img width="814" height="167" alt="image" src="https://github.com/user-attachments/assets/e7f6a01f-1d63-489e-940f-4561cba129cb" />

```
Your identification has been saved in /home/usuario/.ssh/id_ed25519
Your public key has been saved in /home/usuario/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx alvaro@dam.camara.es
The key's randomart image is:
+--[ED25519 256]--+
|        .o+o     |
...
+----[SHA256]-----+
```
<img width="640" height="420" alt="image" src="https://github.com/user-attachments/assets/b0a2396f-4cd9-49a2-a34a-2f771dceb802" />

**Esto genera dos archivos:**

| Archivo | Descripción |
|---|---|
| `~/.ssh/id_ed25519` |  **Clave PRIVADA** — nunca se comparte con nadie |
| `~/.ssh/id_ed25519.pub` | **Clave PÚBLICA** — esta es la que enviamos al servidor |

>  **¿Por qué ed25519 y no RSA?** Ed25519 usa criptografía de curva elíptica moderna. Genera claves más cortas pero igual de seguras (o más) que RSA-4096, y es mucho más rápido en la autenticación.

---

### 2.3 Paso C — Transferencia de la clave pública al servidor

Copio mi clave pública al servidor con `ssh-copy-id`. Esta será la última vez que tenga que usar la contraseña:

```bash
ssh-copy-id -p 2222 alumno@localhost
```

Me pide la contraseña por última vez: `sistemas_informaticos`

**Salida esperada:**
<img width="1263" height="37" alt="image" src="https://github.com/user-attachments/assets/caf27f2f-dcf5-46fc-b846-144c9168f7cf" />

```
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/usuario/.ssh/id_ed25519.pub"
Number of key(s) added: 1

Now try logging into the machine, with:   "ssh -p '2222' 'alumno@localhost'"
and check to make sure that only the key(s) you wanted were added.
```

### 2.4 Verificación — Conexión sin contraseña

Ahora me conecto de nuevo y compruebo que ya NO me pide contraseña:

```bash
ssh alumno@localhost -p 2222
```

**Entra directamente al servidor sin pedir nada.** 



---

### 2.5 Hardening del servidor SSH

Entro al contenedor y edito el fichero de configuración del servicio SSH:

```bash
ssh alumno@localhost -p 2222
sudo nano /etc/ssh/sshd_config
```

Busco y modifico (o añado al final) las siguientes líneas:

```bash
# ─── HARDENING SSH ─────────────────────────────────────────
# No permitir acceso directo como root
PermitRootLogin no

# Deshabilitar autenticación por contraseña → solo llaves
PasswordAuthentication no

# Máximo 3 intentos fallidos antes de cerrar la conexión
MaxAuthTries 3

# Forzar solo el protocolo SSH versión 2 (la 1 tiene vulnerabilidades)
Protocol 2

# Tiempo máximo para autenticarse: 30 segundos
LoginGraceTime 30
# ───────────────────────────────────────────────────────────
```

Guardo con `Ctrl+O` → `Enter` → `Ctrl+X`.

Reinicio el servicio SSH para aplicar los cambios:

```bash
sudo service ssh restart
```



>  **Error crítico que cometí:** La primera vez puse `PasswordAuthentication no` ANTES de copiar la llave pública al servidor. Me bloqueé fuera sin poder entrar. Tuve que destruir el contenedor con `docker-compose down` y volver a levantarlo desde cero. **Lección aprendida: primero transfiero la llave, luego aplico el hardening.**

---

##  FASE 3 — RDP: El Escritorio en el Navegador

### 3.1 Opción A — Conexión vía cliente RDP (Windows)

Abro el cliente de Escritorio Remoto nativo de Windows:

```
Win + R → mstsc → Enter
```

En el campo "Equipo" escribo:

```
localhost:3389
```

Pulso **Conectar**. Aparece el escritorio Ubuntu XFCE del contenedor.

---

### 3.2 Opción B — Conexión vía navegador (Guacamole)

Si el cliente RDP da problemas, abro el navegador y voy a:

```
http://localhost:3000
```

Aparece directamente el escritorio Ubuntu XFCE del contenedor dentro del navegador, sin necesidad de ningún cliente externo.

>  Esto funciona gracias a **Apache Guacamole**, que convierte el protocolo RDP en una aplicación web accesible desde cualquier navegador.

---

### 3.3 Prueba de éxito — Crear el archivo PRUEBA_LOGRADA.txt

Dentro del escritorio remoto, abro una terminal y ejecuto:

```bash
echo "Práctica completada. Álvaro López de San Román - DAM 1º 2024/2025" > ~/Desktop/PRUEBA_LOGRADA.txt
```

Verifico que el archivo existe en el escritorio del contenedor:

```bash
cat ~/Desktop/PRUEBA_LOGRADA.txt
```

**Salida:**
<img width="1915" height="910" alt="image" src="https://github.com/user-attachments/assets/c864dfa0-5765-4746-bd4d-baef1b0ebb4c" />

```
Práctica completada. Álvaro López de San Román - DAM 1º 2024/2025
```

---

*@author Alvaro Lopez de San Roman — 1º DAM 2024/2025*
