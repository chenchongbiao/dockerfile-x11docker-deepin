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

FROM linuxdeepin/deepin:latest

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
    env DEBIAN_FRONTEND=noninteractive apt-get install -y \
        dde-api-proxy \
        dde-application-manager \
        dde-clipboard \
        dde-control-center \
        dde-daemon \
        dde-desktop \
        dde-shell \
        dde-file-manager \
        dde-launchpad \
        dde-permission-manager \
        dde-qt5integration \
        dde-qt6integration \
        dde-session \
        dde-session-shell \
        dde-session-ui \
        deepin-default-settings \
        deepin-desktop-base \
        deepin-wallpapers-nonfree \
        deepin-desktop-schemas \
        deepin-desktop-theme \
        deepin-icon-theme \
        deepin-kwin-x11 \
        deepin-terminal \
        lastore-daemon \
        startdde  \
        deepin-osconfig && \
    /cleanup

# deepin-desktop-environment-extras
RUN apt-get update && \
    env DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        dcc-network-plugin \
        dss-network-plugin \
        deepin-sync-daemon \
        dde-api \
        dde-disk-mount-plugin \
        deepin-system-monitor \
        dde-widgets \
        systemd-timesyncd \
        libimageeditor \
        uimg-installer \
        fonts-noto \
        deepin-app-store \
        deepin-home-appstore-daemon \
        deepin-app-store-runtime \
        deepin-anything-server \
        dde-desktop-plugins \
        dde-file-manager-plugins \
        dde-file-manager-common-plugins \
        dde-file-manager-daemon-plugins \
        dde-file-manager-preview-plugins \
        uos-ai && \
    /cleanup

# 添加输入法支持
ENV XMODIFIERS=@im=fcitx QT4_IM_MODULE=fcitx QT_IM_MODULE=fcitx GTK_IM_MODULE=fcitx
RUN apt-get update && \
    env DEBIAN_FRONTEND=noninteractive apt-get install -y \
        fcitx5-frontend-qt6 \
        fcitx5-pinyin-gui \
        fcitx5-unikey && \
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
# RUN sed -i '/WatchdogSec=3min/{n;s/^/XDG_SESSION_TYPE=x11\n/}' /lib/systemd/system/systemd-logind.service
CMD ["dde-session"]
