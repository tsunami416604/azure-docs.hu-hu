---
title: Az Azure File Sync helyszíni tűzfal- és proxybeállításai | Microsoft dokumentumok
description: Az Azure File Sync helyszíni hálózati konfigurációja
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7f398012edc25ba6a04e230fa8049e7264f857bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294525"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Az Azure File Sync proxy- és tűzfalbeállításai
Az Azure File Sync csatlakoztatja a helyszíni kiszolgálókat az Azure Files-hoz, lehetővé téve a többhelyes szinkronizálást és a felhőrétegezési funkciókat. Így a helyszíni kiszolgálónak csatlakoznia kell az internethez. Egy informatikai rendszergazdának el kell döntenie, hogy a kiszolgáló számára melyik legjobb elérési utat kell elérnie az Azure felhőszolgáltatásaihoz.

Ez a cikk betekintést nyújt a kiszolgáló azure-fájlszinkronizáláshoz való sikeres és biztonságos csatlakoztatásához rendelkezésre álló konkrét követelményekbe és lehetőségekbe.

## <a name="overview"></a>Áttekintés
Az Azure File Sync vezénylési szolgáltatásként működik a Windows Server, az Azure-fájlmegosztás és számos más Azure-szolgáltatás között az adatok szinkronizálásához a szinkronizálási csoportban leírtak szerint. Ahhoz, hogy az Azure File Sync megfelelően működjön, konfigurálnia kell a kiszolgálókat a következő Azure-szolgáltatásokkal való kommunikációra:

- Azure Storage
- Azure File Sync
- Azure Resource Manager
- Hitelesítési szolgáltatások

> [!Note]  
> A Windows Server Azure File Sync ügynöke elindítja az összes kérelmet a felhőszolgáltatások, ami azt eredményezi, hogy csak a kimenő forgalmat a tűzfal szempontjából. <br /> Egyetlen Azure-szolgáltatás sem kezdeményez kapcsolatot az Azure File Sync ügynökkel.

## <a name="ports"></a>Portok
Az Azure File Sync a fájladatokat és a metaadatokat kizárólag HTTPS-kapcsolaton keresztül helyezi át, és a 443-as portot kimenő en kell megnyitni.
Ennek eredményeképpen minden forgalom titkosítva van.

## <a name="networks-and-special-connections-to-azure"></a>Hálózatok és speciális kapcsolatok az Azure-ral
Az Azure File Sync ügynök nem rendelkezik a speciális csatornák, például [ExpressRoute,](../../expressroute/expressroute-introduction.md)stb az Azure-ra vonatkozó követelményekkel.

Az Azure File Sync minden olyan eszközzel működik, amely lehetővé teszi az Azure elérését, automatikusan alkalmazkodik a különböző hálózati jellemzőkhöz, például a sávszélességhez, a késéshez, valamint a finomhangoláshoz felügyeleti vezérlést kínál. Jelenleg nem minden funkció érhető el. Ha egy adott viselkedést szeretne konfigurálni, tudassa velünk az [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670)segítségével.

## <a name="proxy"></a>Proxy
Az Azure File Sync támogatja az alkalmazás-specifikus és a gép-szintű proxy beállításokat.

**Az alkalmazásspecifikus proxybeállítások** lehetővé teszik egy proxy konfigurációját kifejezetten az Azure File Sync forgalomhoz. Az alkalmazásspecifikus proxybeállítások at a 4.0.1.0-s vagy újabb ügynökverzió támogatja, és az ügynök telepítése során vagy a Set-StorageSyncConfiguration PowerShell-parancsmag használatával konfigurálhatók.

Az alkalmazásspecifikus proxybeállítások konfigurálásához powershell-parancsok:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
**A gép szintű proxybeállítások** az Azure File Sync ügynök számára transzparensek, mivel a kiszolgáló teljes forgalma a proxyn keresztül történik.

A gép szintű proxybeállítások konfigurálásához kövesse az alábbi lépéseket: 

