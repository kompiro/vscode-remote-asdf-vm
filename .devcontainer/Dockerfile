FROM ubuntu:bionic AS dev-base

ARG USER_UID=1000
ARG USER_GID=$USER_UID
ARG USERNAME=dev

ENV DEBIAN_FRONTEND=noninteractive

RUN echo "APT::Install-Recommends 0;" >> /etc/apt/apt.conf.d/01norecommends \
  && echo "APT::Install-Suggests 0;" >> /etc/apt/apt.conf.d/01norecommends \
  && apt update \
  && apt upgrade \
  && apt install -y \
    apt-utils \
    dialog \
    wget \
    ca-certificates \
    build-essential \
    less \
    automake \
    autoconf \
    libreadline-dev \
    libncurses-dev \
    libssl-dev \
    libyaml-dev \
    libxslt-dev \
    libffi-dev \
    libtool \
    unixodbc-dev \
    unzip \
    curl \
    git \
    vim \
    emacs \
    sudo \
    bsdmainutils \
    gpg \
    dirmngr \
  # Clean up
  && apt-get autoremove -y \
  && apt-get clean -y \
  && rm -rf /var/lib/apt/lists/* \
  && groupadd --gid $USER_GID $USERNAME \
  && useradd --uid $USER_GID --gid $USERNAME --shell /bin/bash --create-home --groups sudo $USERNAME \
  && echo "%${USERNAME}    ALL=(ALL)   NOPASSWD:    ALL" >> /etc/sudoers.d/${USERNAME} \
  && mkdir -p /home/$USERNAME/.vscode-server/extensions \
      /home/$USERNAME/.vscode-server-insiders/extensions \
  && chown -R $USERNAME \
      /home/$USERNAME/.vscode-server \
      /home/$USERNAME/.vscode-server-insiders

ENV DEBIAN_FRONTEND=dialog

FROM dev-base AS setup-asdf

ARG USERNAME=dev

RUN git clone https://github.com/asdf-vm/asdf.git /opt/asdf \
  && cd /opt/asdf \
  && git checkout "$(git describe --abbrev=0 --tags)" \
  && mkdir /opt/asdf-data \
  && chown -R $USERNAME /opt/asdf-data

FROM setup-asdf AS dev-env

ARG USERNAME=dev

USER $USERNAME
WORKDIR /home/$USERNAME
ENV HOME=/home/$USERNAME \
  TZ="JST-9" \
  ASDF_DIR="/opt/asdf" \
  ASDF_DATA_DIR="/opt/asdf-data"

RUN echo '\n. /opt/asdf/asdf.sh' >> ~/.bashrc \
  && echo '\n. /opt/asdf/completions/asdf.bash' >> ~/.bashrc \
  && echo 'legacy_version_file = yes' >> ~/.asdfrc

VOLUME ["${ASDF_DATA_DIR}"]

CMD [ "/bin/bash" ]