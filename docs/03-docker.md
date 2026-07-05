# 03 - Instalación de Docker

## Objetivo

En esta práctica instalaremos **Docker Engine** sobre las dos máquinas virtuales del laboratorio utilizando Ansible.

Docker nos permitirá ejecutar aplicaciones de forma aislada mediante contenedores, sin necesidad de crear nuevas máquinas virtuales.

En las siguientes prácticas utilizaremos Docker para desplegar toda la plataforma de monitorización.

---

## ¿Qué es Docker?

Docker es una plataforma de contenedores que permite empaquetar aplicaciones junto con todas sus dependencias.

A diferencia de una máquina virtual, un contenedor comparte el núcleo (kernel) del sistema operativo anfitrión, lo que hace que sea mucho más ligero y rápido de desplegar.

En este laboratorio Docker será utilizado para ejecutar:

- Node Exporter
- Prometheus
- Grafana

---

## Arquitectura

```text
                Equipo del alumno
                       │
                  Ansible
                       │
        ┌──────────────┴──────────────┐
        │                             │
        ▼                             ▼
   so-lab-node-01               so-lab-node-02
        │                             │
        ▼                             ▼
      Docker                        Docker
```

---

## Acceder al directorio de Ansible

Desde la raíz del repositorio:

```bash
cd ansible
```

---

## Ejecutar el Playbook

Ejecutar:

```bash
ansible-playbook playbooks/02-docker.yml
```

---

## ¿Qué realiza este Playbook?

Durante la ejecución Ansible llevará a cabo las siguientes tareas:

- Instalación de Docker Engine.
- Instalación de Docker Compose.
- Arranque del servicio Docker.
- Configuración del servicio para que se inicie automáticamente con el sistema.
- Incorporación del usuario `vagrant` al grupo `docker`.

---

## Resultado esperado

La salida final será similar a:

```text
PLAY RECAP

node01 : ok=4  changed=2  failed=0
node02 : ok=4  changed=2  failed=0
```

El número de tareas puede variar ligeramente dependiendo de la versión de Docker instalada, pero el campo **failed** deberá ser siempre igual a **0**.

---

## Comprobar la instalación

Verificar la versión de Docker instalada:

```bash
ansible lab -a "docker --version"
```

Resultado esperado:

```text
node01 | CHANGED | rc=0 >>
Docker version xx.x.x

node02 | CHANGED | rc=0 >>
Docker version xx.x.x
```

---

## Comprobar el estado del servicio

Verificar que Docker está ejecutándose:

```bash
ansible lab -a "systemctl status docker --no-pager"
```

También puede utilizarse una comprobación más sencilla:

```bash
ansible lab -a "systemctl is-active docker"
```

Resultado esperado:

```text
node01 | CHANGED | rc=0 >>
active

node02 | CHANGED | rc=0 >>
active
```

---

## Comprobar los contenedores

En este momento todavía no se ha desplegado ninguna aplicación.

Podemos comprobarlo ejecutando:

```bash
ansible lab -a "docker ps"
```

Resultado esperado:

```text
CONTAINER ID   IMAGE   COMMAND   CREATED   STATUS   PORTS   NAMES
```

No aparecerá ningún contenedor en ejecución.

Esto es completamente normal.

---

## Idempotencia

Si volvemos a ejecutar el mismo Playbook:

```bash
ansible-playbook playbooks/02-docker.yml
```

Ansible comprobará que Docker ya se encuentra instalado y únicamente realizará las acciones necesarias.

---

## Conceptos aprendidos

Durante esta práctica se han introducido los siguientes conceptos:

- Docker Engine.
- Docker Compose.
- Servicios de Linux.
- Contenedores.
- Automatización mediante Ansible.

---

## Finalizar esta práctica

Volver a la raíz del repositorio:

```bash
cd ..
```

En la siguiente práctica desplegaremos la plataforma de monitorización utilizando Docker sobre las máquinas configuradas.