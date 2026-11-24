# SmartCrop Control

**Sistema integrado de sensorización agrícola y control de actuadores con administración centralizada en red.**

Proyecto Intermodular del ciclo formativo de grado superior **Administración de Sistemas Informáticos en Red (ASIR)**, curso 2026/27 · IES Triana.

**Autor:** Antonio Pilar Moya

---

## Descripción

**SmartCrop Control** es una solución IoT *on-premise* para **monitorizar las condiciones ambientales** de un cultivo o invernadero y **automatizar su riego**, todo desde un único nodo hardware: una **Raspberry Pi 5 (8 GB)** con **Ubuntu Server ARM64**.

El funcionamiento, a grandes rasgos:

1. Un sensor **DHT22** conectado a los pines GPIO mide temperatura y humedad.
2. Un script en **Python 3** lee las mediciones de forma periódica y las guarda en una base de datos **MySQL/MariaDB** local.
3. Un servidor **Apache2 + PHP** publica un panel web en la red local donde se consultan los datos y se accionan las electroválvulas de riego mediante un **módulo de relés de 2 canales**.

**Problema que resuelve:** las soluciones comerciales de sensorización agrícola suelen ser caras, exigen licencias anuales y dependen de la nube de terceros (*vendor lock-in*), lo que compromete la privacidad de los datos y obliga a tener Internet permanente. SmartCrop Control ofrece una alternativa **autónoma, modular, de bajo coste y 100 % software libre** para pequeñas y medianas explotaciones.

---

## Justificación y objetivos

### Justificación

- Optimizar el uso de los recursos hídricos y reducir costes operativos.
- Mantener el **control absoluto de los datos** (sin servicios cloud de pago).
- Concentrar captación, almacenamiento y servidor en un solo dispositivo de bajo consumo, sin máquinas virtuales ni dependencias externas.
- Reducir al mínimo el coste de implantación, apoyándose en hardware de bajo consumo y software libre.

### Objetivo general

Desarrollar e implementar una solución IoT integral y una infraestructura de servidor concentradora sobre Raspberry Pi 5 que permita la **monitorización ambiental** y la **automatización remota del riego** mediante GPIO y servicios de red locales.

### Objetivos específicos

- **Sistemas:** instalar, configurar y endurecer Ubuntu Server (usuarios, permisos y servicios).
- **Servicios web:** desplegar Apache y un panel de administración; cifrar las comunicaciones con **HTTPS (SSL/TLS)**.
- **Bases de datos:** diseñar una base de datos relacional para el histórico de lecturas y crear usuarios con **mínimo privilegio**.
- **Hardware/IoT:** programar scripts en Python 3 para leer el DHT22 y controlar el módulo de relés.
- **Seguridad:** configurar el cortafuegos **UFW** y automatizar **copias de seguridad** con Cron.
- **Calidad:** documentar y ejecutar una fase de pruebas y validación (conectividad, seguridad y rendimiento).

---

## Arquitectura y tecnologías

El sistema se organiza en **tres capas independientes**:

| Capa | Componentes |
|------|-------------|
| **Captación y actuación (GPIO)** | Raspberry Pi 5, sensor DHT22, módulo de relés de 2 canales |
| **Servidor e infraestructura local** | Ubuntu Server ARM64, scripts Python, MySQL/MariaDB, Apache2 + PHP, UFW |
| **Usuario y acceso (LAN)** | PCs, tablets o móviles que acceden al panel web responsive |

### Tecnologías y módulos de ASIR relacionados

| Área / Módulo ASIR | Tecnologías y configuración | Aplicación en el proyecto |
|--------------------|-----------------------------|---------------------------|
| **Hardware** (FH) | Raspberry Pi 5 (8 GB), sensor **DHT22**, módulo de **relés** de 2 canales (5 V, optoacoplado), cableado Dupont | Nodo central, medición de temperatura/humedad y conmutación de electroválvulas |
| **Sistemas Operativos** (ISO / ASO) | **Ubuntu Server ARM64**, Bash, Cron | Administración de usuarios y servicios, automatización de tareas y backups |
| **Redes y Seguridad** (PAR / SRI / SAD) | **IP estática** (`x.x.x.x/x`), **SSH**, cortafuegos **UFW** (puertos 22 y 80), OpenSSL (HTTPS) | Direccionamiento fijo en la LAN, administración remota y filtrado del tráfico entrante |
| **Bases de Datos** (GBD / ASGBD) | **MySQL / MariaDB** (`smartcrop_db`) | Almacenamiento de usuarios, dispositivos, lecturas y auditoría con integridad referencial |
| **Web y Scripts** (IAW) | **Apache2**, **PHP 8.x**, HTML5 / CSS3 / JavaScript, **Python 3** | Panel web, API local y scripts recolectores de datos |

