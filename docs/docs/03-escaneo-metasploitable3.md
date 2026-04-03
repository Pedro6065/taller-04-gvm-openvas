# 03 — Configuración y ejecución del escaneo de Metasploitable3

## Objetivo

Configurar un escaneo completo de vulnerabilidades contra Metasploitable3
usando la interfaz web de GVM y analizar los resultados obtenidos.

---

## Acceso a la interfaz web

Abrir el navegador en Kali Linux y acceder a:

```
https://127.0.0.1:9392
```

Iniciar sesión con las credenciales del administrador creadas durante `gvm-setup`.

---

## Análisis previo con Nmap

Antes de lanzar el escaneo con GVM, se realizó un reconocimiento con Nmap
para identificar los servicios activos en Metasploitable3:

```bash
nmap -sV -A 192.168.56.102
```

### Puertos y servicios detectados

| Puerto | Estado | Servicio | Versión |
|---|---|---|---|
| 21/tcp | open | ftp | ProFTPD 1.3.5 |
| 22/tcp | open | ssh | OpenSSH 6.6.1p1 Ubuntu |
| 80/tcp | open | http | Apache httpd 2.4.7 |
| 445/tcp | open | netbios-ssn | Samba smbd 4.3.11 |
| 631/tcp | open | ipp | CUPS 1.7 |
| 3306/tcp | open | mysql | MySQL (unauthorized) |
| 8080/tcp | open | http | Jetty 8.1.7.v20120910 |

---

## Paso 1 — Crear el Target

En la interfaz web de GVM:

```
Configuration → Targets → New Target
```

| Campo | Valor |
|---|---|
| Name | metasploitable3 |
| Hosts | 192.168.56.102 |

Guardar el target.

---

## Paso 2 — Crear la Task

```
Scans → Tasks → New Task
```

| Campo | Valor |
|---|---|
| Name | Scan Metasploitable3 |
| Scan Config | Full and Fast |
| Target | metasploitable3 |

Guardar la task.

---

## Paso 3 — Ejecutar el escaneo

Hacer clic en el botón **Play** (▶) junto a la task creada.

> ⏱️ El escaneo completo tarda entre **15 y 40 minutos** dependiendo
> del número de servicios y vulnerabilidades encontradas.

Durante el escaneo se puede seguir el progreso en tiempo real desde
`Scans → Tasks`.

---

## Paso 4 — Análisis de resultados

Una vez completado el escaneo, acceder al informe:

```
Scans → Reports
```

### Clasificación de severidad

| Nivel | Significado |
|---|---|
| **Critical** | Vulnerabilidad crítica — explotación inmediata posible |
| **High** | Alto riesgo |
| **Medium** | Riesgo medio |
| **Low** | Riesgo bajo |

### Resultados obtenidos

| Pregunta | Respuesta |
|---|---|
| Vulnerabilidades críticas detectadas | 1 |
| Servicio con más vulnerabilidades | SSL/TLS (TLSv1.0 y TLSv1.1 deprecated) |

### CVEs identificados con mayor puntuación CVSS

- CVE-2011-3389
- CVE-2015-0204
- CVE-2023-41928
- CVE-2024-41270
- CVE-2025-3200

---

## Comparativa Nmap vs GVM

| Puerto | Nmap | GVM |
|---|---|---|
| 3306 MySQL | Detectado (unauthorized) | Detectado |
| 139 / 111 | Filtered | Detectado internamente |
| 58873 | No detectado | Puerto alto dinámico/temporal |

> El puerto 58873 era un puerto alto dinámico, probablemente un servicio
> interno o temporal que no responde a escaneos externos. Nmap no lo
> detecta porque está filtrado desde el exterior.

---

## Preguntas del taller

**¿Qué diferencia hay entre escaneo activo y pasivo?**

El escaneo activo envía paquetes directamente al objetivo para provocar
respuestas. El escaneo pasivo solo observa el tráfico existente sin
interactuar con el objetivo.

**¿Por qué un escaneo de vulnerabilidades tarda más que un escaneo de puertos?**

Porque hace mucho más trabajo: no solo comprueba si un puerto está abierto,
sino qué servicio hay detrás, su versión y si tiene vulnerabilidades conocidas,
lo que implica muchas más peticiones, análisis y consultas a bases de datos.

**¿Por qué es importante validar las vulnerabilidades detectadas?**

Para evitar desperdiciar recursos corrigiendo falsos positivos y priorizar
solo los riesgos reales que afectan a la infraestructura.

**¿Qué problemas pueden causar los falsos positivos?**

Generan alertas erróneas que distraen a los equipos de seguridad de amenazas
reales, desperdiciando tiempo y recursos valiosos.

---

## Siguiente paso

→ [04 — Explotación de Apache Struts](./04-explotacion-apache-struts.md)