FROM debian:jessie

LABEL maintainer = "Dmitry Zarva <zarv1k@gmail.com>"

ARG steam_user
ARG steam_password
ARG amxmod_version

RUN useradd -ms /bin/bash cstrike

RUN apt update && apt install -y lib32gcc1 curl \
    && chown cstrike:cstrike /opt

USER cstrike

# Install SteamCMD
RUN mkdir -p /opt/steam && cd /opt/steam && \
    curl -sqL "https://steamcdn-a.akamaihd.net/client/installer/steamcmd_linux.tar.gz" | tar zxvf -

# Install HLDS
RUN mkdir -p /opt/hlds
# There is a bug, described here: https://developer.valvesoftware.com/wiki/SteamCMD - Bug: 	HLDS (appid 90) currently requires multiple runs of the app_update command before all the required files are successfully installed. Simply run app_update 90 validate multiple times until no more updates take place.
# Seems the workaround for "app_update 90" bug does not work anymore, (see https://forums.alliedmods.net/showthread.php?p=2518786 as the source of previous workaround)
# So for now, just try to run "app_update 90 validate" a few times with ignoring errors for downloading the appid 90 completely
RUN /opt/steam/steamcmd.sh +login $steam_user $steam_password +force_install_dir /opt/hlds +app_update 90 validate +quit || :
RUN /opt/steam/steamcmd.sh +login $steam_user $steam_password +force_install_dir /opt/hlds +app_update 90 validate +quit || :
RUN /opt/steam/steamcmd.sh +login $steam_user $steam_password +force_install_dir /opt/hlds +app_update 90 validate +quit || :
RUN /opt/steam/steamcmd.sh +login $steam_user $steam_password +force_install_dir /opt/hlds +app_update 70 validate +quit || :
RUN /opt/steam/steamcmd.sh +login $steam_user $steam_password +force_install_dir /opt/hlds +app_update 10 validate +quit || :
RUN /opt/steam/steamcmd.sh +login $steam_user $steam_password +force_install_dir /opt/hlds +app_update 90 validate +quit
RUN mkdir -p ~/.steam && ln -s /opt/hlds ~/.steam/sdk32
RUN ln -s /opt/steam/ /opt/hlds/steamcmd
COPY --chown=cstrike:cstrike image/hlds-amxmodx/steam_appid.txt /opt/hlds/steam_appid.txt

# Install metamod
RUN mkdir -p /opt/hlds/cstrike/addons/metamod/dlls
COPY --chown=cstrike:cstrike mod/metamod /opt/hlds/cstrike/addons/metamod/dlls
COPY --chown=cstrike:cstrike image/hlds-amxmodx/liblist.gam /opt/hlds/cstrike/liblist.gam

# Install AMX mod X
RUN curl -sqL "http://www.amxmodx.org/release/amxmodx-$amxmod_version-base-linux.tar.gz" | tar -C /opt/hlds/cstrike/ -zxvf -
RUN curl -sqL "http://www.amxmodx.org/release/amxmodx-$amxmod_version-cstrike-linux.tar.gz" | tar -C /opt/hlds/cstrike/ -zxvf -

# Install dproto
RUN mkdir -p /opt/hlds/cstrike/addons/dproto
COPY --chown=cstrike:cstrike mod/dproto/dproto_i386.so /opt/hlds/cstrike/addons/dproto/dproto_i386.so

USER root
RUN apt remove -y curl
USER cstrike

WORKDIR /opt/hlds

ENTRYPOINT ["./hlds_run"]
