
# Laboratorio de Sistemas Operativos Modernos

## Objetivo

El objetivo de este laboratorio es introducir al alumno en los conceptos fundamentales de administración de sistemas modernos, combinando los conocimientos clásicos de sistemas operativos con herramientas de automatización e infraestructura utilizadas actualmente en entornos profesionales.

Durante el laboratorio se recorrerá el ciclo completo de vida de una infraestructura:

1. Creación de máquinas virtuales.
2. Configuración automática de sistemas.
3. Instalación de servicios.
4. Despliegue de contenedores.
5. Monitorización y observabilidad.

Al finalizar, el alumno habrá construido una pequeña plataforma completamente automatizada utilizando Vagrant, Ansible, Docker, Prometheus y Grafana.


## Arquitectura del laboratorio
                    Equipo del alumno
                            |
              +-------------+-------------+
              |                           |
              v                           v
         Vagrant                     Ansible
              |                           |
              |                     Configura
              |                           |
              +-------------+-------------+
                            |
                            v

          +--------------------------------------+
          |      VirtualBox / Hypervisor         |
          +--------------------------------------+
                    |                 |
                    |                 |
                    v                 v

              +-----------+     +-----------+
              |  node01   |     |  node02   |
              | Monitoring|     |  Worker   |
              +-----------+     +-----------+
                    |                 |
                    |                 |
                    +--------+--------+
                             |
                             v

                      Node Exporter

node01
- Docker
- Prometheus
- Grafana
- Node Exporter

node02
- Docker
- Node Exporter
###  node01:
- Docker
- Prometheus
- Grafana
- Node Exporter

### node02:
- Docker
- Node Exporter

⸻

## Parte 1 - Infraestructura con Vagrant

Vagrant es una herramienta de Infraestructura como Código orientada a la creación y gestión de máquinas virtuales. Permite describir una infraestructura mediante un fichero de texto (Vagrantfile) y reproducirla tantas veces como sea necesario.

Antes de herramientas como Vagrant, la creación de laboratorios requería crear las máquinas virtuales manualmente desde VirtualBox o VMware. Con Vagrant, el laboratorio queda definido como código y puede ser compartido, versionado y reconstruido de forma automática.

En este laboratorio, Vagrant será responsable únicamente de la creación de las máquinas virtuales. La configuración interna de los sistemas operativos será responsabilidad de Ansible.

### Objetivo

Aprender a crear máquinas virtuales de forma reproducible utilizando Infraestructura como Código.

En esta fase se desplegarán dos máquinas virtuales Ubuntu con direccionamiento IP estático y preparadas para ser administradas posteriormente mediante Ansible.

### Conceptos aprendidos

* Virtualización
* Infraestructura como Código
* Redes privadas
* SSH
* Automatización

### Desplegar las máquinas virtuales

```
vagrant up
```

Ver estado de las máquinas

```
vagrant status
```

Acceder a una máquina

```
vagrant ssh so-lab-node-01
vagrant ssh so-lab-node-02
```

Comprobar configuración SSH
```
vagrant ssh-config
```

⸻

## Parte 2 - Automatización con Ansible

### Objetivo

Aprender a configurar sistemas Linux de forma automática e idempotente.

Las tareas realizadas incluyen:

* Instalación de paquetes.
* Creación de usuarios.
* Gestión de directorios.
* Configuración del sistema.

### Conceptos aprendidos

* Inventario
* Playbooks
* Módulos
* Idempotencia
* Automatización

### Ver inventario

```
ansible-inventory --graph
```

Comprobar conectividad
```
ansible lab -m ping
```

### Ejecutar configuración base
```
ansible-playbook playbooks/01-base.yml
```

⸻

## Parte 3 - Instalación de Docker

### Objetivo

Automatizar la instalación de un motor de contenedores.

Docker permite ejecutar aplicaciones aisladas sin necesidad de desplegar nuevas máquinas virtuales.

### Conceptos aprendidos

* Contenedores
* Servicios Linux
* Gestión de grupos
* Docker Engine

### Ejecutar instalación

```
ansible-playbook playbooks/02-docker.yml
```

Verificar instalación
```
ansible lab -a "docker --version"
```


## Parte 4 - Despliegue Monitorización y Observabilidad

### Objetivo

Desplegar una plataforma de monitorización centralizada capaz de recopilar métricas de varios servidores.

### Componentes

#### Node Exporter

Recopila métricas del sistema operativo:

* CPU
* Memoria
* Disco
* Red
* Procesos

#### Prometheus

Recopila y almacena las métricas generadas por Node Exporter.

#### Grafana

Permite visualizar las métricas mediante dashboards.

#### Desplegar monitorización

