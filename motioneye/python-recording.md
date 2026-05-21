# Python Based Recording

Bu çalışma kapsamında MotionEye dışında Python tabanlı recording yapısı da test edilmiştir.

Amaç, CSI kameradan alınan görüntüyü belirli sürelerle segmentlere ayırarak MP4 formatında kaydetmektir.

# Recording Yapısı

Kullanılan Python kodu:

```python
from picamera2 import Picamera2, Preview
from picamera2.encoders import H264Encoder
from picamera2.outputs import FfmpegOutput
from time import sleep

picam = Picamera2()

config = picam.create_video_configuration(
    main={"size": (1920, 1080)},
    lores={"size": (640, 480)},
    display="lores"
)

picam.configure(config)

picam.start_preview(Preview.QTGL)

encoder = H264Encoder(bitrate=8_000_000)

picam.start()

try:
    i = 1

    while True:
        output = FfmpegOutput(f"/mnt/ssd/segment{i}.mp4")

        picam.start_recording(encoder, output)

        sleep(60)

        picam.stop_recording()

        i += 1

except KeyboardInterrupt:
    pass

picam.stop()


