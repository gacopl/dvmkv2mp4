# dvmkv2mp4

Beta release, converts any Dolby Vision mkv to mp4 compatible with LG OLEDs, tested with emby

# Requirements
ffmpeg 4.4
mp4box 1.0.1 - it's important to use this version for mp4 converts work on LG C8
dovi_tool
docker for PGS2SRT conversion

# Requirements install on Ubuntu 20.04
```sudo add-apt-repository ppa:savoury1/ffmpeg4
sudo apt-get update
sudo apt-get install gpac ffmpeg 
wget https://github.com/quietvoid/dovi_tool/releases/download/1.4.6/dovi_tool-1.4.6-x86_64-unknown-linux-musl.tar.gz
tar -zxf dovi_tool-1.4.6-x86_64-unknown-linux-musl.tar.gz
mv dist/dovi_tool /usr/local/bin/```

#Installation
Download dvmkv2mp4
chmod a+x dvmkv2mp4
mv dvmkmc2mp4 /usr/local/bin
