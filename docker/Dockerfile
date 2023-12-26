FROM ubuntu:20.04

ARG mediainforepo="https://mediaarea.net/repo/deb/repo-mediaarea_1.0-19_all.deb"
ARG dovitoollink="https://github.com/quietvoid/dovi_tool/releases/download/2.1.0/dovi_tool-2.1.0-x86_64-unknown-linux-musl.tar.gz"
ARG hdr10plustoollink="https://github.com/quietvoid/hdr10plus_tool/releases/download/1.6.0/hdr10plus_tool-1.6.0-x86_64-unknown-linux-musl.tar.gz"
ARG mp4boxlink="https://github.com/gpac/gpac.git"
ARG mp4boxtag="v2.2.1"
ARG dotnetlink="https://download.visualstudio.microsoft.com/download/pr/48fbc600-8228-424e-aaed-52b7e601c277/c493b8ac4629341f1e5acc4ff515fead/dotnet-runtime-6.0.10-linux-x64.tar.gz"
ARG pgs2srtlink="https://github.com/Tentacule/PgsToSrt/releases/download/v1.4.2/PgsToSrt-1.4.2.zip"
ARG tesseractlink="https://github.com/tesseract-ocr/tessdata.git"
ARG ffmpeg6link="https://johnvansickle.com/ffmpeg/releases/ffmpeg-release-amd64-static.tar.xz"

COPY /dvmkv2mp4 /usr/local/bin

RUN chmod a+x /usr/local/bin/dvmkv2mp4

# Install MEDIAINFO MKVTOOLNIX FFMPEG WGET
RUN \
  printf "\n---Install mediainfo mkvtoolnix ffmpeg and wget---\n\n" && \
  apt-get update && \
  apt-get install -y software-properties-common wget && \
  add-apt-repository ppa:savoury1/ffmpeg4 && \
  wget -O /usr/share/keyrings/gpg-pub-moritzbunkus.gpg https://mkvtoolnix.download/gpg-pub-moritzbunkus.gpg && \
  echo "deb [signed-by=/usr/share/keyrings/gpg-pub-moritzbunkus.gpg] https://mkvtoolnix.download/ubuntu/ focal main" | tee -a /etc/apt/sources.list && \
  wget ${mediainforepo} -O temp.deb && \
  dpkg -i temp.deb && \
  rm temp.deb && \
  apt-get update && \
  apt-get install -y \
    ffmpeg \
    mediainfo \
    mkvtoolnix \
    jq \
    bc

# DOVI_TOOL
RUN \
  printf "\n---Download dovi_tool---\n\n" && \
  wget -O - ${dovitoollink} | \
  tar -zx -C /usr/local/bin/

# FFMPEG6
RUN \
  printf "\n---Download ffmpeg6---\n\n" && \
  wget -O - ${ffmpeg6link} | \
  tar -x -J -C /usr/local/bin/

# HDR10PLUS_TOOL
RUN \
  printf "\n---Download hdr10plus_tool---\n\n" && \
  wget -O - ${hdr10plustoollink} | \
  tar -zx -C /usr/local/bin/

# MP4BOX
RUN \
  printf "\n---Download build utils---\n\n" && \
  apt-get install -y build-essential pkg-config git && \
  apt-get install -y zlib1g-dev && \
  printf "\n---Build mp4box---\n\n" && \
  mkdir mp4box && \
  cd mp4box && \
  git clone --depth 1 --branch ${mp4boxtag} ${mp4boxlink} gpac_public && \
  cd gpac_public && \
  ./configure --static-bin && \
  make -j $(nproc) && \
  make install && \
  MP4Box -version && \
  cd ../.. && \
  rm -Rf mp4box

# PGS2SRT
RUN \
  printf "\n---Download dotnet runtime---\n\n" && \
  mkdir /opt/dotnet && \
  wget -O - ${dotnetlink} | \
  tar -zx -C /opt/dotnet/ && \
  printf "\n---Install tesseract and git---\n\n" && \
  apt-get install -y libtesseract4 unzip git && \
  printf "\n---Download pgs2srt---\n\n" && \
  mkdir /opt/PgsToSrt && \
  wget ${pgs2srtlink} -O temp.zip && \
  unzip -d /opt/PgsToSrt/ temp.zip && \
  rm temp.zip && \
  printf "\n---Download tesseract models---\n\n" && \
  cd /opt/PgsToSrt/net6 && \
  git clone --depth 1 ${tesseractlink} && \
  printf "\n---Uninstall unzip and git and cleanup apt cache---\n\n" && \
  apt-get purge -y software-properties-common build-essential pkg-config git zlib1g-dev unzip git && \
  apt-get autoremove -y && \
  rm -rf \ 
    /var/lib/apt/lists/* \
    /var/tmp/*

RUN \
  printf "\n---Create \"convert\" directory for volume attachment---\n\n" && \
  mkdir /convert

VOLUME ["/convert"]

WORKDIR /convert

ENTRYPOINT ["dvmkv2mp4"]