1. Proxybeállítások konfigurálása .NET alkalmazásokhoz 

   - A két fájl szerkesztése:  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - Adja hozzá a <system.net> szakaszt a machine.config fájlokban (a <system.serviceModel> szakasz alatt).  Módosítsa a 127.0.01:8888 módosítást a proxykiszolgáló IP-címére és portjára. 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. A WinHTTP-proxy beállításainak megadása 

   - Futtassa a következő parancsot egy rendszergazda jogú parancssorból vagy a PowerShellből a meglévő proxybeállítás megtekintéséhez:   

     netsh winhttp show proxy

   - Futtassa a következő parancsot egy rendszergazda jogú parancssorból vagy a PowerShell ből a proxybeállítás beállításához (módosítsa a 127.0.01:8888-as módosítást a proxykiszolgáló IP-címére és portjára):  

     netsh winhttp set proxy 127.0.0.1:8888

3. Indítsa újra a Storage Sync Agent szolgáltatást a következő parancs futtatásával egy rendszergazda jogú parancssorból vagy powershellből: 

      net stop filesyncsvc

      Megjegyzés: A Storage Sync Agent (filesyncsvc) szolgáltatás automatikusan elindul, miután leállt.

## <a name="firewall"></a>Tűzfal
Amint azt egy korábbi szakaszban említettük, a 443-as portnak nyitott kimenőnek kell lennie. Az adatközpontban, ágban vagy régióban lévő szabályzatok alapján a porton keresztüli forgalom adott tartományokra való további korlátozása szükséges vagy szükséges lehet.

Az alábbi táblázat a kommunikációhoz szükséges tartományokat ismerteti:

| Szolgáltatás | Nyilvános felhővégpont | Azure Government-végpont | Használat |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | `https://management.azure.com` | https://management.usgovcloudapi.net | Minden felhasználói hívás (például a PowerShell) megy/ ezen az URL-címen keresztül, beleértve a kezdeti kiszolgáló regisztrációs hívást. |
| **Azure Active Directory** | https://login.windows.net<br>`https://login.microsoftonline.com` | https://login.microsoftonline.us | Az Azure Resource Manager-hívásokat hitelesített felhasználónak kell kezdeményeznie. A siker érdekében ez az URL-cím használható a felhasználói hitelesítéshez. |
| **Azure Active Directory** | https://graph.microsoft.com/ | https://graph.microsoft.com/ | Az Azure File Sync központi telepítésének részeként létrejön egy egyszerű szolgáltatás az előfizetés Azure Active Directoryjában. Ehhez az URL-cím használatos. Ez a fő névvel az Azure File Sync szolgáltatás minimális jogosultságkészletének delegálása. Az Azure File Sync kezdeti beállítását végző felhasználónak előfizetés-tulajdonosi jogosultságokkal rendelkező hitelesített felhasználónak kell lennie. |
| **Azure Storage** | &ast;core.windows.net | &ast;core.usgovcloudapi.net | Amikor a kiszolgáló letölt egy fájlt, majd a kiszolgáló hatékonyabban hajtja végre az adatmozgást, amikor közvetlenül az Azure-fájlmegosztással beszél a storage-fiókban. A kiszolgáló rendelkezik egy SAS-kulccsal, amely csak célzott fájlmegosztási hozzáférést tesz lehetővé. |
| **Azure File Sync** | &ast;one.microsoft.com<br>&ast;.afs.azure.net | &ast;afs.azure.us | A kiszolgáló első regisztrációja után a kiszolgáló egy regionális URL-címet kap az adott régióban az Azure File Sync szolgáltatáspéldányhoz. A kiszolgáló az URL-cím segítségével közvetlenül és hatékonyan kommunikálhat a szinkronizálást kezelő példánysal. |
| **Microsoft PKI** | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | Az Azure File Sync ügynök telepítése után a PKI URL-cím használatával letölti az Azure File Sync szolgáltatással és az Azure fájlmegosztással való kommunikációhoz szükséges köztes tanúsítványokat. Az OCSP URL-cím a tanúsítvány állapotának ellenőrzésére szolgál. |

