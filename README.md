# rpicamv3stream

Streaming the official camera module V3 from a raspberry pi 5

## Stream pipeline setup

This will start rpicam-vid and pipe the video to gstreamer, which will convert it to h264 and stream to localhost via UDP on port 5000

```
rpicam-vid --camera 0 -t 0 --width 1280 --height 720 --framerate 30 --codec yuv420 --profile baseline --level 4 --flush --inline -o - | \
gst-launch-1.0 fdsrc ! videoparse format=i420 width=1280 height=720 framerate=30/1 ! videoconvert ! x264enc tune=zerolatency bitrate=500 speed-preset=superfast ! mpegtsmux ! udpsink host=127.0.0.1 port=5000
```

## MediaMTX setup

download the latest [mediamtx](https://github.com/bluenviron/mediamtx/) media server and setup the path for camera0 with UDP source in [mediamtx.yml]()

```
paths:
  cam0:
    source: udp://:5000
```

## Start streaming

run the stream pipeline and the mediamtx server, now the stream is accessable via different protocols:

- RTSP on port :8554 (TCP), :8000 (UDP/RTP), :8001 (UDP/RTCP)
- RTMP on port :1935
- HLS on port :8888
- WebRTC on port :8889 (HTTP), :8189 (ICE/UDP)
- SRT on port :8890 (UDP)

The stream can be accessed in the browser via:
- HLS: http://<rpi-ip>:8888/cam0/
- WebRTC: http://<rpi-ip>:8889/cam0/
