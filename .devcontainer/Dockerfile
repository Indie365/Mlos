FROM mcr.microsoft.com/devcontainers/miniconda:3

# Add some additional packages for the devcontainer terminal environment.
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends \
        bash bash-completion \
        less colordiff \
        curl jq \
        vim-nox neovim python3-pynvim \
        make \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Set some cache dirs to be owned by the vscode user even as we're currently
# executing as root to build the container image.
# NOTE: We do *not* mark these as volumes - it doesn't help rebuilding at all.

ARG PIP_CACHE_DIR=/var/cache/pip
ENV PIP_CACHE_DIR=/var/cache/pip
RUN mkdir -p ${PIP_CACHE_DIR} \
    && chown -R vscode:conda ${PIP_CACHE_DIR} \
    && chmod -R u=rwX,g=rwXs,o=rX ${PIP_CACHE_DIR}

ARG CONDA_PKGS_DIRS=/opt/conda/pkgs
ENV CONDA_PKGS_DIRS=/opt/conda/pkgs
RUN mkdir -p ${CONDA_PKGS_DIRS} \
    && chown -R vscode:conda ${CONDA_PKGS_DIRS} \
    && chmod -R u=rwX,g=rwXs,o=rX ${CONDA_PKGS_DIRS}

# Use the mamba solver (necessary for some quality of life speedups due to
# required packages to support Windows)
RUN umask 0002 \
    && /opt/conda/bin/conda install -v -y -n base conda-libmamba-solver \
    && /opt/conda/bin/conda config --set solver libmamba

# Update the base. This helps save space by making sure the same version
# python is used for both the base env and mlos_core env.
RUN umask 0002 \
    && /opt/conda/bin/conda update -v -y -n base -c defaults --all \
    && /opt/conda/bin/conda clean -v -y -a

# Install some additional editor packages for the base environment.
RUN umask 0002 \
    && /opt/conda/bin/conda run -n base pip install -U pynvim

# Setup (part of) the mlos_core environment in the devcontainer.
# NOTEs:
# - The mlos_core_deps.yml file is prepared by the prep-container-build script(s).
# - The rest happens during first container start once the source is available.
# See Also: updateContentCommand in .devcontainer/devcontainer.json
RUN mkdir -p /opt/conda/pkgs/cache/ && chown -R vscode:conda /opt/conda/pkgs/cache/
USER vscode
RUN /opt/conda/bin/conda init bash \
    && /opt/conda/bin/conda config --set solver libmamba
COPY --chown=vscode:conda mlos_core_deps.yml /tmp/conda-tmp/
RUN umask 0002 \
    && /opt/conda/bin/conda env update -n mlos_core -v -f /tmp/conda-tmp/mlos_core_deps.yml \
    && /opt/conda/bin/conda clean -v -y -a \
    && mkdir -p /opt/conda/pkgs/cache/ && chown -R vscode:conda /opt/conda/pkgs/cache/
RUN mkdir -p /home/vscode/.conda/envs \
    && ln -s /opt/conda/envs/mlos_core /home/vscode/.conda/envs/mlos_core