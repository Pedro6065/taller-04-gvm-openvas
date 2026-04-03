# Taller 04 — Análisis de vulnerabilidades con GVM/OpenVAS

![Estado](https://img.shields.io/badge/Estado-Completado-success)
![Curso](https://img.shields.io/badge/Curso-IFCT0210-blue)
![Entorno](https://img.shields.io/badge/Entorno-VirtualBox-orange)

Documentación completa del taller de análisis de vulnerabilidades usando **Greenbone Vulnerability Management (GVM/OpenVAS)** sobre un entorno virtualizado con Kali Linux y Metasploitable3.

> ⭐ Taller completado como único alumno de la clase.

---

## 🎯 Objetivos del taller

- Instalar y configurar GVM/OpenVAS en Kali Linux
- Ejecutar un escaneo completo de vulnerabilidades contra Metasploitable3
- Analizar e interpretar el informe de resultados
- Explotar una vulnerabilidad real identificada en el escaneo (Apache Struts)

---

## 🖥️ Entorno de laboratorio

| Máquina | Sistema operativo | IP | Rol |
|---|---|---|---|
| Kali Linux | Kali 2024 | 192.168.56.101 | Atacante / GVM |
| Metasploitable3 | Ubuntu 14.04 | 192.168.56.102 | Objetivo |
| Ubuntu | Ubuntu 24.04 | 192.168.56.103 | Auxiliar |

Red: **VirtualBox Host-Only** · Adaptador: `vboxnet0`

---

## 📁 Estructura del repositorio

```
taller-04-gvm-openvas/
├── README.md                          ← este archivo
├── docs/
│   ├── 01-instalacion-gvm.md          ← instalación de Greenbone en Kali
│   ├── 02-problema-postgresql.md      ← resolución error collation PostgreSQL
│   ├── 03-escaneo-metasploitable3.md  ← configuración y ejecución del escaneo
│   └── 04-explotacion-apache-struts.md ← explotación CVE con Metasploit
└── screenshots/                       ← capturas del proceso
```

---

## 📋 Resumen del proceso

### 1. Instalación de GVM
Instalación de Greenbone Vulnerability Management sobre Kali Linux mediante `gvm-setup`, incluyendo actualización de feeds NVT, SCAP y CERT.

### 2. Problema PostgreSQL
Durante la configuración apareció un error de **collation mismatch** en la base de datos PostgreSQL. Se resolvió recreando el cluster con la configuración de locale correcta.

### 3. Escaneo de Metasploitable3
Escaneo completo (*Full and Fast*) contra `192.168.56.102`, obteniendo un informe detallado con vulnerabilidades clasificadas por severidad (Critical, High, Medium, Low).

### 4. Explotación — Apache Struts
Usando un módulo de Metasploit se explotó exitosamente la vulnerabilidad de Apache Struts detectada por GVM:

```
exploit/multi/http/struts2_content_type_ognl
TARGETURI: /continuum/index.action
```

---

## 🔗 Documentación detallada

| Documento | Contenido |
|---|---|
| [01 — Instalación GVM](./docs/01-instalacion-gvm.md) | Comandos de instalación y configuración inicial |
| [02 — Error PostgreSQL](./docs/02-problema-postgresql.md) | Diagnóstico y solución del error de collation |
| [03 — Escaneo](./docs/03-escaneo-metasploitable3.md) | Configuración del target y ejecución del escaneo |
| [04 — Explotación](./docs/04-explotacion-apache-struts.md) | Explotación de Apache Struts con Metasploit |

---

## ⚠️ Aviso legal

Este laboratorio se realizó en un **entorno aislado y controlado** con fines exclusivamente educativos, en el marco del certificado IFCT0210. Ninguna de las técnicas documentadas aquí debe aplicarse contra sistemas sin autorización expresa.

---

<sub>IFCT0210 · Operación de Sistemas Informáticos · Almería 2025-2026</sub>