> [!Important]
> Ha engedélyezi a &ast;forgalmat a .one.microsoft.com, a forgalom nem csak a szinkronizálási szolgáltatás lehetséges a kiszolgálóról. Az altartományok alatt sokkal több Microsoft-szolgáltatás érhető el.

Ha &ast;az .one.microsoft.com túl tág, korlátozhatja a kiszolgáló kommunikációját azáltal, hogy engedélyezi a kommunikációt az Azure Files Sync szolgáltatás csak explicit regionális példányai számára. A választás melyik példány(ok) attól függ, hogy a tároló szinkronizálási szolgáltatás melyik régiójában telepítette és regisztrálta a kiszolgálót. Ezt a régiót az alábbi táblázat "Elsődleges végpont URL-címének" nevezi.

Az üzletmenet folytonossága és a vész-helyreállítási (BCDR) okok miatt előfordulhat, hogy az Azure-fájlmegosztások egy globálisan redundáns (GRS) tárfiókban. Ebben az esetben az Azure-fájlmegosztások a párosított régióra kerülnek, ha tartós regionális kimaradás lép ki. Az Azure File Sync ugyanazokat a regionális párosításokat használja, mint a storage. Így ha GRS-tárfiókokat használ, engedélyeznie kell a további URL-címeket, hogy a kiszolgáló az Azure File Sync párosított régiójával beszélgethessen. Az alábbi táblázat ezt a "Párosított régiót" hívja meg. Emellett van egy traffic manager profil URL-jét, amelyet engedélyezni kell is. Ez biztosítja, hogy a hálózati forgalom zökkenőmentesen átirányítható a párosított régióba feladatátvétel esetén, és az alábbi táblázatban "Felderítési URL"-nek nevezik.

