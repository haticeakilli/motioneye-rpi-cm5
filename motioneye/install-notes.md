# MotionEye Installation Notes

## Platform

- Raspberry Pi Compute Module 5 (CM5)
- Debian 13 (Trixie)
- Python 3.13

---

# Amaç

Bu çalışma kapsamında USB kameradan görüntü alıp MotionEye üzerinde recording işlemleri gerçekleştirmek hedeflenmiştir.

---

# Sistem Hazırlığı

Sistemde MotionEye’in çalışması için gereken temel paketleri hazırlar.

```bash
sudo apt update
sudo apt --no-install-recommends install ca-certificates curl python3
sudo apt --no-install-recommends install python3-dev gcc libjpeg62-turbo-dev libcurl4-openssl-dev libssl-dev
```

# Pip Kurulum Dosyasının İndirilmesi

MotionEye yüklemesi yapabilmek amacıyla Python için resmi pip kurulum betiğini indirir.

```bash
curl -sSfO 'https://bootstrap.pypa.io/get-pip.py'
```

# Pip’in Sistem Paketlerine Müdahalesine İzin Verme

Python kütüphanesinin son dağıtım sürümlerinde, Python modüllerinin venv ortamları dışında kurulumunu engelleyen bir dosya sunar. Ancak MotionEye, katı sürüm gereklilikleri olmayan az sayıda bağımlılığa sahip olduğundan APT aracılığıyla yüklediğiniz herhangi bir Python paketini bozma olasılığı çok düşüktür. Bu engeli aşmak için, pip.conf dosyasının [global] bölümüne break-system-packages=true ekler.

```bash
grep -q '\[global\]' /etc/pip.conf 2> /dev/null || printf '%b' '[global]\n' | sudo tee -a /etc/pip.conf > /dev/null
sudo sed -i '/^\[global\]/a\break-system-packages=true' /etc/pip.conf
```

# Pip Kurulumu

Python paket yöneticisini yükler. 

```bash
sudo apt install python3-pip
rm get-pip.py
```

# MotionEye Kurulumu

Bu yöntem otomatik kurulum yöntemidir.

```bash
sudo python3 -m pip install --pre motioneye
sudo motioneye_init
```

# MotionEye Servisi

MotionEye Linux üzerinde systemd servisi olarak çalıştırılmıştır.

```bash
sudo systemctl start motioneye
sudo systemctl status motioneye
```

# Web Arayüzüne Erişim

Artık MotionEye'ın web arayüzüne erişebilmek için hostname -I ile Raspberry Pi'nin IP adresini alabilirsiniz.

http://raspberry_ip:8765 (örnek)



