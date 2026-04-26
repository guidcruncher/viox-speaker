FROM alpine:latest AS base
ARG TZ=UTC

ENV SPEAKER_NAME="VIOX Music Server"
ENV VIOX_HOST=tcp://127.0.0.1:1704
 
RUN <<EOF
addgroup -S appgroup -g 1001
addgroup -S sudo
getent group sudo
adduser -S appuser -u 1001 -G appgroup -h /home/appuser -D
addgroup appuser sudo

mkdir -p /run/user /tmp /etc/runlevels/default /etc/runlevels/sysinit \
  /etc/runlevels/boot /etc/runlevels/shutdown \
  /etc/runlevels/hotplugged /etc/runlevels/manual \
  /var/lib/bluealsa /var/run/bluealsa /run/bluealsa
 
chmod 777 /tmp
chmod 700 /run/user
apk update
apk -U --no-cache add util-linux openrc mdevd-openrc sudo \
  alsa-tools alsa-utils alsaconf avahi libgcc gcompat alsa-lib \
  alsa-plugins icu-data-full curl nano jq xz gzip nodejs-current npm tzdata \
  su-exec envsubst sox caddy gettext jq procps socat snapcast-client tini

echo "uhid" > /etc/modules-load.d/uhid.conf

adduser root lp
adduser appuser lp
mkdir -p /run/openrc

touch /run/openrc/softlevel

echo "%sudo ALL = (ALL) NOPASSWD: ALL " >/etc/sudoers.d/nopasswd
echo "Defaults exempt_group = sudo " >>/etc/sudoers.d/nopasswd

sed -i '/getty/d' /etc/inittab

ln -f -s "/usr/share/zoneinfo/$TZ" '/etc/localtime'
echo "$TZ" | tee /etc/timezone
EOF

RUN mkdir -p /etc/bashrc.d

COPY ./config/asound.conf /etc/asound.conf
COPY ./scripts/* /usr/local/bin/
RUN chmod +x /usr/local/bin/*

ENTRYPOINT ["/sbin/tini", "--"]
CMD ["/usr/local/bin/entrypoint.sh"]
