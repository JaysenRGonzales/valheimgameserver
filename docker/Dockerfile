FROM ubuntu:20.04

ENV DEBIAN_FRONTEND=noninteractive

# Install base dependencies

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Install UTF-8 unicode
RUN echo "**** Install UTF-8 ****" \
    && apt-get update \
    && apt-get install -y locales apt-utils debconf-utils
RUN localedef -i en_US -c -f UTF-8 -A /usr/share/locale/locale.alias en_US.UTF-8

## Upgrade Ubuntu
RUN echo "**** apt upgrade ****" \
    && apt-get update; \
    apt-get upgrade -y

## Install Base LinuxGSM Requirements
RUN echo "**** Install Base LinuxGSM Requirements ****" \
    && apt-get update \
    && apt-get install -y software-properties-common \
    && add-apt-repository multiverse \
    && apt-get update \
    && apt-get install -y \
    bc \
    binutils \
    bsdmainutils \
    bzip2 \
    ca-certificates \
	cron \
    cpio \
    dos2unix\
    curl \
    distro-info \
    file \
    gzip \
    hostname \
    jq \
    lib32gcc1 \
    lib32stdc++6 \
    netcat \
    python3 \
    tar \
    tmux \
    unzip \
    util-linux \
    wget \
    xz-utils \
    # Docker Extras
    cron \
    iproute2 \
    iputils-ping \
    nano \
    vim \
    sudo \
    tini

# Install SteamCMD
RUN echo "**** Install SteamCMD ****" \
    && echo steam steam/question select "I AGREE" | debconf-set-selections \
    && echo steam steam/license note '' | debconf-set-selections \
    && dpkg --add-architecture i386 \
    && apt-get update -y \
    && apt-get install -y --no-install-recommends \
    libatomic1 \
    libpulse0 \
    libpulse-dev \
    libc6-dev \
    libsdl2-2.0-0:i386 \
    steamcmd \
    && ln -s /usr/games/steamcmd /usr/bin/steamcmd

# Install NodeJS
RUN echo "**** Install NodeJS ****" \
    && curl -sL https://deb.nodesource.com/setup_16.x | bash - \
    && apt-get update && apt-get install -y nodejs

# Install GameDig https://docs.linuxgsm.com/requirements/gamedig
RUN echo "**** Install GameDig ****" \
    && npm install -g gamedig

# Install Cleanup
RUN echo "**** Cleanup ****"  \
    && apt-get -y autoremove \
    && apt-get -y clean \
    && rm -rf /var/lib/apt/lists/* \
    && rm -rf /tmp/* \
    && rm -rf /var/tmp/*

##Need use xterm for LinuxGSM##

ENV DEBIAN_FRONTEND=noninteractive

ARG USERNAME=linuxgsm
ARG USER_UID=1000
ARG USER_GID=$USER_UID

# Install linuxgsm
RUN wget https://raw.githubusercontent.com/GameServerManagers/LinuxGSM/master/linuxgsm.sh

RUN set -ex; \
    groupadd -g 750 -o linuxgsm; \
    adduser --uid 750 --disabled-password --gecos "" --ingroup linuxgsm linuxgsm; \
    chown linuxgsm:linuxgsm /linuxgsm.sh; \
    chmod +x /linuxgsm.sh; \
    cp /linuxgsm.sh /home/linuxgsm/linuxgsm.sh; \
    usermod -G tty linuxgsm; \
    chown -R linuxgsm:linuxgsm /home/linuxgsm/; \
    chmod 755 /home/linuxgsm

USER linuxgsm

WORKDIR /home/linuxgsm

ENV TERM=xterm
ENV PATH=$PATH:/home/linuxgsm


# Install vhserver
USER root
COPY ./vhserverstart.sh .
RUN  chmod +x vhserverstart.sh;
RUN dos2unix vhserverstart.sh;
RUN apt update -y ;
RUN dpkg --add-architecture i386; apt update; apt install distro-info libpulse-dev pigz -y
RUN apt install uuid-runtime -y 

USER linuxgsm

RUN echo 127 | ./linuxgsm.sh install; \
    printf "Y\nY\nY\n" | vhserver install; \
    mkdir valheimgameserverbackup;

COPY ./vhserver.cfg ./lgsm/config-lgsm/vhserver/

USER root
RUN chgrp linuxgsm /home/linuxgsm/lgsm/config-lgsm/vhserver/vhserver.cfg 
RUN chown linuxgsm /home/linuxgsm/lgsm/config-lgsm/vhserver/vhserver.cfg
RUN dos2unix /home/linuxgsm/lgsm/config-lgsm/vhserver/vhserver.cfg

# Container config
USER linuxgsm
EXPOSE 2456-2458

# # Install linuxgsm

# RUN wget https://raw.githubusercontent.com/GameServerManagers/LinuxGSM/master/linuxgsm.sh

# RUN set -ex; \
#     groupadd -g 750 -o linuxgsm; \
#     adduser --uid 750 --disabled-password --gecos "" --ingroup linuxgsm linuxgsm; \
#     chown linuxgsm:linuxgsm /linuxgsm.sh; \
#     chmod +x /linuxgsm.sh; \
#     cp /linuxgsm.sh /home/linuxgsm/linuxgsm.sh; \
#     usermod -G tty linuxgsm; \
#     chown -R linuxgsm:linuxgsm /home/linuxgsm/; \
#     chmod 755 /home/linuxgsm

# USER linuxgsm

# WORKDIR /home/linuxgsm

# ENV TERM=xterm
# ENV PATH=$PATH:/home/linuxgsm
# ENV value=127

# # Install steamcmd

# USER root
# RUN apt update -y ;
# RUN dpkg --add-architecture i386; apt update; apt install distro-info libpulse-dev pigz -y

# #install dos2unix 
# RUN apt-get install dos2unix;

# RUN echo steam steam/question select "I AGREE" | debconf-set-selections; \
#     echo steam steam/license note '' | debconf-set-selections; \
#     dpkg --add-architecture i386; \
#     apt-get update -y; \
#     apt-get install -q -y --no-install-recommends steamcmd; \
#     ln -sf /usr/games/steamcmd /usr/bin/steamcmd; \
#     apt-get autoremove -q -y; \
#     rm -rf /var/lib/apt/lists/*;

# USER linuxgsm
# COPY ./vhserverstart.sh .

# USER root
# #convert DOS to UNIX like
# RUN chmod +x vhserverstart.sh;
# RUN dos2unix vhserverstart.sh;
# RUN dos2unix -r /home/linuxgsm/

# USER linuxgsm
# RUN chmod +x /home/linuxgsm/linuxgsm.sh;
# # 127 is valheim server install
# RUN echo "127" | /home/linuxgsm/linuxgsm.sh install; 
# RUN yes | vhserver install; 
# RUN mkdir valheimgameserverbackup;


# COPY ./vhserver.cfg ./lgsm/config-lgsm/vhserver/
# USER root
# RUN chgrp linuxgsm /home/linuxgsm/lgsm/config-lgsm/vhserver/vhserver.cfg 
# RUN chown linuxgsm /home/linuxgsm/lgsm/config-lgsm/vhserver/vhserver.cfg

# USER linuxgsm
# # # Container config

# EXPOSE 2456-2458

ENTRYPOINT ["vhserverstart.sh"]
