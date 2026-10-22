# PHP 7.4 için Windows x64 NTS topluluk derlemesi

[English installation guide](README.md)

**21 Ekim 2026** tarihinde açıklanan **CVE-2026-109618** ve **CVE-2026-109619** için PHP ekibinin yayımladığı resmî PHP 7.4 kaynak yamasından derlenmiş Windows FastCGI paketi.

**[Windows x64 NTS ZIP dosyasını indir](https://github.com/serkanburat/php74-patched-windows/releases/download/v2026.10.22-1/php74-patched-windows-x64-nts-712e60f9edfd.zip)** · [Release ve doğrulama dosyaları](https://github.com/serkanburat/php74-patched-windows/releases/tag/v2026.10.22-1)

Bu, topluluk tarafından derlenmiş bir pakettir; resmî PHP Windows dağıtımı değildir. Kapsamı yukarıdaki iki düzeltmedir; sürekli PHP 7.4 bakımı sağlamaz. Hazır ikili dosyalar **Windows x64, Non Thread Safe (NTS), IIS/FastCGI** içindir.

## Kaynak ve sürüm

| Bilgi | Değer |
| --- | --- |
| Resmî kaynak dalı | [`PHP-7.4-CVE-2026-109618-109619`](https://github.com/php/php-src/tree/PHP-7.4-CVE-2026-109618-109619) |
| Kaynak commit'i | [`712e60f9edfd03f0960fc4fc3c2c375f42b90aa2`](https://github.com/php/php-src/commit/712e60f9edfd03f0960fc4fc3c2c375f42b90aa2) |
| Çalışma zamanı sürüm etiketi | `7.4.31-dev` |
| Mimari / iş parçacığı modeli | Windows x64 / NTS |
| PHP'nin bildirdiği derleyici | Visual C++ 2017 (VC15) |
| Başarılı derleme | [GitHub Actions 39783222676](https://github.com/serkanburat/php74-patched-windows/actions/runs/39783222676) |

Resmî commit her iki CVE'yi de düzelttiğini belirtir. PHP ekibi bu kaynağı desteği sona ermiş sürüm için asgari kaynak yaması olarak yayımlamıştır. Yamalı kaynağın Windows sürüm başlığında `7.4.31-dev` etiketi kalmıştır; derlemeyi tanımlayan kesin bilgi kaynak commit'idir.

ZIP; `php.exe`, `php-cgi.exe`, `php7.dll`, paketle gelen eklentiler, bağımlılık DLL'leri, INI şablonları ve lisans bildirimlerini içerir. Uygulamaya özel `php.ini` ve ilave üçüncü taraf eklentiler pakete eklenmemiştir.

## İndirme ve SHA-256 kontrolü

**Releases → Assets** altındaki çalışma zamanı ZIP'sini indirin. GitHub'ın otomatik oluşturduğu “Source code” arşivleri hazır Windows paketi değildir. Release dosyalarını indirmek için GitHub hesabı gerekmez.

`php74-patched-windows-x64-nts-712e60f9edfd.zip` dosyasının SHA-256 değeri:

```text
cd3627ce0e42cbfb76ba14030c4ea3e02d279fb3173bec99222196ee8edbe176
```

İndirilen ZIP'nin bulunduğu dizinde PowerShell ile doğrulayın:

```powershell
$zip = Join-Path (Get-Location) 'php74-patched-windows-x64-nts-712e60f9edfd.zip'
$expected = 'cd3627ce0e42cbfb76ba14030c4ea3e02d279fb3173bec99222196ee8edbe176'
if ((Get-FileHash -LiteralPath $zip -Algorithm SHA256).Hash -ine $expected) {
    throw 'SHA-256 uyusmuyor. Bu dosyayi kullanmayin.'
}
```

Release eklerinde ayrıca `SHA256SUMS.txt`, `BUILD-PROVENANCE.json` ve `upstream-tests.xml` bulunur.

## IIS üzerinde mevcut PHP'nin yanına kurulum

1. Mevcut PHP yapılandırmasını ve sitenin `web.config` dosyasını yedekleyin. Geri dönüş için mevcut PHP handler'ının executable yolunu not alın.
2. Microsoft Visual C++ Redistributable x64 bileşeninin kurulu olduğundan emin olun. [Microsoft indirme sayfası](https://learn.microsoft.com/en-us/cpp/windows/latest-supported-vc-redist?view=msvc-170).
3. ZIP'yi yeni bir dizine, örneğin `C:\PHP74-Patched` altına açın. Paketin DLL'leri de aynı dağıtım içinde kalsın.
4. İki çalıştırılabilir dosyayı kontrol edin:

   ```powershell
   & 'C:\PHP74-Patched\php.exe' -n -v
   & 'C:\PHP74-Patched\php-cgi.exe' -n -v
   ```

5. `php.ini-production` şablonundan `C:\PHP74-Patched\php.ini` oluşturun; uygulamanızın ihtiyaç duyduğu ayarları aktarın. IIS için mevcut ilgili satırları şu değerlere getirin:

   ```ini
   extension_dir = "C:\PHP74-Patched\ext"
   cgi.force_redirect = 0
   fastcgi.impersonate = 1
   ```

   Gerekli eklentileri etkinleştirin. İlave DLL'ler **PHP 7.4, x64 ve NTS** ile uyumlu olmalıdır. Uygulamaya özel dizin, oturum, dosya yükleme ve izin ayarlarını kontrol edin.

6. **IIS Manager → sunucu → FastCGI Settings** içinde tam yolu `C:\PHP74-Patched\php-cgi.exe`, arguments alanı boş olan bir uygulama ekleyin. Ortam değişkeni olarak `PHPRC=C:\PHP74-Patched` tanımlayın.
7. **Bir site → Handler Mappings** altında `*.php` isteklerini karşılayan handler'ın executable alanını yeni yola yönlendirin. Birden fazla PHP kaydı varsa sıralamasını kontrol edin.
8. İlgili sitenin application pool'unu recycle edin. Login, veri okuma/yazma, dosya yükleme ve indirmeyi sınayın. Sonuçları doğrulayarak diğer sitelere geçin.

Geri dönüş için ilgili sitenin handler executable alanını önceki yola döndürüp havuzunu recycle edin. Yeni çalışma zamanının kullanılması için mevcut PHP işlemleri yenilenmelidir.

## Doğrulama ve bağımsız derleme

Yayımlanan ZIP, başarılı Actions çalışmasının değiştirilmemiş çıktısıdır. CLI ve CGI başlangıç kontrolleri, paketle gelen eklentilerin temel kontrolleri ve **yedi upstream upload/session regresyon testi** geçmiştir. Test raporu release'e eklenmiştir.

Paket sahibi ayrıca Windows Server 2019 üzerinde IIS/FastCGI başlangıcını ve uygulama girişini denemiştir. Bunlar işlev kontrolleridir; kapsamlı exploit testi veya her uygulama için uyumluluk garantisi değildir.

[Derleme iş akışı](.github/workflows/build-php74.yml), kaynak ve Windows derleme araçlarını kesin commit'lere sabitler, commit'leri doğrular ve alınan kaynağı derler. Bağımsız derlemek için **Actions → PHP 7.4 patched - Windows x64 NTS → Run workflow** yolunu kullanabilirsiniz. Yeniden derlenen dosyaların zaman damgaları ve hash değerleri farklı olabilir.

Başka bir platform veya mimari için bağlantısı verilen resmî kaynak yamasından başlayın. Bu depodaki hazır paket yalnızca Windows x64 NTS içindir.

## Resmî kaynaklar ve katkı

- [PHP'nin 21 Ekim 2026 güvenlik duyurusu](https://www.php.net/archive/2026.php)
- [CVE-2026-109618: RCE via OOM corruption](https://github.com/php/php-src/security/advisories/GHSA-w8hw-w9fx-j79w)
- [CVE-2026-109619: RCE via stale global state](https://github.com/php/php-src/security/advisories/GHSA-6wmf-4rv5-p3rp)
- [İki açığı düzelten resmî commit](https://github.com/php/php-src/commit/712e60f9edfd03f0960fc4fc3c2c375f42b90aa2)

Güvenlik düzeltmeleri PHP ekibine aittir. Bu depo, topluluk Windows derlemesini ve kurulum açıklamasını sağlar. Paketleme veya derleme sorunlarını [Issues](https://github.com/serkanburat/php74-patched-windows/issues) üzerinden, gizli yapılandırma ve kişisel bilgileri çıkartarak bildirebilirsiniz.

## Lisanslar

Çalışma zamanı [PHP License 3.01](LICENSE) lisansını korur. Paketle gelen kütüphanelerin kendi lisans ve bildirimleri ZIP içinde ve [THIRD-PARTY-NOTICES.txt](THIRD-PARTY-NOTICES.txt) dosyasında bulunur.

This product includes PHP software, freely available from <http://www.php.net/software/>.
