---
title: Azure biztonsági csoportok – áttekintés
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
ms.author: malop
ms.reviewer: kumud
ms.openlocfilehash: 25c732d1311e2bcffe0fda0d5e427d5df5f99da6
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70065928"
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
|Priority | Egy 100 és 4096 közötti szám. A szabályok feldolgozása prioritási sorrendben történik. Az alacsonyabb sorszámúak feldolgozása a magasabb sorszámúak előtt történik, mivel az alacsonyabb sorszámok magasabb prioritást jelölnek. Ha az adatforgalom megfelel valamelyik szabálynak, a feldolgozás leáll. Ennek eredményeképp az olyan alacsonyabb prioritású (magasabb számú) szabályokat, amelyek attribútumai megegyeznek a magasabb prioritású szabályokéival, a rendszer nem dolgozza fel.|
|Forrás vagy cél| Bármelyik vagy egy egyéni IP-cím, Classless Inter-Domain Routing- (CIDR-) blokk (például 10.0.0.0/24), [szolgáltatáscímke](#service-tags) vagy [alkalmazásbiztonsági csoport](#application-security-groups). Ha egy Azure-erőforrás címét adja meg, az erőforráshoz rendelt magánhálózati IP-címet adja meg. A hálózati biztonsági csoportok feldolgozása azután történik, hogy az Azure a bejövő forgalomhoz a nyilvános IP-címeket magánhálózati IP-címekre fordítja le, de még mielőtt, hogy a magánhálózati IP-címeket nyilvános IP-címekre fordítaná le a kimenő forgalomhoz. További tudnivalók az Azure-beli [IP-címekről](virtual-network-ip-addresses-overview-arm.md). Tartományok, szolgáltatáscímkék vagy alkalmazásbiztonsági csoportok megadásával kevesebb biztonsági szabályt kell majd létrehoznia. A több egyéni IP-cím vagy -tartomány megadásának lehetősége (szolgáltatáscímkékből és alkalmazásbiztonsági csoportokból nem adható meg több) az egyes szabályokban [kibővített biztonsági szabályok](#augmented-security-rules) néven érhető el. Kibővített biztonsági szabályok kizárólag a Resource Manager-alapú üzemi modellben létrehozott hálózati biztonsági csoportokban hozhatóak létre. A klasszikus üzemi modellben létrehozott hálózati biztonsági csoportokban nem adhat meg több IP-címet vagy -címtartományt. További információ az [Azure üzemi modellekről](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
|Protocol     | TCP, UDP, ICMP vagy any.|
|Direction| Megadja, hogy a szabály a bejövő vagy a kimenő adatforgalomra vonatkozik.|
|Porttartomány     |Megadhat egy egyéni portot vagy egy porttartományt is. Megadhatja például a 80-as portot vagy a 10000–10005 tartományt. Tartományok megadásával kevesebb biztonsági szabályt kell majd létrehoznia. Kibővített biztonsági szabályok kizárólag a Resource Manager-alapú üzemi modellben létrehozott hálózati biztonsági csoportokban hozhatóak létre. A klasszikus üzemi modellben létrehozott hálózati biztonsági csoportokban egyazon szabályban nem adhat meg több portot vagy porttartományt.   |
|Action     | Engedélyezés vagy letiltás        |

A hálózati biztonsági csoportok biztonsági szabályait a rendszer prioritásuk szerint, a rekordokkal kapcsolatos 5 információ (forrás, forrásport, cél, célport és protokoll) alapján értékeli ki, hogy a forgalom engedélyezve vagy tiltva legyen. Egy folyamatrekord jön létre a meglévő kapcsolatokhoz. A kommunikáció a folyamatrekordok kapcsolati állapota alapján lesz engedélyezve vagy tiltva. A folyamatrekord teszi lehetővé a hálózati biztonsági csoport állapotalapú működését. Ha bármely címre meghatároz egy kimenő biztonsági szabályt a 80-as porton keresztül, nem szükséges biztonsági szabályt megadnia a bejövő forgalomra a válaszhoz. Ha a kommunikáció kívülről indul, csak egy bejövő biztonsági szabályt kell meghatároznia. Ennek az ellenkezője is igaz. Ha egy porton engedélyezett a bejövő forgalom, nem szükséges egy kimenő biztonsági szabályt is megadni ugyanazon a porton történő válaszadáshoz.
Előfordulhat, hogy a meglévő kapcsolatok nem szakadnak meg az adatfolyamot engedélyező biztonsági szabály eltávolításakor. Az adatfolyam megszakad, ha a kapcsolatokat leállítják, és legalább néhány percig nincs forgalom egyik irányban sem.

Az egy hálózati biztonsági csoporton belül létrehozható biztonsági szabályok száma korlátozott. További részletek: [Az Azure korlátai](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="augmented-security-rules"></a>Kibővített biztonsági szabályok

A kibővített biztonsági szabályok megkönnyítik a virtuális hálózatok biztonsági definícióinak megadását, így nagyobb és összetettebb hálózati biztonsági szabályok alakíthatók ki kevesebb szabállyal. Több portot, több konkrét IP-címet és -tartományt foglalhat egyetlen, könnyen érthető biztonsági szabályba. Kibővített szabályokat a szabályok forrás, cél és port mezőiben is használhat. A biztonsági szabály definíciójának egyszerűbb karbantartása érdekében kombinálhatja a kibővített biztonsági szabályokat [szolgáltatáscímkékkel](#service-tags) vagy [alkalmazásbiztonsági csoportokkal](#application-security-groups). A szabályokban megadható címek, tartományok és portok száma korlátozott. További részletek: [Az Azure korlátai](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="service-tags"></a>Szolgáltatáscímkék

A szolgáltatáscímkék IP-címelőtagok csoportjait jelölik, így a segítségükkel csökkenthető a biztonsági szabályok létrehozásának összetettsége. Nem hozhat létre saját szolgáltatáscímkéket, és nem határozhatja meg, hogy melyik IP-címeket jelöljék az egyes címkék. A szolgáltatáscímkékben lévő címelőtagokat a Microsoft kezeli, és a címek változásával automatikusan frissíti a szolgáltatáscímkéket. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. 

A következő szolgáltatási címkék használhatók a [hálózati biztonsági csoportok szabályaiban](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules). [Azure Firewall hálózati szabályokban](https://docs.microsoft.com/azure/firewall/service-tags)is használhatók a szolgáltatáshoz tartozó, csillaggal (azaz AzureCloud *) rendelkező szolgáltatási címkék. 

* **ApiManagement*** (csak Resource Manager esetén): Ez a címke a APIM dedikált központi telepítések felügyeleti forgalmának előtagjait jelöli. Ha az *ApiManagement* értéket adja meg, az ApiManagement szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható. Ez a címke a bejövő/kimenő biztonsági szabályhoz ajánlott. 
* **AppService*** (csak Resource Manager esetén): Ez a címke az Azure AppService szolgáltatáshoz tartozó címek előtagjait jelöli. Ha az *AppService* értéket adja meg, az AppService szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható. Ha csak egy adott [régióban](https://azure.microsoft.com/regions) szeretné engedélyezni a hozzáférést az AppService szolgáltatáshoz, a régiót az AppService.[régió neve] formátumban adhatja meg. Ez a címke javasolt a kimenő biztonsági szabályok WebApps a frontendek számára.  
* **AppServiceManagement*** (csak Resource Manager esetén): Ez a címke a App Service Environment dedikált központi telepítések felügyeleti forgalmának előtagjait jelöli. Ha az *AppServiceManagement* értéket adja meg, az AppServiceManagement szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható. Ez a címke a bejövő/kimenő biztonsági szabályhoz ajánlott. 
* **AzureActiveDirectory*** (csak Resource Manager esetén): Ez a címke a AzureActiveDirectory szolgáltatás előtagjait jelöli. Ha az *AzureActiveDirectory* értéket adja meg, az AzureActiveDirectory szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható. Ez a címke a kimenő biztonsági szabályokhoz ajánlott.
* **AzureActiveDirectoryDomainServices*** (csak Resource Manager esetén): Ez a címke a Azure Active Directory Domain Services dedikált központi telepítések felügyeleti forgalmának előtagjait jelöli. Ha az értékhez *AzureActiveDirectoryDomainServices* ad meg, a forgalom a AzureActiveDirectoryDomainServices számára engedélyezett vagy megtagadható. Ez a címke a bejövő/kimenő biztonsági szabályhoz ajánlott.  
* **AzureBackup*** (csak Resource Manager esetén): Ez a címke a AzureBackup szolgáltatás előtagjait jelöli. Ha az értékhez *AzureBackup* ad meg, a forgalom a AzureBackup számára engedélyezett vagy megtagadható. Ez a címke függőséget tartalmaz a **Storage** és a **AzureActiveDirectory** címkével. Ez a címke a kimenő biztonsági szabályokhoz ajánlott. 
* **AzureCloud*** (csak Resource Manager esetén): Ez a címke az Azure IP-címtartományt jelöli, beleértve az összes [adatközpont nyilvános IP-címét](https://www.microsoft.com/download/details.aspx?id=41653). Ha az *AzureCloud* értéket adja meg, a nyilvános Azure IP-címekre irányuló forgalom engedélyezhető vagy letiltható. Ha csak egy adott [régióban](https://azure.microsoft.com/regions)szeretné engedélyezni a AzureCloud való hozzáférést, megadhatja a régiót a következő formátumban: AzureCloud. [régió neve]. Ez a címke a kimenő biztonsági szabályokhoz ajánlott. 
* **AzureConnectors*** (csak Resource Manager esetén): Ez a címke a mintavételi/háttér-kapcsolatok Logic Apps összekötők címeinek előtagjait jelöli. Ha az *AzureConnectors* értéket adja meg, az AzureConnectors szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható. Ha csak egy adott [régióban](https://azure.microsoft.com/regions) szeretné engedélyezni a hozzáférést az AzureConnectors szolgáltatáshoz, a régiót az AzureConnectors.[régió neve] formátumban adhatja meg. Ez a címke a bejövő biztonsági szabályhoz ajánlott. 
* **AzureContainerRegistry*** (csak Resource Manager esetén): Ez a címke a Azure Container Registry szolgáltatáshoz tartozó címek előtagjait jelöli. Ha az *AzureContainerRegistry* értéket adja meg, az AzureContainerRegistry felé irányuló forgalom engedélyezhető vagy letiltható. Ha csak egy adott [régióban](https://azure.microsoft.com/regions) szeretné engedélyezni a hozzáférést az AzureContainerRegistry szolgáltatáshoz, a régiót az AzureConainerRegistry.[régió neve] formátumban adhatja meg. Ez a címke a kimenő biztonsági szabályokhoz ajánlott. 
* **AzureCosmosDB*** (csak Resource Manager esetén): Ez a címke az Azure Cosmos Database szolgáltatáshoz tartozó címek előtagjait jelöli. Ha az *AzureCosmosDB* értéket adja meg, az AzureCosmosDB szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható. Ha csak egy adott [régióban](https://azure.microsoft.com/regions) szeretné engedélyezni a hozzáférést az AzureCosmosDB szolgáltatáshoz, a régiót az AzureCosmosDB.[régió neve] formátumban adhatja meg. Ez a címke a kimenő biztonsági szabályokhoz ajánlott. 
* **AzureDataLake*** (csak Resource Manager esetén): Ez a címke a Azure Data Lake szolgáltatáshoz tartozó címek előtagjait jelöli. Ha az *AzureDataLake* értéket adja meg, az AzureDataLake szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható. Ez a címke a kimenő biztonsági szabályokhoz ajánlott. 
* **AzureKeyVault*** (csak Resource Manager esetén): Ez a címke az Azure kulcstartó szolgáltatás előtagjait jelöli. Ha az *AzureKeyVault* értéket adja meg, az AzureKeyVault szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható. Ha csak egy adott [régióban](https://azure.microsoft.com/regions) szeretné engedélyezni a hozzáférést az AzureKeyVault szolgáltatáshoz, a régiót az AzureKeyVault.[régió neve] formátumban adhatja meg. Ez a címke függőséget tartalmaz a **AzureActiveDirectory** címkével. Ez a címke a kimenő biztonsági szabályokhoz ajánlott.  
* **AzureLoadBalancer** (Resource Manager) (Klasszikus**AZURE_LOADBALANCER** ): Ez a címke az Azure infrastruktúra-terheléselosztó eszközét jelöli. A címkét a rendszer lefordítja a [gazdagép azon virtuális IP-címére](security-overview.md#azure-platform-considerations) (168.63.129.16), ahonnan az Azure állapot-mintavételei származnak. Ha nem az Azure Load Balancert használja, ezt a szabályt felül lehet bírálni.
* **AzureMachineLearning*** (csak Resource Manager esetén): Ez a címke a AzureMachineLearning szolgáltatás előtagjait jelöli. Ha az értékhez *AzureMachineLearning* ad meg, a forgalom a AzureMachineLearning számára engedélyezett vagy megtagadható. Ez a címke a kimenő biztonsági szabályokhoz ajánlott. 
* **AzureMonitor*** (csak Resource Manager esetén): Ez a címke a Log Analytics, az alkalmazás-felismerések, a AzMon és az egyéni metrikák (koncert-végpontok) előtagjait jelöli. Ha az értékhez *AzureMonitor* ad meg, a forgalom a AzureMonitor számára engedélyezett vagy megtagadható. Log Analytics esetén ez a címke függőséget tartalmaz a **tárolási** címkével. Ez a címke a kimenő biztonsági szabályokhoz ajánlott.
* **AzurePlatformDNS** (Csak Resource Manager esetén): Ez a címke a DNS-t jelöli, amely egy alapszintű infrastruktúra-szolgáltatás. Ha az értékhez *AzurePlatformDNS* ad meg, letilthatja a DNS alapértelmezett [Azure platformra](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) vonatkozó megfontolását. Kérjük, vegye figyelembe, hogy ezt a címkét használja. A címke használata előtt javasolt a tesztelés. 
* **AzurePlatformIMDS** (Csak Resource Manager esetén): Ez a címke azt az IMDS jelöli, amely egy alapszintű infrastruktúra-szolgáltatás. Ha az értékhez *AzurePlatformIMDS* ad meg, letilthatja a IMDS alapértelmezett [Azure platformra](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) vonatkozó megfontolását. Kérjük, vegye figyelembe, hogy ezt a címkét használja. A címke használata előtt javasolt a tesztelés. 
* **AzurePlatformLKM** (Csak Resource Manager esetén): Ez a címke a Windows licencelési vagy kulcskezelő szolgáltatást jelöli. Ha az értékhez *AzurePlatformLKM* ad meg, letilthatja az alapértelmezett [Azure platformot](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) a licenceléshez. Kérjük, vegye figyelembe, hogy ezt a címkét használja. A címke használata előtt javasolt a tesztelés. 
* **AzureTrafficManager*** (csak Resource Manager esetén): Ez a címke az Azure Traffic Manager mintavételi IP-címeinek IP-címét jelöli. További információk a Traffic Manager mintavételezési IP-címeiről: [Azure Traffic Manager – Gyakori kérdések](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). Ez a címke a bejövő biztonsági szabályhoz ajánlott.  
* **BatchNodeManagement*** (csak Resource Manager esetén): Ez a címke a Azure Batch dedikált központi telepítések felügyeleti forgalmának előtagjait jelöli. Ha az értékhez *BatchNodeManagement* ad meg, a rendszer a Batch szolgáltatásból a számítási csomópontok számára engedélyezi vagy letiltja a forgalmat. Ez a címke a bejövő/kimenő biztonsági szabályhoz ajánlott. 
* **CognitiveServicesManagement** (Csak Resource Manager esetén): Ez a címke a Cognitive Services adatforgalmának előtagjait jelöli. Ha az értékhez *CognitiveServicesManagement* ad meg, a forgalom a CognitiveServicesManagement számára engedélyezett vagy megtagadható. Ez a címke a kimenő biztonsági szabályokhoz ajánlott.  
* **Dynamics365ForMarketingEmail** (Csak Resource Manager esetén): Ez a címke a Dynamics 365 marketing e-mail-szolgáltatásának előtagjait jelöli. Ha az értékhez *Dynamics365ForMarketingEmail* ad meg, a forgalom a Dynamics365ForMarketingEmail számára engedélyezett vagy megtagadható. Ha csak egy adott [régióban](https://azure.microsoft.com/regions)szeretné engedélyezni a Dynamics365ForMarketingEmail való hozzáférést, megadhatja a régiót a következő formátumban: Dynamics365ForMarketingEmail. [régió neve].
* **EventHub*** (csak Resource Manager esetén): Ez a címke az Azure EventHub szolgáltatáshoz tartozó címek előtagjait jelöli. Ha az *EventHub* értéket adja meg, az EventHub szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható. Ha csak egy adott [régióban](https://azure.microsoft.com/regions) szeretné engedélyezni a hozzáférést az EventHub szolgáltatáshoz, a régiót az EventHub.[régió neve] formátumban adhatja meg. Ez a címke a kimenő biztonsági szabályokhoz ajánlott. 
* **GatewayManager** (Csak Resource Manager esetén): Ez a címke a VPN/app Gateway dedikált központi telepítések felügyeleti forgalmának előtagjait jelöli. Ha a *GatewayManager* értéket adja meg, a GatewayManager szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható. Ez a címke a bejövő biztonsági szabályhoz ajánlott. 
* **Internet** (Resource Manager) (Klasszikus**Internet** ): Ez a címke azt az IP-címtartományt jelöli, amely a virtuális hálózaton kívül esik, és a nyilvános interneten érhető el. A címtartományba beletartozik az [Azure tulajdonában lévő nyilvános IP-címtér](https://www.microsoft.com/download/details.aspx?id=41653) is.
* **MicrosoftContainerRegistry*** (csak Resource Manager esetén): Ez a címke a Microsoft Container Registry szolgáltatáshoz tartozó címek előtagjait jelöli. Ha a *MicrosoftContainerRegistry* értéket adja meg, a MicrosoftContainerRegistry felé irányuló forgalom engedélyezhető vagy letiltható. Ha csak egy adott [régióban](https://azure.microsoft.com/regions) szeretné engedélyezni a hozzáférést a MicrosoftContainerRegistry szolgáltatáshoz, a régiót a MicrosoftConainerRegistry.[régió neve] formátumban adhatja meg. Ez a címke a kimenő biztonsági szabályokhoz ajánlott. 
* **ServiceBus*** (csak Resource Manager esetén): Ez a címke a prémium szintű szolgáltatási szinttel jelzi az Azure ServiceBus szolgáltatás előtagjait. Ha a *ServiceBus* értéket adja meg, a ServiceBus szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható. Ha csak egy adott [régióban](https://azure.microsoft.com/regions) szeretné engedélyezni a hozzáférést a ServiceBus szolgáltatáshoz, a régiót a ServiceBus.[régió neve] formátumban adhatja meg. Ez a címke a kimenő biztonsági szabályokhoz ajánlott. 
* **ServiceFabric*** (csak Resource Manager esetén): Ez a címke a ServiceFabric szolgáltatás előtagjait jelöli. Ha az értékhez *ServiceFabric* ad meg, a forgalom a ServiceFabric számára engedélyezett vagy megtagadható. Ez a címke a kimenő biztonsági szabályokhoz ajánlott. 
* **SQL*** (csak Resource Manager esetén): Ez a címke a Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL és Azure SQL Data Warehouse szolgáltatások címeinek előtagjait jelöli. Ha az *SQL* értéket adja meg, az SQL szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható. Ha csak egy adott [régióban](https://azure.microsoft.com/regions)szeretné engedélyezni az SQL-hozzáférést, megadhatja a régiót a következő formátumban: SQL. [régió neve]. A címke a szolgáltatást jelöli, annak adott példányait azonban nem. Például a címke az Azure SQL Database szolgáltatást jelöli, de nem egy adott SQL-adatbázist vagy -kiszolgálót. Ez a címke a kimenő biztonsági szabályokhoz ajánlott. 
* **SqlManagement*** (csak Resource Manager esetén): Ez a címke az SQL dedikált üzemelő példányok felügyeleti forgalmának előtagjait jelöli. Ha az értékhez *SqlManagement* ad meg, a forgalom a SqlManagement számára engedélyezett vagy megtagadható. Ez a címke a bejövő/kimenő biztonsági szabályhoz ajánlott. 
* **Tároló*** (csak Resource Manager esetén): Ez a címke az Azure Storage szolgáltatás IP-címének területét jelöli. Ha a *Storage* értéket adja meg, a Storage szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható. Ha csak egy adott [régióban](https://azure.microsoft.com/regions)szeretné engedélyezni a tárterület elérését, megadhatja a régiót a következő formátumban: Storage. [régió neve]. A címke a szolgáltatást jelöli, annak adott példányait azonban nem. Például a címke az Azure Storage szolgáltatást jelöli, de nem egy adott Azure Storage-fiókot. Ez a címke a kimenő biztonsági szabályokhoz ajánlott. 
* **VirtualNetwork** (Resource Manager) (Klasszikus**VIRTUAL_NETWORK** ): Ez a címke tartalmazza a virtuális hálózati címtartomány (a virtuális hálózathoz meghatározott összes CIDR-tartomány), az összes csatlakoztatott helyszíni címterület, a virtuális [](virtual-network-peering-overview.md) [hálózati átjáróhoz](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json) és a virtuális hálózathoz csatlakoztatott virtuális hálózatokat. a [felhasználó által megadott útvonalakon](virtual-networks-udr-overview.md)használt előtagok. Vegye figyelembe, hogy ez a címke az alapértelmezett útvonalat is tartalmazhatja. 

> [!NOTE]
> Az Azure-szolgáltatások szolgáltatási címkéi a használt felhőből származó előtagokat jelzik. 

> [!NOTE]
> Ha egy [virtuális hálózati szolgáltatásvégpontot](virtual-network-service-endpoints-overview.md) implementál egy szolgáltatáshoz, például az Azure Storage-hoz vagy az Azure SQL Database-hez, az Azure hozzáad egy [útvonalat](virtual-networks-udr-overview.md#optional-default-routes) a szolgáltatás virtuális hálózatának alhálózatához. Az útvonal címelőtagjai ugyanazok a címelőtagok vagy CIDR-tartományok lesznek, mint a megfelelő szolgáltatáscímke.

### <a name="service-tags-in-on-premises"></a>Szolgáltatások címkéi a helyszínen  
Egy helyszíni tűzfallal letöltheti és integrálhatja a szolgáltatáshoz tartozó címkék listáját a következő heti kiadványokban az Azure [Public](https://www.microsoft.com/download/details.aspx?id=56519), az [Egyesült Államok kormánya](https://www.microsoft.com/download/details.aspx?id=57063), a [Kína](https://www.microsoft.com/download/details.aspx?id=57062)és a [németországi](https://www.microsoft.com/download/details.aspx?id=57064) felhők számára.

Ezeket az adatokat programozott módon is lekérheti a **Service tag Discovery API** (nyilvános előzetes verzió) – [Rest](https://aka.ms/discoveryapi_rest), [Azure PowerShell](https://aka.ms/discoveryapi_powershell)és az [Azure CLI](https://aka.ms/discoveryapi_cli)használatával. 

> [!NOTE]
> A következő heti kiadványok (régi verzió) az Azure [nyilvános](https://www.microsoft.com/en-us/download/details.aspx?id=41653), [kínai](https://www.microsoft.com/en-us/download/details.aspx?id=42064)és [németországi](https://www.microsoft.com/en-us/download/details.aspx?id=54770) felhők esetében 2020. június 30-ig elavulttá válik. Kezdje el használni a frissített kiadványokat a fent leírtak szerint. 

## <a name="default-security-rules"></a>Alapértelmezett biztonsági szabályok

Az Azure a következő alapértelmezett szabályokat hozza létre a létrehozott hálózati biztonsági csoportokban:

### <a name="inbound"></a>Bejövő

#### <a name="allowvnetinbound"></a>AllowVNetInBound

|Priority|Source|Forrásportok|Cél|Célportok|Protocol|Access|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|Any|Allow|

#### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Priority|Source|Forrásportok|Cél|Célportok|Protocol|Access|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|Any|Allow|

#### <a name="denyallinbound"></a>DenyAllInbound

|Priority|Source|Forrásportok|Cél|Célportok|Protocol|Access|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|Any|Megtagadás|

### <a name="outbound"></a>Kimenő

#### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Priority|Source|Forrásportok| Cél | Célportok | Protocol | Access |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | Any | Allow |

#### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|Priority|Source|Forrásportok| Cél | Célportok | Protocol | Access |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | Any | Allow |

#### <a name="denyalloutbound"></a>DenyAllOutBound

|Priority|Source|Forrásportok| Cél | Célportok | Protocol | Access |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | Any | Megtagadás |

A **Forrás** és a **Cél** oszlopban a *VirtualNetwork*, *AzureLoadBalancer* és *Internet* értékek [szolgáltatáscímkék](#service-tags), nem IP-címek. A protokoll oszlopban **minden** a TCP, UDP és ICMP protokollt foglalja magában. Szabály létrehozásakor megadhatja a TCP, UDP, ICMP vagy bármelyik lehetőséget. A *0.0.0.0/0* érték a **Forrás** és a **Cél** oszlopban az összes címet jelöli. Az olyan ügyfelek, mint a Azure Portal, az Azure CLI vagy a PowerShell a * vagy bármelyiket használhatja ehhez a kifejezéshez.
 
Az alapértelmezett szabályok nem távolíthatók el, azonban magasabb prioritású szabályok létrehozásával felülírhatók.

## <a name="application-security-groups"></a>Alkalmazásbiztonsági csoportok

Az alkalmazásbiztonsági csoportokkal az alkalmazás struktúrájának természetes bővítményeként konfigurálhatja a hálózati biztonságot, így csoportosíthatja a virtuális gépeket, és ezen csoportok alapján meghatározhatja a hálózati biztonsági szabályokat. A biztonsági szabályokat újrahasznosíthatja nagy léptékben is a konkrét IP-címek manuális karbantartása nélkül. A platform képes kezelni a konkrét IP-címek és a szabálykészletek jelentette összetettséget, így Ön az üzleti logikára összpontosíthat. Az alkalmazásbiztonsági csoportok könnyebb megértése érdekében tekintsük a következő példát:

![Alkalmazásbiztonsági csoportok](./media/security-groups/application-security-groups.png)

Az előző képen az *NIC1* és az *NIC2* az *AsgWeb* alkalmazásbiztonsági csoport tagjai. Az *NIC3* az *AsgLogic* alkalmazásbiztonsági csoport tagja. Az *NIC4* az *AsgDb* alkalmazásbiztonsági csoport tagja. Bár ebben a példában mindegyik hálózati adapter egyetlen alkalmazásbiztonsági csoport tagja, egy hálózati adapter akár több alkalmazásbiztonsági csoportnak is tagja lehet, az [Azure korlátainak](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) megfelelően. Egyik hálózati adapterhez sincs hálózati biztonsági csoport társítva. Az *NSG1* mindkét alhálózathoz társítva van, és a következő szabályokat tartalmazza:

### <a name="allow-http-inbound-internet"></a>Allow-HTTP-Inbound-Internet

Ez a szabály az internetről a webkiszolgálókra irányuló forgalom engedélyezéséhez szükséges. Mivel az internetről bejövő forgalmat az alapértelmezett [DenyAllInbound](#denyallinbound) biztonsági szabály tiltja, az *AsgLogic* és az *AsgDb* alkalmazásbiztonsági csoportok esetében nincs szükség további szabályokra.

|Priority|Source|Forrásportok| Cél | Célportok | Protocol | Access |
|---|---|---|---|---|---|---|
| 100 | Internet | * | AsgWeb | 80 | TCP | Allow |

### <a name="deny-database-all"></a>Deny-Database-All

Mivel az alapértelmezett [AllowVNetInBound](#allowvnetinbound) biztonsági szabály az azonos virtuális hálózaton lévő erőforrások között minden kommunikációt engedélyez, ez a szabály az összes erőforrástól érkező forgalom tiltásához szükséges.

|Priority|Source|Forrásportok| Cél | Célportok | Protocol | Access |
|---|---|---|---|---|---|---|
| 120 | * | * | AsgDb | 1433 | Any | Megtagadás |

### <a name="allow-database-businesslogic"></a>Allow-Database-BusinessLogic

Ez a szabály engedélyezi az *AsgLogic* alkalmazásbiztonsági csoportról az *AsgDb* alkalmazásbiztonsági csoportra irányuló forgalmat. A szabály prioritása magasabb a *Deny-Database-All* szabály prioritásánál. Ennek eredményeként ez a szabály a *Deny-Database-All* szabály előtt lesz kiértékelve, ezért az *AsgLogic* alkalmazásbiztonsági csoporttól érkező forgalom engedélyezve lesz, az összes többi forgalom pedig le lesz tiltva.

|Priority|Source|Forrásportok| Cél | Célportok | Protocol | Access |
|---|---|---|---|---|---|---|
| 110 | AsgLogic | * | AsgDb | 1433 | TCP | Allow |

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

- **VM1**: A rendszer feldolgozza a *NSG1* biztonsági szabályait, mivel a *Subnet1* és a *VM1* -hez van társítva a *Subnet1*-ben. Ha nem hozott létre egy szabályt, amely engedi a 80-as port bejövő forgalmát, a forgalmat az alapértelmezett [DenyAllInbound](#denyallinbound) biztonsági szabály letiltja, és azt az *NSG2* soha nem értékeli ki, mivel az *NSG2* a hálózati adapterhez van rendelve. Ha az *NSG1* rendelkezik olyan biztonsági szabállyal, amely engedélyezi a forgalmat a 80-as porton, a forgalmat ezután az *NSG2* feldolgozza. Ahhoz, hogy a 80-as porton engedélyezve legyen a virtuális gépre irányuló forgalom, az *NSG1* és az *NSG2* csoportnak egyaránt rendelkeznie kell olyan szabállyal, amely engedi az internetről beérkező forgalmat a 80-as porton.
- **VM2**: A rendszer a *NSG1* szabályait dolgozza fel, mivel a *VM2* a *Subnet1*is része. Mivel a *VM2* hálózati adapteréhez nincs hálózati biztonsági csoport rendelve, erre a gépre az *NSG1* által átengedett minden forgalom megérkezik, és az *NSG1* által letiltott minden forgalom blokkolva lesz. Ha egy alhálózathoz egy hálózati biztonsági csoport van rendelve, az adott alhálózaton az összes erőforrás számára le lesz tiltva vagy engedélyezve lesz a forgalom.
- **VM3**: Mivel a *subnet2 alhálózattal*nem tartozik hálózati biztonsági csoport, a forgalom az alhálózatba kerül, és a *NSG2*dolgozza fel, mivel a *NSG2* a *VM3*csatolt hálózati adapterhez van társítva.
- **VM4**: A forgalom *VM4,* mert egy hálózati biztonsági csoport nincs társítva a *Subnet3*vagy a virtuális gép hálózati adapteréhez. Ha egy alhálózathoz vagy hálózati adapterhez nincs hálózati biztonsági csoport rendelve, ezeken a teljes hálózati forgalom engedélyezve lesz.

### <a name="outbound-traffic"></a>Kimenő forgalom

A kimenő forgalom esetében az Azure először a hálózati biztonsági csoport hálózati adapterhez rendelt szabályait dolgozza fel (ha van hálózati adapter), majd a hálózati biztonsági csoport alhálózathoz rendelt szabályait (ha van alhálózat).

- **VM1**: A rendszer feldolgozza a *NSG2* biztonsági szabályait. Ha nem hoz létre egy biztonsági szabályt, amely letiltja a 80-as port internetre irányuló kimenő forgalmát, a forgalmat az alapértelmezett [AllowInternetOutbound](#allowinternetoutbound) biztonsági szabály az *NSG1* és az *NSG2* csoporton egyaránt engedélyezi. Ha az *NSG2* rendelkezik olyan biztonsági szabállyal, amely letiltja a forgalmat a 80-as porton, a forgalom le lesz tiltva, és az *NSG1* soha nem értékeli ki. Ahhoz, hogy a 80-as porton le legyen tiltva a virtuális gépről érkező forgalom, legalább az egyik hálózati biztonsági csoportnak rendelkeznie kell olyan szabállyal, amely letiltja az internetre irányuló kimenő forgalmat a 80-as porton.
- **VM2**: A rendszer minden forgalmat a hálózati adapteren keresztül továbbít az alhálózathoz, mivel a *VM2* csatolt hálózati adapterhez nincs hozzárendelve hálózati biztonsági csoport. Az *NSG1* szabályai fel lesznek dolgozva.
- **VM3**: Ha a *NSG2* olyan biztonsági szabállyal rendelkezik, amely megtagadja a 80-es portot, a rendszer megtagadja a forgalmat. Ha az *NSG2* rendelkezik olyan biztonsági szabállyal, amely engedélyezi a forgalmat a 80-as porton, a 80-as porton engedélyezve lesz az internetre irányuló kimenő forgalom, mivel a *Subnet2* hálózathoz nincs hálózati biztonsági csoport rendelve.
- **VM4**: Minden hálózati forgalom engedélyezett a *VM4,* mert a hálózati biztonsági csoport nincs társítva a virtuális géphez csatolt hálózati adapterhez vagy a *Subnet3*.

A hálózati adapterekhez rendelt összesített szabályokat könnyen megismerheti a hálózati adapter [érvényes biztonsági szabályainak](virtual-network-network-interface.md#view-effective-security-rules) megtekintésével. Emellett az Azure Network Watcher [IP-forgalom ellenőrzése](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szolgáltatásával is megállapíthatja, hogy a kommunikáció engedélyezett-e a hálózati adapterre/adapterről. Az IP-forgalom ellenőrzése megmutatja, hogy a kommunikáció engedélyezve vagy blokkolva van-e, valamint hogy melyik hálózati biztonsági szabály engedélyezi vagy blokkolja a forgalmat.

> [!NOTE]
> A hálózati biztonsági csoportok az alhálózatokhoz vagy a klasszikus üzemi modellben üzembe helyezett virtuális gépekhez és felhőalapú szolgáltatásokhoz, valamint a Resource Manager-alapú üzemi modellben lévő alhálózatokhoz vagy hálózati adapterekhez vannak társítva. Az Azure üzembehelyezési modellekkel kapcsolatos további információkért lásd: [Az Azure üzemi modelljeinek megismerése](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

> [!TIP]
> Hacsak nincs erre kifejezett oka, javasoljuk, hogy az egyes hálózati biztonsági csoportokat egy alhálózathoz vagy egy hálózati adapterhez rendelje, mindkettőhöz ne. Mivel az alhálózathoz rendelt hálózati biztonsági csoport szabályai ütközhetnek a hálózati adapterhez rendelt hálózati biztonsági csoport szabályaival, nem várt kommunikációs problémák merülhetnek fel, amelyek hibaelhárítást igényelnek.

## <a name="azure-platform-considerations"></a>Tudnivalók az Azure platformhoz

- **A gazdagép csomópontjának virtuális IP-címe**: Az olyan alapszintű infrastruktúra-szolgáltatások, mint a DHCP, a DNS, a IMDS és az állapot-figyelés a virtualizált gazdagép IP-címeinek 168.63.129.16 és 169.254.169.254 keresztül érhetők el. Ezek az IP-címek a Microsofthoz tartoznak, és az összes régióban használt virtualizált IP-címek erre a célra szolgálnak.
- **Licencelés (kulcskezelő szolgáltatás)** : A virtuális gépeken futó Windows-rendszerképeknek licenccel kell rendelkezniük. A licenceléshez el kell küldeni egy licencelési kérelmet a Kulcskezelő szolgáltatás ilyen kérelmeket kezelő kiszolgálóinak. A kérelmet az 1688-as kimenő porton küldi el a rendszer. Az [alapértelmezett 0.0.0.0/0 útvonalat](virtual-networks-udr-overview.md#default-route) használó konfigurációkban ez a platformszabály le van tiltva.
- **Elosztott terhelésű készletekben lévő virtuális gépek**: Az alkalmazott forrásport és címtartomány az eredeti számítógépről, nem pedig a terheléselosztóból származnak. A célport és a címtartomány nem a terheléselosztóhoz, hanem a célszámítógéphez tartozik.
- **Azure-szolgáltatás példányai**: Számos Azure-szolgáltatás, például a HDInsight, az Application Service-környezet és a Virtual Machine Scale Sets példányai virtuális hálózati alhálózatokban vannak telepítve. A virtuális hálózatokon üzembe helyezhető szolgáltatások teljes listájához lásd a [Virtuális hálózatok az Azure-szolgáltatásokhoz](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) című témakört. Mindenképp ismerkedjen meg behatóan az egyes szolgáltatások portkövetelményeivel, mielőtt egy hálózati biztonsági csoportot alkalmazna az erőforrást üzemeltető alhálózatra. Ha a valamely szolgáltatás által használt portokat letiltja, a szolgáltatás nem megfelelően fog működni.
- **Kimenő e-mailek küldése**: A Microsoft azt javasolja, hogy a hitelesített SMTP-továbbító szolgáltatásokat használja (általában az 587-as TCP-porton keresztül, de gyakran másokat is) az Azure Virtual Machines-ból küldött e-mailek küldéséhez. Az SMTP-továbbítási szolgáltatások a feladói jellegzetességek biztosítására szakosodtak, így minimalizálják annak lehetőségét, hogy a külső e-mail-szolgáltatók visszautasítsák az üzeneteket. Ilyen SMTP-továbbítási szolgáltatás például, a teljesség igénye nélkül, az Exchange Online Protection és a SendGrid. Az SMTP-továbbítási szolgáltatások használata nincsen korlátozva az Azure-ban, függetlenül attól, hogy milyen előfizetése van. 

  Amennyiben 2017. november 15. előtt hozta létre Azure-előfizetését, az SMTP-továbbítási szolgáltatások használata mellett közvetlenül a 25-ös TCP-porton keresztül is küldhet e-maileket. Amennyiben 2017. november 15. után fizetett elő, nem biztos hogy küldhet e-maileket közvetlenül a 25-ös porton keresztül. A 25-ös porton keresztül folytatott kimenő kommunikáció viselkedése az előfizetés típusától függ, amely lehet:

     - **Nagyvállalati szerződés**: A 25-ös porton keresztüli kimenő kommunikáció engedélyezett. Közvetlenül a virtuális gépekről küldhet kimenő e-maileket a külső e-mail-szolgáltatóknak, és az Azure platform korlátozásai nem érvényesülnek. 
     - **Utólagos elszámolás:** A 25-ös porton keresztüli kimenő kommunikáció minden erőforrás esetében le van tiltva. Ha közvetlenül a virtuális gépéről szeretne e-mailt küldenie egy külső e-mail-szolgáltatónak (hitelesített SMTP-továbbítás használata nélkül), kérheti a korlátozás feloldását. A kérelmeket a Microsoft saját belátása szerint értékeli és hagyja jóvá, a visszaélések kiküszöbölésére szolgáló megfelelő ellenőrzések elvégzése után. Kérelem benyújtásához támogatási esetet kell nyitnia a *Technikai*, *Virtuális hálózati kapcsolat*, *Sikertelen e-mail-küldés (SMTP/25-ös port)* problématípus kiválasztásával. A támogatási esetben részletesen indokolja, hogy előfizetésének miért kell közvetlenül a levelezési szolgáltatónak e-mailt küldenie a hitelesített SMTP-továbbítás használata helyett. Amennyiben előfizetését felmentik a korlátozás alól, csak a mentesítés dátuma után létrehozott virtuális gépek képesek a 25-ös porton keresztüli kimenő kommunikációra.
     - **MSDN, Azure Pass, Azure in Open licencprogram, Education, BizSpark és ingyenes próbaverzió**: A 25-ös porton keresztüli kimenő kommunikáció minden erőforrás esetében le van tiltva. Nem küldhető kérelem a korlátozás feloldására, mert a kérelmek nem teljesíthetők. Ha mindenképp szeretne e-mailt küldeni a virtuális gépről, használjon SMTP-továbbítási szolgáltatást.
     - **Felhőalapú szolgáltató**: Azok az ügyfelek, akik egy felhőalapú szolgáltatón keresztül használják az Azure-erőforrásokat, létrehozhatnak egy támogatási esetet a felhőalapú szolgáltatóval, és kérhetik, hogy a szolgáltató hozzon létre egy feloldási esetet az nevében, ha nem használható biztonságos SMTP-továbbító.

  Amennyiben az Azure engedélyezi az e-mailek küldését a 25-ös porton keresztül, a Microsoft nem tudja garantálni, hogy a levelező szolgáltatók elfogadják a virtuális gépről érkező bejövő e-maileket. Ha egy szolgáltató elutasítja az Ön virtuális gépéről érkező leveleket, vele együttműködésben oldja meg az üzenetküldési vagy levélszemétszűrési problémákat, vagy használjon hitelesített SMTP-továbbítási szolgáltatást.

## <a name="next-steps"></a>További lépések

* Ismerje meg [a hálózati biztonsági csoportok létrehozását](tutorial-filter-network-traffic.md).