```
ansible-playbook playbooks/03-monitoring.yml
```


#### Acceso a los servicios

##### Grafana

```
http://192.168.56.11:3000
```

Usuario:
```
admin

Contraseña:

admin
```

##### Prometheus

```
http://192.168.56.11:9090
```

##### Node Exporter

Node 1:
```
http://192.168.56.11:9100
```

Node 2:
```
http://192.168.56.12:9100
```

⸻
 
## Despliegue completo

Si se desea desplegar el laboratorio completo:
```
ansible-playbook playbooks/site.yml
```



## Parte 5  - Configuración Monitorización y Observabilidad

Hasta este punto hemos desplegado los componentes de monitorización mediante Ansible:

* Node Exporter
* Prometheus
* Grafana

Sin embargo, Grafana todavía no sabe dónde se encuentran las métricas almacenadas por Prometheus. En esta sección realizaremos dicha integración manualmente.


### Acceder a Grafana

Abrir un navegador y acceder a:
```
http://192.168.56.11:3000
```

Credenciales iniciales:

```
Usuario: admin
Contraseña: admin
```

En el primer acceso Grafana solicitará cambiar la contraseña.


### Verificar Prometheus

Antes de configurar Grafana, verificar que Prometheus está funcionando correctamente.

Acceder a:
```
http://192.168.56.11:9090
```

Seleccionar:

Status -> Targets

Deberían aparecer los siguientes objetivos en estado UP:
```
192.168.56.11:9100
192.168.56.12:9100
```

Si ambos aparecen en estado UP, Prometheus está recopilando métricas correctamente.



### Crear la Data Source de Prometheus

En Grafana:

```
Connections
    -> Data Sources
        -> Add data source
```

Seleccionar:

```
Prometheus
```

Configurar:
```
Name: Prometheus
URL:
http://prometheus:9090
```

Pulsar:
```
Save & Test
```

Debería mostrarse el mensaje:
```
Data source is working
```



### Importar Dashboard de Node Exporter

Grafana permite importar dashboards mediante ficheros JSON.

En este laboratorio se proporciona un dashboard ya preparado dentro del repositorio:

```
grafana/dashboards/node-exporter-full.json
```

#### Importar Dashboard

Acceder a Grafana:

http://192.168.56.11:3000

En el menú lateral:

Dashboards
    -> New
        -> Import

Arrastrar el fichero:

```
grafana/dashboards/node-exporter-full.json
```

o pulsar:

Upload dashboard JSON file

y seleccionarlo manualmente.

Seleccionar Data Source

Durante la importación Grafana solicitará la fuente de datos.
```
Seleccionar:

Prometheus
```

y pulsar:
```
Import

Validación
```

Una vez importado el dashboard deberían visualizarse métricas de los dos nodos del laboratorio:

node01
node02

Entre otras:

* CPU
* Memoria
* Disco
* Red
* Load Average
* Filesystem
* Procesos

Si las gráficas muestran información para ambos nodos, la integración entre Node Exporter, Prometheus y Grafana se ha realizado correctamente.


### Validar la monitorización

Una vez importado el dashboard deberían visualizarse métricas de:

* CPU
* Memoria
* Load Average
* Disco
* Filesystem
* Red
* Procesos

Tanto para:

node01
node02



Ejercicio Propuesto

Investigar las siguientes métricas:

1. ¿Qué porcentaje de CPU consume cada nodo?
2. ¿Cuánta memoria libre tiene cada servidor?
3. ¿Qué filesystem presenta mayor ocupación?
4. ¿Qué ocurre en Grafana al ejecutar un estrés de CPU?
5. ¿Qué ocurre al detener uno de los Node Exporter?

Documentar los resultados obtenidos.


Observa en tiempo real cómo evolucionan las gráficas de CPU en Grafana al ejecutar en un node02:

```
docker run --rm -it progrium/stress \
  --cpu 2 \
  --timeout 60
```





### Retos Propuestos

1. Añadir una tercera máquina virtual al laboratorio.
2. Incorporar la nueva máquina al inventario Ansible.
3. Configurar Prometheus para monitorizar el nuevo nodo.
4. Crear un dashboard personalizado en Grafana.
5. Desplegar una aplicación Docker propia.
6. Analizar el consumo de CPU y memoria de dicha aplicación.
7. Crear un nuevo playbook para desplegar servicios web.



### Tecnologías Utilizadas

* Vagrant
* VirtualBox
* Ubuntu Linux
* Ansible
* Docker
* Prometheus
* Grafana

Este laboratorio pretende mostrar la evolución desde la administración manual de sistemas hasta los modelos modernos de automatización e Infraestructura como Código utilizados actualmente en entornos empresariales.
