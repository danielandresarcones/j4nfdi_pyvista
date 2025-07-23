FROM quay.io/jupyter/base-notebook:notebook-7.4.3

# Copy all files (e.g., environment.yml, notebooks)
COPY . ${HOME}
USER root

# Install system dependencies required for OpenGL and headless rendering
RUN apt-get update && apt-get install -y --no-install-recommends \
    libxft2 \
    libxrender1 \
    libxext6 \
    libgl1 \
    libglu1-mesa \
    libxi6 \
    libxinerama1 \
    libxcursor1 \
    libx11-6 \
    libxrandr2 \
    build-essential \
    xvfb \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Set correct ownership of home directory
RUN chown -R ${NB_UID}:${NB_GID} ${HOME}

USER ${NB_UID}

# Ensure conda is initialized properly
SHELL ["bash", "-c"]

# Create conda environment
RUN mamba env create -f binder/environment.yml && \
    conda clean --all -f -y && \
    echo "conda activate pyvista" >> ~/.bashrc

# Activate environment
ENV CONDA_DEFAULT_ENV=pyvista
ENV PATH=/opt/conda/envs/pyvista/bin:$PATH

# Install Jupyter kernel and support ipykernel
RUN pip install ipykernel && \
    conda run -n fenicsx-env python -m ipykernel install --user --name=pyvista --display-name "Python (pyvista)"

# Make sure PyVista uses the correct backend in notebooks
ENV PYVISTA_JUPYTER_BACKEND=pythreejs

# Run Jupyter with virtual X display (avoids X server errors)
CMD ["xvfb-run", "-s", "-screen 0 1280x800x24", "start-notebook.sh"]
