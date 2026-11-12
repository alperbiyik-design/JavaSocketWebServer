# Java Socket Web Sunucusu (Port: 1989)

Bu proje, **Nesne Yönelimli Programlama (OOP)** dersi kapsamında herhangi bir üçüncü parti kütüphane veya harici çerçeve (framework) kullanılmadan, yalnızca standart **Java SE (`java.net` ve `java.io`)** paketleri kullanılarak geliştirilmiş çok iş parçacıklı (multi-threaded) bir HTTP Web Sunucusudur.

Uygulama **1989** numaralı TCP portu üzerinden gelen tarayıcı isteklerini dinler ve bağlanan istemcilere **SOLID** yazılım tasarım ilkelerine uygun olarak yapılandırılmış dinamik bir HTML/CSS biyografi sayfası döndürür.

## Temel Özellikler

* **Sıfır Harici Bağımlılık (Zero 3rd-Party Dependency):** Yalnızca standart Java SE (`java.net.ServerSocket`, `java.net.Socket`, `java.io.*`) kütüphaneleri kullanılmıştır.
* **1989. Port Üzerinden HTTP/1.1 Yayını:** Standart web tarayıcıları (`http://localhost:1989`) ile tam uyumlu HTTP/1.1 yanıt başlıkları (`200 OK`, `Content-Type`, `Content-Length`, `Connection: close`) üretir.
* **Çok İş Parçacıklı (Multi-Threaded) İstemci Yönetimi:** Her gelen tarayıcı bağlantısı `Runnable` arayüzünü uygulayan bağımsız bir `Thread` üzerinden eşzamanlı olarak işlenir.
* **SOLID Prensiplerine Tam Uyum:** Tek Sorumluluk (SRP), Açık/Kapalı (OCP), Liskov Yerine Geçme (LSP), Arayüz Ayrımı (ISP) ve Bağımlılıkların Tersine Çevrilmesi (DIP) ilkeleri mimarinin tamamına uygulanmıştır.
* **Özelleştirilmiş HTML & CSS Arayüzü:** `<h1>` boyutunda Ad Soyad, `<h2>` boyutunda Öğrenci Numarası ve modern CSS özellikleri (renk paleti, tipografi, kart düzeni) ile biçimlendirilmiş kısa biyografi içerir.

## Proje Mimarisi ve Dosya Yapısı

```text
JavaSocketWebServer/
├── src/
│   └── webserver/
│       ├── Main.java                          # Uygulama başlangıç noktası ve Dependency Injection kurulumu
│       ├── WebServer.java                     # 1989 portunu dinleyen ve bağlantıları kabul eden ana sunucu sınıfı
│       ├── ClientConnectionHandler.java       # Her istemci soketini ayrı bir Thread içinde işleyen sınıf
│       ├── ContentProvider.java               # Sayfa içeriği üretimi için soyut arayüz (OCP & DIP)
│       ├── BiographyHtmlProvider.java         # Öğrenci bilgilerini H1, H2 ve CSS'li HTML olarak üreten sınıf (SRP & LSP)
│       ├── HttpResponseFormatter.java         # HTTP yanıt paketleme işlemi için soyut arayüz (ISP)
│       ├── StandardHttpResponseFormatter.java # HTTP/1.1 200 OK başlıklarını ve gövdeyi byte dizisine çeviren sınıf
│       └── StudentProfile.java                # Öğrenci bilgilerini taşıyan değişmez (immutable) veri modeli sınıfı
└── README.md
```

## SOLID Prensiplerinin Uygulamadaki Karşılıkları

| Prensip | Kısaltma | Uygulandığı Sınıf / Arayüz | Mimari Açıklama |
| :--- | :--- | :--- | :--- |
| **Single Responsibility Principle** | SRP | Tüm Sınıflar (`StudentProfile`, `WebServer`, `StandardHttpResponseFormatter` vb.) | Her sınıfın değişmek için tek bir nedeni vardır. Veri modeli, HTML üretimi, HTTP protokol formatlama ve soket dinleme işlemleri birbirinden tamamen ayrılmıştır. |
| **Open/Closed Principle** | OCP | `ContentProvider`, `HttpResponseFormatter` | Sunucu yeni içerik türlerine (örn. JSON veya farklı bir HTML şablonu) açıktır; ancak yeni bir içerik sağlayıcı eklendiğinde `WebServer` veya `ClientConnectionHandler` kodlarında hiçbir değişiklik yapılmaz. |
| **Liskov Substitution Principle** | LSP | `BiographyHtmlProvider`, `StandardHttpResponseFormatter` | Alt sınıflar, uyguladıkları arayüzlerin (`ContentProvider` ve `HttpResponseFormatter`) sözleşmelerini tam olarak yerine getirir ve üst türlerinin yerine sorunsuzca kullanılabilir. |
| **Interface Segregation Principle** | ISP | `ContentProvider`, `HttpResponseFormatter` | Tek bir şişkin arayüz yerine, yalnızca içerik sağlama ve yalnızca HTTP yanıtı biçimlendirme görevlerine odaklanan küçük, ayrıştırılmış arayüzler tasarlanmıştır. |
| **Dependency Inversion Principle** | DIP | `WebServer`, `ClientConnectionHandler`, `Main` | Yüksek seviyeli sunucu modülleri (`WebServer`), düşük seviyeli somut sınıflara (`BiographyHtmlProvider`) doğrudan bağımlı değildir; her iki katman da soyutlamara (`interface`) bağımlıdır. |

## Kurulum ve Çalıştırma

### Gereksinimler
* **Java Development Kit (JDK):** JDK 8 veya üzeri
* **Herhangi bir Web Tarayıcısı:** Google Chrome, Mozilla Firefox, Microsoft Edge vb.

### 1. Projeyi Klonlayın
```bash
git clone [https://github.com/alperbiyik-design/JavaSocketWebServer.git](https://github.com/alperbiyik-design/JavaSocketWebServer.git)
cd JavaSocketWebServer
```

### 2. IntelliJ IDEA ile Çalıştırma
1. IntelliJ IDEA'yı açın ve proje klasörünü içe aktarın (**File -> Open**).
2. `src/webserver/Main.java` dosyasını açın.
3. `main` metodu yanındaki yeşil **Run (▶)** düğmesine tıklayın.

### 3. Komut Satırı (Terminal) ile Derleme ve Çalıştırma
```bash
mkdir -p out
javac -d out src/webserver/*.java
java -cp out webserver.Main
```

### 4. Tarayıcı Üzerinden Erişim
Sunucu başladıktan sonra herhangi bir web tarayıcısını açarak aşağıdaki adrese gidin:

```text
http://localhost:1989
```

## Ağ ve Soket İletişim Özeti

* **IP Adresi (`127.0.0.1` / `localhost`):** Ağ üzerindeki cihazın kimlik adresidir; yerel testlerde geri döngü (loopback) adresi kullanılır.
* **Port (`1989`):** İşletim sistemi üzerinde çalışan ilgili sunucu sürecine (process) açılan mantıksal iletişim kapısıdır.
* **Socket (`ServerSocket` & `Socket`):** IP adresi ve port numarasının birleşimiyle oluşan, tarayıcı ile Java uygulaması arasında çift yönlü veri akışı (`InputStream` / `OutputStream`) sağlayan uç noktadır (endpoint).
