# Imagen base
FROM ubuntu:22.04

# Actualizar e instalar paquetes necesarios y sudo
RUN apt-get update && apt-get install -y \
    python3.10 \
    python3-pip \
    python3.10-venv \
    bash-completion \
    iputils-ping \
    net-tools \
    git \
    sudo \
    curl \
    wget \
    build-essential \
    nano \
    htop \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Crear el usuario dev_container, agregarlo al grupo sudo, y configurar sudo sin contraseña
RUN useradd -m -G sudo -s /bin/bash dev_container && \
    echo 'dev_container ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

# Habilitar autocompletado y mejoras en la terminal para el usuario dev_container
RUN echo "if [ -f /etc/bash_completion ]; then . /etc/bash_completion; fi" >> /home/dev_container/.bashrc && \
    echo "bind 'set show-all-if-ambiguous on'" >> /home/dev_container/.bashrc && \
    echo "bind 'set completion-ignore-case on'" >> /home/dev_container/.bashrc

# Cambiar al nuevo usuario
USER dev_container

# Establecer el directorio de trabajo
WORKDIR /home/dev_container

# Instalar herramientas de desarrollo adicionales para Python
RUN pip install --upgrade pip && \
    pip install virtualenv wheel setuptools

# Copiar la carpeta .devcontainer al directorio de trabajo del contenedor
COPY .devcontainer /home/dev_container/.devcontainer

# Copiar el archivo README.md al directorio de trabajo del contenedor
COPY README.md /home/dev_container/

# Comando por defecto para ejecutar el contenedor en modo interactivo con bash
CMD ["/bin/bash"]
