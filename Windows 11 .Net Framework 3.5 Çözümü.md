### Windows 11 (Canary) Üzerinde .NET 3.5 Gerektiren Eski Uygulamalar için Format Atmadan Çözüm Rehberi

Bu rehber, Windows 11 Canary (Build 27975.984) üzerinde .NET Framework 3.5 ve eski uygulamaların çalıştırılması sürecini adım adım anlatır.

---

## 1. Durum Tespiti
- Windows Özellikleri kısmında ".NET Framework 3.5" görünmüyor.
- Program eski, .NET 2.0 hedefli.
- SQL veya COM bağlantıları eski sürümlerden farklı olabilir.

## 2. Ön Gereksinimler
- Windows 11 Education / Canary Build 27975.984
- Yönetici yetkileri
- Windows 11 orijinal ISO dosyası (eğer offline kurulum gerekirse)

## 3. .NET 3.5 Yükleme Adımları

### A. ISO Bağlayarak Kurulum (Doğrudan SXS Yolundan)
1. Windows 11 ISO dosyasını bağlayın (çift tıklayarak sanal sürücüye ekleyin).
2. ISO bağlandıktan sonra sürücü harfini öğrenin (örnek: `D:`).
3. Yönetici CMD açın ve aşağıdaki komutu yazın:
```cmd
DISM /Online /Enable-Feature /FeatureName:NetFx3 /All /Source:D:\sources\sxs /LimitAccess
```
4. Komut satırı `The operation completed successfully.` mesajını verdikten sonra işlem tamamlanır.
5. PowerShell üzerinden kontrol edin:
```powershell
Get-WindowsOptionalFeature -Online -FeatureName NetFx3
```
- Eğer `State : Enabled` yazıyorsa .NET 3.5 başarıyla aktif edilmiştir.

> Bu yöntemde ISO içindeki `sources\sxs` klasöründe yer alan **microsoft-windows-netfx3-ondemand-package~31bf3856ad364e35~amd64~~.cab** dosyası doğrudan kullanılır. Dosya dışarı çıkarılmaz; DISM komutu kaynak olarak doğrudan bağlı ISO içindeki konumu kullanır.

### B. Manuel .CAB Dosyası ile Alternatif Kurulum
1. Eğer ISO’dan kurulum başarısız olursa, `sources\sxs` klasöründeki `microsoft-windows-netfx3-ondemand-package~31bf3856ad364e35~amd64~~.cab` dosyasını manuel olarak kaynak gösterebilirsiniz.
2. Yönetici CMD açın ve şu komutu çalıştırın (örneğin ISO sürücüsü `D:` ise):
```cmd
DISM /Online /Add-Package /PackagePath:"D:\sources\sxs\microsoft-windows-netfx3-ondemand-package~31bf3856ad364e35~amd64~~.cab"
```
3. Başarılı şekilde tamamlandığında aşağıdaki mesaj görünür:
```
The operation completed successfully.
```
4. Ardından özelliği aktif edin:
```cmd
DISM /Online /Enable-Feature /FeatureName:NetFx3 /All
```
5. Bu işlemle .NET 2.0, 3.0 ve 3.5 özellikleri aktif hale gelir.

### C. Manuel Offline Installer (Alternatif)
1. [Microsoft .NET Framework 3.5 SP1 Offline Installer](https://www.microsoft.com/en-us/download/details.aspx?id=25150) indir.
2. Yönetici CMD’den çalıştır:
```cmd
dotnetfx35.exe /q /norestart
```
3. Sistemi yeniden başlat.

## 4. Programın 2.0 Derleme Hatası Çözümü
1. Programın çalıştırıldığı klasöre `uygulama.exe.config` dosyası oluştur.
2. İçeriğini şu şekilde ayarla:
```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <startup useLegacyV2RuntimeActivationPolicy="true">
    <supportedRuntime version="v4.0" />
    <supportedRuntime version="v2.0.50727" />
  </startup>
</configuration>
```
3. Kaydet ve programı tekrar çalıştır.

## 5. Ek Kontroller
- `C:\Windows\Microsoft.NET\Framework\v2.0.50727` ve `Framework64` klasörlerinin varlığı
- Programın 32-bit veya 64-bit çalıştığını kontrol etme
- SQL veya COM bağlantı ayarlarını güncelleme gerekirse IT ile kontrol etme

## 6. Özet
- .NET 3.5 başarıyla yüklendi.
- ISO bağlanarak `sources\sxs` yolundan .CAB dosyası referans alınarak kurulum yapıldı, dosya dışarı çıkarılmadı.
- Eski uygulama format atmadan çalışır hale getirildi.
- SQL / COM bağlantısı güncellenerek tam uyumluluk sağlandı.
- Gelecekte benzer sorunlarda aynı adımlar uygulanabilir.

---

**Not:** Windows 11 Canary sürümleri bazı eski özellikleri eksik barındırabilir. Bu rehber özellikle **format atmadan ve mevcut sistem korunarak** çözüm sağlamak için hazırlandı.

