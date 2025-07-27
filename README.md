# Proyecto InnovaSys - Configuración de Servidor DevOps con Ansible

Este repositorio contiene la configuración automatizada de un servidor Ubuntu 24.04 (`servidor-innovasys`) utilizando Ansible, cumpliendo con los requisitos del proyecto "InnovaSys". El playbook despliega un servidor web Apache para un portal de intranet y un servidor de archivos Samba para un repositorio centralizado.

## Estructura del Proyecto

El proyecto está organizado en roles de Ansible para garantizar modularidad y reusabilidad:

* `playbook.yml`: El playbook principal que orquesta la ejecución de los roles.
* `inventory.ini`: El archivo de inventario que define los nodos gestionados.
* `roles/apache`: Contiene la lógica para instalar y configurar el servidor web Apache.
    * `tasks/main.yml`: Tareas de Apache.
    * `handlers/main.yml`: Handlers para Apache.
    * `templates/index.html.j2`: Plantilla Jinja2 para la página de bienvenida.
    * `defaults/main.yml`: Variables por defecto para el rol Apache (ej. `nombre_empresa`).
* `roles/samba`: Contiene la lógica para instalar y configurar el servidor de archivos Samba.
    * `tasks/main.yml`: Tareas de Samba.
    * `handlers/main.yml`: Handlers para Samba.
    * `templates/smb.conf.j2`: Plantilla Jinja2 para la configuración de Samba.
    * `defaults/main.yml`: Variables por defecto para el rol Samba.
* `vars/all.yml`: Archivo de variables globales (si aplica).

## Prerequisitos

Antes de ejecutar este playbook, asegúrate de tener lo siguiente configurado en tu **Nodo de Control (Linux Lite)**:

* **Ansible:** Versión 2.9 o superior (idealmente la que estás usando, `ansible [core 2.16.3]`). Puedes verificarla con `ansible --version`.
* **Conectividad SSH:** El nodo de control debe poder conectarse vía SSH al `servidor-innovasys` (Ubuntu 24.04). Asegúrate de tener una clave SSH configurada o la contraseña del usuario `operador` en el servidor gestionado.
* **Sudo:** El usuario que Ansible utiliza en el `servidor-innovasys` (`operador`) debe tener privilegios de `sudo` sin necesidad de una contraseña (sudoers `NOPASSWD`) o estar listo para proporcionar la contraseña cuando se le solicite.

## Cómo Ejecutar el Playbook

Sigue estos pasos para desplegar la configuración en tu `servidor-innovasys`:

1.  **Clonar el Repositorio:**
    Abre una terminal en tu nodo de control (Linux Lite) y clona este repositorio:
    ```bash
    git clone [https://github.com/Fernando682449/innova_sys_ansible.git](https://github.com/Fernando682449/innova_sys_ansible.git)
    ```

2.  **Navegar al Directorio del Proyecto:**
    Ingresa al directorio del proyecto clonado:
    ```bash
    cd innova_sys_ansible
    ```

3.  **Ejecutar el Playbook de Ansible:**
    Utiliza el siguiente comando para ejecutar el playbook. Se te pedirá la contraseña de `sudo` para el usuario remoto en el servidor gestionado (`BECOME password:`).

    ```bash
    ansible-playbook -i inventory.ini playbook.yml -K
    ```
    * `-i inventory.ini`: Especifica el archivo de inventario.
    * `playbook.yml`: Es el nombre de tu playbook principal.
    * `-K` (o `--ask-become-pass`): Le indica a Ansible que solicite la contraseña para elevar privilegios (sudo) en el servidor gestionado.

    **¡Importante!** Introduce la contraseña de `sudo` para el usuario que Ansible usa para conectarse al `servidor-innovasys` (probablemente `operador`).

## Verificación Post-Ejecución

Una vez que el playbook se complete sin errores (`failed=0` en el `PLAY RECAP`), puedes verificar la configuración:

1.  **Servidor Web (Apache):**
    Abre un navegador web y accede a la dirección IP de tu `servidor-innovasys` (ej. `http://192.168.56.11`). Deberías ver la página de bienvenida personalizada de InnovaSys.

2.  **Servidor de Archivos (Samba):**
    Desde tu gestor de archivos (Thunar) en Linux Lite, intenta conectar al recurso compartido Samba: `smb://<IP_SERVIDOR>/proyectos/` (ej. `smb://192.168.56.11/proyectos/`). Utiliza el usuario `devuser1` y la contraseña de Samba que configuraste. Deberías poder acceder y crear archivos dentro del directorio.

---

¡Disfruta automatizando con Ansible!
