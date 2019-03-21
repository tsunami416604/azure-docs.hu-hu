---
title: Konfigurálással és felügyelettel kapcsolatos problémák a Microsoft Azure Cloud Services – gyakori kérdések |} A Microsoft Docs
description: Ez a cikk konfigurálása és kezelése a Microsoft Azure Cloud Services – gyakori kérdések listája.
services: cloud-services
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: genli
ms.openlocfilehash: 90b6798f7b7ab3acc552135c73bb1e491e4a0111
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57835520"
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Konfigurációs és felügyeleti problémák az Azure Cloud Services: Gyakori kérdések (GYIK)

Ez a cikk tartalmazza a konfigurációs és felügyeleti kapcsolatos problémák – gyakori kérdések [a Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Is tud kezelni a [Cloud Services Virtuálisgép-méretet lapján](cloud-services-sizes-specs.md) mérete információt.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

**Tanúsítványok**

- [Miért nem fejeződött be a tanúsítványlánc saját Felhőszolgáltatás SSL-tanúsítvány?](#why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete)
- [Mire használható a "Windows Azure eszközök titkosítási tanúsítványt a Extensions"?](#what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions)
- [Hogyan tudok generálhatnak-tanúsítvány aláírási kérelem (CSR) nélkül "RDP-ing" a példányhoz?](#how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance)
- [A Cloud Service felügyeleti tanúsítvány lejár. Hogyan újíthatja meg?](#my-cloud-service-management-certificate-is-expiring-how-to-renew-it)
- [Hogyan automatizálhatja az SSL-certificate(.pfx) fő és köztes certificate(.p7b) telepítését?](#how-to-automate-the-installation-of-main-ssl-certificatepfx-and-intermediate-certificatep7b)
- [Mire használható a "Microsoft Azure Service Management for MachineKey" tanúsítvány?](#what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate)

**Figyelés és naplózás**

- [Milyen az Azure Portalon, amely megkönnyíti a későbbi Felhőszolgáltatás képességekre kezelése és az alkalmazások figyelése?](#what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications)
- [Miért érdemes az IIS a naplózási könyvtár való írás leállítása?](#why-does-iis-stop-writing-to-the-log-directory)
- [Hogyan engedélyezhetem WAD naplózás a Cloud Services?](#how-do-i-enable-wad-logging-for-cloud-services)

**Hálózati konfiguráció**

- [Hogyan állíthatom be az üresjárati időkorlátot az Azure load balancer?](#how-do-i-set-the-idle-timeout-for-azure-load-balancer)
- [Hogyan társítható egy statikus IP-címet a Cloud Service?](#how-do-i-associate-a-static-ip-address-to-my-cloud-service)
- [Mik azok a funkciók és képességek, amely az Azure alapszintű IP-CÍMEK/Azonosítók és DDOS biztosít?](#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)
- [Hogyan lehet engedélyezni a HTTP/2 a Cloud Services virtuális Gépen?](#how-to-enable-http2-on-cloud-services-vm)

**Engedélyek**

- [A Microsoft belső mérnökei távoli asztal Felhőszolgáltatás-példányok engedélye nélkül is?](#can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission)
- [Cloud Service virtuális géphez a távoli asztal nem lehet az RDP-fájl használatával. Követések beolvasása e hiba: Hitelesítési hiba történt (kód: 0x80004005)](#i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005)

**Méretezés**

- [Nem skálázhatók túl az X példányok](#i-cannot-scale-beyond-x-instances)
- [Hogyan konfigurálhatok automatikus méretezést, memória-metrikák alapján?](#how-can-i-configure-auto-scale-based-on-memory-metrics)

**Általános**

- [Hogyan adhatok hozzá "nosniff" webhelyem?](#how-do-i-add-nosniff-to-my-website)
- [Hogyan testre az IIS webkiszolgálói szerepkörökhöz?](#how-do-i-customize-iis-for-a-web-role)
- [Mi az a kvótakorlát miatt a Cloud Services számára?](#what-is-the-quota-limit-for-my-cloud-service)
- [Miért jelenik meg nagyon kevés a szabad lemezterület a meghajtón, a virtuális gépemen Cloud Service?](#why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space)
- [Hogyan adhatom hozzá egy kártevőirtó bővítmény saját felhőszolgáltatások automatikus módon átalakítson?](#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)
- [Hogyan lehet engedélyezni a kiszolgálónév jelzése (SNI) Cloud Services?](#how-to-enable-server-name-indication-sni-for-cloud-services)
- [Hogyan adhatok hozzá címkéket az Azure Cloud Service?](#how-can-i-add-tags-to-my-azure-cloud-service)
- [Az Azure Portalon, a Cloud Service SDK-verzió nem jeleníti meg. Hogyan kaphatok, amely?](#the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that)
- [Állítsa le a Felhőszolgáltatás több hónapig szeretnék. Hogyan csökkenthető a számlázási költségeket felhőszolgáltatás az IP-cím elvesztése nélkül?](#i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address)


## <a name="certificates"></a>Tanúsítványok

### <a name="why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete"></a>Miért nem fejeződött be a tanúsítványlánc saját Felhőszolgáltatás SSL-tanúsítvány?
    
Azt javasoljuk, hogy telepítsék a teljes tanúsítványlánc (levél cert, köztes tanúsítványok és főtanúsítványt) csak a levél tanúsítvány helyett. A levéltanúsítvány telepítésekor támaszkodik a tanúsítványlánc készítését a megbízható tanúsítványok walking Windows. Ha időszakos hálózati vagy DNS-probléma lép fel az Azure-ban vagy a Windows Update Windows próbál érvényesíteni a tanúsítványt, akkor a tanúsítvány érvénytelen tekinthetők. A teljes tanúsítványlánc telepítése, a probléma elkerülhető. A blog: [kapcsolt SSL-tanúsítvány telepítése](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/) bemutatja, hogyan teheti ezt.

### <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>Mire használható a "Windows Azure eszközök titkosítási tanúsítványt a Extensions"?

Ezek a tanúsítványok automatikusan jönnek létre, amikor bekerül a Felhőszolgáltatás-bővítmény. Leggyakrabban a WAD-bővítmény vagy az RDP-bővítmény, de előfordulhat, hogy mások, például a kártevőirtó vagy naplógyűjtő bővítmény. Ezek a tanúsítványok csak titkosítása és visszafejtése a bővítmény privát konfigurációjának szolgálnak. A lejárati dátum nem érvényesítve, így nem számít, hogy a tanúsítvány lejárt. 

Ezek a tanúsítványok figyelmen kívül hagyhatja. Ha azt szeretné, a tanúsítványok megtisztítása, próbálja meg őket az összes törlése. Azure kivételt fogja kijelezni hiba, ha megpróbálja használatban lévő tanúsítvány törlése.

### <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>Hogyan tudok generálhatnak-tanúsítvány aláírási kérelem (CSR) nélkül "RDP-ing" a példányhoz?

Tekintse meg a következő dokumentum:

[Egy tanúsítványt a Windows Azure webhelyek (WAWS) beszerzése](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

A CSR-fájl egy szövegfájlba. Nincs hozhatók létre a gépen, amelyen a tanúsítványt végső soron fogja használni. Bár ez a dokumentum egy App Service-ben íródott, a CSR-fájl létrehozása általános és a Cloud Serviceshez is vonatkozik.

### <a name="my-cloud-service-management-certificate-is-expiring-how-to-renew-it"></a>A Cloud Service felügyeleti tanúsítvány lejár. Hogyan újíthatja meg?

A következő PowerShell-parancsok segítségével a felügyeleti tanúsítványok megújítása:

    Add-AzureAccount
    Select-AzureSubscription -Current -SubscriptionName <your subscription name>
    Get-AzurePublishSettingsFile

A **Get-AzurePublishSettingsFile** létrehoz egy új felügyeleti tanúsítványt **előfizetés** > **felügyeleti tanúsítványok** az Azure Portalon. Az új tanúsítvány neve "YourSubscriptionNam]-[CurrentDate] – hitelesítő adatok" tűnik.

### <a name="how-to-automate-the-installation-of-main-ssl-certificatepfx-and-intermediate-certificatep7b"></a>Hogyan automatizálhatja az SSL-certificate(.pfx) fő és köztes certificate(.p7b) telepítését?

Automatizálhatja ezt a feladatot egy indítási szkriptet (kötegelt/cmd/PowerShell) segítségével, és regisztrálja az indítási szkript a szolgáltatásdefiníciós fájlban. Adja hozzá az indítási szkript és a tanúsítvány (.p7b kiterjesztésű) a projektmappa fájllistájának az ugyanabban a könyvtárban az indítási szkript.

### <a name="what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate"></a>Mire használható a "Microsoft Azure Service Management for MachineKey" tanúsítvány?

Ez a tanúsítvány az Azure-alapú webes szerepkörök számítógépkulcsok titkosítására szolgál. További tudnivalókért tekintse meg a tanácsadó [https://docs.microsoft.com/security-updates/securityadvisories/2018/4092731].

További információkért tekintse át a következő cikkeket:
- [Indítási feladatok futtatásának a felhőszolgáltatások és konfigurálása](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks)
- [Gyakori Felhőszolgáltatás indítási feladatok](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks-common)

## <a name="monitoring-and-logging"></a>Monitorozás és naplózás

### <a name="what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications"></a>Milyen az Azure Portalon, amely megkönnyíti a későbbi Felhőszolgáltatás képességekre kezelése és az alkalmazások figyelése?

Létrehoz egy új tanúsítványt a távoli asztal protokoll (RDP) lehetőség hamarosan elérhető lesz. Másik lehetőségként futtassa ezt a szkriptet:

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
Válassza ki a blob- vagy helyi, a csdef és a szolgáltatáskonfigurációs séma feltöltése hely lehetősége hamarosan elérhető lesz. Használatával [New-AzureDeployment](/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-4.0.0), minden egyes hely érték között állítható be.

A példány szintjén metrikák monitorozását teszi lehetővé. További figyelési funkciók is elérhetők a [a Cloud Services monitorozása hogyan](cloud-services-how-to-monitor.md).

### <a name="why-does-iis-stop-writing-to-the-log-directory"></a>Miért érdemes az IIS a naplózási könyvtár való írás leállítása?
Már kipróbálta a naplózási könyvtár írása a helyi tárolási kvótát. A probléma megoldása három dolgot valamelyikét teheti:
* Diagnosztika engedélyezése az IIS és a diagnosztika rendszeres időközönként helyezte át a blob storage.
* Naplófájlok manuálisan távolítsa el a naplózási könyvtárat.
* Növelje a helyi erőforrások vonatkozó kvótakorlátját.

További információkért tekintse meg a következő dokumentumokat:
* [Diagnosztikai adatok tárolása és megtekintése az Azure Storage-ban](cloud-services-dotnet-diagnostics-storage.md)
* [IIS-naplók a Cloud Service-ben írás befejezése](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/)

### <a name="how-do-i-enable-wad-logging-for-cloud-services"></a>Hogyan engedélyezhetem WAD naplózás a Cloud Services?
Engedélyezheti a Windows Azure Diagnostics (WAD) naplózás használatával a következő beállításokat:
1. [A Visual Studióból engedélyezése](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)
2. [.NET-kódon keresztül engedélyezése](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics)
3. [Powershell-lel engedélyezése](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell)

A felhőszolgáltatás az aktuális WAD beállítások eléréséhez használható [Get-AzureServiceDiagnosticsExtensions](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell#get-current-diagnostics-extension-configuration) ps parancsot, vagy Ön is megtekinthetik az alkalmazást "Cloud Services--> bővítmények" panelen portálon keresztül.


## <a name="network-configuration"></a>Hálózati konfiguráció

### <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Hogyan állíthatom be az üresjárati időkorlátot az Azure load balancer?
A szolgáltatás definíciós (csdef) fájlban ehhez hasonló az időkorlát adhatja meg:

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
Lásd: [új: Az Azure Load Balancer konfigurálható üresjárati időkorlát](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/) további információt.

### <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>Hogyan társítható egy statikus IP-címet a Cloud Service?
Statikus IP-cím beállításához szeretne létrehozni a fenntartott IP-cím. A fenntartott IP-Címek társíthatók a egy új Felhőszolgáltatást vagy egy meglévő üzemelő példányt. A következő dokumentumokat a részleteket lásd:
* [Fenntartott IP-cím létrehozása](../virtual-network/virtual-networks-reserved-public-ip.md#manage-reserved-vips)
* [Egy létező felhőszolgáltatás IP-címének fenntartása](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
* [Új Felhőszolgáltatás fenntartott IP társítása](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-new-cloud-service)
* [Az üzemelő példányban fenntartott IP társítása](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-running-deployment)
* [Felhőszolgáltatás fenntartott IP társítása a szolgáltatás konfigurációs fájl segítségével](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

### <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Mik azok a funkciók és képességek, amely az Azure alapszintű IP-CÍMEK/Azonosítók és DDOS biztosít?
Az Azure IP-CÍMEK/Azonosítók rendelkezik, az Adatközpont fizikai kiszolgálók fenyegetések elleni védelemre. Emellett ügyfelei külső gyártók biztonsági megoldások, például a webalkalmazás-tűzfalak, hálózati tűzfalakat, kártevőirtó, behatolásérzékelési, megelőzési rendszerek (IDS/IPS) és több üzembe. További információkért lásd: [az adatokat és adategységeket, és a globális biztonsági szabványoknak való megfelelés](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity).

A Microsoft folyamatosan figyeli a kiszolgálók, hálózatok és alkalmazásokat a fenyegetések észlelését. Az Azure multipronged fenyegetés-felügyeleti megközelítést használ behatolásérzékelési, elosztott-szolgáltatásmegtagadásos (DDoS-) támadások megelőzésére, behatolási tesztelési, viselkedési elemzési, anomáliadetektálás, és gépi tanulási folyamatosan megerősítése a defense és csökkentheti a kockázatokat. A Microsoft Antimalware az Azure védi az Azure Cloud Services és virtual machines. Lehetősége van például a webes alkalmazás fire falak, hálózati tűzfalakat, kártevőirtó, a behatolásérzékelő és -megelőző rendszerek (IDS/IPS) és több ezenkívül üzembe helyezéséhez a külső biztonsági megoldásokat.

### <a name="how-to-enable-http2-on-cloud-services-vm"></a>Hogyan lehet engedélyezni a HTTP/2 a Cloud Services virtuális Gépen?

Windows 10 és Windows Server 2016 kapható HTTP/2 támogatása ügyfél és a kiszolgáló oldalán. Ha csatlakozik, az ügyfél (böngésző) TLS protokollon keresztüli az IIS-kiszolgálóra, amely egyezteti a HTTP/2 keresztüli TLS-bővítményeket, akkor nem kell semmilyen módosítást a kiszolgálói oldalon. Ez azért, mert a h2-14 fejléc adja meg a HTTP/2 használatát keresztüli TLS, alapértelmezés szerint továbbítja. Ha azonban az ügyfél egy frissítési fejléc frissítése a HTTP/2 küldi, majd szüksége módosítás alatt annak érdekében, hogy a frissítés működését, és végül egy HTTP/2-kapcsolatot a kiszolgálói oldalon. 

1. Futtassa a regedit.exe.
2. Keresse meg a beállításkulcsot: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters.
3. Hozzon létre egy új DWORD értéket **DuoEnabled**.
4. Állítsa az értékét 1-re.
5. Indítsa újra a kiszolgálót.
6. Nyissa meg a **Default Web Site** és **kötések**, hozzon létre egy új TLS-kötést az imént létrehozott önaláírt tanúsítványt. 

További információkért lásd:

- [HTTP/2 az IIS-kiszolgálón](https://blogs.iis.net/davidso/http2)
- [Videó: A Windows 10-es HTTP/2: Böngésző, az alkalmazások és a webalkalmazás-kiszolgáló](https://channel9.msdn.com/Events/Build/2015/3-88)
         

Ezeket a lépéseket sikerült automatikus egy indítási feladaton keresztül, így, amikor új PaaS-példány jön létre, azt megteheti a beállításjegyzék változásainak fent. További információkért lásd: [konfigurálása és a egy felhőalapú szolgáltatás indítási feladatok futtatásának](cloud-services-startup-tasks.md).

 
Miután ez megtörtént, ellenőrizheti a HTTP/2 engedélyezve van-e, vagy nem a következő módszerek egyikével:

- Engedélyezi az IIS-naplók protokoll verziója, és keresse meg az IIS-naplók. HTTP/2 megjelenik a naplókban. 
- Engedélyezze a F12 fejlesztői eszközét, az Internet Explorer és Microsoft Edge-ben, és váltson a hálózat lapon ellenőrizze a protokollt. 

További információkért lásd: [HTTP/2 az IIS-kiszolgálón](https://blogs.iis.net/davidso/http2).

## <a name="permissions"></a>Engedélyek

### <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Hogyan lehet megvalósítani a Cloud Services szerepköralapú hozzáférés?
A cloud Services nem támogatja a szerepköralapú hozzáférés-vezérlés (RBAC) modellben a, mert nem egy Azure Resource Manager-alapú szolgáltatás.

Lásd: [megismerheti az Azure-ban a különféle szerepkörök](../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="remote-desktop"></a>Távoli asztal

### <a name="can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission"></a>A Microsoft belső mérnökei távoli asztal Felhőszolgáltatás-példányok engedélye nélkül is?
A Microsoft szigorú folyamat, amely nem teszi lehetővé a távoli asztal belső mérnökei azokat a Felhőszolgáltatás (e-mailben vagy egyéb írásbeli kommunikációs) írásos engedélye nélkül a webhely tulajdonosa vagy az általa kijelölt szervet követi.

### <a name="i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005"></a>Cloud Service virtuális géphez a távoli asztal nem lehet az RDP-fájl használatával. Követések beolvasása e hiba: Hitelesítési hiba történt (kód: 0x80004005)

Ez a hiba akkor fordulhat elő, ha az Azure Active Directory tartományhoz csatlakozó gépről az RDP-fájlt használ. A probléma megoldásához kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal a letöltött RDP-fájlt, majd **szerkesztése**.
2. Hozzáadása "&#92;", mielőtt a felhasználónév előtag. Például **. \username** helyett **felhasználónév**.

## <a name="scaling"></a>Méretezés

### <a name="i-cannot-scale-beyond-x-instances"></a>Nem skálázhatók túl az X példányok
Az Azure-előfizetés esetében használható Processzormagok száma. Skálázás nem működik, ha az összes rendelkezésre álló magot használja. Például ha a korlát 100 maggal rendelkezik, ez azt jelenti, A1 méretű virtuális gép 100 példány lehet a felhőalapú szolgáltatás, vagy 50 A2 méretű virtuálisgép-példányok.

### <a name="how-can-i-configure-auto-scale-based-on-memory-metrics"></a>Hogyan konfigurálhatok automatikus méretezést, memória-metrikák alapján?

Automatikus skálázás memória-metrikák alapján a Cloud Services jelenleg nem támogatott. 

Ez a probléma megoldásához használhatja az Application Insights. Automatikus skálázás támogatja az Application Insights-metrikák forrásként, és skálázhatja a szerepkör-példányok számát, például a "Memória" Vendég mérőszám alapján.  Konfigurálja az Application Insights a Felhőszolgáltatás-projekt csomagfájl (csak *.cspkg), és a szolgáltatás a feat megvalósítása az Azure diagnosztikai bővítmény engedélyezése kell.

Hogyan hasznosítható egy egyéni metrika az Application Insights konfigurálása az automatikus méretezés a Cloud Services használatával a további részletekért lásd: [első lépésként az automatikus skálázás egyéni metrika az Azure-ban](../azure-monitor/platform/autoscale-custom-metric.md)

A Cloud Services integrálása az Azure Diagnostics az Application Insights további információkért lásd: [diagnosztikai adatok küldése a felhőalapú szolgáltatás, a virtuális gép vagy a Service Fabric Application Insights](../azure-monitor/platform/diagnostics-extension-to-application-insights.md)

További információ az Application Insights engedélyezése a Cloud Services szolgáltatáshoz, talál [Application Insights az Azure Cloud Services](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices)

Azure diagnosztikai naplózás engedélyezése a Cloud Services kapcsolatos további információkért lásd: [diagnosztika beállítása az Azure Cloud Services és virtual machines](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)

## <a name="generic"></a>Általános

### <a name="how-do-i-add-nosniff-to-my-website"></a>Hogyan adhatok hozzá "nosniff" webhelyem?
A beállítás hozzáadásához, hogy megakadályozza a MIME-típusok elemzés, a *web.config* fájlt.

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

Azt is megteheti ezt az IIS-beállításként. Használja a következő parancsot a [gyakori indítási feladatok](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) cikk.

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

### <a name="how-do-i-customize-iis-for-a-web-role"></a>Hogyan testre az IIS webkiszolgálói szerepkörökhöz?
Az IIS indítási parancsfájlt használja a [gyakori indítási feladatok](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) cikk.

### <a name="what-is-the-quota-limit-for-my-cloud-service"></a>Mi az a kvótakorlát miatt a Cloud Services számára?
Lásd: [szolgáltatásspecifikus korlátozza](../azure-subscription-service-limits.md#subscription-limits).

### <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>Miért jelenik meg nagyon kevés a szabad lemezterület a meghajtón, a virtuális gépemen Cloud Service?
Ez az elvárt működés, és azt nem okozhat az alkalmazás bármilyen probléma. Az Azure PaaS virtuális gépeken, amely lényegében használ fel, double, amely fájlok általában foglalnak terület mennyisége a % approot % meghajtót be van kapcsolva a naplózást. Azonban, több szempontot érdemes figyelembe vennie, amely lényegében információkká ez nem probléma.

Meghajtó mérete % approot % számítjuk ki, hogy < mérete .cspkg + napló maximális mérete > + margó szabad terület, vagy 1,5 GB, amelyik érték nagyobb. A virtuális gép mérete nem befolyásolta ehhez a számításhoz. (A Virtuálisgép-méret csak befolyásolja az ideiglenes C: meghajtó méretét.) 

Írni próbált a % approot % meghajtó nem támogatja. Ha az Azure virtuális géphez, kell teheti meg egy ideiglenes LocalStorage erőforrás (vagy a másik lehetőség, például a Blob storage-ban az Azure Files, stb.). Így nincs értelme az approot % % mappában lévő szabad terület mennyisége. Ha nem biztos benne, hogy az alkalmazás a % approot % meghajtóra ír, mindig engedélyezheti, a szolgáltatás néhány napig futtassa, és hasonlítsa össze az "előtte" és "utána" méreteket. 

Az Azure nem fog kiírni, semmit a % approot % meghajtóra. Után a virtuális Merevlemezt a .cspkg alapján létrehozott, és azokat az Azure virtuális gép csatlakoztatva van, előfordulhat, hogy a meghajtó írni egyedül az alkalmazás. 

A napló beállításra nem konfigurálható, így Ön nem kapcsolhatja ki.

### <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>Hogyan adhatom hozzá egy kártevőirtó bővítmény saját felhőszolgáltatások automatikus módon átalakítson?

A kártevőirtó bővítmény indítási feladat a PowerShell-parancsfájl használatával engedélyezheti. Kövesse ezeket a cikkeket, megvalósításához: 
 
- [PowerShell indítási feladat létrehozása](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Set-AzureServiceAntimalwareExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/Set-AzureServiceAntimalwareExtension?view=azuresmps-4.0.0 )

Kártevőirtó központi telepítési forgatókönyvei és engedélyezését a portálról kapcsolatos további információkért lásd: [kártevőirtó központi telepítési forgatókönyv](../security/azure-security-antimalware.md#antimalware-deployment-scenarios).

### <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>Hogyan lehet engedélyezni a kiszolgálónév jelzése (SNI) Cloud Services?

A Cloud Services SNI engedélyezheti az alábbi módszerek egyikének használatával:

**1. módszer: PowerShell-lel**

Az SNI-kötés a PowerShell-parancsmag használatával konfigurálható **New-WebBinding** az egy indítási feladat egy felhőalapú szolgáltatás a szerepkörpéldányhoz, az alábbi:
    
    New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags 
    
Amint [Itt](https://technet.microsoft.com/library/ee790567.aspx), a $sslFlags az értékek a következők valamelyike lehet:

|Érték|Jelentés|
------|------
|0|Nincs SNI|
|1|SNI engedélyezve |
|2 |Kötelező központi tanúsítvány Store használó nem SNI|
|3|SNI-kötést használó központi tárolására |
 
**2. módszer: Kód használata**

Az SNI-kötés sikerült is konfigurálni a szerepkör elindítása a kód keresztül ez leírt [blogbejegyzés](https://blogs.msdn.microsoft.com/jianwu/2014/12/17/expose-ssl-service-to-multi-domains-from-the-same-cloud-service/):

    
    //<code snip> 
                    var serverManager = new ServerManager(); 
                    var site = serverManager.Sites[0]; 
                    var binding = site.Bindings.Add(“:443:www.test1.com”, newCert.GetCertHash(), “My”); 
                    binding.SetAttributeValue(“sslFlags”, 1); //enables the SNI 
                    serverManager.CommitChanges(); 
    //</code snip> 
    
A fenti módszerek bármelyikével a megadott gazdanevek megfelelő tanúsítványának (*.pfx) kell először telepíteni egy indítási feladat segítségével szerepkörpéldányain vagy kódot ahhoz, hogy a hatékony SNI-kötés használatával.

### <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>Hogyan adhatok hozzá címkéket az Azure Cloud Service? 

Felhőszolgáltatások, a klasszikus erőforrások. Csak erőforrások Azure Resource Manager támogatása a címkék lettek létrehozva. A klasszikus erőforrások, például a felhőalapú szolgáltatás nem tudja címkékkel. 

### <a name="the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that"></a>Az Azure Portalon, a Cloud Service SDK-verzió nem jeleníti meg. Hogyan kaphatok, amely?

Dolgozunk, ez a szolgáltatás elérhetővé az Azure Portalon. Eközben segítségével a következő PowerShell-parancsokat az SDK-verzió beolvasása:

    Get-AzureService -ServiceName "<Cloud Service name>" | Get-AzureDeployment | Where-Object -Property SdkVersion -NE -Value "" | select ServiceName,SdkVersion,OSVersion,Slot

### <a name="i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address"></a>Állítsa le a Felhőszolgáltatás több hónapig szeretnék. Hogyan csökkenthető a számlázási költségeket felhőszolgáltatás az IP-cím elvesztése nélkül?

Egy már üzembe helyezett felhőalapú szolgáltatás a számítási és a használt tárolási díjat számítjuk fel. Tehát akkor is, ha az Azure virtuális gép leállítása, továbbra is felszámolt tárolására. 

Íme, mi mindent csökkentése érdekében a számlázás a szolgáltatás IP-címét elvesztése nélkül:

1. [IP-címének fenntartása](../virtual-network/virtual-networks-reserved-public-ip.md) az üzemelő példányok törlése előtt.  Ön csak kell fizetnie az IP-címet. IP-cím számlázással kapcsolatos további információkért lásd: [IP-címek díjszabása](https://azure.microsoft.com/pricing/details/ip-addresses/).
2. Törölje a központi telepítéseket. Ne törölje a xxx.cloudapp.net, így a jövőben használhatja.
3. Ha a Felhőszolgáltatás újra a azonos fenntartott IP-cím használatával szeretné, hogy az előfizetésben lefoglalt, lásd: [fenntartott IP-címek a Cloud Services és Virtual Machines](https://azure.microsoft.com/blog/reserved-ip-addresses/).

