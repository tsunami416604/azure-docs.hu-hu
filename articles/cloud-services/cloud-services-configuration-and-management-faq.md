---
title: "Konfigurációs és kezelésének számos, a Microsoft Azure Cloud Services – gyakori kérdések |} Microsoft Docs"
description: "Ez a cikk a kapcsolatos gyakori kérdések konfigurálása és kezelése a Microsoft Azure Felhőszolgáltatások sorolja fel."
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: genli
ms.openlocfilehash: 35c8e2a2029b3f29b45004c1308de8b3a108f698
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2017
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Konfigurációs és kezelésének számos Azure-szolgáltatásokhoz: gyakran ismételt kérdések (GYIK)

Ez a cikk tartalmazza a konfigurációs és kezelésének számos kapcsolatos gyakran ismételt kérdések [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Is tud kezelni a [felhőalapú szolgáltatások Virtuálisgép-méretet lap](cloud-services-sizes-specs.md) mérete információt.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-add-nosniff-to-my-website"></a>Hogyan "nosniff" hozzáadása a webhely?
Megakadályozza a felhasználókat a MIME-típusok elemzés, adja hozzá a megfelelő értéket a *web.config* fájlt.

```xml
<configuration>
   <system.webServer>
      <httpProtocol>
         <customHeaders>
            <add name="X-Content-Type-Options" value="nosniff" />
         </customHeaders>
      </httpProtocol>
   </system.webServer>
</configuration>
```

Azt is megteheti ezt az IIS-beállításként. Használja a következő parancsot a [közös indítási feladatok](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) cikk.

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

## <a name="how-do-i-customize-iis-for-a-web-role"></a>Hogyan testre az IIS egy webes szerepkör?
Az IIS indítási parancsfájl használatát a [közös indítási feladatok](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) cikk.

## <a name="i-cannot-scale-beyond-x-instances"></a>I nem felüli X példányok
Az Azure-előfizetés használhatja magok száma van korlátozva. Skálázás nem működik, ha az összes rendelkezésre álló magot használja. Például ha egy vonatkozó felső korlátját: 100, ez azt jelenti, 100 A1 méretű virtuálisgép-példánya lehet a felhőalapú szolgáltatás, vagy 50 A2 méretű virtuálisgép-példánya.

## <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Hogyan is létrehozható a Felhőszolgáltatások szerepkörön alapuló hozzáférés?
Cloud Services nem támogatja a szerepköralapú hozzáférés-vezérlést (RBAC) modell a, mert nincs olyan Azure Resource Manager-alapú szolgáltatás.

Lásd: [hagyományos előfizetés rendszergazdái és az Azure RBAC](../active-directory/role-based-access-control-what-is.md#azure-rbac-vs-classic-subscription-administrators).

## <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Hogyan állíthatom üresjárati időkorlátját az Azure terheléselosztó?
Az időtúllépés adhat meg a szolgáltatás definíciós (csdef) fájl ehhez hasonló:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="mgVS2015Worker" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10100"   idleTimeoutInMinutes="30" />
    </Endpoints>
  </WorkerRole>
```
Lásd: [új: Azure Load Balancer konfigurálható az üresjárati időkorlátjának](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/) további információt.

## <a name="can-microsoft-internal-engineers-rdp-to-cloud-service-instances-without-permission"></a>A Microsoft belső szakemberei RDP cloud service példányokhoz engedélye nélkül is?
A Microsoft egy szigorú folyamat, amely a felhőszolgáltatás (e-mailben vagy más írásbeli kommunikációs) írásbeli engedélye nélkül történő távoli asztali belső mérnökök nem teszi lehetővé a tulajdonos vagy az általa kijelölt szervet követi.

## <a name="why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete"></a>Miért nem fejeződött be a tanúsítványlánc a felhőalapú szolgáltatás SSL-tanúsítvány?
Azt javasoljuk, hogy a teljes tanúsítványlánc (a levél cert, a köztes tanúsítványok és a legfelső szintű tanúsítvány) helyett a levéltanúsítvány telepítését. A levéltanúsítvány telepítésekor támaszkodnak Windows érdekében a megbízható tanúsítványok Listáját a tanúsítványlánc létrehozásához. Ha a hálózati vagy DNS-probléma történt jelentkezik az Azure vagy a Windows Update a Windows megpróbálja érvényesíteni a tanúsítványt, akkor a tanúsítvány érvénytelen tekinthetők. A teljes tanúsítványlánc telepítése, a probléma elkerülhető. A következő blog [láncolt SSL-tanúsítvány telepítése](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/) bemutatja, hogyan ehhez.

## <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>Hogyan rendelheti hozzá egy statikus IP-címet az a cloud service?
Egy statikus IP-cím beállításához létrehozásához szükséges egy fenntartott IP-cím. A foglalt IP-cím is lehet társítani, egy új felhőalapú szolgáltatás, vagy egy meglévő telepítését. Tekintse meg a részleteket a következő dokumentumokat:
* [A fenntartott IP-cím létrehozása](../virtual-network/virtual-networks-reserved-public-ip.md#manage-reserved-vips)
* [Foglaljon le egy meglévő felhőszolgáltatáshoz IP-címe](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
* [Új felhőalapú szolgáltatás foglalt IP-cím hozzárendelése](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-new-cloud-service)
* [Egy futó telepítéshez foglalt IP-cím hozzárendelése](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-running-deployment)
* [A foglalt IP-cím egy felhőszolgáltatásra történő társíthatók a szolgáltatás konfigurációs fájl használatával](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

## <a name="what-is-the-quota-limit-for-my-cloud-service"></a>Mi az, hogy a kvótakorlát miatt a felhőalapú szolgáltatáshoz?
Lásd: [szolgáltatásspecifikus korlátozza](../azure-subscription-service-limits.md#subscription-limits).

## <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>Miért nem jelennek meg a meghajtót, a felhőalapú szolgáltatás VM nagyon kevés a szabad lemezterület?
Ez az elvárt működés, és ennek nem szabad következtében az alkalmazásba bármilyen probléma. Naplózási be van kapcsolva a % uproot % meghajtót az Azure PaaS virtuális gépeken, amelyek alapvetően használ a fájlok általában foglalnak lemezterületről összeg fel. Azonban számos szempontot figyelembe vennie, amely lényegében a ikonná ez nem probléma.

% Approot % meghajtó méretének kiszámítása: < méret .cspkg + napló maximális mérete > + os szabad lemezterület, vagy 1,5 GB-os, amelyik történik, nagyobb. A virtuális gép mérete nincs hatással a számítás rendelkezik. (A Virtuálisgép-méretet csak hatással van az ideiglenes C: meghajtóra.) 

A % approot % meghajtóra írása nem támogatott. Ha az Azure virtuális gépen, Önnek kell megtennie ideiglenes LocalStorage az erőforrás (vagy a másik lehetőség, például a Blob-tároló, Azure-fájlok, stb.). Így mappa % approot % szabad terület mennyisége értéke nem értelmezhető. Ha nem biztos benne, hogy az alkalmazás a % approot % meghajtóra ír, néhány nap múlva futtatása, és hasonlítsa össze a szolgáltatás mindig megőrizheti a "előtt" és "utána" méretét. 

Azure nem fog kiírni, semmit a % approot % meghajtóra. Amikor a virtuális Merevlemezt a .cspkg alapján létrehozott, és az Azure virtuális Géphez csatlakoztatva, előfordulhat, hogy a meghajtó írni egyedül az alkalmazás. 

A napló profilbeállításai nem konfigurálható, ezért nem kapcsolható ki azt.

## <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Mik a szolgáltatásokat és képességeket biztosító Azure alapvető IP-CÍMEK/Azonosítók és DDOS?
Azure datacenter fizikai kiszolgálók fenyegetések elleni védelemre IP-CÍMEK/Azonosítók van. Ezenkívül az ügyfelek külső biztonsági megoldások, például a webalkalmazási tűzfalak, hálózati tűzfalak, kártevőirtó, behatolásérzékelési, megelőzési rendszerek (Azonosítók/IP-CÍMEK), és több telepíthetnek. További információkért lásd: [az adatok és eszközök védelme és a globális biztonsági szabványoknak való megfelelés](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity).

Microsoft folyamatosan figyeli a kiszolgálók, hálózatok és alkalmazások észleli a veszélyeket. Azure multipronged fenyegetés-felügyeleti megközelítést használ behatolásérzékelési, elosztott-szolgáltatásmegtagadásos (DDoS-) támadások megelőzése, tesztelési, viselkedési analytics behatolást vagy a biztonság, anomáliadetektálás és gépi tanulási folyamatosan megerősítése a védelmet, és a kockázatok csökkentése. Az Azure-hoz a Microsoft Antimalware védelmet nyújt az Azure felhőszolgáltatások és virtuális gépek. Lehetősége van harmadik fél biztonsági megoldások emellett, például a webes alkalmazás tűz falakat emelnek, hálózati tűzfalak, kártevőirtó, behatolás felderítésére és megelőzésére rendszerek (Azonosítók/IP-CÍMEK), és több központi telepítéséhez.

## <a name="why-does-iis-stop-writing-to-the-log-directory"></a>Miért nem ne a naplózási könyvtár írjanak IIS?
A helyi tárhelykvótát a naplózási könyvtár írásra kimerített. Ennek elkerülése érdekében tegye három lehetőség közül:
* Lehetővé teszi az IIS és a diagnosztika rendszeresen került át blob-tároló.
* Naplófájlok manuálisan távolítsa el a naplózási könyvtárat.
* Növelje a helyi erőforrások kvótáját.

További információkért lásd a következő dokumentumokat:
* [Diagnosztikai adatok tárolása és megtekintése az Azure Storage-ban](cloud-services-dotnet-diagnostics-storage.md)
* [IIS-napló leállítja a felhőszolgáltatást írt](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/)

## <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>Mi az a célja a "Windows Azure eszközök titkosítási tanúsítványt a Extensions"?
Ezeket a tanúsítványokat automatikusan jönnek létre, amikor egy bővítmény hozzáadódik a felhőalapú szolgáltatás. Leggyakrabban Ez a ÜVEGVATTA kiterjesztés vagy a RDP-bővítményt, de annak oka az lehet, például a kártevőirtó vagy naplógyűjtő bővítmény. Ezek a tanúsítványok csak titkosítása és visszafejtése a bővítmény privát konfigurációjának használják. A lejárati dátum soha nem ellenőrzi, tehát nem számít, ha a tanúsítvány lejárt. 

Ezek a tanúsítványok figyelmen kívül hagyhatja. Ha azt szeretné, a tanúsítványok karbantartása, próbálja meg őket az összes törlése. Azure hibaüzenetet küldjön, ha törli a tanúsítványt, amely használatban van.

## <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>Hogyan tudom generálhatnak egy tanúsítvány-aláírási kérelem (CSR) nélkül "RDP-ing" példányához?
Tekintse meg a következő útmutató:

>[Megszerezni a tanúsítványt a Windows Azure-webhelyek (WAWS)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

Vegye figyelembe, hogy a CSR-e egy szövegfájlt. Nincs a számítógépről a létrehozandó amelyeken a tanúsítvány végső soron kell használni. Bár ez a dokumentum írása egy App Service, a CSR létrehozása általános, és a Felhőszolgáltatások is vonatkozik.

## <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>Hogyan vehetek fel egy kártevőirtó bővítmény a felhőalapú szolgáltatások automatikusan?

Kártevőirtó-bővítmény, az indítási feladat a PowerShell-parancsfájl használatával engedélyezheti. Ezek a cikkek végrehajtásához kövesse: 
 
- [PowerShell indítási feladat létrehozása](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Set-AzureServiceAntimalwareExtension](https://docs.microsoft.com/powershell/module/Azure/Set-AzureServiceAntimalwareExtension?view=azuresmps-4.0.0 )

Kártevőirtó telepítési forgatókönyvek és engedélyezheti a portálról kapcsolatban további információkért lásd: [kártevőirtó telepítési forgatókönyvek](../security/azure-security-antimalware.md#antimalware-deployment-scenarios).

## <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>Hogyan lehet engedélyezni a kiszolgálónév jelzése (SNI) Felhőszolgáltatásai számára?

A Cloud Services SNI engedélyezheti az alábbi módszerek egyikének használatával:

### <a name="method-1-use-powershell"></a>1. módszer: A PowerShell használata

Az SNI kötés a PowerShell-parancsmag segítségével konfigurálható **New-WebBinding** egy felhőalapú szolgáltatás szerepkörpéldányhoz következő indítási feladat a:
    
    New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags 
    
Leírtak [Itt](https://technet.microsoft.com/library/ee790567.aspx), a $sslFlags, a következő értékek egyike lehet:

|Érték|Jelentése|
------|------
|0|Nincs SNI|
|1|Az SNI engedélyezve |
|2 |Nem kötelező, amely használja a központi tanúsítványtároló SNI|
|3|Használja a központi tanúsítványtároló SNI-kötést tárolásához |
 
### <a name="method-2-use-code"></a>2. módszer: Kód használata

Az SNI kötés sikerült is konfigurálni a szerepkör indítása a kód itt leírtak [blogbejegyzés](https://blogs.msdn.microsoft.com/jianwu/2014/12/17/expose-ssl-service-to-multi-domains-from-the-same-cloud-service/):

    
    //<code snip> 
                    var serverManager = new ServerManager(); 
                    var site = serverManager.Sites[0]; 
                    var binding = site.Bindings.Add(“:443:www.test1.com”, newCert.GetCertHash(), “My”); 
                    binding.SetAttributeValue(“sslFlags”, 1); //enables the SNI 
                    serverManager.CommitChanges(); 
    //</code snip> 
    
Használja a fenti módszerek valamelyikét, a megfelelő tanúsítványok (*.pfx) az adott állomásnevek kell először telepíteni egy indítási feladat a szerepkör-példányokon vagy ahhoz, hogy a SNI kötés hatékony kód segítségével.

## <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>Hogyan vehetek fel címkék saját Azure Cloud Service? 

A felhőalapú szolgáltatás egy olyan klasszikus erőforrás. Csak létrejött erőforrásokat Azure Resource Manager támogatása címkék keresztül. Klasszikus erőforrások, például a felhőalapú szolgáltatás nem címkékkel. 

## <a name="what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications"></a>Mik a jövőbeli Felhőszolgáltatás képességek az Azure portálon, így könnyen kezelése, és figyelheti az alkalmazások?

* Új tanúsítvány létrehozása a távoli asztal protokoll (RDP) lehessen hamarosan elérhető. Alternatív megoldásként futtathatja ezt a parancsfájlt:

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
* Megadni, hogy a blob vagy helyi csdef és a szolgáltatáskonfigurációs séma töltse fel a helyre vonatkozó hamarosan elérhető. Használatával [New-AzureDeployment](/powershell/module/azure/new-azuredeployment?view=azuresmps-4.0.0), minden hely érték.
* Lehetővé teszi a példány szintjén metrikát. További figyelési lehetőségek érhetők el az [figyelő felhőalapú szolgáltatások](cloud-services-how-to-monitor.md).


## <a name="how-to-enable-http2-on-cloud-services-vm"></a>Hogyan lehet engedélyezni a HTTP/2 felhőalapú szolgáltatások virtuális gépeken?

Windows 10 és Windows Server 2016 kapható HTTP/2 támogatása az ügyfél és a kiszolgáló oldalán. Ha az ügyfél (böngésző) kapcsolódik a TLS feletti IIS-kiszolgálóra, amely egyezteti a HTTP/2 TLS-bővítményeket keresztül, akkor a kiszolgálóoldali semmilyen módosítást nem kell. Ennek az oka TLS, feletti a HTTP/2 használatát megadó h2-14 fejléc alapértelmezés szerint küldi. Ha viszont az ügyfél küld egy frissítési fejlécben frissítése a HTTP/2, akkor szüksége a változtatások alább a kiszolgáló oldalán, annak érdekében, hogy a frissítési működik, és végül egy HTTP/2-kapcsolatot. 

1. Futtassa a regedit.exe.
2. Keresse meg a beállításkulcs: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters.
3. Hozzon létre egy új DWORD értéket **DuoEnabled**.
4. Állítsa be az értékét 1-re.
5. Indítsa újra a kiszolgálót.
6. Lépjen a **alapértelmezett webhely** és a **kötések**, hozzon létre egy új TLS-kötést az imént létrehozott önaláírt tanúsítványt. 

További információkért lásd:

- [A HTTP/2 IIS-kiszolgálón](https://blogs.iis.net/davidso/http2)
- [Videó: HTTP/2, a Windows 10: böngésző, az alkalmazások és a webkiszolgáló](https://channel9.msdn.com/Events/Build/2015/3-88)
         

Vegye figyelembe, hogy egy indítási feladattal a fenti lépéseket sikerült automatizált, így amikor egy új PaaS-példány jön létre, a beállításjegyzék változásainak fent lehet hasznos. További információkért lásd: [hogyan lehet konfigurálni és futtatni egy felhőalapú szolgáltatás indítási feladatokat](cloud-services-startup-tasks.md).

 
Miután ez megtörtént, ellenőrizheti, hogy a HTTP/2 engedélyezve van, vagy nem használatával a következő módszerek egyikét:

- Protokoll verzió az IIS-napló engedélyezése, és keresse meg azokat az IIS-naplókba. A naplók azt fogja megjeleníteni az HTTP/2. 
- F12 fejlesztői eszközét az Internet Explorer vagy Edge engedélyezése, és a hálózati lapon ellenőrizze a protokoll váltani. 

További információkért lásd: [HTTP/2 IIS](https://blogs.iis.net/davidso/http2).

## <a name="the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that"></a>Az Azure-portálon az SDK-verzió a felhőalapú szolgáltatás nem jeleníti meg. Hogyan kaphatok, amely?

Jelenleg is dolgozunk mihamarabb elérhetővé tenni ezt a szolgáltatást az Azure portálon. Eközben segítségével következő PowerShell-parancsok beolvasása az SDK-verzió:

    Get-AzureService -ServiceName "<Cloud service name>" | Get-AzureDeployment | Where-Object -Property SdkVersion -NE -Value "" | select ServiceName,SdkVersion,OSVersion,Slot

## <a name="i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005"></a>Az RDP-fájl használatával nem lehet a távoli asztal felhőalapú szolgáltatás virtuális gépre. I get következő a hiba: hitelesítési hiba történt (kód: 0x80004005)

Ez a hiba akkor fordulhat elő, ha az Azure Active Directory tartományhoz csatlakozó gépről az RDP-fájlt használ. A probléma megoldásához kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal a letöltött RDP-fájlt, és válassza ki **szerkesztése**.
2. Hozzáadása "&#92;" regisztrációja, mivel a felhasználónév előtt előtag. Tegyük fel például, **. \username** helyett **felhasználónév**.

## <a name="i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address"></a>Szeretnék több hónapig a felhőalapú szolgáltatás leállítása. Hogyan felhőszolgáltatás számlázási költségeinek csökkentése az IP-cím elvesztése nélkül?

Egy már telepített Felhőszolgáltatásban lekérdezi számlázva a számítási és tárolási használ. Így akkor is, ha az Azure virtuális gép leállítása, Ön továbbra is beolvasása számlázzuk tárolására. 

Ez mit tehet a számlázási csökkenteni a szolgáltatás az IP-cím elvesztése nélkül:

1. [Az IP-címet lefoglalni](../virtual-network/virtual-networks-reserved-public-ip.md) a központi telepítések törlése előtt.  Csak a számlázás történik az IP-cím. IP-cím számlázással kapcsolatos további információkért lásd: [árképzési IP-címek](https://azure.microsoft.com/pricing/details/ip-addresses/).
2. Törölje a központi telepítéseket. Ne törölje a xxx.cloudapp.net, hogy használhassa a továbbiakban.
3. Ha azt szeretné, hogy az azonos tartalék IP-cím használatával a felhőalapú szolgáltatás újratelepíteni az előfizetésében lefoglaló című [fenntartott IP-címek felhőalapú szolgáltatásokhoz és a virtuális gépek](https://azure.microsoft.com/blog/reserved-ip-addresses/).

## <a name="my-cloud-service-management-certificate-is-expiring-how-to-renew-it"></a>A felhőalapú szolgáltatás felügyeleti tanúsítvány lejárati ideje. Újítsa meg, hogyan?

A következő PowerShell-parancsok segítségével a felügyeleti tanúsítványok:

    Add-AzureAccount
    Select-AzureSubscription -Current -SubscriptionName <your subscription name>
    Get-AzurePublishSettingsFile

A **Get-AzurePublishSettingsFile** hoz létre egy új felügyeleti tanúsítvány **előfizetés** > **felügyeleti tanúsítványok** az Azure portálon. Az új tanúsítvány neve "YourSubscriptionNam]-[CurrentDate] - hitelesítő adatok" tűnik.

## <a name="how-can-i-configure-auto-scale-based-on-memory-metrics"></a>Hogyan konfigurálható a memória mérőszámok alapján automatikus méretezése?

Cloud Services memória mérőszámok alapján automatikus méretezése jelenleg nem támogatott. 

A probléma megoldásához használhatja az Application Insights. Automatikus méretezése metrikák forrásként az Application Insights támogatja, és a szerepkör-példányok száma, például a "Memória" Vendég metrika alapján is méretezhető.  Konfigurálja az Application Insights a Felhőszolgáltatás-projekt csomagfájl (*.cspkg), és engedélyezi a szolgáltatás a feat végrehajtásához Azure Diagnostics bővítményt kell.

Az Application Insights automatikus méretezése konfigurálása Felhőszolgáltatások keresztül egyéni metrika magukat, hogy hogyan további részletekért lásd: [egyéni mértéket az Azure-ban megismerkedés automatikus méretezése](../monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md)


Az Application insights szolgáltatással integrálása Azure Diagnostics Felhőszolgáltatások további információkért lásd: [az Application Insights diagnosztikai adatok küldése a felhőalapú szolgáltatás, a virtuális gép vagy a Service Fabric](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md)

Az Application Insights Felhőszolgáltatásai számára engedélyezni készül az további információkért lásd: [Application Insights az Azure Cloud Services csomag](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices)

További információ az Azure Diagnostics naplózásának engedélyezése a Felhőszolgáltatások: [diagnosztika beállítása az Azure Cloud Services és a virtuális gépek](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)

## <a name="how-to-automate-the-installation-of-main-ssl-certificatepfx-and-intermediate-certificatep7b"></a>Hogyan fő SSL tanúsítványt (.pfx) és köztes certificate(.p7b) telepítését?

Ez a feladat indítási parancsfájl (kötegelt/cmd/PowerShell) segítségével automatizálhatja, és regisztrálja az adott indítási parancsfájl a szolgáltatásdefiníciós fájlban. Adja hozzá az indítási parancsfájl és a tanúsítványok (.p7b fájl) ugyanabban a könyvtárban, az indítási parancsfájl projektmappában.

További információkért tekintse át a következő cikkeket:
- [Hogyan lehet konfigurálni és egy felhőalapú szolgáltatás indítási feladatok futtatása](https://docs.microsoft.com/en-us/azure/cloud-services/cloud-services-startup-tasks)
- [A felhőalapú szolgáltatás indítási gyakori feladatok](https://docs.microsoft.com/en-us/azure/cloud-services/cloud-services-startup-tasks-common)