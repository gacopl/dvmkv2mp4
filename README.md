# dvmkv2mp4 - Convert any Dolby Vision/HDR10+ MKV to MP4 that runs on many devices

Converts any Dolby Vision (Profile 5, 7 Single Track, 7 Dual Track, 8) / HDR10+ mkv to mp4 (DV5, DV8) compatible with LG OLEDs, Nvidia Shield and possibly more tested with Emby on LG 77CX, 48CX, 65C8

# Feuatures:
- autodetects source material and chooses proper workflow
- converting from any 5,7,8 DV profile to DV mp4
- converting from HDR10+ to DV8 mp4
- extended compatibility mode - make files run on AppleTV and LG C8,C9 models by faking DV Profile to 5
- verifies HDR10+ metadata before conversion (lots of fake releases out there)
- converts any truehd, dts etc to high bitrate Dolby Digital Plus, copies without conversion supported tracks like ac3, eac3
- keeps audio/subtitle track delays in resulting mp4
- keeps chapters
- converts PGS subtitiles found to SRT subtitiles with PGSToSRT
- extracts all SRT/Subrip tracks to SRT files
- can inject subtitles into mp4 as subtitle tracks
- can create backup mkv with .asm extension (audio subs meta) that has the original audio (truehd etc) subtitles tracks, chapters but without video to safekeep for future comeback conversions to original mkv and not waste place as you can easily demux the mp4 video and mux it with that mkv to come back to original
- can filter and leave only desired language tracks
- MacOS support

# Requirements
- ffmpeg 6.0
- ffprobe 6.x
- **mp4box 2.2.1**
- dovi_tool 2.1.0
- hdr10plus_tool 1.6.0
- mediainfo v21
- dotnet6 for PGS2SRT conversion
- 3xSize of free space for file you want to convert
- jq, bc


# Usage
```
In directory containing Dolby Vision mkv (or mp4) simply run `dvmkv2mp4`it will process any mkvs found in that dir

Options:
-l | --langs          - filter audio and subtitle tracks by lang comma separated if not hit by filter keep all tracks
-c | --ext-compat     - for extended device compatiblity ALWAYS FAKE target DV profile to be 5 (for APPLE/LG C7,8,9 and more)
-a | --asm            - create audio-subs-meta mkv file
-r | --remove-source  - remove source video after successful conversion
-s | --add-subs       - add srt subtitles to mp4 as subtitle tracks
-d | --debug          - keep intermediary conversion files
-m | --mp4-source     - enable convert from MP4 source mode instead of MKV
-f | --override-type  - override detected source type to one of dv5,dv7,dv8,hdr10plus in case metadata is broken in source file
--dest-dir            - destination directory (optional)
--temp-dir            - directory for temporary files (optional), defaults to --dest-dir or working directory. Creates a 'tmp' subfolder.

Examples:

dvmkv2mp4 -l und,pol,eng -r -a # will process mkvs found in folder keep only undefined, Polish and English tracks, will remove source file once done and will create audio-subs-meta file for future needs

dvmkv2mp4 -l und,pol,eng -c -r # will process mkvs found in folder keep only undefined, Polish and English tracks, will remove source file once done and will create AppleTV LG C8,C9 Compatible mp4 by faking resulting profile to DV5

dvmkv2mp4 -l und,pol,eng -c -m -f dv8 # will process mp4s found in folder keep only undefined, Polish and English tracks, will create AppleTV LG C8,C9 Compatible mp4 by faking resulting profile to DV5, will override and use appropriate workflow as if source file would be DV8
```


# Docker Installation - **preferred for unexperienced users**
The following section assumes that you installed Docker on your machine. For further instruction to install Docker follow the official site: https://docs.docker.com/get-docker/

Pull latest docker image it has ALL the right dependencies baked in - 

```docker pull ghcr.io/gacopl/dvmkv2mp4:main```


The usage is similar to the normal variant but here you need to attach your folder where you would like to convert your files. You can also add the flags at the end of the command as mentioned in usage section.

Example:
```
# docker run -it -u $(id -u):$(id -g) --rm -v <YOUR_FOLDER_PATH>:/convert dvmkv2mp4

docker run -it -u $(id -u):$(id -g) --rm -v /media/mkvvideos:/convert dvmkv2mp4 -l und,pol,eng -r -a
```
This example does the same which is mentioned in setion Usage.

