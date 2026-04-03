# 02 — Resolución del error de collation en PostgreSQL

## Contexto

Durante la ejecución de `sudo gvm-setup`, el proceso de inicialización
de la base de datos falló con un error relacionado con la configuración
de **collation** (ordenación de caracteres) en PostgreSQL.

Este error impide que GVM cree correctamente su base de datos interna y
es uno de los problemas más comunes en instalaciones de GVM sobre Kali
Linux con configuración de locale no estándar.

---

## El error

El error aparece durante `gvm-setup` con un mensaje similar a:

```
ERROR: Database cluster initialization failed.
could not create unique index "pg_database_datname_index"
ERROR: could not open file "global/pg_database": No such file or directory
```

O en su variante más específica:

```
initdb: error: invalid locale settings; check LANG and LC_* environment variables
```

O al intentar iniciar PostgreSQL:

```
FATAL: database locale is incompatible with operating system
DETAIL: The database was initialized with LC_COLLATE "en_US.UTF-8",
        but the operating system provides "C".
```

---

## Diagnóstico

### Verificar el estado del cluster PostgreSQL

```bash
sudo pg_lsclusters
```

Resultado que muestra el problema:

```
Ver Cluster Port Status Owner    Data directory              Log file
15  main    5432 down   postgres /var/lib/postgresql/15/main /var/log/postgresql/...
```

El cluster aparece como `down` — no ha podido iniciarse correctamente.

### Verificar los locales disponibles en el sistema

```bash
locale -a
```

### Comprobar la configuración actual

```bash
sudo -u postgres psql -c "SHOW lc_collate;" 2>/dev/null || echo "PostgreSQL no está activo"
```

---

## Solución — Recrear el cluster con locale correcto

> ⚠️ Este proceso elimina el cluster de PostgreSQL y lo recrea desde cero.
> En un entorno de laboratorio esto es seguro porque no hay datos previos.

### Paso 1 — Detener PostgreSQL

```bash
sudo systemctl stop postgresql
```

### Paso 2 — Eliminar el cluster existente

```bash
sudo pg_dropcluster --stop 15 main
```

> Sustituir `15` por la versión de PostgreSQL instalada si es diferente.
> Verificar con `pg_lsclusters`.

### Paso 3 — Recrear el cluster con locale correcto

```bash
sudo pg_createcluster --locale en_US.UTF-8 --start 15 main
```

Resultado esperado:

```
Creating new PostgreSQL cluster 15/main ...
/usr/lib/postgresql/15/bin/initdb -D /var/lib/postgresql/15/main
  --auth-local peer --auth-host scram-sha-256 --no-instructions
  --locale en_US.UTF-8
...
Ver Cluster Port Status Owner    Data directory              Log file
15  main    5432 online postgres /var/lib/postgresql/15/main /var/log/postgresql/...
```

### Paso 4 — Iniciar PostgreSQL

```bash
sudo systemctl start postgresql
sudo systemctl enable postgresql
```

### Paso 5 — Verificar que el cluster está activo

```bash
sudo pg_lsclusters
```

El estado ahora debe mostrar `online`.

### Paso 6 — Relanzar gvm-setup

Con PostgreSQL funcionando correctamente, relanzar la inicialización:

```bash
sudo gvm-setup
```

Esta vez el proceso debe completarse sin errores.

---

## Verificación final

```bash
sudo gvm-check-setup
```

Resultado esperado:

```
GVM installation OK
```

---

## Explicación técnica

PostgreSQL utiliza **collations** para determinar cómo se ordenan y
comparan los caracteres en las consultas. Cuando el locale del sistema
operativo no coincide con el locale con el que se inicializó el cluster,
PostgreSQL no puede arrancar.

En sistemas Kali Linux instalados con configuración mínima o en
máquinas virtuales importadas, el locale del sistema puede estar
configurado como `C` o `POSIX`, mientras que GVM espera encontrar un
cluster con `en_US.UTF-8`. La solución es recrear el cluster con el
locale correcto antes de que GVM intente crear su base de datos.

---

## Siguiente paso

→ [03 — Configuración y ejecución del escaneo](./03-escaneo-metasploitable3.md)
