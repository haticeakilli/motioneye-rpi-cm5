# motioneye-rpi-cm5

Bu çalışmada, Raspberry Pi Compute Module 5 üzerinde MotionEye tabanlı video kayıt ve kamera işleme sistemlerinin analizini içermektedir. Çalışma kapsamında MotionEye kurulumu, CSI kamera entegrasyonu, video recording pipeline yapıları ve edge cihaz davranışları incelenmiştir.

Bu çalışmanın amacı, Raspberry Pi CM5 üzerinde MotionEye çalıştırmak ve USB ile CSI kameraları entegre etmektir. Bunun sonucunda ise kamera device yönetimini analiz ederek edge cihazlarda kayıt ve stabilite davranışlarını gözlemlemektir.

Donanım:
Raspberry Pi Compute Module 5
CM5 IO Board
USB Kamera / CSI Kamera (RPi V2 Camera)
NVMe SSD (harici depolama)

Yazılım:
Debian 13 (Trixie)
MotionEye
FFmpeg
GStreamer
V4L2 / v4l2loopback
Python / Picamera2

İncelenen Konular:
- MotionEye kurulumu
- CSI → V4L2 bridge yapısı
- GStreamer pipeline kullanımı
- v4l2loopback ile sanal kamera oluşturulması
- FFmpeg tabanlı recording işlemleri
- Kamera path yönetimi (`/dev/videoX`)
- Grey screen troubleshooting
- Segment bazlı recording yapısı
- Linux servis yönetimi (`systemd`)

Donanım:
Raspberry Pi Compute Module 5
CM5 IO Board
USB Kamera / CSI Kamera (RPi V2 Camera)
NVMe SSD (harici depolama)

Yazılım:
Debian 13 (Trixie)
MotionEye
FFmpeg
GStreamer
V4L2 / v4l2loopback
Python / Picamera2