| Felhő  | Régió | Elsődleges végpont URL-címe | Párosított régió | Felderítés URL-címe |
|--------|--------|----------------------|---------------|---------------|
| Nyilvános |Kelet-Ausztrália | https:\//kailani-aue.one.microsoft.com | Délkelet-Ausztrália | https:\//tm-kailani-aue.one.microsoft.com |
| Nyilvános |Délkelet-Ausztrália | https:\//kailani-aus.one.microsoft.com | Kelet-Ausztrália | https:\//tm-kailani-aus.one.microsoft.com |
| Nyilvános | Dél-Brazília | https:\//brazilsouth01.afs.azure.net | USA déli középső régiója | https:\//tm-brazilsouth01.afs.azure.net |
| Nyilvános | Közép-Kanada | https:\//kailani-cac.one.microsoft.com | Kelet-Kanada | https:\//tm-kailani-cac.one.microsoft.com |
| Nyilvános | Kelet-Kanada | https:\//kailani-cae.one.microsoft.com | Közép-Kanada | https:\//tm-kailani.cae.one.microsoft.com |
| Nyilvános | Közép-India | https:\//kailani-cin.one.microsoft.com | Dél-India | https:\//tm-kailani-cin.one.microsoft.com |
| Nyilvános | USA középső régiója | https:\//kailani-cus.one.microsoft.com | USA 2. keleti régiója | https:\//tm-kailani-cus.one.microsoft.com |
| Nyilvános | Kelet-Ázsia | https:\//kailani11.one.microsoft.com | Délkelet-Ázsia | https:\//tm-kailani11.one.microsoft.com |
| Nyilvános | USA keleti régiója | https:\//kailani1.one.microsoft.com | USA nyugati régiója | https:\//tm-kailani1.one.microsoft.com |
| Nyilvános | USA 2. keleti régiója | https:\//kailani-ess.one.microsoft.com | USA középső régiója | https:\//tm-kailani-ess.one.microsoft.com |
| Nyilvános | Kelet-Japán | https:\//japaneast01.afs.azure.net | Nyugat-Japán | https:\//tm-japaneast01.afs.azure.net |
| Nyilvános | Nyugat-Japán | https:\//japanwest01.afs.azure.net | Kelet-Japán | https:\//tm-japanwest01.afs.azure.net |
| Nyilvános | Dél-Korea középső régiója | https:\//koreacentral01.afs.azure.net/ | Dél-Korea déli régiója | https:\//tm-koreacentral01.afs.azure.net/ |
| Nyilvános | Dél-Korea déli régiója | https:\//koreasouth01.afs.azure.net/ | Dél-Korea középső régiója | https:\//tm-koreasouth01.afs.azure.net/ |
| Nyilvános | USA északi középső régiója | https:\//northcentralus01.afs.azure.net | USA déli középső régiója | https:\//tm-northcentralus01.afs.azure.net |
| Nyilvános | Észak-Európa | https:\//kailani7.one.microsoft.com | Nyugat-Európa | https:\//tm-kailani7.one.microsoft.com |
| Nyilvános | USA déli középső régiója | https:\//southcentralus01.afs.azure.net | USA északi középső régiója | https:\//tm-southcentralus01.afs.azure.net |
| Nyilvános | Dél-India | https:\//kailani-sin.one.microsoft.com | Közép-India | https:\//tm-kailani-sin.one.microsoft.com |
| Nyilvános | Délkelet-Ázsia | https:\//kailani10.one.microsoft.com | Kelet-Ázsia | https:\//tm-kailani10.one.microsoft.com |
| Nyilvános | Az Egyesült Királyság déli régiója | https:\//kailani-uks.one.microsoft.com | Az Egyesült Királyság nyugati régiója | https:\//tm-kailani-uks.one.microsoft.com |
| Nyilvános | Az Egyesült Királyság nyugati régiója | https:\//kailani-ukw.one.microsoft.com | Az Egyesült Királyság déli régiója | https:\//tm-kailani-ukw.one.microsoft.com |
| Nyilvános | USA nyugati középső régiója | https:\//westcentralus01.afs.azure.net | USA nyugati régiója, 2. | https:\//tm-westcentralus01.afs.azure.net |
| Nyilvános | Nyugat-Európa | https:\//kailani6.one.microsoft.com | Észak-Európa | https:\//tm-kailani6.one.microsoft.com |
| Nyilvános | USA nyugati régiója | https:\//kailani.one.microsoft.com | USA keleti régiója | https:\//tm-kailani.one.microsoft.com |
| Nyilvános | USA nyugati régiója, 2. | https:\//westus201.afs.azure.net | USA nyugati középső régiója | https:\//tm-westus201.afs.azure.net |
| Government | USA-beli államigazgatás – Arizona | https:\//usgovarizona01.afs.azure.us | USA-beli államigazgatás – Texas | https:\//tm-usgovarizona01.afs.azure.us |
| Government | USA-beli államigazgatás – Texas | https:\//usgovtexas01.afs.azure.us | USA-beli államigazgatás – Arizona | https:\//tm-usgovtexas01.afs.azure.us |

- Ha helyileg redundáns (LRS) vagy zónaredundáns (ZRS) tárfiókokat használ, csak az "Elsődleges végpont URL-címe" alatt felsorolt URL-címet kell engedélyeznie.

- Ha globálisan redundáns (GRS) tárfiókokat használ, engedélyezze a három URL-t.

**Példa:** Üzembe helyez egy tárolószinkronizálási szolgáltatást, `"West US"` és regisztrálja vele a kiszolgálót. Azok az URL-címek, amelyekkel a kiszolgáló az eset hez képest kommunikálhat, a következők:

> - https:\//kailani.one.microsoft.com (elsődleges végpont: USA nyugati pontja)
> - https:\//kailani1.one.microsoft.com (párosított feladatátvételi régió: USA keleti régiója)
> - https:\//tm-kailani.one.microsoft.com (az elsődleges régió felderítési URL-címe)