---

## Funcionalidades principales

- **Monitorización en tiempo real** de temperatura y humedad relativa.
- **Histórico de lecturas** con gráfica de evolución de las últimas 24 horas.
- **Control remoto del riego** (ON/OFF) desde el navegador mediante el módulo de relés.
- **Autenticación y roles:** el *Administrador* puede accionar el riego; el *Operador* solo consulta.
- **Auditoría:** registro de qué usuario activa o desactiva cada relé, y cuándo.
- **Gestión de usuarios** desde el propio panel (contraseñas cifradas con `password_hash`).
- **Copias de seguridad automáticas** de la base de datos mediante Bash + Cron.
- **Seguridad perimetral** con UFW, que limita el tráfico entrante a los puertos esenciales (SSH y web).

### Modelo de datos

La base de datos `smartcrop_db` se compone de cuatro tablas relacionadas por claves foráneas (relaciones 1:N):

- `USUARIOS` → `DISPOSITIVOS` (un usuario registra N dispositivos)
- `DISPOSITIVOS` → `LECTURAS_SENSORES` (un dispositivo genera N lecturas)
- `USUARIOS` → `REGISTRO_ACTUACIONES` (un usuario realiza N acciones sobre los relés)

---

## Planificación

El proyecto se desarrolla en **7 fases (épicas)** entre **septiembre de 2026 y junio de 2027**. La gestión se lleva en un tablero Kanban en Jira.

| Fase | Épica | Fechas | Contenido principal |
|:----:|-------|--------|---------------------|
| **1** | Elección de idea y viabilidad | 15/09/2026 – 22/09/2026 | Lluvia de ideas, estudio de viabilidad técnica y entrega de la propuesta de idea |
| **2** | Anteproyecto y planificación inicial | 23/09/2026 – 15/11/2026 | Memoria del anteproyecto, tablero Kanban en Jira, repositorio con README y publicación de GitHub Pages |
| **3** | Diseño técnico de infraestructura y datos | 16/11/2026 – 20/12/2026 | Topología de red y política UFW, modelo Entidad-Relación y scripts DDL de `smartcrop_db`, maquetas en Figma |
| **4** | Despliegue de SO y servicios LAMP | 08/01/2027 – 28/02/2027 | Ubuntu Server ARM64, IP estática y SSH, instalación y securización de Apache2, MySQL/MariaDB y PHP, reglas UFW |
| **5** | Desarrollo IoT y lógica de control | 01/03/2027 – 30/04/2027 | Conexionado GPIO (DHT22 y relés), script Python, dashboard web, conmutación de relés y backups automáticos |
| **6** | Protocolo de pruebas y validación | 01/05/2027 – 20/05/2027 | Pruebas de conectividad, inserción de datos, respuesta de relés y restauración de la base de datos |
| **7** | Documentación final, memoria y defensa | 21/05/2027 – 15/06/2027 | Manuales de administración y de usuario, memoria final y preparación de la defensa ante el tribunal |

---

## Estado actual

- **Fase 1 (Elección de idea y viabilidad):** finalizada.
- **Fase 2 (Anteproyecto):** **finalizada**.
- **Transición a la Fase 3 (Diseño técnico):** se está cerrando la planificación y se inicia el diseño técnico de la infraestructura y los datos.

---

## Enlaces del proyecto

| Recurso | Enlace |
|---------|--------|
| Tablero Kanban (Jira) | [Enlace a mi Tablero Kanban en Jira] |
| Web del proyecto (GitHub Pages) | [https://antoniopilar.github.io/SmartCrop-Control/](https://antoniopilar.github.io/SmartCrop-Control/) |
| Repositorio | [github.com/AntonioPilar/SmartCrop-Control](https://github.com/AntonioPilar/SmartCrop-Control) |

---

## Autor

**Antonio Pilar Moya** · 2.º ASIR · IES Triana · Curso 2026/27
