# Troubleshooting Notes

Bu çalışma sırasında MotionEye üzerinde çeşitli kamera ve recording problemleri gözlemlenmiştir.

Bu bölümde karşılaşılan problemler ve uygulanan çözümler açıklanmaktadır.

---

# 1. Grey Screen Problemi

## Problem

USB kamera MotionEye içerisine eklendiğinde bazı durumlarda canlı görüntü yerine gri ekran oluşmuştur.

Bu durumda kamera algılanıyor görünmesine rağmen görüntü alınamamakta dolayısıyla recording işlemleri başarısız olabilmektedir.

---

# Olası Neden ve Çözümü

Problemin temel nedeni MotionEye’ın yanlış '/dev/videoX' device path’ini seçmesi olmuştur.

USB kameralar Linux üzerinde birden fazla '/dev/videoX' node’u oluşturabilmektedir.

Bu node’ların bazıları gerçek video stream’i taşımamaktadır.

MotionEye kamera yapılandırma dosyası manuel olarak düzenlenmiştir.

MotionEye bazı durumlarda servis yeniden başlatıldığında yapılandırma dosyasını yeniden yazabilmektedir.

Bu durum tekrar yanlış device path seçilmesine neden olmuştur.

Bu problemi engellemek amacıyla yapılandırma dosyası immutable yapılmıştır.

---

# Device Kontrolü

Doğru kamera device’ını belirlemek amacıyla:

```bash
v4l2-ctl --list-devices
```
ile USB 2.0 Camera: USB Camera (usb-xhci-hcd.1-1): benzerinde olan bloğun altındaki herhangi bir /dev/videoX yolunu belirle ve aşağıdaki adımları uygula.

```bash
sudo nano /etc/motioneye/camera-2.conf 
```
İçeriğe ekle:
input -1
v4l2_palette 0 
videodevice /dev/videoX (Zaten ekli, sadece değiştir.)

```bash
sudo chattr +i /etc/motioneye/camera-2.conf
```

Herhangi bir ayar değiştirilecekse:

- sudo chattr -i /etc/motioneye/camera-2.conf
- sudo systemctl stop motioneye
- sudo nano /etc/motioneye/camera-2.conf
- (Değişikliği burada yap.)
- sudo chattr +i /etc/motioneye/camera-2.conf
- sudo systemctl start motioneye




