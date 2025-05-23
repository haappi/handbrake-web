# Node ---------------------------------------------------------------------------------------------
FROM node:bookworm-slim AS main

ARG TARGETARCH

# Configure apt (referenced from the immich project at 'https://github.com/immich-app/base-images/tree/main')
RUN sed -i -e's/ main/ main contrib non-free non-free-firmware/g' /etc/apt/sources.list.d/debian.sources
# RUN sed -i -e's/ bookworm-updates/ bookworm-updates sid/g' /etc/apt/sources.list.d/debian.sources
# RUN touch /etc/apt/preferences.d/prferences && \
# 	echo "Package: *" >> /etc/apt/preferences.d/prferences && \
# 	echo "Pin: release a=unstable" >> /etc/apt/preferences.d/prferences && \
# 	echo "Pin-Priority: 450" >> /etc/apt/preferences.d/prferences
RUN apt update
RUN apt install -y wget
# Install mesa and intel media drivers
RUN if [ $TARGETARCH = "amd64" ]; then \
	apt install -y mesa-utils mesa-va-drivers mesa-vulkan-drivers intel-media-va-driver-non-free vainfo \
	; fi
# Install handbrake
RUN apt-get install -y autoconf automake build-essential cmake git libass-dev libbz2-dev libfontconfig-dev \
         libfreetype6-dev libfribidi-dev libharfbuzz-dev libjansson-dev liblzma-dev libmp3lame-dev libnuma-dev \
	 libogg-dev libopus-dev libsamplerate0-dev libspeex-dev libtheora-dev libtool libtool-bin libturbojpeg0-dev \
  libvorbis-dev libx264-dev libxml2-dev libvpx-dev m4 make meson nasm ninja-build patch python3 pkg-config tar zlib1g-dev
# RUN apt install -y handbrake-cli
RUN wget https://github.com/haappi/handbrake-web/raw/refs/heads/main/HandBrakeCLI -O /usr/local/bin/HandBrakeCLI && \
    chmod +x /usr/local/bin/HandBrakeCLI
# Install dumb-init
RUN apt install -y dumb-init

COPY worker /handbrake-web/worker
COPY shared /handbrake-web/shared
WORKDIR /handbrake-web/worker

# Install node dependencies
ENV NODE_ENV=production
RUN npm install

# Create directories
RUN mkdir /data && chown node /data && mkdir /video && chown node /data

# Default environment variables
ENV HANDBRAKE_MODE=worker
ENV DATA_PATH=/data
ENV VIDEO_PATH=/video

# Start application
ENTRYPOINT ["/usr/bin/dumb-init", "--"]
CMD ["npm", "run", "prod"]
