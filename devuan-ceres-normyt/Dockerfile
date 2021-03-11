FROM b9ace/devuan-ceres:latest
LABEL maintainer=b9AcE

ENV PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin TINI_VERSION=v0.19.0

# Install a system-wide config-file for "youtube-dl" that by default uses aria2 parallel downloads and runs ffmpeg-normalize on everything by default (hint: providing other values for config-file defined arguments on the commandline will override the config-file).
ADD https://gist.githubusercontent.com/b9AcE/22ac08715806d38336a9fb37dfd6aa00/raw/24f25534a1f35fec862c5c2db6ec171646576703/youtube-dl.conf /etc/
RUN /bin/chown root:root /etc/youtube-dl.conf && \
/bin/chmod 00644 /etc/youtube-dl.conf

# All apt-get related operations in a single RUN-statement as per docker's "best practices" documentation page.
# Unfortunately docker doesn't detect cache invalidation due to "apt-get update" signaling upgraded packages, so caching is explicitly disabled using the "ARG CACHEBUST=1" from there onwards.
# Only packages additional to the base b9ace/devuan-ceres:latest image are to be listed here.
ARG CACHEBUST=1
RUN DEBIAN_FRONTEND=noninteractive /usr/bin/apt-get update -y && \
DEBIAN_FRONTEND=noninteractive /usr/bin/apt-get dist-upgrade -y -q && \
DEBIAN_FRONTEND=noninteractive /usr/bin/apt-get install -y --allow-downgrades --no-install-recommends \
	jq autoconf automake build-essential git-core libvorbis-dev pkg-config zlib1g-dev \
	nasm gcc yasm libssl-dev python3-pip aria2 subversion libtool libmpg123-dev libfdk-aac-dev libmp3lame-dev && \
/usr/bin/apt-get clean && \
/usr/bin/python -m pip install ffmpeg-normalize

# Compile FFmpeg with FDK AAC, etc included.
RUN mkdir /root/ffmpeg_sources && cd /root/ffmpeg_sources && \
git clone --depth 1 git://git.ffmpeg.org/ffmpeg.git && cd ffmpeg && \
PKG_CONFIG_PATH="/root/ffmpeg_build/lib/pkgconfig" \
./configure --prefix="/root/ffmpeg_build" --extra-cflags="-I/root/ffmpeg_build/include" \
	--extra-ldflags="-L/root/ffmpeg_build/lib" --bindir="/root/bin" --extra-libs="-ldl" \
	--enable-gpl --enable-version3 --enable-nonfree \
	--enable-libfdk-aac --enable-libmp3lame --enable-openssl \
	--disable-ffplay --disable-ffprobe --disable-doc && \
make -j4 && make install && cp ffmpeg /usr/local/bin/ && \
rm -rf /root/ffmpeg_sources /root/ffmpeg_build

ENTRYPOINT ["/sbin/tini", "--"]

# Using "login -f <user>" instead of e.g. "bash -l" to ensure utmp is updated.
CMD /bin/login -f duser
