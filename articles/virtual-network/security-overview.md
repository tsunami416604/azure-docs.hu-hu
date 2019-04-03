---
title: Az Azure biztonsági csoportok – áttekintés
titlesuffix: Azure Virtual Network
description: Ismerje meg a hálózati és alkalmazásbiztonsági csoportokat A biztonsági csoportok az Azure-erőforrások közötti hálózati forgalom szűrésében segítenek.
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2018
ms.author: malop;kumud
ms.openlocfilehash: 9d45f3a7d20545ac7b66d27505078f21f06adddc
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58881851"
---
# <a name="security-groups"></a>Biztonsági csoportok
<a name="network-security-groups"></a>

Az Azure-beli [virtuális hálózatokban](virtual-networks-overview.md) az Azure-erőforrások bejövő és kimenő hálózati forgalmát hálózati biztonsági csoportokkal szűrheti. A hálózati biztonsági csoportok olyan [biztonsági szabályokat](#security-rules) tartalmaznak, amelyek engedélyezik vagy letiltják a különböző típusú Azure-erőforrások bejövő vagy kimenő hálózati forgalmát. A virtuális hálózatokban üzembe helyezhető és hálózati biztonsági csoportokkal használható Azure-erőforrásokkal kapcsolatos információkért tekintse meg az [Azure-szolgáltatások virtuális hálózati integrációját](virtual-network-for-azure-services.md) ismertető cikket. Az egyes szabályokhoz meghatározhatja a forrást és a célt, valamint a használni kívánt portot és protokollt.

A cikk a hálózati biztonsági csoportokkal kapcsolatos fogalmakat ismerteti, hogy segítséget nyújtson a hatékony használatban. Ha korábban még nem hozott létre hálózati biztonsági csoportot, ebben a rövid [oktatóanyagban](tutorial-filter-network-traffic.md) némi gyakorlatra tehet szert. Ha már ismeri a hálózati biztonsági csoportok működését, és kezelni szeretné őket, tekintse meg a [hálózati biztonsági csoportok kezelését](manage-network-security-group.md) bemutató témakört. Ha kommunikációs problémákat tapasztal, és hibaelhárítást végezne a hálózati biztonsági csoportokon, tekintse meg [a virtuális gépek hálózatiforgalom-szűrési problémáinak diagnosztizálását](diagnose-network-traffic-filter-problem.md) ismertető rövid útmutatót. A [hálózati biztonsági csoportok folyamatnaplóinak](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) engedélyezésével [elemezheti azon erőforrások bejövő és kimenő hálózati forgalmát](../network-watcher/traffic-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), amelyekhez hálózati biztonsági csoport van társítva.

## <a name="security-rules"></a>Biztonsági szabályok

A hálózati biztonsági csoportok nulla vagy tetszőleges számú szabályt tartalmazhatnak, az Azure-előfizetések [korlátain](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) belül. Az egyes szabályok az alábbi tulajdonságokat határozzák meg:

|Tulajdonság  |Magyarázat  |
|---------|---------|
|Name (Név)|Egy egyedi név a hálózati biztonsági csoporton belül.|
|Prioritás | Egy 100 és 4096 közötti szám. A szabályok feldolgozása prioritási sorrendben történik. Az alacsonyabb sorszámúak feldolgozása a magasabb sorszámúak előtt történik, mivel az alacsonyabb sorszámok magasabb prioritást jelölnek. Ha az adatforgalom megfelel valamelyik szabálynak, a feldolgozás leáll. Ennek eredményeképp az olyan alacsonyabb prioritású (magasabb számú) szabályokat, amelyek attribútumai megegyeznek a magasabb prioritású szabályokéival, a rendszer nem dolgozza fel.|
|Forrás vagy cél| Bármelyik vagy egy egyéni IP-cím, Classless Inter-Domain Routing- (CIDR-) blokk (például 10.0.0.0/24), [szolgáltatáscímke](#service-tags) vagy [alkalmazásbiztonsági csoport](#application-security-groups). Ha egy Azure-erőforrás címét adja meg, az erőforráshoz rendelt magánhálózati IP-címet adja meg. A hálózati biztonsági csoportok feldolgozása azután történik, hogy az Azure a bejövő forgalomhoz a nyilvános IP-címeket magánhálózati IP-címekre fordítja le, de még mielőtt, hogy a magánhálózati IP-címeket nyilvános IP-címekre fordítaná le a kimenő forgalomhoz. További tudnivalók az Azure-beli [IP-címekről](virtual-network-ip-addresses-overview-arm.md). Tartományok, szolgáltatáscímkék vagy alkalmazásbiztonsági csoportok megadásával kevesebb biztonsági szabályt kell majd létrehoznia. A több egyéni IP-cím vagy -tartomány megadásának lehetősége (szolgáltatáscímkékből és alkalmazásbiztonsági csoportokból nem adható meg több) az egyes szabályokban [kibővített biztonsági szabályok](#augmented-security-rules) néven érhető el. Kibővített biztonsági szabályok kizárólag a Resource Manager-alapú üzemi modellben létrehozott hálózati biztonsági csoportokban hozhatóak létre. A klasszikus üzemi modellben létrehozott hálózati biztonsági csoportokban nem adhat meg több IP-címet vagy -címtartományt. További információ az [Azure üzemi modellekről](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
|Protokoll     | TCP, UDP vagy bármely, amely tartalmazza a (de nem kizárólag) a TCP, UDP és ICMP. Az ICMP önmagában nem adható meg, ezért ha erre van szüksége, használja a Bármely lehetőséget. |
|Irány| Megadja, hogy a szabály a bejövő vagy a kimenő adatforgalomra vonatkozik.|
|Porttartomány     |Megadhat egy egyéni portot vagy egy porttartományt is. Megadhatja például a 80-as portot vagy a 10000–10005 tartományt. Tartományok megadásával kevesebb biztonsági szabályt kell majd létrehoznia. Kibővített biztonsági szabályok kizárólag a Resource Manager-alapú üzemi modellben létrehozott hálózati biztonsági csoportokban hozhatóak létre. A klasszikus üzemi modellben létrehozott hálózati biztonsági csoportokban egyazon szabályban nem adhat meg több portot vagy porttartományt.   |
|Műveletek     | Engedélyezés vagy letiltás        |

A hálózati biztonsági csoportok biztonsági szabályait a rendszer prioritásuk szerint, a rekordokkal kapcsolatos 5 információ (forrás, forrásport, cél, célport és protokoll) alapján értékeli ki, hogy a forgalom engedélyezve vagy tiltva legyen. Egy folyamatrekord jön létre a meglévő kapcsolatokhoz. A kommunikáció a folyamatrekordok kapcsolati állapota alapján lesz engedélyezve vagy tiltva. A folyamatrekord teszi lehetővé a hálózati biztonsági csoport állapotalapú működését. Ha bármely címre meghatároz egy kimenő biztonsági szabályt a 80-as porton keresztül, nem szükséges biztonsági szabályt megadnia a bejövő forgalomra a válaszhoz. Ha a kommunikáció kívülről indul, csak egy bejövő biztonsági szabályt kell meghatároznia. Ennek az ellenkezője is igaz. Ha egy porton engedélyezett a bejövő forgalom, nem szükséges egy kimenő biztonsági szabályt is megadni ugyanazon a porton történő válaszadáshoz.
Előfordulhat, hogy a meglévő kapcsolatok nem szakadnak meg az adatfolyamot engedélyező biztonsági szabály eltávolításakor. Az adatfolyam megszakad, ha a kapcsolatokat leállítják, és legalább néhány percig nincs forgalom egyik irányban sem.

Az egy hálózati biztonsági csoporton belül létrehozható biztonsági szabályok száma korlátozott. További részletek: [Az Azure korlátai](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="augmented-security-rules"></a>Kibővített biztonsági szabályok

A kibővített biztonsági szabályok megkönnyítik a virtuális hálózatok biztonsági definícióinak megadását, így nagyobb és összetettebb hálózati biztonsági szabályok alakíthatók ki kevesebb szabállyal. Több portot, több konkrét IP-címet és -tartományt foglalhat egyetlen, könnyen érthető biztonsági szabályba. Kibővített szabályokat a szabályok forrás, cél és port mezőiben is használhat. A biztonsági szabály definíciójának egyszerűbb karbantartása érdekében kombinálhatja a kibővített biztonsági szabályokat [szolgáltatáscímkékkel](#service-tags) vagy [alkalmazásbiztonsági csoportokkal](#application-security-groups). A címek, a tartományok és a portokkal, amelyeken a szabályban megadhatja száma korlátozva van. További részletek: [Az Azure korlátai](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="service-tags"></a>Szolgáltatáscímkék

 A szolgáltatáscímkék IP-címelőtagok csoportjait jelölik, így a segítségükkel csökkenthető a biztonsági szabályok létrehozásának összetettsége. Nem hozhat létre saját szolgáltatáscímkéket, és nem határozhatja meg, hogy melyik IP-címeket jelöljék az egyes címkék. A szolgáltatáscímkékben lévő címelőtagokat a Microsoft kezeli, és a címek változásával automatikusan frissíti a szolgáltatáscímkéket. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. 
 
 Letöltheti és integrálhatja az előtag részleteit tartalmazó szolgáltatáscímkék listáját egy helyszíni tűzfallal a következő heti közzétételekben az Azure [nyilvános](https://www.microsoft.com/download/details.aspx?id=56519), [USA-államigazgatási](https://www.microsoft.com/download/details.aspx?id=57063), [kínai](https://www.microsoft.com/download/details.aspx?id=57062) és [németországi](https://www.microsoft.com/download/details.aspx?id=57064) felhőiben.

 A következő szolgáltatáscímkék érhetőek el biztonsági szabályok meghatározásához. A neveik az egyes [Azure üzembehelyezési modellekben](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) némiképp eltérőek.

* **VirtualNetwork** (Resource Manager) (**VIRTUAL_NETWORK** klasszikus üzemi modell esetén): Ez a címke tartalmazza a virtuális hálózat címterét (az összes meghatározott CIDR-tartományt a virtuális hálózat), az összes csatlakoztatott helyszíni címteret és [társviszonyban álló](virtual-network-peering-overview.md) virtuális hálózatokat vagy a virtuális hálózat csatlakozik egy [virtuális hálózati átjáró](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* **AzureLoadBalancer** (Resource Manager) (**AZURE_LOADBALANCER** klasszikus üzemi modell esetén): Ez a címke az Azure infrastruktúra terheléselosztóját jelöli. A címkét a rendszer lefordítja a [gazdagép azon virtuális IP-címére](security-overview.md#azure-platform-considerations) (168.63.129.16), ahonnan az Azure állapot-mintavételei származnak. Ha nem az Azure Load Balancert használja, ezt a szabályt felül lehet bírálni.
* **Internetes** (Resource Manager) (**INTERNET** klasszikus üzemi modell esetén): Ez a címke az IP-címteret, amely a virtuális hálózaton kívül, és a nyilvános interneten érhető el. A címtartományba beletartozik az [Azure tulajdonában lévő nyilvános IP-címtér](https://www.microsoft.com/download/details.aspx?id=41653) is.
* **AzureCloud** (csak Resource Manager): Ez a címke az IP-címteret például az összes Azure- [adatközpont nyilvános IP-címek](https://www.microsoft.com/download/details.aspx?id=41653). Ha az *AzureCloud* értéket adja meg, a nyilvános Azure IP-címekre irányuló forgalom engedélyezhető vagy letiltható. Ha csak egy adott [régióban](https://azure.microsoft.com/regions) szeretné engedélyezni a hozzáférést az AzureCloud szolgáltatáshoz, megadhat egy régiót. Ha például csak az USA keleti régiójában szeretné engedélyezni a hozzáférést az Azure AzureCloud szolgáltatáshoz, megadhatja az *AzureCloud.EastUS* szolgáltatáscímkét. 
* **AzureTrafficManager** (csak Resource Manager): Ez a címke azt jelzi, hogy az Azure Traffic Manager mintavételi IP-címeket az IP-címterét. További információk a Traffic Manager mintavételezési IP-címeiről: [Azure Traffic Manager – Gyakori kérdések](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). 
* **Tárolási** (csak Resource Manager): Ez a címke azt jelzi, hogy az Azure Storage szolgáltatás IP-címterét. Ha a *Storage* értéket adja meg, a Storage szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható. Ha csak egy adott [régióban](https://azure.microsoft.com/regions) szeretné engedélyezni a hozzáférést a Storage szolgáltatáshoz, megadhat egy régiót. Ha például csak az USA keleti régiójában szeretné engedélyezni a hozzáférést az Azure Storage szolgáltatáshoz, megadhatja a *Storage.EastUS* szolgáltatáscímkét. A címke a szolgáltatást jelöli, annak adott példányait azonban nem. Például a címke az Azure Storage szolgáltatást jelöli, de nem egy adott Azure Storage-fiókot. A címke által jelölt címelőtagokat az **Internet** címke is jelöli. 
* **SQL** (csak Resource Manager): Ez a címke címelőtagjait Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL, és az Azure SQL Data Warehouse szolgáltatásokhoz. Ha az *SQL* értéket adja meg, az SQL szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható. Ha csak egy adott [régióban](https://azure.microsoft.com/regions) szeretné engedélyezni a hozzáférést az SQL szolgáltatáshoz, megadhat egy régiót. Ha például csak az USA keleti régiójában szeretné engedélyezni a hozzáférést az Azure SQL Database-hez, megadhatja az *Sql.EastUS* szolgáltatáscímkét. A címke a szolgáltatást jelöli, annak adott példányait azonban nem. Például a címke az Azure SQL Database szolgáltatást jelöli, de nem egy adott SQL-adatbázist vagy -kiszolgálót. A címke által jelölt címelőtagokat az **Internet** címke is jelöli. 
* **AzureCosmosDB** (csak Resource Manager): Ez a címke az Azure Cosmos Database szolgáltatás címelőtagjait. Ha az *AzureCosmosDB* értéket adja meg, az AzureCosmosDB szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható. Ha csak egy adott [régióban](https://azure.microsoft.com/regions) szeretné engedélyezni a hozzáférést az AzureCosmosDB szolgáltatáshoz, a régiót az AzureCosmosDB.[régió neve] formátumban adhatja meg. 
* **Azurekeyvault értékre van** (csak Resource Manager): Ez a címke az Azure KeyVault szolgáltatás címelőtagjait. Ha az *AzureKeyVault* értéket adja meg, az AzureKeyVault szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható. Ha csak egy adott [régióban](https://azure.microsoft.com/regions) szeretné engedélyezni a hozzáférést az AzureKeyVault szolgáltatáshoz, a régiót az AzureKeyVault.[régió neve] formátumban adhatja meg. 
* **Az EventHub** (csak Resource Manager): Ez a címke az Azure Event hubhoz szolgáltatás címelőtagjait. Ha az *EventHub* értéket adja meg, az EventHub szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható. Ha csak egy adott [régióban](https://azure.microsoft.com/regions) szeretné engedélyezni a hozzáférést az EventHub szolgáltatáshoz, a régiót az EventHub.[régió neve] formátumban adhatja meg. 
* **A ServiceBus** (csak Resource Manager): Ez a címke az Azure Szolgáltatásbusz-szolgáltatás, a prémium szintű szolgáltatási szintet címelőtagjait. Ha a *ServiceBus* értéket adja meg, a ServiceBus szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható. Ha csak egy adott [régióban](https://azure.microsoft.com/regions) szeretné engedélyezni a hozzáférést a ServiceBus szolgáltatáshoz, a régiót a ServiceBus.[régió neve] formátumban adhatja meg. 
* **MicrosoftContainerRegistry** (csak Resource Manager): Ez a címke a Microsoft Container Registry szolgáltatás címelőtagjait. Ha a *MicrosoftContainerRegistry* értéket adja meg, a MicrosoftContainerRegistry felé irányuló forgalom engedélyezhető vagy letiltható. Ha csak egy adott [régióban](https://azure.microsoft.com/regions) szeretné engedélyezni a hozzáférést a MicrosoftContainerRegistry szolgáltatáshoz, a régiót a MicrosoftConainerRegistry.[régió neve] formátumban adhatja meg. 
* **AzureContainerRegistry** (csak Resource Manager): Ez a címke az Azure Container Registry szolgáltatás címelőtagjait. Ha az *AzureContainerRegistry* értéket adja meg, az AzureContainerRegistry felé irányuló forgalom engedélyezhető vagy letiltható. Ha csak egy adott [régióban](https://azure.microsoft.com/regions) szeretné engedélyezni a hozzáférést az AzureContainerRegistry szolgáltatáshoz, a régiót az AzureConainerRegistry.[régió neve] formátumban adhatja meg. 
* **Az App Service** (csak Resource Manager): Ez a címke az Azure App Service szolgáltatás címelőtagjait. Ha az *AppService* értéket adja meg, az AppService szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható. Ha csak egy adott [régióban](https://azure.microsoft.com/regions) szeretné engedélyezni a hozzáférést az AppService szolgáltatáshoz, a régiót az AppService.[régió neve] formátumban adhatja meg. 
* **AppServiceManagement** (csak Resource Manager): Ez a címke az Azure App Service Management szolgáltatás címelőtagjait. Ha az *AppServiceManagement* értéket adja meg, az AppServiceManagement szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható. 
* **Az ApiManagement** (csak Resource Manager): Ez a címke az Azure Api Management szolgáltatás címelőtagjait. Ha az *ApiManagement* értéket adja meg, az ApiManagement szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható.  
* **AzureConnectors** (csak Resource Manager): Ez a címke az Azure-összekötő szolgáltatás címelőtagjait. Ha az *AzureConnectors* értéket adja meg, az AzureConnectors szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható. Ha csak egy adott [régióban](https://azure.microsoft.com/regions) szeretné engedélyezni a hozzáférést az AzureConnectors szolgáltatáshoz, a régiót az AzureConnectors.[régió neve] formátumban adhatja meg. 
* **GatewayManager** (csak Resource Manager): Ez a címke az Azure-átjáró Manager szolgáltatás címelőtagjait. Ha a *GatewayManager* értéket adja meg, a GatewayManager szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható.  
* **AzureDataLake** (csak Resource Manager): Ez a címke az Azure Data Lake szolgáltatás címelőtagjait. Ha az *AzureDataLake* értéket adja meg, az AzureDataLake szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható. 
* **AzureActiveDirectory** (csak Resource Manager): Ez a címke az AzureActiveDirectory szolgáltatás címelőtagjait. Ha az *AzureActiveDirectory* értéket adja meg, az AzureActiveDirectory szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható.  
* **AzureMonitor** (csak Resource Manager): Ez a címke a AzureMonitor szolgáltatás címelőtagjait. Ha megad *AzureMonitor* értékénél, engedélyezett vagy tiltott forgalmat a AzureMonitor. 
* **ServiceFabric** (csak Resource Manager): Ez a címke a ServiceFabric-szolgáltatás címelőtagjait. Ha megad *ServiceFabric* értékénél, engedélyezett vagy tiltott forgalmat ServiceFabric való. 
* **AzureMachineLearning** (csak Resource Manager): Ez a címke a AzureMachineLearning szolgáltatás címelőtagjait. Ha megad *AzureMachineLearning* értékénél, engedélyezett vagy tiltott forgalmat a AzureMachineLearning. 

> [!NOTE]
> Az Azure-szolgáltatások szolgáltatáscímkék címelőtagjait használja a megadott felhőből. 

> [!NOTE]
> Ha egy [virtuális hálózati szolgáltatásvégpontot](virtual-network-service-endpoints-overview.md) implementál egy szolgáltatáshoz, például az Azure Storage-hoz vagy az Azure SQL Database-hez, az Azure hozzáad egy [útvonalat](virtual-networks-udr-overview.md#optional-default-routes) a szolgáltatás virtuális hálózatának alhálózatához. Az útvonal címelőtagjai ugyanazok a címelőtagok vagy CIDR-tartományok lesznek, mint a megfelelő szolgáltatáscímke.

## <a name="default-security-rules"></a>Alapértelmezett biztonsági szabályok

Az Azure a következő alapértelmezett szabályokat hozza létre a létrehozott hálózati biztonsági csoportokban:

### <a name="inbound"></a>Bejövő

#### <a name="allowvnetinbound"></a>AllowVNetInBound

|Prioritás|Forrás|Forrásportok|Cél|Célportok|Protokoll|Hozzáférés|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|Összes|Engedélyezés|

#### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Prioritás|Forrás|Forrásportok|Cél|Célportok|Protokoll|Hozzáférés|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|Összes|Engedélyezés|

#### <a name="denyallinbound"></a>DenyAllInbound

|Prioritás|Forrás|Forrásportok|Cél|Célportok|Protokoll|Hozzáférés|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|Összes|Megtagadás|

### <a name="outbound"></a>Kimenő

#### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Prioritás|Forrás|Forrásportok| Cél | Célportok | Protokoll | Hozzáférés |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | Összes | Engedélyezés |

#### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|Prioritás|Forrás|Forrásportok| Cél | Célportok | Protokoll | Hozzáférés |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | Összes | Engedélyezés |

#### <a name="denyalloutbound"></a>DenyAllOutBound

|Prioritás|Forrás|Forrásportok| Cél | Célportok | Protokoll | Hozzáférés |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | Összes | Megtagadás |

A **Forrás** és a **Cél** oszlopban a *VirtualNetwork*, *AzureLoadBalancer* és *Internet* értékek [szolgáltatáscímkék](#service-tags), nem IP-címek. A Protokoll oszlopban az **Összes** érték a TCP, UDP és ICMP lehetőségeket foglalja magában. Szabályok létrehozásakor választhatja a TCP, az UDP vagy az Összes lehetőséget, az ICMP-t külön azonban nem adhatja meg. Ezért ha egy szabályhoz az ICMP szükséges, válassza az *Összes* lehetőséget a protokollnál. A *0.0.0.0/0* érték a **Forrás** és a **Cél** oszlopban az összes címet jelöli. Ügyfelek, például az Azure portal, Azure CLI-vel, vagy a Powershell segítségével *, sem a kifejezésben.
 
Az alapértelmezett szabályok nem távolíthatók el, azonban magasabb prioritású szabályok létrehozásával felülírhatók.

## <a name="application-security-groups"></a>Alkalmazásbiztonsági csoportok

Az alkalmazásbiztonsági csoportokkal az alkalmazás struktúrájának természetes bővítményeként konfigurálhatja a hálózati biztonságot, így csoportosíthatja a virtuális gépeket, és ezen csoportok alapján meghatározhatja a hálózati biztonsági szabályokat. A biztonsági szabályokat újrahasznosíthatja nagy léptékben is a konkrét IP-címek manuális karbantartása nélkül. A platform képes kezelni a konkrét IP-címek és a szabálykészletek jelentette összetettséget, így Ön az üzleti logikára összpontosíthat. Az alkalmazásbiztonsági csoportok könnyebb megértése érdekében tekintsük a következő példát:

![Alkalmazásbiztonsági csoportok](./media/security-groups/application-security-groups.png)

Az előző képen az *NIC1* és az *NIC2* az *AsgWeb* alkalmazásbiztonsági csoport tagjai. Az *NIC3* az *AsgLogic* alkalmazásbiztonsági csoport tagja. Az *NIC4* az *AsgDb* alkalmazásbiztonsági csoport tagja. Bár ebben a példában mindegyik hálózati adapter egyetlen alkalmazásbiztonsági csoport tagja, egy hálózati adapter akár több alkalmazásbiztonsági csoportnak is tagja lehet, az [Azure korlátainak](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) megfelelően. Egyik hálózati adapterhez sincs hálózati biztonsági csoport társítva. Az *NSG1* mindkét alhálózathoz társítva van, és a következő szabályokat tartalmazza:

### <a name="allow-http-inbound-internet"></a>Allow-HTTP-Inbound-Internet

Ez a szabály az internetről a webkiszolgálókra irányuló forgalom engedélyezéséhez szükséges. Mivel az internetről bejövő forgalmat az alapértelmezett [DenyAllInbound](#denyallinbound) biztonsági szabály tiltja, az *AsgLogic* és az *AsgDb* alkalmazásbiztonsági csoportok esetében nincs szükség további szabályokra.

|Prioritás|Forrás|Forrásportok| Cél | Célportok | Protokoll | Hozzáférés |
|---|---|---|---|---|---|---|
| 100 | Internet | * | AsgWeb | 80 | TCP | Engedélyezés |

### <a name="deny-database-all"></a>Deny-Database-All

Mivel az alapértelmezett [AllowVNetInBound](#allowvnetinbound) biztonsági szabály az azonos virtuális hálózaton lévő erőforrások között minden kommunikációt engedélyez, ez a szabály az összes erőforrástól érkező forgalom tiltásához szükséges.

|Prioritás|Forrás|Forrásportok| Cél | Célportok | Protokoll | Hozzáférés |
|---|---|---|---|---|---|---|
| 120 | * | * | AsgDb | 1433 | Összes | Megtagadás |

### <a name="allow-database-businesslogic"></a>Allow-Database-BusinessLogic

Ez a szabály engedélyezi az *AsgLogic* alkalmazásbiztonsági csoportról az *AsgDb* alkalmazásbiztonsági csoportra irányuló forgalmat. A szabály prioritása magasabb a *Deny-Database-All* szabály prioritásánál. Ennek eredményeként ez a szabály a *Deny-Database-All* szabály előtt lesz kiértékelve, ezért az *AsgLogic* alkalmazásbiztonsági csoporttól érkező forgalom engedélyezve lesz, az összes többi forgalom pedig le lesz tiltva.

|Prioritás|Forrás|Forrásportok| Cél | Célportok | Protokoll | Hozzáférés |
|---|---|---|---|---|---|---|
| 110 | AsgLogic | * | AsgDb | 1433 | TCP | Engedélyezés |

Az alkalmazásbiztonsági csoportokat forrásként vagy célként megadó szabályok csak az adott alkalmazásbiztonsági csoport tagságába tartozó hálózati adapterekre lesznek alkalmazva. Ha a hálózati adapter nem tagja az alkalmazásbiztonsági csoportnak, a szabály nem lesz az adapterre alkalmazva, még akkor sem, ha a hálózati biztonsági csoport az alhálózathoz van rendelve.

Az alkalmazásbiztonsági csoportok a következő korlátozásokkal rendelkeznek:

-   Előfizetésenként korlátozott számú alkalmazásbiztonsági csoporttal rendelkezhet az egyes előfizetésekben, valamint egyéb korlátozások is vonatkoznak az alkalmazásbiztonsági csoportokra. További részletek: [Az Azure korlátai](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- A biztonsági szabályokban forrásként és célként egy biztonsági csoportot adhat meg. Nem adhat meg több alkalmazásbiztonsági csoportot a forrásban vagy a célban.
- Az egyes alkalmazásbiztonsági csoportokhoz rendelt összes hálózati adapternek ugyanazon a virtuális hálózaton kell lennie, amelyen az alkalmazásbiztonsági csoporthoz rendelt első hálózati adapter található. Például ha az *AsgWeb* nevű alkalmazásbiztonsági csoporthoz rendelt első hálózati adapter a *VNet1* virtuális hálózaton található, akkor az *ASGWeb* csoporthoz rendelt összes többi hálózati adapternek is a *VNet1* hálózaton kell lennie. Nem adhat különböző virtuális hálózatokról származó hálózati adaptereket ugyanahhoz az alkalmazásbiztonsági csoporthoz.
- Ha egy biztonsági szabály forrásaként és céljaként határoz meg alkalmazásbiztonsági csoportokat, mindkét alkalmazásbiztonsági csoport hálózati adaptereinek ugyanazon a virtuális hálózaton kell lenniük. Ha például az *AsgLogic* a *VNet1* hálózatról tartalmaz hálózati adaptereket, az *AsgDb* pedig a *VNet2* hálózatról, nem rendelheti hozzá az *AsgLogic* csoportot forrásként az *AsgDb* csoportot pedig célként egy adott szabályban. A forrás és a cél alkalmazásbiztonsági csoporthoz tartozó összes hálózati adapternek ugyanazon a virtuális hálózaton kell lennie.

> [!TIP]
> A szükséges biztonsági szabályok számának csökkentése, valamint a szabályok módosíthatósága érdekében tervezze meg a szükséges alkalmazásbiztonsági csoportokat, és ha lehetséges, szolgáltatáscímkék vagy alkalmazásbiztonsági csoportok használatával hozza létre a szabályokat, ne egyedi IP-címek vagy IP-címtartományok alapján.

## <a name="how-traffic-is-evaluated"></a>A forgalom kiértékelése

Az Azure-beli virtuális hálózatokban több Azure-szolgáltatásból is helyezhet üzembe erőforrásokat. A teljes listáért tekintse meg [a virtuális hálózatokban üzembe helyezhető szolgáltatásokat](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) ismertető témakört. A virtuális gépek mindegyik virtuális hálózatának [alhálózatához](virtual-network-manage-subnet.md#change-subnet-settings) és [hálózati adapteréhez](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group) nulla vagy egy hálózati biztonsági csoport rendelhető hozzá. Egy adott hálózati biztonsági csoport tetszőleges számú alhálózathoz és hálózati adapterhez rendelhető.

A következő képen különböző forgatókönyvek láthatók a hálózati biztonsági csoportok üzembe helyezésére az internetről bejövő és az internetre kimenő hálózati forgalom engedélyezéséhez a 80-as TCP-porton keresztül:

![NSG-feldolgozás](./media/security-groups/nsg-interaction.png)

A fenti kép és a következő leírás mutatja be, hogy az Azure hogyan dolgozza fel a hálózati biztonsági csoportok bejövő és kimenő szabályait:

### <a name="inbound-traffic"></a>Bejövő forgalom

A bejövő forgalom esetében az Azure először a hálózati biztonsági csoport alhálózathoz rendelt szabályait dolgozza fel (ha van alhálózat), majd a hálózati biztonsági csoport hálózati adapterhez rendelt szabályait (ha van hálózati adapter).

- **VM1**: A biztonsági szabályok *NSG1* lesz feldolgozva, mert hozzá rendelve a *Subnet1* és *VM1* van *Subnet1*. Ha nem hozott létre egy szabályt, amely engedi a 80-as port bejövő forgalmát, a forgalmat az alapértelmezett [DenyAllInbound](#denyallinbound) biztonsági szabály letiltja, és azt az *NSG2* soha nem értékeli ki, mivel az *NSG2* a hálózati adapterhez van rendelve. Ha az *NSG1* rendelkezik olyan biztonsági szabállyal, amely engedélyezi a forgalmat a 80-as porton, a forgalmat ezután az *NSG2* feldolgozza. Ahhoz, hogy a 80-as porton engedélyezve legyen a virtuális gépre irányuló forgalom, az *NSG1* és az *NSG2* csoportnak egyaránt rendelkeznie kell olyan szabállyal, amely engedi az internetről beérkező forgalmat a 80-as porton.
- **VM2**: A szabályok *NSG1* vannak feldolgozva, mert *VM2* is *Subnet1*. Mivel a *VM2* hálózati adapteréhez nincs hálózati biztonsági csoport rendelve, erre a gépre az *NSG1* által átengedett minden forgalom megérkezik, és az *NSG1* által letiltott minden forgalom blokkolva lesz. Ha egy alhálózathoz egy hálózati biztonsági csoport van rendelve, az adott alhálózaton az összes erőforrás számára le lesz tiltva vagy engedélyezve lesz a forgalom.
- **VM3**: Mivel nincs hálózati biztonsági csoport társítva *Subnet2*, adatforgalom engedélyezett az alhálózat be és dolgozza fel *NSG2*, mert *NSG2* kapcsolódik a hálózathoz csatlakoztatott adapter *VM3*.
- **VM4**: Forgalom irányulni *VM4,* , mert a hálózati biztonsági csoport nincs társítva a *Subnet3*, vagy a hálózati adaptert a virtuális gépen. Ha egy alhálózathoz vagy hálózati adapterhez nincs hálózati biztonsági csoport rendelve, ezeken a teljes hálózati forgalom engedélyezve lesz.

### <a name="outbound-traffic"></a>Kimenő forgalom

A kimenő forgalom esetében az Azure először a hálózati biztonsági csoport hálózati adapterhez rendelt szabályait dolgozza fel (ha van hálózati adapter), majd a hálózati biztonsági csoport alhálózathoz rendelt szabályait (ha van alhálózat).

- **VM1**: A biztonsági szabályok *NSG2* dolgozza fel. Ha nem hoz létre egy biztonsági szabályt, amely letiltja a 80-as port internetre irányuló kimenő forgalmát, a forgalmat az alapértelmezett [AllowInternetOutbound](#allowinternetoutbound) biztonsági szabály az *NSG1* és az *NSG2* csoporton egyaránt engedélyezi. Ha az *NSG2* rendelkezik olyan biztonsági szabállyal, amely letiltja a forgalmat a 80-as porton, a forgalom le lesz tiltva, és az *NSG1* soha nem értékeli ki. Ahhoz, hogy a 80-as porton le legyen tiltva a virtuális gépről érkező forgalom, legalább az egyik hálózati biztonsági csoportnak rendelkeznie kell olyan szabállyal, amely letiltja az internetre irányuló kimenő forgalmat a 80-as porton.
- **VM2**: Összes forgalom keresztül zajlik a hálózati adapter az alhálózathoz, mert a hálózati adapter csatlakoztatva *VM2* nincs hálózati biztonsági csoport társítva van hozzá. Az *NSG1* szabályai fel lesznek dolgozva.
- **VM3**: Ha *NSG2* biztonsági szabályt, amely megtagadja a 80-as porton, a forgalmat a rendszer megtagadja. Ha az *NSG2* rendelkezik olyan biztonsági szabállyal, amely engedélyezi a forgalmat a 80-as porton, a 80-as porton engedélyezve lesz az internetre irányuló kimenő forgalom, mivel a *Subnet2* hálózathoz nincs hálózati biztonsági csoport rendelve.
- **VM4**: Az összes hálózati forgalom irányulni a *VM4,* , mert a hálózati biztonsági csoport nincs csatolva a virtuális géphez, vagy a hálózati adapterhez társított *Subnet3*.

A hálózati adapterekhez rendelt összesített szabályokat könnyen megismerheti a hálózati adapter [érvényes biztonsági szabályainak](virtual-network-network-interface.md#view-effective-security-rules) megtekintésével. Emellett az Azure Network Watcher [IP-forgalom ellenőrzése](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szolgáltatásával is megállapíthatja, hogy a kommunikáció engedélyezett-e a hálózati adapterre/adapterről. Az IP-forgalom ellenőrzése megmutatja, hogy a kommunikáció engedélyezve vagy blokkolva van-e, valamint hogy melyik hálózati biztonsági szabály engedélyezi vagy blokkolja a forgalmat.

> [!NOTE]
> Hálózati biztonsági csoportok társítva, az alhálózatokra vagy virtuális gépek és a klasszikus üzemi modellel üzembe helyezett cloud services és alhálózatokhoz vagy a hálózati adapterek a Resource Manager-alapú üzemi modellben. Az Azure üzembehelyezési modellekkel kapcsolatos további információkért lásd: [Az Azure üzemi modelljeinek megismerése](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

> [!TIP]
> Hacsak nincs erre kifejezett oka, javasoljuk, hogy az egyes hálózati biztonsági csoportokat egy alhálózathoz vagy egy hálózati adapterhez rendelje, mindkettőhöz ne. Mivel az alhálózathoz rendelt hálózati biztonsági csoport szabályai ütközhetnek a hálózati adapterhez rendelt hálózati biztonsági csoport szabályaival, nem várt kommunikációs problémák merülhetnek fel, amelyek hibaelhárítást igényelnek.

## <a name="azure-platform-considerations"></a>Tudnivalók az Azure platformhoz

- **Virtuális IP-címét a gazdacsomópont**: Alapvető infrastruktúra szolgáltatásokat, például a DHCP, DNS, IMDS és szolgáltatásállapot-figyelést 168.63.129.16 és 169.254.169.254 a virtuális gazdagép IP-címekkel vannak megadva. Ezen IP-címek tartoznak, és csak a virtualizált IP-címeit az összes régióban erre a célra.
- **Licencelés (kulcskezelő szolgáltatás)**: A virtuális gépeken futó Windows-rendszerképeket licencelni kell. A licenceléshez el kell küldeni egy licencelési kérelmet a Kulcskezelő szolgáltatás ilyen kérelmeket kezelő kiszolgálóinak. A kérelmet az 1688-as kimenő porton küldi el a rendszer. Az [alapértelmezett 0.0.0.0/0 útvonalat](virtual-networks-udr-overview.md#default-route) használó konfigurációkban ez a platformszabály le van tiltva.
- **Virtuális gépek elosztott terhelésű készletek**: A alkalmazni forrás forrásport és címtartomány a forrásszámítógépről származik, nem a terheléselosztó vannak. A célport és a címtartomány nem a terheléselosztóhoz, hanem a célszámítógéphez tartozik.
- **Azure szolgáltatáspéldányok**: Több Azure szolgáltatás, például a Virtual Machine Scale Sets, HDInsight és az alkalmazásszolgáltatási környezetek példányait virtuális hálózati alhálózatokon vannak üzembe helyezve. A virtuális hálózatokon üzembe helyezhető szolgáltatások teljes listájához lásd a [Virtuális hálózatok az Azure-szolgáltatásokhoz](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) című témakört. Mindenképp ismerkedjen meg behatóan az egyes szolgáltatások portkövetelményeivel, mielőtt egy hálózati biztonsági csoportot alkalmazna az erőforrást üzemeltető alhálózatra. Ha a valamely szolgáltatás által használt portokat letiltja, a szolgáltatás nem megfelelően fog működni.
- **Kimenő e-mailek küldése**: A Microsoft azt javasolja, hogy hitelesített SMTP-továbbítási szolgáltatásokkal (általában 587 TCP-porton, de gyakran más, keresztül is) az Azure Virtual Machines e-mail üzenetek küldéséhez. Az SMTP-továbbítási szolgáltatások a feladói jellegzetességek biztosítására szakosodtak, így minimalizálják annak lehetőségét, hogy a külső e-mail-szolgáltatók visszautasítsák az üzeneteket. Ilyen SMTP-továbbítási szolgáltatás például, a teljesség igénye nélkül, az Exchange Online Protection és a SendGrid. Az SMTP-továbbítási szolgáltatások használata nincsen korlátozva az Azure-ban, függetlenül attól, hogy milyen előfizetése van. 

  Amennyiben 2017. november 15. előtt hozta létre Azure-előfizetését, az SMTP-továbbítási szolgáltatások használata mellett közvetlenül a 25-ös TCP-porton keresztül is küldhet e-maileket. Amennyiben 2017. november 15. után fizetett elő, nem biztos hogy küldhet e-maileket közvetlenül a 25-ös porton keresztül. A 25-ös porton keresztül folytatott kimenő kommunikáció viselkedése az előfizetés típusától függ, amely lehet:

     - **Nagyvállalati szerződés**: 25-ös porton keresztüli kimenő kommunikáció engedélyezve van. Közvetlenül a virtuális gépekről küldhet kimenő e-maileket a külső e-mail-szolgáltatóknak, és az Azure platform korlátozásai nem érvényesülnek. 
     - **Utólagos elszámolás:** 25 porton keresztüli kimenő kommunikáció minden erőforráson blokkolva van. Ha közvetlenül a virtuális gépéről szeretne e-mailt küldenie egy külső e-mail-szolgáltatónak (hitelesített SMTP-továbbítás használata nélkül), kérheti a korlátozás feloldását. A kérelmeket a Microsoft saját belátása szerint értékeli és hagyja jóvá, a visszaélések kiküszöbölésére szolgáló megfelelő ellenőrzések elvégzése után. Kérelem benyújtásához támogatási esetet kell nyitnia a *Technikai*, *Virtuális hálózati kapcsolat*, *Sikertelen e-mail-küldés (SMTP/25-ös port)* problématípus kiválasztásával. A támogatási esetben részletesen indokolja, hogy előfizetésének miért kell közvetlenül a levelezési szolgáltatónak e-mailt küldenie a hitelesített SMTP-továbbítás használata helyett. Amennyiben előfizetését felmentik a korlátozás alól, csak a mentesítés dátuma után létrehozott virtuális gépek képesek a 25-ös porton keresztüli kimenő kommunikációra.
     - **Az MSDN, Azure Pass, Azure in Open, Education, BizSpark és ingyenes próbaverzió**: 25 porton keresztüli kimenő kommunikáció minden erőforráson blokkolva van. Nem küldhető kérelem a korlátozás feloldására, mert a kérelmek nem teljesíthetők. Ha mindenképp szeretne e-mailt küldeni a virtuális gépről, használjon SMTP-továbbítási szolgáltatást.
     - **Felhőszolgáltató**: Ügyfelek által felhasznált Azure-erőforrások felhőszolgáltató keresztül a hozzon létre egy támogatási esetet a felhőszolgáltatóval, és a kérés, hogy a szolgáltató egy feloldása eset létrehozása, a felhasználók nevében, ha egy biztonságos SMTP-továbbítás nem használható.

  Amennyiben az Azure engedélyezi az e-mailek küldését a 25-ös porton keresztül, a Microsoft nem tudja garantálni, hogy a levelező szolgáltatók elfogadják a virtuális gépről érkező bejövő e-maileket. Ha egy szolgáltató elutasítja az Ön virtuális gépéről érkező leveleket, vele együttműködésben oldja meg az üzenetküldési vagy levélszemétszűrési problémákat, vagy használjon hitelesített SMTP-továbbítási szolgáltatást.

## <a name="next-steps"></a>További lépések

* Ismerje meg [a hálózati biztonsági csoportok létrehozását](tutorial-filter-network-traffic.md).