### <a name="allow-list-for-azure-file-sync-ip-addresses"></a>Az Azure File Sync IP-címeinek listájának engedélyezése
Az Azure File Sync támogatja a [szolgáltatáscímkék](../../virtual-network/service-tags-overview.md)használatát, amelyek egy adott Azure-szolgáltatás IP-címelőtagjainak csoportját képviselik. A szolgáltatáscímkék segítségével tűzfalszabályokat hozhat létre, amelyek lehetővé teszik az Azure File Sync szolgáltatással való kommunikációt. Az Azure File Sync `StorageSyncService`szolgáltatáscímkéje a.

Ha az Azure File Sync-et használja az Azure-ban, használhatja a szolgáltatáscímke nevét közvetlenül a hálózati biztonsági csoportban a forgalom engedélyezéséhez. Ennek módjáról a Hálózati biztonsági csoportok ( [Hálózati biztonsági csoportok](../../virtual-network/security-overview.md)) témakörben olvashat bővebben.

Ha az Azure File Sync helyszíni használata, a szolgáltatáscímke API-t a tűzfal engedélyezési listájának adott IP-címtartományai lehívásához használhatja. Ezt az információt kétfélekul lehet beadni:

- A szolgáltatáscímkéket támogató összes Azure-szolgáltatás címkéjének IP-címtartományainak aktuális listáját hetente teszik közzé a Microsoft letöltőközpontban JSON-dokumentum formájában. Minden Azure-felhő saját JSON-dokumentummal rendelkezik, amely az adott felhőhöz tartozó IP-címtartományokkal rendelkezik:
    - [Azure Public](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Az Azure Amerikai Egyesült Államok kormánya](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure China](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure Germany](https://www.microsoft.com/download/details.aspx?id=57064)
- A szolgáltatáscímke felderítési API (előzetes verzió) lehetővé teszi a szolgáltatáscímkék aktuális listájának programozott lekérését. Előzetes verzióban a szolgáltatáscímke felderítési API-ja olyan információkat adhat vissza, amelyek kevésbé aktuálisak, mint a Microsoft letöltőközpontban közzétett JSON-dokumentumokból visszaadott információk. Az API-felületet az automatizálási preferenciája alapján használhatja:
    - [REST API](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Azure CLI](https://docs.microsoft.com/cli/azure/network#az-network-list-service-tags)

Mivel a szolgáltatáscímke felderítési API-ja nem frissül olyan gyakran, mint a Microsoft letöltőközpontban közzétett JSON-dokumentumok, javasoljuk, hogy a JSON-dokumentum mal a helyszíni tűzfal engedélyezési listájának frissítéséhez használja a JSON-dokumentumot. Ez a következőképpen végezhető el:

```PowerShell
# The specific region to get the IP address ranges for. Replace westus2 with the desired region code 
# from Get-AzLocation.
$region = "westus2"

# The service tag for Azure File Sync. Do not change unless you're adapting this
# script for another service.
$serviceTag = "StorageSyncService"

# Download date is the string matching the JSON document on the Download Center. 
$possibleDownloadDates = 0..7 | `
    ForEach-Object { [System.DateTime]::Now.AddDays($_ * -1).ToString("yyyyMMdd") }

# Verify the provided region
$validRegions = Get-AzLocation | `
    Where-Object { $_.Providers -contains "Microsoft.StorageSync" } | `
    Select-Object -ExpandProperty Location

if ($validRegions -notcontains $region) {
    Write-Error `
            -Message "The specified region $region is not available. Either Azure File Sync is not deployed there or the region does not exist." `
            -ErrorAction Stop
}

# Get the Azure cloud. This should automatically based on the context of 
# your Az PowerShell login, however if you manually need to populate, you can find
# the correct values using Get-AzEnvironment.
$azureCloud = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty Name

# Build the download URI
$downloadUris = @()
switch($azureCloud) {
    "AzureCloud" { 
        $downloadUris = $possibleDownloadDates | ForEach-Object {  
            "https://download.microsoft.com/download/7/1/D/71D86715-5596-4529-9B13-DA13A5DE5B63/ServiceTags_Public_$_.json"
        }
    }

    "AzureUSGovernment" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/6/4/D/64DB03BF-895B-4173-A8B1-BA4AD5D4DF22/ServiceTags_AzureGovernment_$_.json"
        }
    }

    "AzureChinaCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/9/D/0/9D03B7E2-4B80-4BF3-9B91-DA8C7D3EE9F9/ServiceTags_China_$_.json"
        }
    }

    "AzureGermanCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/0/7/6/076274AB-4B0B-4246-A422-4BAF1E03F974/ServiceTags_AzureGermany_$_.json"
        }
    }

    default {
        Write-Error -Message "Unrecognized Azure Cloud: $_" -ErrorAction Stop
    }
}

