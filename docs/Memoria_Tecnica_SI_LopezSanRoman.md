

# **Infraestructura Híbrida de Acceso Remoto Seguro mediante Docker y Apache Guacamole**

**Proyecto Final de Documentación — Sprint 1**

# **Álvaro López De San Román**                  

ÍNDICE 

[**1\. Análisis de Necesidades	3**](#1.-análisis-de-necesidades)

[1.1 Contexto y Problemática Actual	3](#1.1-contexto-y-problemática-actual)

[1.2 Solución Propuesta: Infraestructura Híbrida Docker-Guacamole	3](#1.2-solución-propuesta:-infraestructura-híbrida-docker-guacamole)

[1.3 Justificación Técnica y Beneficios (TCO)	4](#1.3-justificación-técnica-y-beneficios-\(tco\))

[**2\. Diseño de la Infraestructura	5**](#heading=h.z3zv6jdoaet8)

[2.1 Arquitectura del Sistema	5](#heading=h.g9ls4jqffo3b)

[2.2 Componentes Desplegados	5](#heading=h.8ijrhz1j9990)

[**3\. Implementación	5**](#heading=h.if9rcj4hgbjh)

[3.1 Configuración del Entorno Docker	5](#heading=h.yxzo4b6auvmf)

[3.2 Configuración SSH y Hardening	5](#heading=h.qs5fp84p3vaw)

[3.3 Acceso Remoto Gráfico (RDP y Guacamole)	5](#heading=h.uxa7a24wzcpj)

[**4\. Verificación y Pruebas	5**](#heading=h.omj0f1i1igcn)

[**5\. Conclusiones	5**](#heading=h.fgoonwe637kp)

[**6\. Referencias	5**](#6.-referencias)

## **1\. Análisis de Necesidades** {#1.-análisis-de-necesidades}

### 1.1 Contexto y Problemática Actual {#1.1-contexto-y-problemática-actual}

La empresa objeto de este proyecto es una startup de desarrollo de software multiplataforma este equipo técnico necesita utilizar estos servidores de desarrollo y bases de datos ubicados en un centro de datos remoto lo cual la latencia también será unos de los problemas. El modelo operativo consistía en conectar a cada servidor individualmente mediante protocolos RDP y SSH directos, lo que implicaba mantener abiertos múltiples puertos .

Esta situación es una serie de problemas técnicos y de seguridad de carácter crítico. En primer lugar, la superficie de ataque del sistema era considerablemente amplia, ya que  cada puerto expuesto en el firewall representa un vector de entrada para actores maliciosos que intentan entrar para hacer daño. Los servicios SSH y RDP directos son objetivos habituales de ataques, tal y como recogen numerosos análisis de seguridad perimetral. En segundo lugar, la gestión cada técnico mantenía sus propias contraseñas para cada servidor, lo que dificulta la auditoría de accesos y el cumplimiento de políticas de seguridad internas. En tercer lugar, la solución no era escalable, un nuevo servidor al entorno requería abrir nuevos puertos e  instalar clientes adicionales en los equipos de los técnicos y gestionar nuevas credenciales de forma manual.

### 1.2 Solución Propuesta: Infraestructura Híbrida Docker-Guacamole {#1.2-solución-propuesta:-infraestructura-híbrida-docker-guacamole}

Tras analizar los requerimientos de accesibilidad y seguridad correspondientes , se hemos  optado por implementar una infraestructura híbrida basada en dos componentes principales por un lado un servidor SSH para administración, y un entorno de escritorio remoto accesible tanto vía protocolo RDP como a través de un cliente web integrado (Apache Guacamole), todo ello orquestado mediante Docker Compose.

Esta arquitectura aporta dos ventajas operativas fundamentales:

**Centralización.** Se establece un único punto de entrada a la infraestructura. En lugar de exponer múltiples puertos en el firewall para cada servidor, basta con publicar los puertos 2222 (SSH), 3389 (RDP) y 3000 acceso a la web Guacamole. 

**Aislamiento.** Cada servicio opera en su propio entorno estanco. El servidor SSH (linuxserver/openssh-server) y el servidor de escritorio remoto (linuxserver/webtop:ubuntu-xfce) no comparten procesos ni dependencias, lo que reduce el radio de impacto en caso de comprometerse uno de ellos.

### 1.3 Justificación Técnica y Beneficios (TCO) {#1.3-justificación-técnica-y-beneficios-(tco)}

La elección de esta solución responde también a la necesidad de tener que  optimizar el Coste Total de Propiedad de la infraestructura. Al apoyarse en el software distribuido bajo licencias permisivas —Apache License 2.0 para Docker y Guacamole, y licencia BSD modificada para OpenSSH—, la empresa evita costes de licenciamiento recurrentes que serían inevitables con soluciones comerciales equivalentes como Citrix Virtual Apps o Microsoft Remote Desktop Services en su modalidad CAL.

Asimismo, la portabilidad inherente a la contenedorización garantiza que el tiempo de recuperación ante desastres (RTO, Recovery Time Objective) sea mínimo. En caso de fallo del servidor físico, basta con disponer del fichero `docker-compose.yml` y los datos persistentes para restaurar toda la infraestructura en cuestión de minutos, cumpliendo así con los requisitos no funcionales de disponibilidad que cualquier sistema informático profesional debe garantizar \[1\].

Según García Notario (2015), un análisis de requisitos riguroso es condición necesaria para que las decisiones de diseño tomadas durante la fase de implementación sean sostenibles a largo plazo \[2\]. En este caso, la detección temprana de la problemática de exposición perimetral ha permitido diseñar una arquitectura que no solo resuelve el problema inmediato, sino que sienta las bases para una expansión ordenada de la infraestructura sin comprometer la seguridad del sistema.

## 6\. Referencias {#6.-referencias}

\[1\] Drake, J. M. (2008). *Análisis de requisitos y especificación de una aplicación* \[en línea\]. Disponible en: [https://www.ctr.unican.es/asignaturas/ingenieria\_software\_4\_f/doc/m3\_08\_especificacion-2011.pdf](https://www.ctr.unican.es/asignaturas/ingenieria_software_4_f/doc/m3_08_especificacion-2011.pdf)

\[2\] García Notario, D. (2015). *Análisis de requisitos en el desarrollo del software* \[en línea\]. Disponible en: [https://e-archivo.uc3m.es/rest/api/core/bitstreams/a66b0a2d-fa7c-483f-ac5e-1476ff2da8eb/content](https://e-archivo.uc3m.es/rest/api/core/bitstreams/a66b0a2d-fa7c-483f-ac5e-1476ff2da8eb/content)

\[3\] Apache Software Foundation. (2024). *Apache Guacamole — Clientless Remote Desktop Gateway* \[en línea\]. Disponible en: [https://guacamole.apache.org/](https://guacamole.apache.org/)

\[4\] OpenBSD Project. (2024). *OpenSSH Manual Pages* \[en línea\]. Disponible en: [https://www.openssh.com/manual.html](https://www.openssh.com/manual.html)  
