FROM registry.gitlab.com/paddy-hack/devuan:beowulf
LABEL maintainer=b9AcE

ENV PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin TINI_VERSION=v0.19.0

ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /sbin/tini

# Enable the beowulf repositories main, contrib and non-free for convenience, but remove all others to set a well known base.
# Make the tiny init executable as per the developer's GitHub-page and dyne's Dockerfile.
RUN /bin/echo "deb	http://deb.devuan.org/merged		beowulf		main contrib non-free" > /etc/apt/sources.list && \
/bin/chmod 00700 /sbin/tini

# Add a sudo-capable non-superuser "duser" with password set to "devdev".
# Display brief usage info on first instance login to user "duser", but only if it's an interactive login. If logging in using e.g. "bash -l", as many do in docker, neither /etc/motd nor /etc/issue get displayed, so the info is displayed though the login-scripts instead.
# Add a system-wide decent default alias for "ls".
RUN /usr/sbin/useradd -c "Devuan User" -G sudo,users -m -s /bin/bash -U -p '$6$iZRf1GvN4WwThS2f$xYmxMZF6sJBG1WuojhS0sx/tmw.XXP/5563OAEIB71Wb4BtbGdQexKOEoibQz24wQ4OdQWmfxXK1DQrfJ/t1I1' duser && \
/bin/echo -e "\\n\\nLogged in as: duser\\nDefault password: devdev\\nPassword should be changed if machine accessible via network, etc.\\nThis message is only displayed on first login on the machine.\\nHave a nice day!\\n\\n">>/home/duser/.firstlogin && \
/bin/echo 'if [ -f "/home/duser/.firstlogin" -a -v "PS1" ] ; then /bin/cat /home/duser/.firstlogin && /bin/rm -f /home/duser/.firstlogin; fi'>>/home/duser/.profile && \
/bin/echo "alias la='/bin/ls -halF --color=force'">>/etc/bash.bashrc

# Retrieve and install a reasonable system-wide default config-file for "tmux".
ADD https://gist.github.com/b9AcE/2a24fc4991a0431f022a61a415a9cbef/raw/0b2be63bc93eb7fcb49f29d2e894c667a997645d/tmux.conf /etc/tmux.conf
RUN /bin/chown root:root /etc/tmux.conf && \
/bin/chmod 00644 /etc/tmux.conf


# All apt-get related operations in a single RUN-statement as per docker's "best practices" documentation page.
# Unfortunately docker doesn't detect cache invalidation due to "apt-get update" signaling upgraded packages, so caching is explicitly disabled using the "ARG CACHEBUST=1" from there onwards.
# Before installing anything using the APT system, update "apt-utils" to ensure the current version is in use, then install "man-db" to ensure the manuals are usable as this is intended as an interactive use system.
# Set the default "python" to be version 3.x using the "alternatives" system to move away from the outdated version 2.x series.
# Install "youtube-dl" using a freshly updated "pip" to ensure getting the current versions of both.
ARG CACHEBUST=1
RUN DEBIAN_FRONTEND=noninteractive /usr/bin/apt-get update -y && \
DEBIAN_FRONTEND=noninteractive /usr/bin/apt-get install -y --no-install-recommends apt-utils && \
DEBIAN_FRONTEND=noninteractive /usr/bin/apt-get install -y --no-install-recommends man-db && \
/usr/bin/update-alternatives --force --all --skip-auto && \
DEBIAN_FRONTEND=noninteractive /usr/bin/apt-get dist-upgrade -y -q && \
DEBIAN_FRONTEND=noninteractive /usr/bin/apt-get install -y --allow-downgrades --no-install-recommends \
	whiptail dialog xz-utils wget git procps iproute2 tmux htop iftop acl netcat-openbsd \
	less bzip2 file net-tools vim-tiny adduser dmsetup inetutils-ping netbase python2 \
	python3 bash bash-builtins bash-completion openssh-client gnupg ca-certificates psmisc \
	sudo dnsutils whois python3-pip python-pip && \
DEBIAN_FRONTEND=noninteractive /usr/bin/apt-get clean && \
DEBIAN_FRONTEND=noninteractive /usr/bin/apt autoremove -y && \
/usr/bin/update-alternatives --install /usr/bin/python python /usr/bin/python2 10 && \
/usr/bin/update-alternatives --install /usr/bin/python python /usr/bin/python3 20 && \
/usr/bin/python -m pip install --upgrade --upgrade-strategy eager --compile pip && \
/usr/bin/python -m pip install --upgrade --upgrade-strategy eager --compile youtube-dl ffmpeg-normalize && \
/bin/chmod 00755 /usr/local/bin/youtube-dl && \
/bin/ln -s youtube-dl /usr/local/bin/ytdl && \
/bin/ln -s youtube-dl /usr/local/bin/y

ENTRYPOINT ["/sbin/tini", "--"]

# Using "login -f <user>" instead of e.g. "bash -l" to ensure utmp is updated.
CMD /bin/login -f duser