# Manual installation - advanced users

This is intended for advanced users that do not wish to use docker and are able to provide requirements on their system.

## Requirements install on Ubuntu 20.04
```
# MEDIAINFO MKVTOOLNIX FFMPEG
sudo wget -O /usr/share/keyrings/gpg-pub-moritzbunkus.gpg https://mkvtoolnix.download/gpg-pub-moritzbunkus.gpg
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/gpg-pub-moritzbunkus.gpg] https://mkvtoolnix.download/ubuntu/ focal main" | sudo tee -a /etc/apt/sources.list
wget https://mediaarea.net/repo/deb/repo-mediaarea_1.0-19_all.deb && sudo dpkg -i repo-mediaarea_1.0-19_all.deb && sudo apt-get update
sudo apt-get install mediainfo mkvtoolnix jq bc
# FFMPEG
wget -O - "https://johnvansickle.com/ffmpeg/releases/ffmpeg-release-amd64-static.tar.xz" | tar -x -J -C /usr/local/bin/
# DOVI_TOOL
wget https://github.com/quietvoid/dovi_tool/releases/download/2.1.0/dovi_tool-2.1.0-x86_64-unknown-linux-musl.tar.gz
tar -zxf dovi_tool-2.1.0-x86_64-unknown-linux-musl.tar.gz
sudo mv dist/dovi_tool /usr/local/bin/
# HDR10PLUS_TOOL
wget https://github.com/quietvoid/hdr10plus_tool/releases/download/1.6.0/hdr10plus_tool-1.6.0-x86_64-unknown-linux-musl.tar.gz
tar -zxf hdr10plus_tool-1.6.0-x86_64-unknown-linux-musl.tar.gz
sudo mv dist/hdr10plus_tool /usr/local/bin/
# MP4BOX
sudo apt-get install build-essential pkg-config git
sudo apt-get install zlib1g-dev
git clone --depth 1 --branch v2.2.1 https://github.com/gpac/gpac.git gpac_public
cd gpac_public
./configure --static-bin
make
sudo make install
MP4Box -version # MAKE SURE IT SAYS 2.2.1
# PGS2SRT
wget https://download.visualstudio.microsoft.com/download/pr/48fbc600-8228-424e-aaed-52b7e601c277/c493b8ac4629341f1e5acc4ff515fead/dotnet-runtime-6.0.10-linux-x64.tar.gz
tar -zxf dotnet-runtime-6.0.10-linux-x64.tar.gz
sudo mkdir /opt/dotnet
sudo mv * /opt/dotnet
cd /opt
sudo apt install libtesseract4
sudo mkdir /opt/PgsToSrt
cd /opt/PgsToSrt
sudo wget https://github.com/Tentacule/PgsToSrt/releases/download/v1.4.2/PgsToSrt-1.4.2.zip
sudo unzip PgsToSrt-1.4.2.zip
cd net6
sudo git clone --depth 1 https://github.com/tesseract-ocr/tessdata.git
```

## Installation
Download dvmkv2mp4 make it executable and move
```
wget https://raw.githubusercontent.com/gacopl/dvmkv2mp4/main/dvmkv2mp4
chmod a+x dvmkv2mp4
mv dvmkv2mp4 /usr/local/bin/
```

To use dvmkv2mp4 in OS X:
- install all required packages as for Ubuntu 20.04
- install ionice-MacOS
- install gnu-sed
- install jq

# Roadmap
- find solution to bad source files that have broken audio without delay tags and that create audio lag in result file
- find solution to bad seeking on LG C8 with extended compatiblity files that are large
- convert directly from Bluray bdmv mpls file (have it working in alpha state already)

# Shoutouts
* to @quietvoid for dovi_tool and hdr10plus_tool this whole thing wouldn't be possible without him
* to @szasza576 for dockerfile
* to makeMKV and avsForum for inspiration to work it out and put it all together
* to all other contributors in this repo

# Disclaimer
This is a hobby project created by personal need it could be A LOT better written (I hate those evals) but priority was to make it fast, I code a lot at work so I mostly choose to spend free time with kids and watching movies over this I have a life you know :) 

Therefore I didn't have nor wanted to spend too much time on it so don't judge the code quality. I did some features for You though beta was working already fine for me, I wanted to give back something to community

PRs are welcome :)
