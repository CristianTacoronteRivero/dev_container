
# Proyecto de Desarrollo con Contenedor Dev

Este proyecto está configurado para utilizar un contenedor de desarrollo base definido mediante el fichero Dockerfile, con el objetivo de proporcionar un entorno de desarrollo replicable y fácil de configurar.

## Contenido

1. [Requisitos](#requisitos)
2. [Cómo iniciar el contenedor de desarrollo](#cómo-iniciar-el-contenedor-de-desarrollo)
3. [Comandos de uso](#comandos-de-uso)
4. [Descripción del entorno de trabajo](#descripción-del-entorno-de-trabajo)
5. [Credenciales de acceso](#credenciales-de-acceso)
6. [Extensiones y herramientas instaladas](#extensiones-y-herramientas-instaladas)
7. [Personalización](#personalización)

## Requisitos

Para utilizar este proyecto, necesitarás:

- Docker instalado en tu máquina.
- Visual Studio Code con la extensión [Remote - Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) instalada.

## Cómo iniciar el contenedor de desarrollo

1. **Clona el repositorio** en tu máquina local.

   ```bash
   git clone https://github.com/tu_usuario/tu_proyecto.git
   cd tu_proyecto
   ```

2. **Inicia Visual Studio Code** y abre la carpeta del proyecto.

3. **Abre el contenedor de desarrollo**. En Visual Studio Code, abre el comando de paleta `Ctrl+Shift+P` y selecciona "Remote-Containers: Rebuild andReopen Container". Esto construirá e iniciará el contenedor especificado en el archivo `devcontainer.json`.

   > **Nota**: El contenedor se construirá automáticamente usando el `Dockerfile` proporcionado, por lo que no necesitas construirlo manualmente.

   En el caso de que tengas el contenedor ya construidos y solamente quieras entrar, debes seleccionar la opción "Open Attached Container". Pero para ello, debes de asegurarte de que el contenedor está en ejecución para que lo identifique. Para ello, se puede usar el siguiente comando bash: `docker run -it -d --name dev-container --network host -v /ruta/local:/workspace/dev_container cristiantr/dev_container_image:latest`


## Comandos de uso

Dentro del contenedor de desarrollo, puedes ejecutar los comandos de Python y otras herramientas directamente desde la terminal de Visual Studio Code.

Ejemplo de comandos comunes:

```bash
# Ejecutar un script Python
python script.py

# Instalar dependencias adicionales si es necesario
pip install <nombre_de_paquete>

# Verificar el código con pylint
pylint script.py
```

## Descripción del entorno de trabajo

El contenedor base está configurado para que trabajes en el directorio `/workspace/dev_container`. Aquí es donde se debe desarrollar todo el código del proyecto.

Además, la configuración del contenedor monta tu directorio local como un volumen dentro del contenedor, permitiendo que los cambios en tu máquina local se sincronicen automáticamente con el contenedor. Si en el conteneor ampliado, usando como imagen base **cristiantr/dev_container_image:latest**, no se especifica que elementos copiar, al iniciar el contenedor de desarrolo mediante la extensión de VSCode, se copia todo el contexto especificado en el archivo `.devcontainer/devcontainer.json`.

El contenedor de desarrollo también está configurado con acceso de red mediante el parámetro `--network=host`, lo que te permite ejecutar aplicaciones que necesiten comunicación de red sin problemas.

## Credenciales de acceso

El contenedor de desarrollo utiliza el usuario `dev_container`. No es necesario ingresar una contraseña, ya que todo está preconfigurado para el acceso automático cuando inicias el contenedor desde Visual Studio Code.

## Extensiones y herramientas instaladas

El entorno de desarrollo incluye varias extensiones útiles para trabajar con Python y otros aspectos del proyecto. Entre ellas:

- `ms-python.python`: Extensión principal de Python.
- `ms-python.vscode-pylance`: Proporciona autocompletado y análisis estático.
- `ms-toolsai.jupyter`: Permite trabajar con notebooks Jupyter.
- `Codeium.codeium`: Autocompletado basado en inteligencia artificial.
- `njpwerner.autodocstring`: Ayuda a generar docstrings automáticamente.
- `mhutchie.git-graph`, `donjayamanne.githistory`: Herramientas para el control de versiones.
- Y más...

Además, se ha configurado el tema "One Dark Pro" para proporcionar una interfaz moderna y oscura en Visual Studio Code.

## Personalización

Si se desea modificar o agregar más extensiones, puedes hacerlo editando el archivo `devcontainer.json`. Por ejemplo, puedes agregar nuevas extensiones o cambiar configuraciones específicas del editor.

### Crear nuevo contenedor de desarrollo a partir del base

Para crear un nuevo contenedor de desarrollo a partir del base, se puede utilizar la extensión `Remote - Containers: Rebuild andReopen Container`. y especificar en el archivo `devcontainer.json` el nombre del dockerfile que deseas utilizar.

Por ejemplo, en el caso de que se desee crear un contenedor para ejecutar un proyecto MILP que use el solver GLPk, para ello se construiría en primer lugar el siguiente dockerfile llamado `glpk.Dockerfile`:

```dockerfile
# Utiliza imagen de Docker Hub como base
FROM cristiantr/dev_container_image:latest

# Actualizar los paquetes e instalar GLPK
RUN sudo apt-get update && \
    sudo apt-get install -y \
    glpk-utils \
    libglpk-dev

# Instalar el solver glpk
RUN pip install glpk

# Establecer el directorio de trabajo qcorrespondiente al proyecto
WORKDIR /home/dev_container/project_glpk

# Comando por defecto para ejecutar el contenedor en modo interactivo con bash
CMD ["/bin/bash"]
```

En este caso se está usando la imagen base ya preconfigurada en el archivo Dockerfile y que se encuentra en Docker Hub y, de esta forma, se logra obtener un contenedor de desarrollo para ejecutar un proyecto MILP con el solver GLPK.

Y en el caso del archivo de configuración `devcontainer.json` quedaría de la siguiente forma:

```json
{
    "name": "GLPK Development Container",
    "build": {
        "dockerfile": "glpk.Dockerfile",
        "context": ".."
    },
    "runArgs": [
        "--name=glpk-container",
        "--network=host"
    ],
    "customizations": {
        "vscode": {
            "extensions": [
                "ms-python.python",
                "ms-python.vscode-pylance",
                "ms-toolsai.jupyter",
                "yzhang.markdown-all-in-one",
                "Codeium.codeium",
                "ms-python.black-formatter",
                "njpwerner.autodocstring",
                "mhutchie.git-graph",
                "donjayamanne.githistory",
                "ms-python.isort",
                "ms-python.pylint",
                "ms-python.debugpy",
                "donjayamanne.python-extension-pack",
                "KevinRose.vsc-python-indent",
                "ShaneLiesegang.vscode-simple-icons-rev",
                "zhuangtongfa.material-theme"
            ],
            "settings": {
                "workbench.colorTheme": "One Dark Pro"
            }
        }
    },
    "remoteUser": "dev_container"
}
```

> **Nota importante**: Este proyecto utiliza una imagen base pública disponible en Docker Hub. Puedes encontrarla en: [cristiantr/dev_container_image](https://hub.docker.com/r/cristiantr/dev_container_image). Si necesitas modificar esta imagen, puedes clonar el repositorio o usarla como base para tus propios contenedores.
