ARG BASE_CONTAINER=jupyter/scipy-notebook
FROM $BASE_CONTAINER

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

USER root

ARG julia_version="1.5.3"
ARG julia_checksum="f190c938dd6fed97021953240523c9db448ec0a6760b574afd4e9924ab5615f1"

RUN apt-get update \
    && apt-get -y install --no-install-recommends apt-utils dialog 2>&1 \
    #
    # Install development tools
    && apt-get -y install\
       git openssh-client less iproute2 procps lsb-release fonts-dejavu\
       gfortran gcc g++\
       python3-dev python3-pip\
       libxml2-dev libxslt1-dev zlib1g-dev

# Install Python stuff
COPY requirements.txt ./
RUN pip --disable-pip-version-check --no-cache-dir install -r requirements.txt
    # Clean up
RUN apt-get autoremove -y \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/*

ENV JULIA_DEPOT_PATH=/opt/julia \
    JULIA_PKGDIR=/opt/julia \
    JULIA_VERSION="${julia_version}"

WORKDIR /tmp

# Download and Install Julia
RUN mkdir "/opt/julia-${JULIA_VERSION}" && \
    wget -q https://julialang-s3.julialang.org/bin/linux/x64/$(echo "${JULIA_VERSION}" | cut -d. -f 1,2)"/julia-${JULIA_VERSION}-linux-x86_64.tar.gz" && \
    echo "f190c938dd6fed97021953240523c9db448ec0a6760b574afd4e9924ab5615f1 *julia-${JULIA_VERSION}-linux-x86_64.tar.gz" | sha256sum -c - && \
    tar xzf "julia-${JULIA_VERSION}-linux-x86_64.tar.gz" -C "/opt/julia-${JULIA_VERSION}" --strip-components=1 && \
    rm "/tmp/julia-${JULIA_VERSION}-linux-x86_64.tar.gz"
RUN ln -fs /opt/julia-*/bin/julia /usr/local/bin/julia

# Prepare directory for julia packages
RUN mkdir /etc/julia && \
    echo "push!(Libdl.DL_LOAD_PATH, \"$CONDA_DIR/lib\")" >> /etc/julia/juliarc.jl && \
    # Create JULIA_PKGDIR \
    mkdir "${JULIA_PKGDIR}" && \
    chown "${NB_USER}" "${JULIA_PKGDIR}" && \
    fix-permissions "${JULIA_PKGDIR}"

# Update and Install Julia stuff
RUN julia -e 'import Pkg; Pkg.update()' && \
    (test $TEST_ONLY_BUILD || julia -e 'import Pkg; Pkg.add("HDF5")') && \
    julia -e 'using Pkg; \
              pkg"add Gadfly"; \
              pkg"add Convex"; \
              pkg"add RDatasets"; \
              pkg"add IJulia"; \
              pkg"add Ipopt"; \
              pkg"add JuMP"; \
              pkg"add Spectra"; \
              pkg"add SQLite"; \
              pkg"add LsqFit"; \
              pkg"add Optim"; \
              pkg"add NMF"; \
              pkg"add DataFrames"; \
              pkg"add JLD"; \
              pkg"add Plots"; \
              pkg"add PyCall"; \
              pkg"add PyPlot"; \
              pkg"add ExcelReaders"; \
              pkg"add ScikitLearn"; \
              pkg"add XGBoost"; \
              pkg"add LightGBM"; \
              pkg"add MLBase"; \
              pkg"add Lathe"; \
              pkg"add ShapML"; \
              pkg"add SCS"; \
              pkg"precompile"; \
              import Pkg; Pkg.update()' && \
    # move kernelspec out of home
    mv "${HOME}/.local/share/jupyter/kernels/julia"* "${CONDA_DIR}/share/jupyter/kernels/" && \
    chmod -R go+rx "${CONDA_DIR}/share/jupyter" && \
    rm -rf "${HOME}/.local" && \
    fix-permissions "${JULIA_PKGDIR}" "${CONDA_DIR}/share/jupyter"

USER $NB_UID

WORKDIR $HOME