# Find most recent file
$found = $false 
foreach($downloadUri in $downloadUris) {
    try { $response = Invoke-WebRequest -Uri $downloadUri -UseBasicParsing } catch { }
    if ($response.StatusCode -eq 200) {
        $found = $true
        break
    }
}

if ($found) {
    # Get the raw JSON 
    $content = [System.Text.Encoding]::UTF8.GetString($response.Content)

    # Parse the JSON
    $serviceTags = ConvertFrom-Json -InputObject $content -Depth 100

    # Get the specific $ipAddressRanges
    $ipAddressRanges = $serviceTags | `
        Select-Object -ExpandProperty values | `
        Where-Object { $_.id -eq "$serviceTag.$region" } | `
        Select-Object -ExpandProperty properties | `
        Select-Object -ExpandProperty addressPrefixes
} else {
    # If the file cannot be found, that means there hasn't been an update in
    # more than a week. Please verify the download URIs are still accurate
    # by checking https://docs.microsoft.com/azure/virtual-network/service-tags-overview
    Write-Verbose -Message "JSON service tag file not found."
    return
}
```

Ezután a tűzfal frissítéséhez `$ipAddressRanges` használhatja a bejövő IP-címtartományokat. A tűzfal/hálózati készülék webhelyén a tűzfal frissítésével kapcsolatos információkért tekintse meg.

## <a name="test-network-connectivity-to-service-endpoints"></a>Hálózati kapcsolat tesztelése a szolgáltatás végpontjaihoz
Miután egy kiszolgáló regisztrálva van az Azure File Sync szolgáltatás, a Test-StorageSyncNetworkConnectivity parancsmag és ServerRegistration.exe lehet használni, hogy teszteljék a kommunikáció támpontokkal (URL-ek) jellemző en a kiszolgálón. Ez a parancsmag segíthet a hibaelhárításban, ha a hiányos kommunikáció megakadályozza, hogy a kiszolgáló teljes mértékben együttműködjön az Azure File Sync szolgáltatással, és használható a proxy- és tűzfal-konfigurációk finomhangolására.

A hálózati kapcsolati teszt futtatásához telepítse az Azure File Sync agent 9.1-es vagy újabb verzióját, és futtassa a következő PowerShell-parancsokat:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Test-StorageSyncNetworkConnectivity
```

## <a name="summary-and-risk-limitation"></a>Összefoglalás és kockázatkorlátozás
A jelen dokumentum korábbi listái tartalmazzák az Azure File Sync url-címeket, amelyekkel jelenleg kommunikál. A tűzfalaknak engedélyezniük kell a forgalmat ezekhez a tartományokhoz. A Microsoft arra törekszik, hogy ezt a listát folyamatosan frissítsük.

A tartománykorlátozó tűzfalszabályok beállítása a biztonság növelésének mércéje lehet. Ha ezeket a tűzfal-konfigurációkat használja, szem előtt kell tartania, hogy az URL-címek hozzáadódnak, és idővel változhatnak. Rendszeresen ellenőrizze ezt a cikket.

## <a name="next-steps"></a>További lépések
- [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
- [Az Azure File Sync üzembe helyezése](storage-sync-files-deployment-guide.md)
- [Az Azure File Sync monitorozása](storage-sync-files-monitoring.md)
