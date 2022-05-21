# dvmkv2mp4 - Convert any Dolby Vision/HDR10+ MKV to MP4 that runs on many devices

Converts any Dolby Vision (Profile 5, 7 Single Track, 7 Dual Track, 8) / HDR10+ mkv to mp4 (DV4, DV5, DV8) compatible with LG OLEDs, Nvidia Shield and possibly more tested with Emby on LG 77CX, 48CX, 65C8

Feuatures:
- autodetects source material and chooses proper workflow
- converting from any 5,7,8 DV profile to DV mp4
- converting from HDR10+ to DV8 mp4
- verifies HDR10+ metadata before conversion (lots of fake releases out there)
- converts any truehd, dts etc to high bitrate Dolby Digital Plus, copies without conversion supported tracks like ac3, eac3
- keeps chapters
- converts PGS subtitiles found to SRT subtitiles with PGSToSRT
- extracts all SRT/Subrip tracks to SRT files
- can create backup mkv with .asm extension (audio subs meta) that has the original audio (truehd etc) subtitles tracks, chapters but without video to safekeep for future comeback conversions to original mkv and not waste place as you can easily demux the mp4 video and mux it with that mkv to come back to original
- can filter and leave only desired language tracks

# Requirements
- ffmpeg 4.4
- **mp4box 1.0.1 - it's important to use this version otherwise script will fail**
- dovi_tool
- hdr10plus_tool
- mediainfo v21
- dotnet5 for PGS2SRT conversion
- 3xSize of free space for file you want to convert

# Requirements install on Ubuntu 20.04
```
# MEDIAINFO MKVTOOLNIX FFMPEG
sudo add-apt-repository ppa:savoury1/ffmpeg4
sudo wget -O /usr/share/keyrings/gpg-pub-moritzbunkus.gpg https://mkvtoolnix.download/gpg-pub-moritzbunkus.gpg
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/gpg-pub-moritzbunkus.gpg] https://mkvtoolnix.download/ubuntu/ focal main" | sudo tee -a /etc/apt/sources.list
wget https://mediaarea.net/repo/deb/repo-mediaarea_1.0-19_all.deb && sudo dpkg -i repo-mediaarea_1.0-19_all.deb && sudo apt-get update
sudo apt-get install ffmpeg mediainfo mkvtoolnix
# DOVI_TOOL
wget https://github.com/quietvoid/dovi_tool/releases/download/1.4.6/dovi_tool-1.4.6-x86_64-unknown-linux-musl.tar.gz
tar -zxf dovi_tool-1.4.6-x86_64-unknown-linux-musl.tar.gz
sudo mv dist/dovi_tool /usr/local/bin/
# HDR10PLUS_TOOL
wget https://github.com/quietvoid/hdr10plus_tool/releases/download/1.2.2/hdr10plus_tool-1.2.2-x86_64-unknown-linux-musl.tar.gz
tar -zxf hdr10plus_tool-1.2.2-x86_64-unknown-linux-musl.tar.gz
sudo mv dist/hdr10plus_tool /usr/local/bin/
# MP4BOX
sudo apt-get install build-essential pkg-config git
sudo apt-get install zlib1g-dev
git clone https://github.com/gpac/gpac.git gpac_public
cd gpac_public
git checkout v1.0.1 
./configure --static-bin
make
sudo make install
MP4Box -version # MAKE SURE IT SAYS 1.0.1
# PGS2SRT
wget https://download.visualstudio.microsoft.com/download/pr/546d50b2-d85c-433f-b13b-b896f1bc1916/17d7bbb674bf67c3d490489b20a437b7/dotnet-runtime-5.0.15-linux-x64.tar.gz
tar -zxf dotnet-runtime-5.0.15-linux-x64.tar.gz
sudo mkdir /opt/dotnet
sudo mv * /opt/dotnet
cd /opt
sudo apt install libtesseract4
sudo mkdir /opt/PgsToSrt
cd /opt/PgsToSrt
sudo wget https://github.com/Tentacule/PgsToSrt/releases/download/v1.4.2/PgsToSrt-1.4.2.zip
sudo unzip PgsToSrt-1.4.2.zip
cd net5
sudo git clone https://github.com/tesseract-ocr/tessdata.git
```

# Installation
Download dvmkv2mp4 make it executable and move
```
wget https://raw.githubusercontent.com/gacopl/dvmkv2mp4/main/dvmkv2mp4
chmod a+x dvmkv2mp4
mv dvmkv2mp4 /usr/local/bin/
```
# Usage
```
In directory containing Dolby Vision mkv simply run `dvmkv2mp4`it will process any mkvs found in that dir
Options:
-l | --langs          - filter audio and subtitle tracks by lang comma separated if not hit by filter keep all tracks
-a | --asm            - create audio-subs-meta mkv file
-r | --remove-source  - remove source video after conversion
-d | --debug          - keep intermediary conversion files
-v | --version        - print version

dvmkv2mp4 -l und,pol,eng -r -a # will process mkvs found in folder keep only undefined, Polish and English tracks, will remove source file once done and will create audio-subs-meta file for future needs
```

# Docker (experimental)
The following section assumes that you installed Docker on your machine. For further instruction to install Docker follow the official site: https://docs.docker.com/get-docker/

## Build Docker image
Currently the image works only on x64 Linux. Also note that the final image size will be close to 6 GB which is normal as it includes the Tesseract OCR models.

To build your Docker image run the following command:
```
docker build -t dvmkv2mp4  https://github.com/gacopl/dvmkv2mp4.git#main -f docker/Dockerfile
```
## Use the Docker image
The usage is similar to the normal variant but here you need to attach your folder where you would like to convert your files. You can also add the flags at the end of the command.

Example:
```
# docker run -it --rm -v <YOUR_FOLDER_PATH>:/convert dvmkv2mp4

docker run -it --rm -v /media/mkvvideos:/convert dvmkv2mp4 -l und,pol,eng -r -a
```
This example does the same which is mentioned in setion Usage.

# Roadmap
- fix sound delay in output mp4 when source mkv had audio streams with audio delay tag
- github action to build ready docker images for pulling
- on MP4Box fail rerun with -no-probe switch which works with stubborn releases
- helper scripts for Radarr, Sonarr to automatically run on import
- convert directly from Bluray bdmv mpls file (have it working in alpha state already)

# Shoutouts
* to @quietvoid for dovi_tool and hdr10plus_tool this whole thing wouldn't be possible without him
* to @szasza576 for dockerfile
* to makeMKV and avsForum for inspiration to work it out and put it all together

# Disclaimer
This is a hobby project created by personal need it could be A LOT better written (I hate those evals) but priority was to make it fast, I code a lot at work so I mostly choose to spend free time with kids and watching movies over this I have a life you know :) 

Therefore I didn't have nor wanted to spend too much time on it so don't judge the code quality. I did some features for You though beta was working already fine for me, I wanted to give back something to community

PRs are welcome :)