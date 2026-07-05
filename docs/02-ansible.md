# 02 - Automatización de la configuración con Ansible

## Objetivo

En esta segunda parte del laboratorio utilizaremos **Ansible** para automatizar la configuración de las máquinas virtuales creadas con Vagrant.

Hasta ahora únicamente disponemos de dos servidores Ubuntu recién instalados. A partir de este momento comenzaremos a configurarlos de forma automática, evitando realizar tareas manuales en cada uno de ellos.

---

## ¿Qué es Ansible?

Ansible es una herramienta de automatización que permite administrar uno o varios servidores desde un único equipo de control.

A diferencia de otras herramientas de configuración, Ansible:

- No necesita instalar agentes en los servidores.
- Utiliza SSH para conectarse a las máquinas.
- Ejecuta las tareas descritas en archivos YAML llamados **Playbooks**.
- Es **idempotente**, es decir, ejecutar el mismo Playbook varias veces produce siempre el mismo resultado.

En este laboratorio utilizaremos nuestro equipo local como **Control Node**, desde el que administraremos ambos servidores.

---

## Arquitectura

```text
                    Equipo del alumno
                           │
                    Ansible Control Node
                           │
            ┌──────────────┴──────────────┐
            │                             │
            ▼                             ▼
       so-lab-node-01               so-lab-node-02
          (Monitoring)                 (Worker)
```

---

## Acceder al directorio de Ansible

Desde la raíz del repositorio:

```bash
cd ansible
```

---

## Inventario

Ansible necesita conocer qué servidores debe administrar.

Para ello utilizaremos el fichero:

```text
inventory.ini
```

En él se encuentran definidos:

- El grupo **monitoring**.
- El grupo **workers**.
- El grupo **lab**, que engloba ambos grupos.

Esta organización nos permitirá ejecutar Playbooks sobre todos los servidores o únicamente sobre un grupo concreto.

---

## Comprobar el inventario

Ejecutar:

```bash
ansible-inventory --graph
```

Debería mostrarse una estructura similar a:

```text
@all
 ├── @ungrouped
 └── @lab
      ├── @monitoring
      │    └── node01
      └── @workers
           └── node02
```

---

## Comprobar conectividad

Antes de ejecutar cualquier Playbook es recomendable comprobar que Ansible puede conectarse correctamente a todas las máquinas.

Ejecutar:

```bash
ansible lab -m ping
```

Resultado esperado:

```text
node01 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}

node02 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

Si ambos servidores responden con **pong**, la conectividad SSH es correcta y podemos continuar.

---

## Ejecutar el Playbook de configuración base

El primer Playbook prepara las máquinas para el resto del laboratorio.

Ejecutar:

```bash
ansible-playbook playbooks/01-base.yml
```

---

## ¿Qué hace este Playbook?

Durante su ejecución se realizan las siguientes tareas:

- Actualización de la caché de paquetes.
- Instalación de herramientas básicas del sistema.
- Creación del usuario `alumno`.
- Creación del directorio `/opt/sistemas-lab`.
- Creación de un fichero informativo del laboratorio.
- Verificación del nombre de cada servidor.

---

## Resultado esperado

Al finalizar la ejecución deberá aparecer una salida similar a:

```text
PLAY RECAP

node01 : ok=7  changed=4  failed=0
node02 : ok=7  changed=4  failed=0
```

Es importante comprobar que el número de tareas **failed** es igual a **0**.

---

## ¿Qué significa el PLAY RECAP?

Al finalizar cada Playbook, Ansible muestra un resumen del resultado obtenido.

Por ejemplo:

```text
ok=7
changed=4
failed=0
```

Significado de cada campo:

| Campo | Descripción |
|--------|-------------|
| ok | Tareas ejecutadas correctamente. |
| changed | Tareas que han realizado modificaciones en el sistema. |
| failed | Tareas que han producido un error. |
| skipped | Tareas omitidas. |

---

## Idempotencia

Una de las características más importantes de Ansible es la **idempotencia**.

Si volvemos a ejecutar exactamente el mismo Playbook:

```bash
ansible-playbook playbooks/01-base.yml
```

la mayoría de las tareas ya no necesitarán realizar cambios.

Esto significa que Ansible comprobará primero el estado del sistema antes de modificarlo.

Gracias a este comportamiento es posible ejecutar un mismo Playbook tantas veces como sea necesario sin provocar configuraciones duplicadas o inconsistentes.

---

## Verificaciones manuales

Podemos comprobar que las máquinas han sido configuradas correctamente ejecutando algunos comandos desde Ansible.

Mostrar el hostname de ambos servidores:

```bash
ansible lab -a "hostname"
```

Comprobar el usuario creado:

```bash
ansible lab -a "id alumno"
```

Comprobar el directorio creado:

```bash
ansible lab -a "ls -ld /opt/sistemas-lab"
```

---

## Finalizar esta práctica

Volver a la raíz del repositorio:

```bash
cd ..
```

En la siguiente práctica instalaremos **Docker** automáticamente sobre ambos servidores utilizando un nuevo Playbook de Ansible.

| Práctica | Descripción |
|----------|-------------|
| **03** | [Instalación de Docker](03-docker.md) |
