FROM ubuntu:20.04

ARG mediainforepo="https://mediaarea.net/repo/deb/repo-mediaarea_1.0-19_all.deb"
ARG dovitoollink="https://github.com/quietvoid/dovi_tool/releases/download/1.4.6/dovi_tool-1.4.6-x86_64-unknown-linux-musl.tar.gz"
ARG hdr10plustoollink="https://github.com/quietvoid/hdr10plus_tool/releases/download/1.2.2/hdr10plus_tool-1.2.2-x86_64-unknown-linux-musl.tar.gz"
ARG mp4boxlink="https://github.com/gpac/gpac.git"
ARG mp4boxtag="v1.0.1"
ARG dotnetlink="https://download.visualstudio.microsoft.com/download/pr/546d50b2-d85c-433f-b13b-b896f1bc1916/17d7bbb674bf67c3d490489b20a437b7/dotnet-runtime-5.0.15-linux-x64.tar.gz"
ARG pgs2srtlink="https://github.com/Tentacule/PgsToSrt/releases/download/v1.4.2/PgsToSrt-1.4.2.zip"
ARG tesseractlink="https://github.com/tesseract-ocr/tessdata.git"

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
    mkvtoolnix && \
  printf "\n---Uninstall unneccassary tools and cleanup apt cache---\n\n" && \
  apt-get purge -y software-properties-common && \
  apt-get autoremove -y && \
  rm -rf \
    /var/lib/apt/lists/* \
    /var/tmp/*

# DOVI_TOOL
RUN \
  printf "\n---Download dovi_tool---\n\n" && \
  wget -O - ${dovitoollink} | \
  tar -zx -C /usr/local/bin/

# HDR10PLUS_TOOL
RUN \
  printf "\n---Download hdr10plus_tool---\n\n" && \
  wget -O - ${hdr10plustoollink} | \
  tar -zx -C /usr/local/bin/ && \
  mv /usr/local/bin/dist/* /usr/local/bin/

# MP4BOX
RUN \
  printf "\n---Download build utils---\n\n" && \
  apt-get update && \
  apt-get install -y build-essential pkg-config git && \
  apt-get install -y zlib1g-dev && \
  printf "\n---Build mp4box---\n\n" && \
  mkdir mp4box && \
  cd mp4box && \
  git clone --branch ${mp4boxtag} ${mp4boxlink} gpac_public && \
  cd gpac_public && \
  ./configure --static-bin && \
  make -j $(nproc) && \
  make install && \
  MP4Box -version && \
  cd ../.. && \
  printf "\n---Uninstall build utils and cleanup apt cache---\n\n" && \
  rm -Rf mp4box && \
  apt-get purge -y build-essential pkg-config git && \
  apt-get purge -y zlib1g-dev && \
  apt-get autoremove -y && \
  rm -rf \ 
    /var/lib/apt/lists/* \
    /var/tmp/*

# PGS2SRT
RUN \
  printf "\n---Download dotnet runtime---\n\n" && \
  mkdir /opt/dotnet && \
  wget -O - ${dotnetlink} | \
  tar -zx -C /opt/dotnet/ && \
  printf "\n---Install tesseract and git---\n\n" && \
  apt-get update && \
  apt-get install -y libtesseract4 unzip git && \
  printf "\n---Download pgs2srt---\n\n" && \
  mkdir /opt/PgsToSrt && \
  wget ${pgs2srtlink} -O temp.zip && \
  unzip -d /opt/PgsToSrt/ temp.zip && \
  rm temp.zip && \
  printf "\n---Download tesseract models---\n\n" && \
  cd /opt/PgsToSrt/net5 && \
  git clone ${tesseractlink} && \
  printf "\n---Uninstall unzip and git and cleanup apt cache---\n\n" && \
  apt-get purge -y unzip git && \
  apt-get autoremove -y && \
  rm -rf \ 
    /var/lib/apt/lists/* \
    /var/tmp/*

RUN \
  printf "\n---Create \"convert\" directory for volumne attachment---\n\n" && \
  mkdir /convert

VOLUME ["/convert"]

WORKDIR /convert

ENTRYPOINT ["dvmkv2mp4"]
