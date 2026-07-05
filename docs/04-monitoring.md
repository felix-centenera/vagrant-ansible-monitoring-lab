# 04 - Despliegue de la plataforma de monitorización

## Objetivo

En esta práctica desplegaremos una plataforma completa de monitorización utilizando Docker y Ansible.

La plataforma estará formada por tres componentes:

- **Node Exporter**, encargado de recopilar las métricas del sistema operativo.
- **Prometheus**, encargado de almacenar dichas métricas.
- **Grafana**, encargado de visualizarlas mediante dashboards.

Al finalizar esta práctica tendremos una infraestructura completamente desplegada, aunque todavía será necesario realizar una pequeña configuración manual de Grafana.

---

## Arquitectura

```text
                    Equipo del alumno
                           │
                       Ansible
                           │
          ┌────────────────┴────────────────┐
          │                                 │
          ▼                                 ▼
     so-lab-node-01                    so-lab-node-02
      (Monitoring)                       (Worker)
          │                                 │
          │                                 │
          │                         Node Exporter
          │                                 ▲
          │                                 │
          ▼                                 │
   ┌─────────────────┐                      │
   │  Prometheus     │──────────────────────┘
   └─────────────────┘
            │
            ▼
   ┌─────────────────┐
   │    Grafana      │
   └─────────────────┘
```

---

## Componentes

### Node Exporter

Node Exporter recopila información del sistema operativo y la expone mediante una interfaz HTTP.

Entre otras métricas recopila:

- CPU
- Memoria
- Disco
- Sistema de archivos
- Interfaces de red
- Procesos
- Carga del sistema (Load Average)

En este laboratorio se desplegará una instancia de Node Exporter en **cada máquina virtual**.

---

### Prometheus

Prometheus consulta periódicamente todos los Node Exporter y almacena sus métricas en una base de datos de series temporales.

En este laboratorio Prometheus únicamente se desplegará en **node01**.

---

### Grafana

Grafana será la herramienta encargada de representar visualmente todas las métricas almacenadas por Prometheus.

En esta práctica únicamente desplegaremos Grafana.

Su configuración se realizará en la siguiente práctica.

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
ansible-playbook playbooks/03-monitoring.yml
```

---

## ¿Qué realiza este Playbook?

Durante la ejecución Ansible realizará automáticamente las siguientes tareas:

### En ambos nodos

- Crear el directorio de trabajo para Node Exporter.
- Generar el fichero `docker-compose.yml`.
- Descargar la imagen Docker de Node Exporter.
- Iniciar el contenedor.

### Únicamente en node01

- Crear el directorio de monitorización.
- Generar la configuración de Prometheus.
- Crear el `docker-compose.yml`.
- Descargar las imágenes Docker de Prometheus y Grafana.
- Iniciar ambos contenedores.

---

## Resultado esperado

La salida final será similar a:

```text
PLAY RECAP

node01 : ok=9  changed=5  failed=0
node02 : ok=4  changed=2  failed=0
```

Es importante comprobar que el número de tareas **failed** sea igual a **0**.

---

## Comprobar los contenedores desplegados

Ejecutar:

```bash
ansible lab -a "docker ps"
```

Resultado esperado:

### node01

```text
prometheus
grafana
node-exporter
```

### node02

```text
node-exporter
```

Esto confirma que:

- Prometheus únicamente se ejecuta en el servidor de monitorización.
- Grafana únicamente se ejecuta en el servidor de monitorización.
- Node Exporter está desplegado en ambos servidores.

---

## Acceder a Prometheus

Abrir el navegador y acceder a:

```text
http://192.168.56.11:9090
```

Deberá mostrarse la página principal de Prometheus.

> ![Data Sources](../imgDoc/1prometheus.png)

---

## Verificar los Targets

En Prometheus acceder a:

```text
Status
    └── Targets
```

Deberán aparecer dos objetivos en estado **UP**.

- node01
- node02

Si ambos aparecen en estado **UP**, Prometheus está recopilando correctamente las métricas de ambos servidores.

![Data Sources](../imgDoc/2prometheustargets.png)

---

## Acceder a Grafana

Abrir el navegador y acceder a:

```text
http://192.168.56.11:3000
```

Credenciales iniciales:

```text
Usuario: admin
Contraseña: admin
```

En este momento Grafana estará instalada, pero todavía **no dispondrá de ninguna fuente de datos ni de ningún dashboard**.

Esta configuración se realizará en la siguiente práctica.

---

## Conceptos aprendidos

Durante esta práctica se han introducido los siguientes conceptos:

- Monitorización de sistemas.
- Exportación de métricas.
- Series temporales.
- Scraping mediante Prometheus.
- Contenedores Docker.
- Arquitectura centralizada de monitorización.

---

## Finalizar esta práctica

Volver a la raíz del repositorio:

```bash
cd ..
```

En la siguiente práctica conectaremos Grafana con Prometheus e importaremos un dashboard para visualizar las métricas de ambos servidores.