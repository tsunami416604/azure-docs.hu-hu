---
title: DNS-továbbítás konfigurálása Azure Files hoz | Microsoft dokumentumok
description: Az Azure Files hálózati lehetőségeinek áttekintése.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 35dfbcb274721049f2160719222ca89038c93356
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80082499"
---
# <a name="configuring-dns-forwarding-for-azure-files"></a>DNS-továbbítás konfigurálása Azure-fájlokhoz
Az Azure Files lehetővé teszi, hogy privát végpontokat hozzon létre a fájlmegosztásokat tartalmazó tárfiókokhoz. Bár számos különböző alkalmazás számára hasznos, a privát végpontok különösen hasznosak az Azure-fájlmegosztásokhoz való csatlakozáshoz a helyszíni hálózatról VPN- vagy ExpressRoute-kapcsolaton keresztül, privát társviszony-létesítés használatával. 

Annak érdekében, hogy a tárfiókhoz való csatlakozás oka a hálózati alagúton keresztül haladjon, a tárfiók teljesen minősített tartománynevét (FQDN) a privát végpont privát IP-címére kell feloldania. Ennek elérése érdekében a tárolási végpont utótagot`core.windows.net` (nyilvános felhőrégiók esetében) kell továbbítania az Azure privát DNS-szolgáltatásához, amely a virtuális hálózaton belül érhető el. Ez az útmutató bemutatja, hogyan kell beállítani és konfigurálni a DNS-továbbítást úgy, hogy megfelelően feloldódjon a tárfiók privát végpontIP-címére.

Azt javasoljuk, hogy olvassa el [az Azure Files telepítésének tervezése](storage-files-planning.md) és az Azure Files hálózati [szempontokat,](storage-files-networking-overview.md) mielőtt elvégezná a cikkben ismertetett lépéseket.

## <a name="overview"></a>Áttekintés
Az Azure Files kétféle végpontot biztosít az Azure-fájlmegosztások eléréséhez: 
- Nyilvános végpontok, amelyek nyilvános IP-címmel rendelkeznek, és a világ bármely pontjáról elérhetők.
- Privát végpontok, amelyek egy virtuális hálózaton belül léteznek, és a virtuális hálózat címteréből rendelkeznek privát IP-címmel.

Nyilvános és privát végpontok léteznek az Azure storage-fiókban. A tárfiók egy felügyeleti konstrukció, amely egy megosztott tárolókészletet képvisel, amelyben több fájlmegosztást, valamint egyéb tárolási erőforrásokat, például blobtárolókat vagy várólistákat telepíthet.

Minden tárfiók teljesen minősített tartománynévvel (FQDN) rendelkezik. A nyilvános felhőrégiók, ez a teljes tartománynév követi a mintát, `storageaccount.file.core.windows.net` ahol `storageaccount` a tárfiók neve. Ha erre a névre vonatkozókéréseket hajt végre, például a megosztást a munkaállomásra csatlakoztatja az SMB használatával, az operációs rendszer DNS-vizsgálatot hajt végre, hogy a teljesen minősített tartománynevet egy IP-címre oldja meg, amelyre az SMB-kérelmek küldéséhez használható.

Alapértelmezés szerint `storageaccount.file.core.windows.net` a nyilvános végpont IP-címére oldódik fel. A nyilvános végpont egy tárfiók üzemelteti egy Azure storage-fürt, amely számos más tárfiókok nyilvános végpontok üzemelteti. Amikor létrehoz egy privát végpontot, egy privát DNS-zóna kapcsolódik ahhoz a virtuális `storageaccount.file.core.windows.net` hálózathoz, amelyhez hozzá lett adva, és a cname rekord leképezése a tárfiók magánhálózati végpontjának privát IP-címéhez tartozó A rekordbejegyzéshez. Ez lehetővé `storageaccount.file.core.windows.net` teszi, hogy fqdn a virtuális hálózaton belül, és azt feloldja a privát végpont IP-címét.

