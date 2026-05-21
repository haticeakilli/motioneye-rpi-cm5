# CSI to V4L2 Bridge Pipeline

## Problem

Raspberry Pi CSI kameralar doğrudan V4L2 uyumlu '/dev/videoX' device’ları olarak çalışmamaktadır.

CSI kameralar Raspberry Pi’nin libcamera altyapısı üzerinden çalıştığı için MotionEye tarafından doğrudan algılanamamaktadır.

MotionEye ise standart V4L2 uyumlu kamera device’ları beklemektedir.

Bu uyumsuzluk CSI kameranın MotionEye üzerinde kullanılamamasına neden olmaktadır.

---

# Çözüm Yaklaşımı

Bu problemi çözmek amacıyla:

- 'v4l2loopback'
- 'GStreamer'
- sanal V4L2 device yapısı

kullanılmıştır.

Bu yapı sayesinde CSI kamera stream’i sanal bir '/dev/video10' device’ına aktarılmıştır.

MotionEye daha sonra bu sanal cihazı standart kamera gibi kullanabilmiştir.

---

# v4l2loopback Kurulumu

Öncelikle v4l2loopback modülü kurulmuştur.

```bash
sudo apt install -y v4l2loopback-dkms v4l2loopback-utils
```

# Sanal Kamera Device Oluşturma

```bash
sudo modprobe v4l2loopback video_nr=10 card_label="CSI-Bridge" exclusive_caps=1 
```

Bu işlem sonucunda /dev/video10 isimli sanal bir V4L2 kamera device’ı oluşturulmuştur.

Eşzamanlı olarak köprülenmek istenen her CSI kamera için ayrı bir v4l2loopback video device gerekir. Örneğin 3 CSI kamera için: sudo modprobe v4l2loopback video_nr=10,11,12 card_label="CSI-Bridge-0","CSI-Bridge-1","CSI-Bridge-2" exclusive_caps=1

Ayrıca v4l2loopback modülü bir kez yüklenir, ayrı ayrı yapmak durumunda ilki dışındaki modprobe yok sayılacaktır.


# Modül Yapılandırması

```bash
echo v4l2loopback | sudo tee /etc/modules-load.d/v4l2loopback.conf
sudo nano /etc/modprobe.d/v4l2loopback.conf
```

Dosya içerisine:

options v4l2loopback video_nr=10 card_label=CSI-Bridge exclusive_caps=1

eklenmiştir.

# Servis dosyasını oluşturma - GStreamer Pipeline

CSI kamera görüntüsü GStreamer pipeline ile sanal V4L2 cihazına aktarılmıştır.

```bash
sudo nano /etc/systemd/system/csi-bridge.service
```
İçine ekle:

[Unit]
Description=CSI Camera (libcamera) -> V4L2 Bridge
After=network.target
Before=motioneye.service

[Service]
Type=simple
ExecStart=/usr/bin/gst-launch-1.0 libcamerasrc ! \
 video/x-raw,format=NV12,width=640,height=480,framerate=15/1 ! \
 videoconvert ! video/x-raw,format=YUY2 ! \
 v4l2sink device=/dev/video10 sync=false
Restart=always
RestartSec=2

[Install]
WantedBy=multi-user.target






