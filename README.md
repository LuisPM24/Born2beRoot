*Este proyecto ha sido creado como parte del currículo de 42 por lupalomi*

# Descripción
Este proyecto consiste en la creación de una máquina virtual junto, la instalación de un sistema operativo (Debian | Rocky) sin GUI y preparar la máquina virtual para funcionar como un servidor.

Dentro del archivo clonado, se puede observar un archivo `signature.txt` que contiene el **shasum** de la máquina virtual. El objetivo de este archivo es evitar que, antes de una evaluación, se produzcan modificaciones en la máquina virtual.

# Instructions

En esta sección se incluiran la mayoría de los comandos usados durante la preparación de este proyecto así como una serie de comandos útiles:

## Comandos de `sudo`
### Instalación de `sudo`

```bash
su
apt install sudo
```

*PD: Después de instalar `sudo` será necesario reiniciar la máquina. Se puede hacer mediante el comando `sudo reboot`.*

### Verificación de la instalación de `sudo`

```bash
su
sudo -V
```

### Modificar las políticas de los usuarios `sudo`

Abre el editor `nano` y permite establecer y modificar ciertas políticas para los usuarios `sudo`.

```bash
nano /etc/sudoers.d/sudo_config
```

## Usuarios y Grupos
### Creación de Usuarios

Este comando crea un usuario con un nombre especificado.

```bash
sudo adduser <nombre de usuario>
```

### Creación de Grupos

Este comando crea un grupo con un nombre especificado.

```bash
sudo addgroup <nombre de grupo>
```

### Añadir un Usuario a un Grupo

Este comando añade un usuario a un grupo ya creado.

```bash
sudo adduser <nombre usuario> <nombre grupo>
```

### Obtener integrantes de un Grupo

Obtiene todos los integrantes de un grupo especificado.

```bash
getent group <nombre grupo>
```

## SSH
### Estado del SSH

Muestra el estado actual del servidor SSH.

```bash
sudo service ssh status
```

### Modificar la configuración del servidor SSH

Abre el editor `nano` para modificar la configuración principal del servidor SSH.

```bash
nano /etc/ssh/sshd_config
```

### Modificar la configuración del cliente SSH

Abre el editor `nano` para modificar la configuración principal del cliente SSH (como tu máquina se conecta a otras).

```bash
nano /etc/ssh/ssh_config
```

**Después de alterar la configuración del cliente o del servidor, será necesario reiniciar el servicio SSH mediante el siguiente comando:**

```bash
sudo service ssh restart
```

### Conectar a maquina virtual desde tu máquina

Permite, desde una terminal, conectar a la máquina virtual.

```bash
ssh <usuario>@localhost -p <puerto configurado>
```

*PD: El puerto desde el que se conecta tu máquina a la máquina virtual se gestiona desde el propio servicio de máquinas virtuales, en mi caso, desde Oracle VM*

## Firewall
### Cambiar estado del firewall

Cambia el estado del firewall de activado a desactivado

```bash
ufw enable
```

### Habilitar un puerto

Crea una nueva regla específica para habilitar un puerto.

```bash
ufw allow <puerto>
```

### Visualizar estado del Firewall

Muestra las reglas habilitadas y para que puertos del Firewall.

```bash
ufw status
```

## Políticas de Contraseñas

### Modificar Logins

Abre el editor `nano` y modifica parte de la política de contraseñas de usuarios.

```bash
nano /etc/login.defs
```

### Modificar reglas de complejidad de Contraseñas

Abre el editor `nano` y permite modificar las reglas de complejidad de las contraseñas.

```bash
nano /etc/pam.d/common-password
```

# Script

En la máquina virtual, existe un script que se debe ejecutar cada 10 minutos. Esta regla viene definida mediante una `crontab` (o tarea programada) mediante el comando:

```bash
sudo crontab -u root -e
```

A continuación se explica cada línea del script.

## Arquitectura

Muestra la arquitectura de la CPU.

```bash
uname -a
```

## Núcleos Físicos

Muestra la cantidad de núcleos físicos.

```bash
grep "physical id" /proc/cpuinfo | wc -l
```

## Núcleos Virtuales

Muestra la cantidad de núcleos virtuales.

```bash
grep processor /proc/cpuinfo | wc -l
```

## Cantidad en MB de memoria RAM usada

```bash
free --mega | awk '$1 == "Mem:" {print $3}'
```

## Total de MB de memoria RAM

```bash
free --mega | awk '$1 == "Mem:" {print $2}'
```

## Porcentaje de memoria RAM usada

```bash
free --mega | awk '$1 == "Mem:" {printf("(%.2f%%)\n", $3/$2*100)}'
```

## Número de memoria del disco usada

```bash
df -m | grep "/dev/" | grep -v "/boot" | awk '{use += $3} {total += $2} END {printf("(%d%%)\n"), use/total*100}'
```

## Porcentage de memoria usada

```bash
vmstat 1 4 | tail -1 | awk '{print $15}'
```

## Fecha del último reinicio

```bash
who -b | awk '$1 == "system" {print $3 " " $4}'
```

## Mostrar si LVM activo

```bash
if [ $(lsblk | grep "lvm" | wc -l) -gt 0 ]; then echo yes; else echo no; fi
```

## Número de conexiones TCP abiertas

```bash
ss -ta | grep ESTAB | wc -l
```

## Número de usuarios

```bash
users | wc -w
```

## Dirección IP y MAC

```bash
ip link | grep "link/ether" | awk '{print $2}'
```

## Número de comandos ejecutados con `sudo`

```bash
journalctl _COMM=sudo | grep COMMAND | wc -l
```