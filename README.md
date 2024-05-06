# Powershell-IP-to-Hostname-Resolver
`Resolve-DnsName` komutu, PowerShell'de bir etki alanı adını (domain name) çözmek için kullanılır ve Windows Server 2012'den itibaren mevcuttur. Bu komut, eski `nslookup` aracının işlevselliğini modern bir PowerShell ortamına taşır.
# Resolve-DnsName Komutu
```
Resolve-DnsName -Name <DomainName> [-Type <QueryType>] [-Server <DnsServer>] [Diğer Parametreler]
```
Önemli Parametreler

  -Name: Zorunlu. Sorgulamak istediğiniz alan adını belirtir. Örneğin, -Name "google.com".

  -Type: İsteğe bağlı. Sorgu türünü belirtir. Varsayılan olarak A (IPv4) kayıtları döndürülür. Diğer türler:
        AAAA: IPv6 adresi
        CNAME: Canonical Name kaydı
        MX: Mail Exchange kaydı
        NS: Name Server kaydı
        SOA: Start of Authority kaydı
        TXT: Metin kaydı

  -Server: İsteğe bağlı. Sorguyu yapmak için kullanılacak DNS sunucusunu belirtir. Örneğin, -Server "8.8.8.8".

  -NoHostsFile: İsteğe bağlı. Ana bilgisayar dosyasını (hosts file) atlar ve doğrudan DNS sunucusuna sorgu gönderir.

  -CacheOnly: İsteğe bağlı. Yalnızca yerel DNS önbelleğindeki (cache) sonuçları döndürür.

  -DnsOnly: İsteğe bağlı. Sorguyu yalnızca DNS sunucusuna gönderir.
Örnek Kullanımlar
Temel A kaydı sorgusu:
`Resolve-DnsName -Name "emreozanmemis.com"`
Bu komut, emerozanmemis.com için varsayılan DNS A kayıtlarını döndürür.

Farklı türde bir sorgu:
`Resolve-DnsName -Name "emreozanmemis.com" -Type MX`
Bu komut, emreozanmemis.com için MX (Mail Exchange) kayıtlarını döndürür.

Özel bir DNS sunucusu kullanarak sorgulama:
`Resolve-DnsName -Name "emreozanmemis.com" -Server "8.8.8.8"`
Burada sorgu, Google’ın DNS sunucusu (8.8.8.8) üzerinden gerçekleştirilir.

Ek İpuçları

  Resolve-DnsName komutu, detaylı DNS bilgilerini almak için -Verbose anahtarını destekler.
  Bu komut, sadece DNS sunucusuna yapılan istekler ve yanıtlar hakkında değil, aynı zamanda hata ayıklama için de değerli bilgiler sağlar.

  Asıl merak edilen ve yapmak istediğiniz daha karmaşık bir iş süreci olan IP adresinin hangi DNS adına sahip olduğu olabilir.

```
###Resolve IP to HostName###
function ResolveIPtoName($IP) {
$ResolvingResults = ""
$ResolvingResults = @()
$IPList += @($IP)
foreach ($IP in $IPList) {
        try {$ResolvingResults += [System.Net.Dns]::GetHostEntry($IP)}
        catch{Write-Host $IP could not be Resolved -BackgroundColor Black -ForegroundColor Red}
        } 
         
$ResolvingResults | Select AddressList, HostName 
    }



###PowerShell üzerinde####
ResolveIPtoName IPadresigiriniz
```

Bu PowerShell script'i, verilen IP adresinin DNS adını (hostname) çözmek için yazılmıştır. Fonksiyon, bir dizi adımla çalışır ve elde ettiği sonuçları kullanıcıya gösterir. Script'i adım adım inceleyelim

# ResolveIPtoName Fonksiyonu
Fonksiyonun Söz Dizimi
```
function ResolveIPtoName($IP) {
    $ResolvingResults = ""
    $ResolvingResults = @()
    $IPList += @($IP)
    foreach ($IP in $IPList) {
        try {
            $ResolvingResults += [System.Net.Dns]::GetHostEntry($IP)
        }
        catch {
            Write-Host "$IP could not be Resolved" -BackgroundColor Black -ForegroundColor Red
        }
    } 
    $ResolvingResults | Select AddressList, HostName 
}
```
Script Açıklaması

  Fonksiyon Tanımlama:
        function ResolveIPtoName($IP) ifadesi ile fonksiyon tanımlanır.
        $IP parametresi, fonksiyona dışarıdan girdi olarak verilen IP adresini temsil eder.

  Başlangıç Değerlerini Tanımlama:
        $ResolvingResults = "": Başlangıçta boş bir string değeri atanır.
        $ResolvingResults = @(): Boş bir dizi oluşturulur, çünkü fonksiyon birden çok sonucu döndürebilir.

  IP Listesini Oluşturma:
        $IPList += @($IP): Tek bir IP adresini, bir dizi formatında saklamak için listeye ekler.

  IP Adreslerini Çözümleme:
        foreach ($IP in $IPList): IP listesi içerisindeki her IP için çözümleme yapılır.
        try-catch bloğu ile hata yönetimi sağlanır:
            try kısmında, [System.Net.Dns]::GetHostEntry($IP) yöntemi ile IP adresinin DNS adı çözülür.
                Bu yöntem, .NET Framework'teki System.Net.Dns sınıfına başvurarak çalışır.
                Eğer başarılı olursa, sonuç $ResolvingResults dizisine eklenir.
            catch kısmında, eğer çözümleme başarısız olursa, ekrana hata mesajı renkli bir şekilde yazdırılır.

  Sonuçları Gösterme:
        Sonuçlar $ResolvingResults dizisinde saklanır.
        Select AddressList, HostName ifadesiyle sadece AddressList (IP adresleri listesi) ve HostName (Çözülen DNS adı) alanları gösterilir.

Kullanım Örneği

Fonksiyonu kullanmak için, IP adresini fonksiyona argüman olarak geçmeniz yeterlidir:
`ResolveIPtoName "8.8.8.8"`
Bu komut, fonksiyonun 8.8.8.8 IP adresini DNS adıyla çözmesini sağlayacak ve ekrana adres listesi ile hostname bilgilerini döndürecektir.

Ek İpuçları

  Script'i daha esnek hale getirmek için, birden çok IP adresini aynı anda çözmek adına $IPList dizi yapısına farklı IP adresleri eklenebilir.
  Çözümleme sonuçlarının daha geniş formatta döndürülmesi için, Select komutuna ek alanlar ekleyebilirsiniz.
  Daha güvenilir hata yönetimi için, catch kısmında log dosyası gibi bir dış kaynağa da yazdırılabilir.

Bu makale, fonksiyonun işlevselliğini detaylı bir şekilde inceleyerek, çözümleme işleminin nasıl yapıldığını anlamanıza yardımcı olabilir.







