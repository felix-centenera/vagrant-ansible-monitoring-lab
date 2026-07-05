# 01 - Creación de la infraestructura con Vagrant

## Objetivo

En esta primera parte del laboratorio se crearán las máquinas virtuales utilizando **Vagrant**.

Vagrant será el encargado de desplegar la infraestructura base del laboratorio. Más adelante utilizaremos **Ansible** para configurar automáticamente los sistemas operativos y desplegar los distintos servicios.

---

## ¿Qué es Vagrant?

Vagrant es una herramienta de **Infraestructura como Código (Infrastructure as Code - IaC)** que permite crear y gestionar máquinas virtuales mediante un fichero de configuración denominado `Vagrantfile`.

Gracias a este enfoque podemos:

- Definir la infraestructura mediante código.
- Versionar la infraestructura en Git.
- Reproducir exactamente el mismo laboratorio en cualquier equipo.
- Eliminar y volver a crear el entorno tantas veces como sea necesario.

En este laboratorio se desplegarán dos máquinas virtuales Ubuntu:

| Máquina | Hostname | Dirección IP | Rol |
|----------|----------|--------------|-----|
| so-lab-node-01 | node01 | 192.168.56.11 | Monitoring |
| so-lab-node-02 | node02 | 192.168.56.12 | Worker |

---

## Arquitectura

```text
                  Vagrant
                      │
                      │
              VirtualBox
                      │
          ┌───────────┴───────────┐
          │                       │
          ▼                       ▼
     so-lab-node-01         so-lab-node-02
        (Monitoring)            (Worker)
```

---

## Acceder al directorio de trabajo

Desde la raíz del repositorio:

```bash
cd Vagrant
```

---

## Crear las máquinas virtuales

Ejecutar:

```bash
vagrant up
```

Durante este proceso Vagrant realizará automáticamente las siguientes tareas:

- Descarga de la imagen Ubuntu (solo la primera vez).
- Creación de las máquinas virtuales.
- Configuración de la red privada.
- Asignación de direcciones IP.
- Provisioning inicial para preparar las máquinas para Ansible.

---

## Resultado esperado

Durante la ejecución aparecerá una salida similar a la siguiente:

```text
Bringing machine 'so-lab-node-01' up with 'virtualbox' provider...
Bringing machine 'so-lab-node-02' up with 'virtualbox' provider...

==> so-lab-node-01: Importing base box 'ubuntu/jammy64'...
...
so-lab-node-01: VM preparada para Ansible

==> so-lab-node-02: Importing base box 'ubuntu/jammy64'...
...
so-lab-node-02: VM preparada para Ansible
```

La primera ejecución puede tardar varios minutos, ya que será necesario descargar la imagen base de Ubuntu.

---

## Comprobar el estado de las máquinas

Una vez finalizado el despliegue:

```bash
vagrant status
```

La salida debería ser similar a:

```text
Current machine states:

so-lab-node-01    running (virtualbox)
so-lab-node-02    running (virtualbox)
```

---

## Acceder a una máquina virtual

Para conectarse mediante SSH a la primera máquina:

```bash
vagrant ssh so-lab-node-01
```

Para acceder a la segunda:

```bash
vagrant ssh so-lab-node-02
```

Para salir de la sesión SSH:

```bash
exit
```

---

## Comprobar la configuración SSH

Vagrant genera automáticamente la configuración necesaria para acceder mediante SSH.

Puede visualizarse con:

```bash
vagrant ssh-config
```

Esta información será utilizada posteriormente por Ansible para conectarse automáticamente a las máquinas virtuales.

---

## Comprobaciones básicas

Una vez dentro de cualquiera de las máquinas, ejecutar los siguientes comandos:

```bash
hostname
```

Muestra el nombre del servidor.

```bash
ip a
```

Muestra las interfaces de red y la dirección IP asignada.

```bash
free -h
```

Muestra la memoria disponible.

```bash
df -h
```

Muestra el espacio disponible en disco.

Estas comprobaciones permiten verificar que la máquina se ha creado correctamente y está lista para la siguiente fase del laboratorio.

---

## Finalizar esta práctica

Salir de la máquina virtual:

```bash
exit
```

Volver a la raíz del repositorio:

```bash
cd ..
```

En la siguiente práctica se utilizará **Ansible** para automatizar la configuración de ambas máquinas virtuales.