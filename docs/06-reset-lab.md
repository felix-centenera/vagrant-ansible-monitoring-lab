# 06 - Reiniciar el laboratorio

## Objetivo

Una de las ventajas de trabajar con Infraestructura como Código (IaC) es que los entornos pueden destruirse y reconstruirse tantas veces como sea necesario.

En esta práctica aprenderemos a detener, eliminar y volver a crear completamente el laboratorio.

---

# Detener las máquinas virtuales

Si únicamente queremos apagar las máquinas virtuales conservando su estado, utilizaremos:

```bash
cd Vagrant

vagrant halt
```

Resultado esperado:

```text
==> so-lab-node-01: Attempting graceful shutdown...
==> so-lab-node-02: Attempting graceful shutdown...
```

Las máquinas permanecerán creadas y podrán volver a arrancarse posteriormente mediante:

```bash
vagrant up
```

---

# Eliminar completamente el laboratorio

Si queremos empezar desde cero, podemos destruir todas las máquinas virtuales.

```bash
vagrant destroy
```

Vagrant solicitará confirmación:

```text
Are you sure you want to destroy the 'so-lab-node-01' VM? [y/N]
```

Responder:

```text
y
```

También puede hacerse sin confirmación:

```bash
vagrant destroy -f
```

---

# ¿Qué elimina Vagrant?

Al destruir el laboratorio se eliminan:

- Máquinas virtuales.
- Discos virtuales.
- Configuración de VirtualBox.
- Estado de las máquinas.

No se elimina:

- El repositorio Git.
- El Vagrantfile.
- Los Playbooks de Ansible.
- Los dashboards.
- La documentación.

---

# Volver a crear el laboratorio

Una vez eliminado, basta con ejecutar nuevamente:

```bash
vagrant up
```

Y posteriormente repetir las prácticas de Ansible:

```bash
cd ../ansible

ansible-playbook playbooks/01-base.yml
ansible-playbook playbooks/02-docker.yml
ansible-playbook playbooks/03-monitoring.yml
```

En pocos minutos el laboratorio volverá a estar completamente operativo.

---

# ¿Por qué destruir el laboratorio?

Recrear un entorno desde cero es una práctica muy habitual en entornos profesionales.

Permite:

- Verificar que toda la infraestructura puede desplegarse automáticamente.
- Detectar configuraciones manuales no documentadas.
- Validar los Playbooks de Ansible.
- Recuperar rápidamente un entorno de pruebas.
- Aprender a confiar en la automatización.

Uno de los principios fundamentales de la Infraestructura como Código es que un servidor no debe ser único o irrepetible: si es necesario, debe poder destruirse y volver a crearse de forma automática.