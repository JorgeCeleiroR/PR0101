[README.md](https://github.com/user-attachments/files/32697276/README.md)
# PR0102 · Instalación y Configuración de Webmin en Ubuntu Server

**Autor:** Luis
**Curso:** 2026/2027 · Despliegue de Aplicaciones Web

## Descripción

Este documento describe, paso a paso, el proceso de instalación y configuración de [Webmin](https://webmin.com/) en una máquina virtual con **Ubuntu Server**, ejecutada sobre **VirtualBox**. Además, se incluye la automatización de todo el proceso mediante scripts de Bash.

---

## 1. Actualizar los repositorios

```bash
sudo apt update
sudo apt upgrade -y
```

- `sudo apt update`: descarga la lista de paquetes disponibles desde los repositorios configurados y comprueba si hay versiones más recientes, pero **no instala nada todavía**.
- `sudo apt upgrade -y`: instala las actualizaciones de los paquetes que ya tenemos en el sistema. El parámetro `-y` confirma automáticamente la instalación sin que el sistema pregunte "¿Desea continuar? [S/n]".

> Este paso es importante porque asegura que el sistema tiene las últimas versiones y parches de seguridad antes de instalar software nuevo.

---

## 2. Instalar dependencias

```bash
sudo apt install software-properties-common apt-transport-https -y
```

- **`software-properties-common`**: proporciona herramientas para gestionar repositorios de software, como el comando `add-apt-repository`.
- **`apt-transport-https`**: permite que `apt` pueda descargar paquetes desde repositorios que usan el protocolo **HTTPS** (necesario porque el repositorio de Webmin lo requiere).

![Instalación de dependencias](https://github.com/user-attachments/assets/eb8d40e2-066f-4f2f-8958-01dbb3d40d9a)

---

## 3. Añadir el repositorio de Webmin

```bash
curl -o webmin-setup-repo.sh https://raw.githubusercontent.com/webmin/webmin/master/webmin-setup-repo.sh
sudo sh webmin-setup-repo.sh
```

- `curl -o webmin-setup-repo.sh <url>`: descarga el script oficial de instalación de Webmin desde GitHub y lo guarda localmente con el nombre `webmin-setup-repo.sh`.
- `sudo sh webmin-setup-repo.sh`: ejecuta ese script con permisos de administrador. Este script se encarga de:
  1. Añadir la **clave GPG** oficial de Webmin, que permite verificar que los paquetes descargados son auténticos y no han sido modificados.
  2. Añadir el **repositorio** de Webmin a la lista de fuentes de `apt`, para que el sistema sepa dónde descargar el paquete.

**Comprobante — descarga del script:**

![Descarga del script](https://github.com/user-attachments/assets/b44b0e28-9798-42be-912d-5b76ba364031)

**Comprobante — ejecución del script:**

![Ejecución del script](https://github.com/user-attachments/assets/ac145117-dff1-48d1-851d-0d3e204583fa)

---

## 4. Instalar Webmin

```bash
sudo apt-get install --install-recommends webmin -y
```

- `--install-recommends`: además del paquete principal, instala también los paquetes "recomendados" asociados (módulos y dependencias adicionales que mejoran la funcionalidad de Webmin, aunque no sean estrictamente obligatorios).
- Este proceso puede tardar un par de minutos, ya que Webmin descarga varios módulos.

![Instalación de Webmin](https://github.com/user-attachments/assets/e3a8da61-a2cd-4350-bf53-6c446ec70da1)

---

## 5. Configurar el cortafuegos (UFW)

```bash
sudo ufw status
sudo ufw allow ssh
sudo ufw allow 10000/tcp
sudo ufw enable
```

- `sudo ufw status`: muestra si el cortafuegos (**U**ncomplicated **F**ire**w**all) está activo y qué reglas tiene configuradas. En este caso partía de un estado `inactive`.
- `sudo ufw allow ssh`: abre el puerto **22** (SSH), necesario para no perder el acceso remoto al servidor al activar el cortafuegos.
- `sudo ufw allow 10000/tcp`: abre el puerto **10000/TCP**, que es el puerto por defecto en el que escucha Webmin.
- `sudo ufw enable`: activa el cortafuegos aplicando las reglas anteriores. Pide confirmación (`y`) porque puede interrumpir conexiones activas si no se han abierto los puertos correctos antes.

**Comprobante — activación:**

![Activación de UFW](https://github.com/user-attachments/assets/eaa165fd-ba99-47ca-9b52-40ca3c1126ba)

**Comprobante — verificación final (opcional):**

```bash
sudo ufw status
```

![Verificación de UFW](https://github.com/user-attachments/assets/62cbd3db-4e9b-4cbe-8d2d-0d8bbd35eafd)

---

## 6. Ejecutar Webmin

```bash
sudo systemctl status webmin
```

Si el servicio no estuviera activo, se pondría en marcha y se dejaría activado en el arranque con:

```bash
sudo systemctl start webmin
sudo systemctl enable webmin
```

- `systemctl status`: comprueba el estado actual del servicio (activo, inactivo, con errores, etc.).
- `systemctl start`: arranca el servicio de forma inmediata.
- `systemctl enable`: hace que el servicio se inicie automáticamente cada vez que arranque la máquina.

En este caso, el servicio ya estaba activo y en ejecución:

![Estado de Webmin](https://github.com/user-attachments/assets/c8b4932a-a74b-4638-a96b-25f36b5369c7)

---

## 7. Asignar contraseña de root para Webmin

```bash
sudo /usr/share/webmin/changepass.pl /etc/webmin root PasswordAlumno
```

En Ubuntu, la cuenta de `root` del sistema operativo está deshabilitada por defecto. Sin embargo, Webmin necesita un usuario `root` propio con el que autenticarse en su panel web. Este comando:

- Ejecuta el script `changepass.pl`, incluido con Webmin.
- Le indica la ruta de configuración de Webmin (`/etc/webmin`).
- Asigna la contraseña **`PasswordAlumno`** al usuario `root` de Webmin, de forma **independiente** de si la cuenta root del sistema está bloqueada o no.

![Cambio de contraseña](https://github.com/user-attachments/assets/0ce2f8df-4db9-40dd-92f7-19f594845502)

---

## 8. Acceder a Webmin desde el navegador

```bash
ip a
```

Este comando muestra todas las interfaces de red de la máquina y sus direcciones IP asignadas. En este caso se detectaron dos interfaces:

| Interfaz | IP | Tipo de red | Accesible desde el navegador del host |
|---|---|---|---|
| `enp0s3` | 10.0.2.15 | NAT (VirtualBox) | ❌ No, es una red interna de VirtualBox |
| `enp0s8` | 192.168.0.2 | Host-Only | ✅ Sí, accesible desde la máquina física |

![Interfaces de red](https://github.com/user-attachments/assets/ed02b077-edcb-4712-ba16-439350ec168c)

Con la IP de la red Host-Only, se accede a Webmin desde el navegador en:

```
https://192.168.0.2:10000
```

> ⚠️ Esta URL es orientativa: la IP y, en su caso, el puerto variarán según la configuración de red de cada máquina.

![Acceso a Webmin desde el navegador](https://github.com/user-attachments/assets/792e18da-ddc1-41f0-84cd-c490eecb1322)

---

## 9. Automatización con scripts de Bash

### 9.1. Estructura de archivos

```
.
├── README.md
├── images
└── scripts
    ├── .env
    └── webmin-install.sh
```

### 9.2. Creación del archivo de variables `.env`

```bash
mkdir -p scripts
nano scripts/.env
```

- `mkdir -p scripts`: crea la carpeta `scripts` (el flag `-p` evita error si la carpeta ya existe).
- `nano scripts/.env`: abre el editor de texto `nano` para crear y editar el archivo `.env`.

Dentro del archivo `.env` se definen las variables de configuración que usará el script de instalación, de modo que no queden "hardcodeadas" dentro del propio script:

```env
WEBMIN_USER="root"
WEBMIN_ROOT_PASSWORD="PasswordAlumno"
WEBMIN_PORT=10000
SSH_PORT=22
```

Para guardar y salir de `nano`: `Ctrl + O` (guardar) → `Enter` (confirmar nombre) → `Ctrl + X` (salir).

![Creación de .env](https://github.com/user-attachments/assets/5b3916c0-5afc-4ef1-9f53-514e628c617b)

> 🔒 **Importante:** el archivo `.env` contiene información sensible (contraseñas). No debe subirse a un repositorio público; hay que añadirlo al `.gitignore`.

### 9.3. Creación del script `webmin-install.sh`

```bash
nano scripts/webmin-install.sh
```

Este script recopila todos los comandos anteriores (actualización, dependencias, repositorio, instalación, cortafuegos, arranque del servicio y cambio de contraseña) para automatizar todo el proceso en una sola ejecución, leyendo las variables desde el archivo `.env`.

![Creación de webmin-install.sh](https://github.com/user-attachments/assets/71f3b4ea-44a9-4971-bcb7-0273e5853cac)

---