Mivel a végső célunk a tárfiókban tárolt Azure-fájlmegosztások elérése a helyszíni hálózati alagútból, például VPN- vagy ExpressRoute-kapcsolatból, konfigurálnia kell a helyszíni DNS-kiszolgálókat, hogy továbbítsák az Azure-ba irányuló kérelmeket. Fájlok szolgáltatás az Azure privát DNS-szolgáltatás. Ehhez be kell állítania `*.core.windows.net` az Azure virtuális hálózatán belül üzemeltetett DNS-kiszolgálóra (vagy az Egyesült Államok kormányának, Németországnak vagy Kína nemzeti felhőinek megfelelő tárolási végpont-utótagot) az Azure virtuális hálózatán tárolt DNS-kiszolgálóra *történő feltételes továbbítást.* Ez a DNS-kiszolgáló ezután rekurzív an-felelek továbbítja a kérelmet az Azure privát DNS-szolgáltatásának, amely a tárfiók teljesen minősített tartománynevét a megfelelő privát IP-címre oldja fel.

Az Azure Files DNS-továbbításának konfigurálására egy virtuális gép futtatásához kell futtatni egy DNS-kiszolgálót a kérelmek továbbításához, azonban ez egy egyszeri lépés a virtuális hálózaton tárolt összes Azure-fájlmegosztás esetében. Emellett ez nem kizárólagos követelmény az Azure Files számára – minden olyan Azure-szolgáltatás, amely támogatja a helyszíni hozzáféréssel rendelkező privát végpontokat, igénybe veheti az ebben az útmutatóban konfigurált DNS-továbbítást: Azure Blob storage, SQL Azure, Cosmos DB, Stb. 

Ez az útmutató bemutatja az Azure storage-végpont DNS-továbbításának konfigurálásának lépéseit, így az Azure Files mellett az összes többi Azure storage-szolgáltatás (Azure Blob storage, Azure Table storage, Azure Queue storage stb.) DNS-névfeloldási kérelmei is megjelennek. az Azure privát DNS-szolgáltatásához. Szükség esetén más Azure-szolgáltatások hoz is hozzáadható további végpontok. A helyszíni DNS-kiszolgálókra visszavezető DNS-továbbítás is konfigurálva lesz, így a virtuális hálózaton (például egy DFS-N-kiszolgálón) belüli felhőbeli erőforrások feloldhatják a helyszíni számítógépneveket. 

## <a name="prerequisites"></a>Előfeltételek
A DNS-továbbítás Azure Files-ba való beállítása előtt el kell végeznie a következő lépéseket:

