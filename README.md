# dvmkv2mp4 - Convert any Dolby Vision profile MKV to MP4 that runs on many devices

Beta release, converts any Dolby Vision mkv (Profile 4, 5 , 7 Single Track, 7 Dual Track, 8) to mp4 (DV4, DV5, DV8) compatible with LG OLEDs, tested with Emby on LG 77CX, 48CX, 65C8

Feuatures:
- converting from any 4,5,7,8 DV profile to mp4
- converts any truehd, dts etc to high bitrate Dolby Digital Plus, copies without conversion supported tracks like ac3, eac3
- keeps chapters
- converts PGS subtitiles found to SRT subtitiles with PGSToSRT
- extracts all SRT tracks to SRT files
- creates backup mkv with .asm extension (audio subs meta) that has the original audio (truehd etc) subtitles tracks, chapters but without video to safekeep for future comeback conversions to original mkv and not waste place as you can easily demux the mp4 video and mux it with that mkv to come back to original
- keeps only UND, POL and ENG audio and subtittles (you can change langs in code LINE 26 and 32)

# Requirements
- ffmpeg 4.4
- **mp4box 1.0.1 - it's important to use this version otherwise script will fail**
- dovi_tool
- mediainfo v21
- docker for PGS2SRT conversion
- 3xSize of free space for file you want to convert

# Requirements install on Ubuntu 20.04
```
sudo add-apt-repository ppa:savoury1/ffmpeg4
sudo wget -O /usr/share/keyrings/gpg-pub-moritzbunkus.gpg https://mkvtoolnix.download/gpg-pub-moritzbunkus.gpg
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/gpg-pub-moritzbunkus.gpg] https://mkvtoolnix.download/ubuntu/ focal main" | sudo tee -a /etc/apt/sources.list
wget https://mediaarea.net/repo/deb/repo-mediaarea_1.0-19_all.deb && sudo dpkg -i repo-mediaarea_1.0-19_all.deb && sudo apt-get update
sudo apt-get install gpac ffmpeg mediainfo mkvtoolnix
wget https://github.com/quietvoid/dovi_tool/releases/download/1.4.6/dovi_tool-1.4.6-x86_64-unknown-linux-musl.tar.gz
tar -zxf dovi_tool-1.4.6-x86_64-unknown-linux-musl.tar.gz
sudo mv dist/dovi_tool /usr/local/bin/
```

# Configuration
By default it keeps only undefined, english and polish audio and subtitiles tracks, you can change this by changing line 26 and 32 in the bash code

By default it removes input to save space after demuxing, be careful or change behaviour in code by commenting out lines 101 and 136

# Installation
Download dvmkv2mp4 make it executable and move
```
wget https://raw.githubusercontent.com/gacopl/dvmkv2mp4/main/dvmkv2mp4
chmod a+x dvmkv2mp4
mv dvmkv2mp4 /usr/local/bin/
```
# Usage
In directory containing Dolby Vision mkv simply run `dvmkv2mp4`it will process any mkvs found in that dir

# Roadmap
- parameters for langs, input removal behaviour, audio-subs-meta creation
- docker version
- convert directly from Bluray bdmv mpls file (have it working in alpha state already)
