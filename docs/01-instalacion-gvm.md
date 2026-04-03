# 01 — Instalación de GVM en Kali Linux

## Información del entorno

| Dato | Valor |
|---|---|
| Sistema | Kali Linux (atacante) |
| IP Kali | 192.168.56.101 |
| Versión GVM instalada | gvm 25.04.3 |
| Puerto interfaz web | 9392 |

---

## Arquitectura de GVM

GVM está formado por varios componentes que trabajan juntos:

```
Interfaz Web (gsad)
        ↓
Greenbone Manager (gvmd)
        ↓
OpenVAS Scanner (ospd-openvas)
        ↓
Network Vulnerability Tests (NVT feeds)
```

| Componente | Función |
|---|---|
| `gvmd` | Gestor principal de vulnerabilidades |
| `ospd-openvas` | Motor de escaneo (ejecuta los escaneos) |
| `gsad` | Interfaz web (Greenbone Security Assistant) |
| feeds NVT/SCAP/CERT | Base de datos de pruebas de vulnerabilidades |

---

## Paso 1 — Actualizar el sistema

```bash
sudo apt update
```

---

## Paso 2 — Instalar GVM

```bash
sudo apt install gvm -y
```

---

## Paso 3 — Inicializar GVM

Este comando realiza la configuración completa del sistema: crea los certificados, descarga los feeds de vulnerabilidades y crea el usuario administrador.

```bash
sudo gvm-setup
```

> ⚠️ Este proceso puede tardar entre **20 y 40 minutos** dependiendo de la velocidad de la conexión, ya que descarga la base de datos completa de vulnerabilidades (NVT, SCAP, CERT).

Resultado esperado de los certificados:

```
GVM client certificate is valid and present as /var/lib/gvm/CA/clientcert.pem.
OK: Your GVM certificate infrastructure passed validation
```

Credenciales generadas durante la instalación:

| Campo | Valor |
|---|---|
| Usuario | admin |
| Contraseña | admin1234 |

---

## Paso 4 — Verificar la instalación

```bash
sudo gvm-check-setup
```

Resultado esperado:

```
GVM installation OK
```

---

## Paso 5 — Iniciar los servicios

```bash
sudo gvm-start
```

Los tres servicios que deben estar activos:

```bash
sudo systemctl status gvmd
sudo systemctl status gsad
sudo systemctl status ospd-openvas
```

---

## Paso 6 — Acceder a la interfaz web

Abrir el navegador en Kali y acceder a:

```
https://127.0.0.1:9392
```

> El navegador mostrará un aviso de certificado autofirmado — es normal. Aceptar la excepción de seguridad y continuar.

Iniciar sesión con las credenciales del paso 3.

---

## Verificación de conectividad con el objetivo

Antes de lanzar cualquier escaneo, comprobar que Kali puede llegar a Metasploitable3:

```bash
ping -c 4 192.168.56.102
```

Resultado esperado:

```
PING 192.168.56.102 (192.168.56.102) 56(84) bytes of data.
64 bytes from 192.168.56.102: icmp_seq=1 ttl=64 time=1.60 ms
64 bytes from 192.168.56.102: icmp_seq=2 ttl=64 time=0.621 ms
64 bytes from 192.168.56.102: icmp_seq=3 ttl=64 time=0.501 ms
64 bytes from 192.168.56.102: icmp_seq=4 ttl=64 time=0.594 ms

--- 192.168.56.102 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3067ms
rtt min/avg/max/mdev = 0.501/0.830/1.604/0.449 ms
```

---

## Siguiente paso

→ [02 — Resolución del error de PostgreSQL](./02-problema-postgresql.md)