- A csatlakoztatni kívánt Azure-fájlmegosztást tartalmazó tárfiók. Ha tudni szeretné, hogyan hozhat létre tárfiókot és Azure-fájlmegosztást, olvassa [el az Azure-fájlmegosztás létrehozása című témakört.](storage-how-to-create-file-share.md)
- A tárfiók privát végpontja. Ha tudni szeretné, hogyan hozhat létre privát végpontot az Azure Files számára, [olvassa el a Privát végpont létrehozása](storage-files-networking-endpoints.md#create-a-private-endpoint)című témakört.
- Az Azure PowerShell-modul [legújabb verziója.](https://docs.microsoft.com/powershell/azure/install-az-ps)

> [!Important]  
> Ez az útmutató feltételezi, hogy a DNS-kiszolgálót a Windows Server en belül használja a helyszíni környezetben. Az útmutatóban ismertetett összes lépés nem csak a Windows DNS-kiszolgálóval, hanem bármely DNS-kiszolgálóval lehetséges.

## <a name="manually-configuring-dns-forwarding"></a>DNS-továbbítás kézi konfigurálása
Ha már rendelkezik DNS-kiszolgálókkal az Azure virtuális hálózatán belül, vagy ha egyszerűen csak a saját virtuális gépeit szeretné DNS-kiszolgálókként telepíteni bármilyen módszerrel, akkor manuálisan konfigurálhatja a DNS-t a beépített DNS-kiszolgálóval. PowerShell-parancsmagok.

A helyszíni DNS-kiszolgálókon hozzon létre `Add-DnsServerConditionalForwarderZone`egy feltételes továbbítót a használatával. Ezt a feltételes továbbítókell telepíteni az összes helyszíni DNS-kiszolgálók hatékony an-nincs megfelelő továbbítása forgalmat az Azure-ba. Ne felejtse el helyettesíteni `<azure-dns-server-ip>` a környezetének megfelelő IP-címeket.

```powershell
$vnetDnsServers = "<azure-dns-server-ip>", "<azure-dns-server-ip>"

$storageAccountEndpoint = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty StorageEndpointSuffix

Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers $vnetDnsServers
```

Az Azure virtuális hálózatán belüli DNS-kiszolgálókon is létre kell helyeznie egy továbbítót, hogy a tárfiók DNS-zónájára vonatkozó kérelmek `168.63.129.16`az Azure privát DNS-szolgáltatásra irányuljanak, amelyet a fenntartott IP-cím irányít. (Ne felejtse `$storageAccountEndpoint` el feldolgozni, ha a parancsokat egy másik PowerShell-munkameneten belül futtatja.)

```powershell
Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers "168.63.129.16"
```

## <a name="using-the-azure-files-hybrid-module-to-configure-dns-forwarding"></a>Az Azure Files Hybrid modul használata a DNS-továbbítás konfigurálásához
Annak érdekében, hogy a DNS-továbbítás konfigurálása a lehető legegyszerűbb legyen, automatizálást biztosítottunk az Azure Files Hybrid modulban. A modulban a DNS-kezeléshez biztosított parancsmagok segítségével üzembe helyezheti a DNS-kiszolgálókat az Azure virtuális hálózatában, és frissítheti a helyszíni DNS-kiszolgálókat, hogy továbbítsa őket. 

Ha még soha nem használta az Azure Files Hybrid modult, először telepítenie kell azt a munkaállomásra. Töltse le az Azure Files Hybrid PowerShell modul [legújabb verzióját:](https://github.com/Azure-Samples/azure-files-samples/releases)

```PowerShell
# Unzip the downloaded file
Expand-Archive -Path AzFilesHybrid.zip

# Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\AzFilesHybrid\CopyToPSPath.ps1 

# Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid
```

A DNS-továbbítási megoldás üzembe helyezése két lépésből áll, létrehozva egy DNS-továbbítási szabálykészletet, amely meghatározza, hogy mely Azure-szolgáltatásoknak kívánja továbbítani a kérelmeket, és a DNS-továbbítók tényleges üzembe helyezését. 

A következő példa továbbítja a kérelmeket a tárfiókba, az Azure Files, az Azure Blob storage, az Azure Table storage és az Azure Queue storage beküldő kéréseit. Ha szükséges, további Azure-szolgáltatás továbbítása a szabályhoz `-AzureEndpoints` a `New-AzDnsForwardingRuleSet` parancsmag paraméterén keresztül. Ne felejtse `<virtual-network-name>`el `<subnet-name>` kicserélni `<virtual-network-resource-group>`a , és a környezetének megfelelő értékeket.

```PowerShell
# Create a rule set, which defines the forwarding rules
$ruleSet = New-AzDnsForwardingRuleSet -AzureEndpoints StorageAccountEndpoint

# Deploy and configure DNS forwarders
New-AzDnsForwarder `
        -DnsForwardingRuleSet $ruleSet `
        -VirtualNetworkResourceGroupName "<virtual-network-resource-group>" `
        -VirtualNetworkName "<virtual-network-name>" `
        -VirtualNetworkSubnetName "<subnet-name>"
```

Ezenkívül hasznosnak/szükségesnek találhatja számos további paraméter megszolgáltatását:

| Paraméter neve | Típus | Leírás |
|----------------|------|-------------|
| `DnsServerResourceGroupName` | `string` | Alapértelmezés szerint a DNS-kiszolgálók ugyanabba az erőforráscsoportba lesznek telepítve, mint a virtuális hálózat. Ha ez nem kívánatos, ez a paraméter lehetővé teszi, hogy válasszon egy alternatív erőforráscsoportot, amelybe telepíteni kell őket. |
| `DnsForwarderRootName` | `string` | Alapértelmezés szerint az Azure-ban üzembe helyezett DNS-kiszolgálók a nevekkel `DnsFwder-*`rendelkeznek, ahol a csillagot iterátor tölti fel. Ez a paraméter megváltoztatja a név `DnsFwder`gyökerét (azaz ). |
| `VmTemporaryPassword` | `SecureString` | Alapértelmezés szerint egy véletlenszerű jelszót választ a virtuális gép által a tartományhoz való csatlakozás előtt lévő ideiglenes alapértelmezett fiókhoz. A tartományhoz való csatlakozás után az alapértelmezett fiók le van tiltva. |
| `DomainToJoin` | `string` | A DNS-virtuális géphez csatlakozni csatlakozó tartomány. Alapértelmezés szerint ez a tartomány annak a számítógépnek a tartománya alapján van kiválasztva, amelyen a parancsmagokat futtatja. |
| `DnsForwarderRedundancyCount` | `int` | A virtuális hálózatra telepítandó DNS-virtuális gépek száma. Alapértelmezés szerint `New-AzDnsForwarder` két DNS-kiszolgálót telepít az Azure virtuális hálózatában egy rendelkezésre állási csoportban a redundancia biztosítása érdekében. Ez a szám igény szerint módosítható. |
| `OnPremDnsHostNames` | `HashSet<string>` | A továbbítók létrehozásához a helyszíni DNS-állomásnevek manuálisan megadott listája. Ez a paraméter akkor hasznos, ha nem kíván továbbítókat alkalmazni az összes helyszíni DNS-kiszolgálón, például ha manuálisan megadott DNS-névvel rendelkező ügyféltartománya van. |
| `Credential` | `PSCredential` | A DNS-kiszolgálók frissítésekor használandó hitelesítő adat. Ez akkor hasznos, ha a bejelentkezett felhasználói fiók nem rendelkezik a DNS-beállítások módosításához szükséges engedélyekkel. |
| `SkipParentDomain` | `SwitchParameter` | Alapértelmezés szerint a DNS-továbbítók a környezetben létező legmagasabb szintű tartományra lesznek alkalmazva. Ha például `northamerica.corp.contoso.com` a gyermektartománya, `corp.contoso.com`a továbbító a hozzá társított `corp.contoso.com`DNS-kiszolgálókhoz lesz létrehozva. Ezzel a paraméterrel továbbítókat `northamerica.corp.contoso.com`hoz létre a rendszerben. |

## <a name="confirm-dns-forwarders"></a>DNS-továbbítók megerősítése
A DNS-továbbítók sikeres alkalmazásának vizsgálata előtt javasoljuk, hogy törölje a `Clear-DnsClientCache`DNS-gyorsítótárat a helyi munkaállomáson a használatával. Annak ellenőrzéséhez, hogy sikeresen fel tudja-e oldani a `Resolve-DnsName` `nslookup`tárfiók teljesen minősített tartománynevét, használja a vagy a .

```powershell
# Replace storageaccount.file.core.windows.net with the appropriate FQDN for your storage account.
# Note the proper suffix (core.windows.net) depends on the cloud your deployed in.
Resolve-DnsName -Name storageaccount.file.core.windows.net
```

Ha a névfeloldás sikeres, akkor a feloldott IP-cím nek meg kell egyeznie a tárfiók IP-címével.

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4
```

Ha már beállított vpn- vagy ExpressRoute-kapcsolatot, `Test-NetConnection` azt is ellenőrizheti, hogy a TCP-kapcsolat sikeresen létrehozható-e a tárfiókhoz.

```PowerShell
Test-NetConnection -ComputerName storageaccount.file.core.windows.net -CommonTCPPort SMB
```

## <a name="see-also"></a>Lásd még
- [Az Azure Files üzembe helyezésének megtervezése](storage-files-planning.md)
- [Az Azure Files hálózati szempontjai](storage-files-networking-overview.md)
- [Az Azure Files hálózati végpontjainak konfigurálása](storage-files-networking-endpoints.md)