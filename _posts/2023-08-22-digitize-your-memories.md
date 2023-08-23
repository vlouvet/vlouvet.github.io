##Hardware Required ##
1. VCR (Using Sony SLV-D350 for this guide)
2. Hauppauge USB-Live 2 (or other analog capture device)
3. RCA Y-W-R A/V cables
4. Computer with H264 encoding 

##Software Required##
1. Linux - Ubuntu 22.04 (Other Linux versions may work)
2. ffmpeg
3. v4l2
4. vlc

##setup process##
1. Install required tools and libraries
```
sudo apt install -y ffmpeg vlc v4l2
```

##Commands Used##
1. query the v4l2 devices available.
```
(Plug in your capture device into the USB Port first!)
v4l2-ctl --list-dev
```
2. v4l2-ctl -d /dev/video4 --get-standard
```Video Standard = 0x0000b000
    NTSC-M/M-JP/M-KR
```
3. v4l2-ctl -d /dev/video4 -s ntsc
4. Use ffmpeg to save the A/V stream from capture device to file AND play on VLC
```
ffmpeg -f alsa -hwaccel auto  -i hw:1 -f video4linux2 -i /dev/video4 -tune film  -r:v ntsc -t 2:50:00 -f matroska pipe:1 MOVIE_NAME.mp4 | vlc -
```
##Additional Resources##
1. The information in this guide builds upon the resources availabe on the following site: http://www.tedfelix.com/linux/hauppauge-usb-live-2-linux.html
2. ffmpeg configuration options for hardware acceleration can be found at this site: 

