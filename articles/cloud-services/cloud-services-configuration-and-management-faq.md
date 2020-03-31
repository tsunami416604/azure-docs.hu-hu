---
title: Konfigurációs és kezelési kérdések – gyakori kérdések
titleSuffix: Azure Cloud Services
description: Ez a cikk a Microsoft Azure Cloud Services konfigurációjával és kezelésével kapcsolatos gyakori kérdéseket sorolja fel.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/23/2018
ms.author: genli
ms.openlocfilehash: 40abd048b047bbece79b7c05d36a1fb189a4f28d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656925"
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Az Azure Cloud Services konfigurációs és kezelési problémái: gyakori kérdések (gyakori kérdések)

Ez a cikk a Microsoft Azure Cloud Services konfigurációs és kezelési problémáival kapcsolatos gyakori kérdéseket [tartalmazza.](https://azure.microsoft.com/services/cloud-services) A [felhőszolgáltatások virtuális gép méretlapján](cloud-services-sizes-specs.md) is megtekintheti a méretadatokat.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

**Tanúsítványok**

- [Miért nem teljes a Cloud Service SSL-tanúsítványtanúsítványlánca?](#why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete)
- [Mi a célja a "Windows Azure Tools titkosítási tanúsítvány a bővítményekhez"?](#what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions)
- [Hogyan hozhatok létre tanúsítvány-aláíró kérelmet (CSR) anélkül, hogy "RDP-ing" lenne a példányban?](#how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance)
- [A felhőszolgáltatás-kezelési tanúsítványom lejár. Hogyan lehet megújítani?](#my-cloud-service-management-certificate-is-expiring-how-to-renew-it)
- [Hogyan lehet automatizálni a fő SSL tanúsítvány(.pfx) és a köztes tanúsítvány(.p7b)?](#how-to-automate-the-installation-of-main-ssl-certificatepfx-and-intermediate-certificatep7b)
- [Mi a célja a "Microsoft Azure Service Management for MachineKey" tanúsítványnak?](#what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate)

**Figyelés és naplózás**

- [Melyek a közelgő Cloud Service-képességek az Azure Portalon, amelyek segíthetnek az alkalmazások kezelésében és figyelésében?](#what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications)
- [Miért hagyja abba az IIS írását a naplókönyvtárba?](#why-does-iis-stop-writing-to-the-log-directory)
- [Hogyan engedélyezhetem a WAD naplózását a Felhőszolgáltatásokszámára?](#how-do-i-enable-wad-logging-for-cloud-services)

**Hálózati konfiguráció**

- [Hogyan állíthatom be az Azure terheléselosztó tétlen időtúlterhelését?](#how-do-i-set-the-idle-timeout-for-azure-load-balancer)
- [Hogyan társíthatok statikus IP-címet a felhőszolgáltatásomhoz?](#how-do-i-associate-a-static-ip-address-to-my-cloud-service)
- [Melyek azok a funkciók és képességek, amelyeket az Azure alapvető IPS/IDS és DDOS nyújt?](#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)
- [Http/2 engedélyezése a Cloud Services virtuális gépén?](#how-to-enable-http2-on-cloud-services-vm)

**Engedélyek**

- [A Microsoft belső mérnökei engedély nélkül tudnak távoli asztalt a Cloud Service-példányokba?](#can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission)
- [Az RDP-fájl használatával nem lehet az asztalt a Felhőszolgáltatás virtuális gépére használni. A következő hibaüzenet jelenik meg: Hitelesítési hiba történt (Kód: 0x80004005)](#i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005)

**Méretezés**

- [Nem tudok az X példányokon túli skálázható](#i-cannot-scale-beyond-x-instances)
- [Hogyan konfigurálhatom az automatikus méretezést a memóriametrikák alapján?](#how-can-i-configure-auto-scale-based-on-memory-metrics)

**Általános**

- [Hogyan vehetek fel "nosniff"-ot a honlapomra?](#how-do-i-add-nosniff-to-my-website)
- [Hogyan szabhatom testre az IIS-t webes szerepkörhöz?](#how-do-i-customize-iis-for-a-web-role)
- [Mi a felhőszolgáltatáskvóta-korlát?](#what-is-the-quota-limit-for-my-cloud-service)
- [Miért mutat a Cloud Service virtuális gép meghajtója nagyon kevés szabad lemezterületet?](#why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space)
- [Hogyan adhatok hozzá kártevőirtó bővítményt a Felhőszolgáltatásaimhoz automatizált módon?](#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)
- [Hogyan lehet engedélyezni a kiszolgálónév-jelzést (SNI) a Felhőszolgáltatások számára?](#how-to-enable-server-name-indication-sni-for-cloud-services)
- [Hogyan adhatok hozzá címkéket az Azure Cloud Szolgáltatásomhoz?](#how-can-i-add-tags-to-my-azure-cloud-service)
- [Az Azure Portalon nem jelenik meg a felhőszolgáltatás SDK-verziója. Hogy szerezhetnék meg?](#the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that)
- [Több hónapra le szeretném állítani a felhőszolgáltatást. Hogyan csökkentheti a felhőszolgáltatás számlázási költségét az IP-cím elvesztése nélkül?](#i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address)


## <a name="certificates"></a>Tanúsítványok

### <a name="why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete"></a>Miért nem teljes a Cloud Service SSL-tanúsítványtanúsítványlánca?
    
Azt javasoljuk, hogy az ügyfelek a levéltanúsítvány helyett a teljes tanúsítványláncot (levéltanúsítvány, köztes tanúsítvány és gyökértanúsítvány) telepítsék. Ha csak a levéltanúsítványt telepíti, a Windows támaszkodik a tanúsítványlánc létrehozásához a megbízható tanúsítványok listájának megjárásával. Ha a Windows a tanúsítvány érvényesítését megkísérlésekor időszakos hálózati vagy DNS-problémák merülnek fel az Azure-ban vagy a Windows Update szolgáltatásban, előfordulhat, hogy a tanúsítvány érvénytelennek minősül. A teljes tanúsítványlánc telepítésével ez a probléma elkerülhető. A blog a [Hogyan kell telepíteni egy láncolt SSL tanúsítvány](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/) megmutatja, hogyan kell ezt csinálni.

### <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>Mi a célja a "Windows Azure Tools titkosítási tanúsítvány a bővítményekhez"?

Ezek a tanúsítványok automatikusan létrejönnek, amikor egy bővítményt hozzáadnak a felhőszolgáltatáshoz. Leggyakrabban ez a WAD-bővítmény vagy az RDP-bővítmény, de lehet, hogy mások, például a kártevőirtó vagy a Naplógyűjtő bővítmény. Ezek a tanúsítványok csak a bővítmény privát konfigurációjának titkosítására és visszafejtésére szolgálnak. A lejárati dátum soha nem ellenőrzi, így nem számít, ha a tanúsítvány lejárt. 

Ezeket a tanúsítványokat figyelmen kívül hagyhatja. Ha meg szeretné tisztítani a tanúsítványokat, próbálja meg mindet törölte. Az Azure hibát fog eljelenni, ha egy használatban lévő tanúsítványt próbál törölni.

### <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>Hogyan hozhatok létre tanúsítvány-aláíró kérelmet (CSR) anélkül, hogy "RDP-ing" lenne a példányban?

Lásd a következő útmutatót:

[Tanúsítvány beszerzése a Windows Azure webhelyekhez (WAWS) való használatra](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

Az CSR csak egy szöveges fájl. Nem kell létrehozni a gép, ahol a tanúsítványt végül használni.Bár ez a dokumentum egy App Service-hez íródott, az ügyfélszolgálati szolgáltatás létrehozása általános, és a Felhőszolgáltatásokra is vonatkozik.

### <a name="my-cloud-service-management-certificate-is-expiring-how-to-renew-it"></a>A felhőszolgáltatás-kezelési tanúsítványom lejár. Hogyan lehet megújítani?

A következő PowerShell-parancsok segítségével megújíthatja a felügyeleti tanúsítványokat:

    Add-AzureAccount
    Select-AzureSubscription -Current -SubscriptionName <your subscription name>
    Get-AzurePublishSettingsFile

A **Get-AzurePublishSettingsFile** új felügyeleti tanúsítványt hoz létre az **Azure Portalon** > az**Előfizetés-kezelési tanúsítványokban.** Az új tanúsítvány neve a "YourSubscriptionNam]-[CurrentDate]-credentials" néven jelenik meg.

### <a name="how-to-automate-the-installation-of-main-ssl-certificatepfx-and-intermediate-certificatep7b"></a>Hogyan lehet automatizálni a fő SSL tanúsítvány(.pfx) és a köztes tanúsítvány(.p7b)?

Ezt a feladatot automatizálhatja egy indítási parancsfájl (batch/cmd/PowerShell) használatával, és regisztrálhatja az indítóparancsfájlt a szolgáltatásdefiníciós fájlban. Adja hozzá az indítási parancsfájlt és a certificate(.p7b fájlt) is az indítóparancsfájl ugyanazon könyvtárának projektmappájába.

### <a name="what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate"></a>Mi a célja a "Microsoft Azure Service Management for MachineKey" tanúsítványnak?

Ez a tanúsítvány az Azure Web Roles számítógép-kulcsainak titkosítására szolgál. További információért tekintse meg [ezt a tanácsadót.](https://docs.microsoft.com/security-updates/securityadvisories/2018/4092731)

További információkért tekintse át a következő cikkeket:
- [Az indítási feladatok konfigurálása és futtatása felhőalapú szolgáltatáshoz](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks)
- [Gyakori felhőszolgáltatás indítási feladatok](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks-common)

## <a name="monitoring-and-logging"></a>Monitorozás és naplózás

### <a name="what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications"></a>Melyek a közelgő Cloud Service-képességek az Azure Portalon, amelyek segíthetnek az alkalmazások kezelésében és figyelésében?

Hamarosan létrejön egy új tanúsítvány a Távoli asztali protokoll (RDP) számára. Azt is megteheti, hogy futtatja ezt a parancsfájlt:

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
Képes választani blob vagy helyi a csdef és cscfg feltöltési hely hamarosan. A [New-AzureDeployment](/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-4.0.0)használatával beállíthatja az egyes helyértékeket.

Metrikák figyelése a példány szintjén. További figyelési lehetőségek érhetők el a [Hogyan figyelheti a felhőszolgáltatásokat.](cloud-services-how-to-monitor.md)

### <a name="why-does-iis-stop-writing-to-the-log-directory"></a>Miért hagyja abba az IIS írását a naplókönyvtárba?
Kimerítette a naplókönyvtárba íráshelyi tárolási kvótáját.Ennek kijavításához a következő három dolgot teheti:
* Engedélyezze az IIS diagnosztikát, és a diagnosztikát rendszeresen áthelyezze a blobstorage-ba.
* A naplófájlok manuális eltávolítása a naplózási könyvtárból.
* Növelje a helyi erőforrások kvótakorlátját.

További információ a következő dokumentumokban található:
* [Diagnosztikai adatok tárolása és megtekintése az Azure Storage szolgáltatásban](/azure/storage/common/storage-introduction)
* [Az IIS-naplók nem írnak a Felhőszolgáltatásban](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/)

### <a name="how-do-i-enable-wad-logging-for-cloud-services"></a>Hogyan engedélyezhetem a WAD naplózását a Felhőszolgáltatásokszámára?
A Windows Azure Diagnosztika (WAD) naplózását a következő beállításokkal engedélyezheti:
1. [Engedélyezés a Visual Studióból](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)
2. [Engedélyezés .NET kódon keresztül](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics)
3. [Engedélyezés a PowerShellen keresztül](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell)

Annak érdekében, hogy a felhőszolgáltatás aktuális WAD-beállításait, használhatja [a Get-AzureServiceDiagnosticsExtensions](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell#get-current-diagnostics-extension-configuration) ps cmd, vagy megtekintheti a portálon keresztül a "Cloud Services --> Extensions" panelen.


## <a name="network-configuration"></a>Hálózati konfiguráció

### <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Hogyan állíthatom be az Azure terheléselosztó tétlen időtúlterhelését?
Az időhosszabbítást a szolgáltatásdefiníciós (csdef) fájlban a következőkhez hasonlóan adhatja meg:

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
További információ: [Konfigurálható tétlen időtúlterhelés az Azure Load Balancer számára.](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/)

### <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>Hogyan társíthatok statikus IP-címet a felhőszolgáltatásomhoz?
Statikus IP-cím beállításához létre kell hoznia egy fenntartott IP-címet. Ez a fenntartott IP-cím társítható egy új felhőszolgáltatáshoz vagy egy meglévő központi telepítéshez. A részleteket lásd a következő dokumentumokban:
* [Fenntartott IP-cím létrehozása](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#manage-reserved-vips)
* [Meglévő felhőszolgáltatás IP-címének lefoglalása](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#reserve-the-ip-address-of-an-existing-cloud-service)
* [Fenntartott IP-cím társítása új felhőszolgáltatáshoz](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-new-cloud-service)
* [Fenntartott IP-cím társítása futó központi telepítéshez](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-running-deployment)
* [Fenntartott IP-cím társítása felhőszolgáltatáshoz szolgáltatáskonfigurációs fájl használatával](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

### <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Melyek azok a funkciók és képességek, amelyeket az Azure alapvető IPS/IDS és DDOS nyújt?
Az Azure ips/IDS-t az adatközpont fizikai kiszolgálóiban a fenyegetések elleni védelemre. Emellett az ügyfelek külső gyártótól származó biztonsági megoldásokat is telepíthetnek, például webalkalmazás-tűzfalakat, hálózati tűzfalakat, kártevőirtókat, behatolásészlelést, megelőzési rendszereket (IDS/IPS) és egyebek. További információt az [Adatok és eszközök védelme és a globális biztonsági szabványoknak való megfelelés című témakörben talál.](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity)

A Microsoft folyamatosan figyeli a kiszolgálókat, hálózatokat és alkalmazásokat a fenyegetések észlelése érdekében. Az Azure többrétű fenyegetéskezelési megközelítése behatolásészlelést, elosztott szolgáltatásmegtagadási (DDoS) támadásmegelőzést, behatolástesztelést, viselkedéselemzést, anomáliadetektálást és gépi tanulást használ a védelem folyamatos erősítése érdekében és csökkenti a kockázatokat. Az Azure-hoz való Microsoft Antimalware védi az Azure Cloud Services szolgáltatást és a virtuális gépeket. Lehetősége van külső biztonsági megoldások telepítésére is, például a webalkalmazások tűzfalai, a hálózati tűzfalak, a kártevőirtó, a behatolásészlelési és -megelőzési rendszerek (IDS/IPS) stb.

### <a name="how-to-enable-http2-on-cloud-services-vm"></a>Http/2 engedélyezése a Cloud Services virtuális gépén?

A Windows 10 és a Windows Server 2016 támogatja a HTTP/2-t mind az ügyfél-, mind a kiszolgálóoldalon. Ha az ügyfél (böngésző) a HTTP/2-t TLS-bővítményeken keresztül egyeztetett TLS-kiszolgálón keresztül csatlakozik az IIS-kiszolgálóhoz, akkor a kiszolgálóoldalon nem kell módosítania. Ennek az az oka, hogy a TLS-en keresztül a HTTP/2 használatát megmeghatározó h2-14 fejléc alapértelmezés szerint elküldésre kerül. Ha viszont az ügyfél egy frissítési fejlécet küld a HTTP/2-re való frissítéshez, akkor az alábbi módosítást a kiszolgálóoldalon kell elvégeznie annak érdekében, hogy a frissítés működjön, és http/2-kapcsolatjön létre. 

1. Futtassa a regedit.exe fájlt.
2. Tallózással keresse meg a rendszerleíró kulcsot: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters.
3. Hozzon létre egy **DuoEnabled**nevű új duplaszó értéket.
4. Állítsa az értékét 1-re.
5. Indítsa újra a kiszolgálót.
6. Nyissa meg az **alapértelmezett webhelyet,** és a **Kötések**területen hozzon létre egy új TLS-kötést az imént létrehozott önaláírt tanúsítvánnyal. 

További információkért lásd:

- [HTTP/2 az IIS-en](https://blogs.iis.net/davidso/http2)
- [Videó: HTTP/2 a Windows 10-ben: Böngésző, alkalmazások és webkiszolgáló](https://channel9.msdn.com/Events/Build/2015/3-88)
         

Ezek a lépések egy indítási feladaton keresztül automatizálhatók, így amikor új PaaS-példány jön létre, a fenti módosításokat a rendszerleíró adatbázisban végezheti el. További információt a [Felhőszolgáltatások indítási feladatainak konfigurálása és futtatása című témakörben talál.](cloud-services-startup-tasks.md)

 
Miután ez megtörtént, az alábbi módszerek egyikével ellenőrizheti, hogy a HTTP/2 engedélyezve van-e vagy sem:

- Engedélyezze a protokollverziót az IIS-naplókban, és tekintse meg az IIS-naplókat. Http/2 lesz a naplókban. 
- Engedélyezze az F12 fejlesztői eszköz engedélyezését az Internet Explorer ben vagy a Microsoft Edge-ben, és váltson a Hálózat lapra a protokoll ellenőrzéséhez. 

További információ: [HTTP/2 on IIS](https://blogs.iis.net/davidso/http2).

## <a name="permissions"></a>Engedélyek

### <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Hogyan valósíthatom meg a szerepköralapú hozzáférést a Felhőszolgáltatásokhoz?
A Cloud Services nem támogatja a szerepköralapú hozzáférés-vezérlési (RBAC) modellt, mivel nem egy Azure Resource Manager-alapú szolgáltatás.

Lásd: [A különböző szerepkörök az Azure-ban.](../role-based-access-control/rbac-and-directory-admin-roles.md)

## <a name="remote-desktop"></a>Távoli asztal

### <a name="can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission"></a>A Microsoft belső mérnökei engedély nélkül tudnak távoli asztalt a Cloud Service-példányokba?
A Microsoft szigorú eljárást követ, amely nem teszi lehetővé a belső mérnökök számára, hogy a tulajdonos vagy a tulajdonos írásos engedélye (e-mail vagy más írásos kommunikáció) nélkül távoli asztalt helyezzenek a Felhőszolgáltatásba.

### <a name="i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005"></a>Az RDP-fájl használatával nem lehet az asztalt a Felhőszolgáltatás virtuális gépére használni. A következő hibaüzenet jelenik meg: Hitelesítési hiba történt (Kód: 0x80004005)

Ez a hiba akkor fordulhat elő, ha az RDP-fájlt az Azure Active Directoryhoz csatlakozott gépről használja. A probléma megoldásához kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal a letöltött RDP-fájlra, majd válassza **a Szerkesztés parancsot.**
2. Adja hozzá a "&#92;" előtagot a felhasználónév elé. Például a **felhasználónév**helyett használja a **.\felhasználónevet.**

## <a name="scaling"></a>Méretezés

### <a name="i-cannot-scale-beyond-x-instances"></a>Nem tudok az X példányokon túli skálázható
Az Azure-előfizetés korlátozza a magok száma használható. A méretezés nem fog működni, ha az összes rendelkezésre álló magot használta. Ha például 100 maggal rendelkezik, ez azt jelenti, hogy 100 A1-es méretű virtuálisgép-példányt használhat a felhőszolgáltatáshoz, vagy 50 A2-es méretű virtuálisgép-példányt.

### <a name="how-can-i-configure-auto-scale-based-on-memory-metrics"></a>Hogyan konfigurálhatom az automatikus méretezést a memóriametrikák alapján?

A felhőszolgáltatások memóriametrikákon alapuló automatikus méretezése jelenleg nem támogatott. 

A probléma megoldásához használhatja az Application Insights. Az automatikus méretezés metrikaforrásként támogatja az Application Insights-ot, és a vendégmetrika, például a "Memória" alapján skálázhatja a szerepkörpéldányok számát.  Az Application Insightskonfigurálást a Cloud Service projektcsomag-fájljában (*.cspkg) kell konfigurálnia, és engedélyeznie kell az Azure Diagnostics bővítményt a szolgáltatáson a bravúr megvalósításához.

Az egyéni metrikák használatával a felhőszolgáltatások automatikus méretezése konfigurálásához az Application Insights segítségével kapcsolatos további részletekért olvassa el az [Automatikus méretezés egyéni metrika szerint történő konfigurálását az Azure-ban című témakörben.](../azure-monitor/platform/autoscale-custom-metric.md)

Az Azure Diagnostics és az Application Insights for Cloud Services szolgáltatással való integrálásáról a [Felhőszolgáltatás, a virtuális gép vagy a Service Fabric diagnosztikai adatainak küldése az Application Insightsba](../azure-monitor/platform/diagnostics-extension-to-application-insights.md) című témakörben talál további információt.

Az Application Insights for Cloud Services engedélyezéséről az [Application Insights for Azure Cloud Services](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices) című témakörben talál további információt.

Az Azure Diagnostics Logging for Cloud Services engedélyezéséről az [Azure Cloud Services és a virtuális gépek diagnosztika beállítása című témakörben](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them) talál további információt.

## <a name="generic"></a>Általános

### <a name="how-do-i-add-nosniff-to-my-website"></a>Hogyan vehetek fel "nosniff"-ot a honlapomra?
Ha meg szeretné akadályozni, hogy az ügyfelek megszagolják a MIME-típusokat, adjon hozzá egy beállítást a *web.config* fájlhoz.

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

Ezt az IIS-ben is hozzáadhatja beállításként. Használja a következő parancsot a [gyakori indítási feladatok](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) cikkben.

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

### <a name="how-do-i-customize-iis-for-a-web-role"></a>Hogyan szabhatom testre az IIS-t webes szerepkörhöz?
Használja az IIS indítási parancsfájlt a [gyakori indítási feladatok](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) cikkből.

### <a name="what-is-the-quota-limit-for-my-cloud-service"></a>Mi a felhőszolgáltatáskvóta-korlát?
Lásd: [Szolgáltatásspecifikus korlátozások](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits).

### <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>Miért mutat a Cloud Service virtuális gép meghajtója nagyon kevés szabad lemezterületet?
Ez a várt viselkedés, és nem okozhat semmilyen problémát az alkalmazás. Naplózás van kapcsolva a %approot% meghajtó az Azure PaaS virtuális gépek, amely lényegében kétszer annyi helyet foglal el, amely et a fájlok általában foglalnak. Azonban van néhány dolog, hogy tudatában legyünk, hogy lényegében kapcsolja be ezt a nem kérdés.

A %approot% meghajtó mérete \<.cspkg + maximális naplóméret + szabad terület> vagy 1,5 GB, attól függően, hogy melyik a nagyobb. A virtuális gép mérete nincs hatással erre a számításra. (A virtuális gép mérete csak az ideiglenes C: meghajtó méretét befolyásolja.) 

Nem támogatott a %approot% meghajtóra írni. Ha az Azure virtuális gép, ezt egy ideiglenes LocalStorage erőforrás (vagy más lehetőség, például a Blob storage, Azure Files, stb. Így a %approot% mappában lévő szabad terület mennyisége nem értelmezhető. Ha nem biztos abban, hogy az alkalmazás a %approot% meghajtóra ír-e, bármikor lehetővé teheti a szolgáltatás futtatását néhány napig, majd összehasonlíthatja az "előtte" és az "utána" méretet. 

Az Azure nem ír semmit a %approot% meghajtóra. Miután a virtuális merevlemez a .cspkg-ból jön létre, és az Azure virtuális géphez van csatlakoztatva, az egyetlen dolog, ami írhat erre a meghajtóra, az alkalmazás. 

A naplóbeállítások nem konfigurálhatók, ezért nem lehet kikapcsolni.

### <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>Hogyan adhatok hozzá kártevőirtó bővítményt a Felhőszolgáltatásaimhoz automatizált módon?

Az indítási feladatban engedélyezheti a Kártevőirtó bővítményt a PowerShell-parancsfájl használatával. A megvalósításhoz kövesse az alábbi cikkekben leírt lépéseket: 
 
- [PowerShell-indítási feladat létrehozása](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Set-AzureServiceAntimalwareExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/Set-AzureServiceAntimalwareExtension?view=azuresmps-4.0.0 )

A kártevőirtó telepítési forgatókönyvekről és a portálról való engedélyezéséről a [Kártevők telepítésének forgatókönyvei](../security/fundamentals/antimalware.md#antimalware-deployment-scenarios)című témakörben talál további információt.

### <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>Hogyan lehet engedélyezni a kiszolgálónév-jelzést (SNI) a Felhőszolgáltatások számára?

Az SNI-t az alábbi módszerek egyikével engedélyezheti a Felhőszolgáltatásokban:

**1. módszer: PowerShell használata**

Az SNI-kötés konfigurálható a PowerShell **Új-WebBinding** parancsmagjával egy felhőszolgáltatás-szerepkörpéldány indítási feladatában az alábbiak szerint:
    
    New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags 
    
Az [itt](https://technet.microsoft.com/library/ee790567.aspx)leírtak szerint a $sslFlags a következők éppen az alábbi értékek egyike lehet:

|Érték|Jelentés|
------|------
|0|Nem SNI|
|1|SNI engedélyezve|
|2|Nem SNI-kötés, amely a központi tanúsítványtárolót használja|
|3|SNI-kötés, amely a központi tanúsítványtárolót használja|
 
**2. módszer: Kód használata**

Az SNI-kötés is konfigurálható a kódot a szerepindítási leírtak szerint ebben a [blogbejegyzésben:](https://blogs.msdn.microsoft.com/jianwu/2014/12/17/expose-ssl-service-to-multi-domains-from-the-same-cloud-service/)

    
    //<code snip> 
                    var serverManager = new ServerManager(); 
                    var site = serverManager.Sites[0]; 
                    var binding = site.Bindings.Add(":443:www.test1.com", newCert.GetCertHash(), "My"); 
                    binding.SetAttributeValue("sslFlags", 1); //enables the SNI 
                    serverManager.CommitChanges(); 
    //</code snip> 
    
A fenti módszerek bármelyikét használva az adott állomásnevekhez tartozó tanúsítványokat (*.pfx) először egy indítási feladat vagy kód használatával kell telepíteni a szerepkörpéldányokra, hogy az SNI-kötés hatékony legyen.

### <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>Hogyan adhatok hozzá címkéket az Azure Cloud Szolgáltatásomhoz? 

A Felhőszolgáltatás egy klasszikus erőforrás. Csak az Azure Resource Manager támogatási címkéken keresztül létrehozott erőforrások. Nem alkalmazhat címkéket a klasszikus erőforrások, például a Cloud Service. 

### <a name="the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that"></a>Az Azure Portalon nem jelenik meg a felhőszolgáltatás SDK-verziója. Hogy szerezhetnék meg?

Azon dolgozunk, hogy ezt a funkciót az Azure Portalon hozza létre. Eközben a következő PowerShell-parancsok segítségével lejuthat az SDK-verzióra:

    Get-AzureService -ServiceName "<Cloud Service name>" | Get-AzureDeployment | Where-Object -Property SdkVersion -NE -Value "" | select ServiceName,SdkVersion,OSVersion,Slot

### <a name="i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address"></a>Több hónapra le szeretném állítani a felhőszolgáltatást. Hogyan csökkentheti a felhőszolgáltatás számlázási költségét az IP-cím elvesztése nélkül?

Egy már üzembe helyezett felhőszolgáltatás kerül számlázásra a számítási és tárolási általa használt. Így még akkor is, ha leállítja az Azure virtuális gép, továbbra is a storage-ért kell fizetnie. 

A következőket teheti a számlázás csökkentése érdekében anélkül, hogy elveszítené a szolgáltatás IP-címét:

1. [Foglalja le az IP-címet](../virtual-network/virtual-networks-reserved-public-ip.md) a központi telepítések törlése előtt.  Csak erre az IP-címre kell fizetnie. Az IP-címek számlázásáról további információt az [IP-címek díjszabása](https://azure.microsoft.com/pricing/details/ip-addresses/)című témakörben talál.
2. Törölje a központi telepítéseket. Ne törölje a xxx.cloudapp.net, hogy a jövőben is használhassa.
3. Ha a felhőszolgáltatást az előfizetésben lefoglalt fenntartott fenntartott fenntartott IP-cím használatával szeretné újratelepíteni, olvassa el [a Felhőszolgáltatások és a virtuális gépek fenntartott IP-címeit.](https://azure.microsoft.com/blog/reserved-ip-addresses/)

