Bu çalışmanın temel problemi, Raspberry Pi Compute Module 5 gibi kaynakları sınırlı bir edge cihaz üzerinde MotionEye kullanarak stabil bir video kayıt sistemi oluşturmaktır.

MotionEye, YUYV formatlı kameradan görüntü alabilmek için Trixie işletim sistemine docker ile değil manuel olarak kurulmuştur. Klasik V4L2 uyumlu kameralarla daha kolay çalışırken, buna ek olarak CSI kamera eklenince libcamera altyapısı üzerinden çalıştığı için doğrudan uyumluluk sorunları oluşabilmektedir.

Bu nedenle çalışmada aşağıdaki problemler ele alınmıştır:

- USB ve CSI kameraların birlikte MotionEye ile kullanılabilir hale getirilmesi
- Kamera device path yönetimi ('/dev/videoX')
- CSI kameranın V4L2 uyumlu sanal cihaza köprülenmesi
- Video kayıt biçimi ve codec uyumluluğu
- SSD üzerine kayıt alma
- Grey screen gibi kamera görüntüleme problemlerinin çözülmesi
- Uzun süreli kayıt senaryolarında stabilite gözlemi
