# x11docker/deepin
# 
# Run deepin desktop in a Docker container. 
# Use x11docker to run image: 
#   https://github.com/mviereck/x11docker 
#
# Run deepin desktop with:
#   x11docker --desktop --init=systemd -- --cap-add=IPC_LOCK -- x11docker/deepin
#
# Run single application:
#   x11docker x11docker/deepin deepin-terminal
#
# Options:
# Persistent home folder stored on host with   --home
# Share host file or folder with option        --share PATH
# Hardware acceleration with option            --gpu
# Clipboard sharing with option                --clipboard
# Language locale setting with option          --lang [=$LANG]
# Sound support with option                    --pulseaudio
# Printer support with option                  --printer
# Webcam support with option                   --webcam
#
# See x11docker --help for further options.

FROM linuxdeepin/beige:latest

ENV SHELL=/bin/bash
ENV LANG=en_US.UTF-8

# cleanup script for use after apt-get
RUN echo "#! /bin/sh\n\
env DEBIAN_FRONTEND=noninteractive apt-get autoremove -y\n\
apt-get clean\n\
find /var/lib/apt/lists -type f -delete\n\
find /var/cache -type f -delete\n\
find /var/log -type f -delete\n\
exit 0\n\
" > /cleanup && chmod +x /cleanup

# dde 桌面环境，从 https://github.com/deepin-community/deepin-desktop-environment/blob/master/debian/control 中查看
# deepin-desktop-environment-base
RUN apt-get update && \
    env DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        deepin-keyring \
        locales-all \
        mesa-utils \
        mesa-utils-extra \
        vim \
        sudo && \
    /cleanup

# deepin-desktop-environment-cli
RUN apt-get update && \
    env DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
    dbus-user-session \
    dbus-x11 \
    xdg-utils \
    apt-transport-https && \
    /cleanup

# deepin-desktop-environment-core
RUN apt-get update && \
    env DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        dde-clipboard \
        dde-control-center \
        dde-desktop \
        dde-dock \
        dde-file-manager \
        dde-launcher \
        dde-qt5integration \
        dde-session-ui \
        deepin-default-settings \
        deepin-wallpapers-nonfree \
        deepin-desktop-base \
        deepin-icon-theme \
        deepin-desktop-theme \
        fonts-noto \
        deepin-terminal \
        startdde  \
        deepin-kwin-x11 \
        deepin-kwin-wayland \
        dde-daemon \
        dde-session-shell \
        dde-session \
        dde-permission-manager \
        dde-application-manager \
        dde-api-proxy && \
    /cleanup

# deepin-desktop-environment-extras
RUN apt-get update && \
    env DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        dde-dconfig-daemon \
        dcc-network-plugin \
        dock-network-plugin \
        dss-network-plugin \
        deepin-sync-daemon \
        dde-api \
        dde-disk-mount-plugin \
        dde-dock-onboard-plugin \
        deepin-system-monitor \
        dde-widgets \
        systemd-timesyncd \
        libimageeditor \
        uimg-installer \
        deepin-app-store \
        deepin-home-appstore-daemon \
        deepin-app-store-runtime \
        deepin-anything-server \
        dde-desktop-plugins \
        dde-file-manager-plugins \
        dde-file-manager-common-plugins \
        dde-file-manager-daemon-plugins \
        dde-file-manager-preview-plugins && \
    /cleanup

# 添加输入法支持
ENV XMODIFIERS=@im=fcitx QT4_IM_MODULE=fcitx QT_IM_MODULE=fcitx GTK_IM_MODULE=fcitx
RUN apt-get update && \
    env DEBIAN_FRONTEND=noninteractive apt-get install -y \
        fcitx-sunpinyin && \
    mkdir -p /etc/xdg/autostart && \
    echo "[Desktop Entry]\n\
Encoding=UTF-8\n\
Version=0.9.4\n\
Type=Application\n\
Name=fcitx\n\
Comment=\n\
Exec=/usr/bin/fcitx-autostart\n\
" > /etc/xdg/autostart/fcitx.desktop && \
    /cleanup && \
    rm /cleanup

# RUN sed -i '1iexport XDG_SESSION_TYPE=x11' 文件名

RUN apt-get update && \
    env DEBIAN_FRONTEND=noninteractive apt-get install -y \
        xinit
# RUN echo 'export XDG_SESSION_TYPE=x11' >> /etc/skel/.bashrc
# RUN echo "export XDG_SESSION_TYPE=x11" >> /etc/X11/Xsession
# RUN echo 'export PATH=$XDG_SESSION_TYPE:$PATH' >> /etc/skel/.bashrc
# RUN echo "#!/bin/bash\n\
#     export XDG_SESSION_TYPE=x11\n\
#     dde-session" > /etc/rc.local && \
#     chmod +x /etc/rc.local
# 给session会话添加x11变量
RUN sed -i '/WatchdogSec=3min/{n;s/^/XDG_SESSION_TYPE=x11\n/}' /lib/systemd/system/systemd-logind.service
CMD ["dde-session"]
