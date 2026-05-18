Sistem temel olarak aşağıdaki katmanlardan oluşur:

1. Kamera Katmanı

Sistemde hem USB kamera hem de Raspberry Pi CSI kamera kullanılmıştır.
USB kamera doğrudan V4L2 uyumlu /dev/videoX device node’ları üzerinden erişilebilir durumdadır. MotionEye bu cihazlara doğrudan erişebilmektedir.
CSI kamera ise Raspberry Pi’nin libcamera altyapısı üzerinden çalışmaktadır. Bu nedenle MotionEye CSI kamerayı standart bir /dev/videoX cihazı gibi doğrudan kullanamamaktadır. Bu uyumsuzluk sistem içerisinde ek bir bridge pipeline ihtiyacı oluşturmuştur.

2. CSI → V4L2 Bridge Katmanı

CSI kamerayı MotionEye ile uyumlu hale getirmek amacıyla v4l2loopback kullanılmıştır. Bu yapı ile sanal bir V4L2 kamera cihazı oluşturulmuştur: /dev/videoX. Bu yaklaşım sayesinde MotionEye CSI kamerayı standart bir V4L2 kamera gibi kullanabilmiştir.

3. GStreamer Pipeline Yapısı

CSI kameradan alınan görüntü GStreamer pipeline kullanılarak sanal V4L2 cihazına aktarılmıştır.

Kullanılan pipeline:

gst-launch-1.0 libcamerasrc ! \
video/x-raw,format=NV12,width=640,height=480,framerate=15/1 ! \
videoconvert ! video/x-raw,format=YUY2 ! \
v4l2sink device=/dev/video10 sync=false

Bu pipeline içerisinde:

libcamerasrc: CSI kameradan görüntü alınır.
video/x-raw: görüntü formatı ve çözünürlük belirlenir.
videoconvert: görüntü format dönüşümü yapılır.
v4l2sink: görüntü sanal /dev/video10 cihazına yazılır.

Bu yapı bir video processing pipeline örneğidir.

4. MotionEye Katmanı

MotionEye sistemin NVR katmanını oluşturmaktadır.

Bu katmanda, 
- canlı görüntüleme
- sürekli kayıt
- kamera yapılandırması
- web arayüzü yönetimi
işlemleri gerçekleştirilmiştir.

MotionEye ayrıca Linux üzerinde systemd servisi olarak çalıştırılmıştır.

sudo systemctl start motioneye
sudo systemctl status motioneye

5. Device Path Yönetimi

USB kamera kullanımında MotionEye zaman zaman yanlış /dev/videoX device path’ini seçebilmektedir. Bu durum görüntü alınamaması problemine neden olmuştur.

Doğru kamera device’ını belirlemek amacıyla:

v4l2-ctl --list-devices

komutu kullanılmıştır.

MotionEye yapılandırma dosyası manuel olarak düzenlenmiştir:

sudo nano /etc/motioneye/camera-2.conf

Ayrıca yapılandırmanın değiştirilmesini engellemek amacıyla dosya immutable yapılmıştır:

sudo chattr +i /etc/motioneye/camera-2.conf

Bu yaklaşım sistem davranışının stabil hale gelmesini sağlamıştır.

6. Storage Katmanı

Video kayıtları NVMe SSD üzerine yazılmıştır.

Storage katmanında,
- recording davranışı
- kayıt sürekliliği
- video formatları
- write işlemleri
gözlemlenmiştir.

7. Python Recording Yapısı

Ek olarak Picamera2 ve FFmpegOutput kullanılarak Python tabanlı segment recording yapısı test edilmiştir. Bu yapıda belirli sürelerle MP4 segmentleri oluşturulmuştur.

Bu yaklaşım, 
- uzun recording senaryoları
- storage yönetimi
- recording stabilitesi
açısından incelenmiştir.

Genel Sistem Akışı:

CSI Camera
    ↓
libcamera
    ↓
GStreamer Pipeline
    ↓
v4l2loopback (/dev/video10)
    ↓
MotionEye
    ↓
NVMe SSD
