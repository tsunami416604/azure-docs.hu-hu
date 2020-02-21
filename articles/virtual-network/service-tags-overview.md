---
title: Azure-szolgáltatás címkék – áttekintés
titlesuffix: Azure Virtual Network
description: További információ a szolgáltatási címkékről. A szolgáltatás-címkék segítségével csökkentheti a biztonsági szabályok létrehozásának bonyolultságát.
services: virtual-network
documentationcenter: na
author: jispar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2019
ms.author: jispar
ms.reviewer: kumud
ms.openlocfilehash: 47488403a10a6b955e15b0048d455cc17016f009
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526246"
---
# <a name="virtual-network-service-tags"></a>Virtuális hálózati szolgáltatás címkéi
<a name="network-service-tags"></a>

A szolgáltatás címkéje egy adott Azure-szolgáltatás IP-címeinek egy csoportját jelöli. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával, minimalizálva a hálózati biztonsági szabályok gyakori frissítéseinek összetettségét.

A szolgáltatási címkék használatával hálózati hozzáférés-vezérlést határozhat meg a [hálózati biztonsági csoportokon](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) vagy [Azure Firewall](https://docs.microsoft.com/azure/firewall/service-tags). A szolgáltatási címkéket adott IP-címek helyett használhatja biztonsági szabályok létrehozásakor. Ha a szolgáltatási címke nevét (például **ApiManagement**) adja meg egy szabály megfelelő *forrás* vagy *cél* mezőjében, engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát.

A szolgáltatás-címkék használatával hálózati elkülönítést érhet el, és biztosíthatja az Azure-erőforrásokat az általános internetről, miközben nyilvános végpontokkal rendelkező Azure-szolgáltatásokhoz fér hozzá. Bejövő/kimenő hálózati biztonsági csoportokra vonatkozó szabályok létrehozása az **internetre** irányuló adatforgalom megtagadásához, valamint az adott Azure-szolgáltatások **AzureCloud** vagy más [elérhető szolgáltatás-címkévé](#available-service-tags) tételének engedélyezése.

## <a name="available-service-tags"></a>Elérhető szolgáltatás címkéi
A következő táblázat tartalmazza a [hálózati biztonsági csoport](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) szabályaiban használható összes szolgáltatás címkéjét.

Az oszlopok azt jelzik, hogy a címke:

- Megfelelő a bejövő vagy kimenő forgalmat lefedő szabályokhoz.
- A [regionális](https://azure.microsoft.com/regions) hatókört támogatja.
- [Azure Firewall](https://docs.microsoft.com/azure/firewall/service-tags) -szabályokban használható.

Alapértelmezés szerint a szolgáltatás címkéi a teljes felhő tartományait tükrözik. Egyes szolgáltatási címkék a megfelelő IP-tartományok adott régióra való korlátozásával is részletesebb szabályozást tesznek lehetővé. A szolgáltatás címkéje például az **Azure Storage-** t jelöli a teljes felhőhöz, de a **Storage. WestUS** leszűkíti a tartományt úgy, hogy csak a WestUS régióból származó tárolási IP-címekre legyenek érvényesek. Az alábbi táblázat azt jelzi, hogy az egyes szolgáltatási címkék támogatják-e az ilyen regionális hatókört.  

| Címke | Cél | Használhat bejövő vagy kimenő adatforgalmat? | Lehet regionális? | Használható a Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ApiManagement** | Felügyeleti forgalom az Azure API Managementhoz – dedikált központi telepítések. <br/><br/>*Megjegyzés:* Ez a címke az Azure API Management szolgáltatási végpontját jelöli a vezérlési sík régiónként. Ez lehetővé teszi, hogy az ügyfelek felügyeleti műveleteket hajtsanak végre a API Management szolgáltatáson konfigurált API-kkal, műveletekkel, házirendekkel és NamedValues.  | Bejövő | Igen | Igen |
| **ApplicationInsightsAvailability** | Application Insights rendelkezésre állás. | Bejövő | Nem | Nem |
| **AppService**    | Azure App Service. A rendszer ezt a címkét a webalkalmazások előtéri kimenő biztonsági szabályaihoz ajánlja. | Kimenő | Igen | Igen |
| **AppServiceManagement** | Felügyeleti forgalom a App Service Environment számára dedikált központi telepítések esetén. | Mindkettő | Nem | Igen |
| **AzureActiveDirectory** | Azure Active Directory. | Kimenő | Nem | Igen |
| **AzureActiveDirectoryDomainServices** | Felügyeleti forgalom a Azure Active Directory Domain Services számára dedikált központi telepítések esetén. | Mindkettő | Nem | Igen |
| **AzureAdvancedThreatProtection** | Az Azure komplex veszélyforrások elleni védelem. | Kimenő | Nem | Nem |
| **AzureBackup** |Azure Backup.<br/><br/>*Megjegyzés:* Ez a címke függőséget tartalmaz a **Storage** és a **AzureActiveDirectory** címkével. | Kimenő | Nem | Igen |
| **AzureBotService** | Azure Bot Service. | Kimenő | Nem | Nem |
| **AzureCloud** | Minden [adatközpont nyilvános IP-címe](https://www.microsoft.com/download/details.aspx?id=56519). | Kimenő | Igen | Igen |
| **AzureCognitiveSearch** | Azure Cognitive Search. <br/><br/>Ez a címke vagy a címke által jelzett IP-címek segítségével biztosítható, hogy az indexelő biztonságos hozzáférést biztosítson az adatforrásokhoz. További részletekért tekintse meg az [Indexelő kapcsolati dokumentációját](https://docs.microsoft.com/azure/search/search-indexer-troubleshooting#connection-errors) . | Bejövő | Nem | Nem |
| **AzureConnectors** | Azure Logic Apps összekötők a mintavétel/háttér kapcsolatokhoz. | Bejövő | Igen | Igen |
| **AzureContainerRegistry** | Azure Container Registry. | Kimenő | Igen | Igen |
| **AzureCosmosDB** | Azure Cosmos DB. | Kimenő | Igen | Igen |
| **AzureDatabricks** | Azure Databricks. | Mindkettő | Nem | Nem |
| **AzureDataExplorerManagement** | Azure Adatkezelő-felügyelet. | Bejövő | Nem | Nem |
| **AzureDataLake** | Azure Data Lake. | Kimenő | Nem | Igen |
| **AzureEventGrid** | Azure Event Grid. <br/><br/>*Megjegyzés:* Ez a címke az USA déli középső régiójában, az USA keleti régiójában, az USA keleti régiójában, az USA 2. nyugati régiójában és az USA középső régiójában található Azure Event Grid | Mindkettő | Nem | Nem |
| **AzureFrontDoor** | Azure bejárati ajtó. | Mindkettő | Nem | Nem |
| **AzureInformationProtection** | Azure Information Protection.<br/><br/>*Megjegyzés:* Ez a címke függőséget tartalmaz a **AzureActiveDirectory** és a **AzureFrontDoor. frontend** címkével. A következő IP-címeket is meg kell adni (ez a függőség hamarosan el lesz távolítva): 13.107.6.181 & 13.107.9.181. | Kimenő | Nem | Nem |
| **AzureIoTHub** | Azure IoT Hub. | Kimenő | Nem | Nem |
| **AzureKeyVault** | Azure Key Vault.<br/><br/>*Megjegyzés:* Ez a címke függőséget tartalmaz a **AzureActiveDirectory** címkével. | Kimenő | Igen | Igen |
| **AzureLoadBalancer** | Az Azure-infrastruktúra Load Balancer. A címke a gazdagép (168.63.129.16) [virtuális IP-címére van](security-overview.md#azure-platform-considerations) lefordítva, ahol az Azure Health-Szondák származnak. Ez nem tartalmazza a Azure Load Balancer-erőforrás felé irányuló forgalmat. Ha nem Azure Load Balancer használ, felülbírálhatja ezt a szabályt. | Mindkettő | Nem | Nem |
| **AzureMachineLearning** | Azure Machine Learning. | Mindkettő | Nem | Igen |
| **AzureMonitor** | Log Analytics, Application Insights, AzMon és egyéni metrikák (GiG-végpontok).<br/><br/>*Megjegyzés:* Log Analytics esetén ez a címke függőséget tartalmaz a **tárolási** címkével. | Kimenő | Nem | Igen |
| **AzurePlatformDNS** | Az alapszintű infrastruktúra (alapértelmezett) DNS szolgáltatás.<br/><br>Ezt a címkét használhatja az alapértelmezett DNS letiltásához. Ha ezt a címkét használja, körültekintően járjon el. Javasoljuk, hogy olvassa el az [Azure platformmal kapcsolatos szempontokat](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations). Javasoljuk továbbá, hogy a címke használata előtt végezzen tesztelést. | Kimenő | Nem | Nem |
| **AzurePlatformIMDS** | Az Azure Instance Metadata Service (IMDS), amely egy alapszintű infrastruktúra-szolgáltatás.<br/><br/>Ezt a címkét használhatja az alapértelmezett IMDS letiltásához. Ha ezt a címkét használja, körültekintően járjon el. Javasoljuk, hogy olvassa el az [Azure platformmal kapcsolatos szempontokat](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations). Javasoljuk továbbá, hogy a címke használata előtt végezzen tesztelést. | Kimenő | Nem | Nem |
| **AzurePlatformLKM** | Windows licencelés vagy kulcskezelő szolgáltatás.<br/><br/>Ezt a címkét használhatja a licencelés alapértelmezett értékének letiltásához. Ha ezt a címkét használja, körültekintően járjon el. Javasoljuk, hogy olvassa el az [Azure platformmal kapcsolatos szempontokat](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations).  Javasoljuk továbbá, hogy a címke használata előtt végezzen tesztelést. | Kimenő | Nem | Nem |
| **AzureResourceManager** | Azure Resource Manager. | Kimenő | Nem | Nem |
| **AzureSiteRecovery** | Azure Site Recovery.<br/><br/>*Megjegyzés:* Ez a címke függőséget tartalmaz a **Storage**, a **AzureActiveDirectory**és a **EventHub** címkével. | Kimenő | Nem | Nem |
| **AzureTrafficManager** | Azure Traffic Manager mintavétel IP-címei.<br/><br/>Traffic Manager mintavétel IP-címeivel kapcsolatos további információkért lásd: [Azure Traffic Manager – gyakori kérdések](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). | Bejövő | Nem | Igen |  
| **BatchNodeManagement** | Felügyeleti forgalom a Azure Batch számára dedikált központi telepítések esetén. | Mindkettő | Nem | Igen |
| **CognitiveServicesManagement** | Az Azure Cognitive Services adatforgalmára vonatkozó címtartományok. | Kimenő | Nem | Nem |
| **Dynamics365ForMarketingEmail** | A Dynamics 365 marketing e-mail-szolgáltatásának címtartományt. | Kimenő | Igen | Nem |
| **ElasticAFD** | Rugalmas Azure bejárati ajtó. | Mindkettő | Nem | Nem |
| **EventHub** | Azure Event Hubs. | Kimenő | Igen | Igen |
| **GatewayManager** | Az Azure VPN Gateway és Application Gateway számára dedikált központi telepítések kezelési forgalma. | Bejövő | Nem | Nem |
| **GuestAndHybridManagement** | Azure Automation és vendég konfiguráció. | Kimenő | Nem | Igen |
| **HDInsight** | Azure-HDInsight. | Bejövő | Igen | Nem |
| **Internet** | Az IP-címtartomány, amely a virtuális hálózaton kívül esik, és a nyilvános interneten érhető el.<br/><br/>A címtartomány magában foglalja az [Azure tulajdonában lévő nyilvános IP-címterület területét](https://www.microsoft.com/download/details.aspx?id=41653). | Mindkettő | Nem | Nem |
| **MicrosoftCloudAppSecurity** | Microsoft Cloud App Security. | Kimenő | Nem | Nem |
| **MicrosoftContainerRegistry** | Microsoft-tároló lemezképek tároló-beállításjegyzéke. <br/><br/>*Megjegyzés:* Adja meg a következő IP-címet is (ez a függőség hamarosan el lesz távolítva): 204.79.197.219. | Kimenő | Igen | Igen |
| **ServiceBus** | Azure Service Bus a prémium szintű szolgáltatási szintet használó forgalom. | Kimenő | Igen | Igen |
| **ServiceFabric** | Azure Service Fabric.<br/><br/>*Megjegyzés:* Ez a címke a Service Fabric szolgáltatási végpontot jelöli a vezérlési sík régiónként. Ez lehetővé teszi, hogy az ügyfelek felügyeleti műveleteket hajtsanak végre a Service Fabric-fürtökhöz a VNET (például végpontot). https://westus.servicefabric.azure.com) | Mindkettő | Nem | Nem |
| **Sql** | Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL és Azure SQL Data Warehouse.<br/><br/>*Megjegyzés:* Ez a címke a szolgáltatást jelöli, de a szolgáltatás adott példányai nem. Például a címke az Azure SQL Database szolgáltatást jelöli, de nem egy adott SQL-adatbázist vagy -kiszolgálót. | Kimenő | Igen | Igen |
| **SqlManagement** | Felügyeleti forgalom az SQL-dedikált üzemelő példányokhoz. | Mindkettő | Nem | Igen |
| **Storage** | Azure Storage. <br/><br/>*Megjegyzés:* Ez a címke a szolgáltatást jelöli, de a szolgáltatás adott példányai nem. Például a címke az Azure Storage szolgáltatást jelöli, de nem egy adott Azure Storage-fiókot. | Kimenő | Igen | Igen |
| **VirtualNetwork** | A virtuális hálózati címtartomány (a virtuális hálózathoz meghatározott összes IP-címtartomány), az összes csatlakoztatott helyszíni [címterület, a virtuális hálózat](virtual-network-peering-overview.md) , a [virtuális hálózati átjáróhoz](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json)csatlakoztatott virtuális hálózatok, a [gazdagép virtuális IP-címe](security-overview.md#azure-platform-considerations), valamint a [felhasználó által megadott útvonalakon](virtual-networks-udr-overview.md)használt előtagok címei. Ez a címke tartalmazhatja az alapértelmezett útvonalakat is. | Mindkettő | Nem | Nem |

>[!NOTE]
>A klasszikus üzembe helyezési modellben (Azure Resource Manager előtt) az előző táblázatban felsorolt címkék egy részhalmaza támogatott. Ezek a címkék máshogy vannak írva:
>
>| Klasszikus helyesírás | Egyenértékű Resource Manager-címke |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| INTERNET | Internet |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Az Azure-szolgáltatások szolgáltatási címkéi a használt Felhőbeli előtagokat jelölik. Az Azure-beli nyilvános felhőben az **SQL** -címke értékének megfelelő mögöttes IP-címtartományok például eltérnek az Azure China Cloud-beli mögöttes tartománytól.

> [!NOTE]
> Ha egy [virtuális hálózati szolgáltatásvégpontot](virtual-network-service-endpoints-overview.md) implementál egy szolgáltatáshoz, például az Azure Storage-hoz vagy az Azure SQL Database-hez, az Azure hozzáad egy [útvonalat](virtual-networks-udr-overview.md#optional-default-routes) a szolgáltatás virtuális hálózatának alhálózatához. Az útvonalon található címek előtagjai ugyanazok a címek előtagjai vagy CIDR, mint a megfelelő szolgáltatási címke.

## <a name="service-tags-on-premises"></a>Szolgáltatási címkék a helyszínen  
A szolgáltatáshoz tartozó aktuális címke és tartomány információ beszerzésével a helyszíni tűzfal konfigurációjának részeként is felvehető. Ez az információ az egyes szolgáltatási címkéknek megfelelő IP-címtartományok aktuális időponthoz tartozó listája. Az információkat programozott módon vagy JSON-fájl letöltésével szerezheti be az alábbi szakaszokban leírtak szerint.

### <a name="use-the-service-tag-discovery-api-public-preview"></a>A Service tag Discovery API használata (nyilvános előzetes verzió)
Programozott módon lekérheti a szolgáltatási címkék aktuális listáját az IP-címtartomány részleteivel együtt:

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Azure CLI](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> Habár a nyilvános előzetes verzióban érhető el, a felderítési API a JSON-letöltések által visszaadott információknál kevésbé naprakész adatokat adhat vissza. (Lásd a következő szakaszt.)


### <a name="discover-service-tags-by-using-downloadable-json-files"></a>Szolgáltatási címkék felderítése a letölthető JSON-fájlokkal 
A szolgáltatáshoz tartozó címkék aktuális listáját tartalmazó JSON-fájlok az IP-címtartomány részleteivel együtt tölthetők le. Ezek a listák hetente frissülnek és jelennek meg. Az egyes felhő helyei a következők:

- [Nyilvános Azure](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure US Government](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure China](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Germany](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>Az adatok egy részhalmaza közzé lett téve az [Azure Public](https://www.microsoft.com/download/details.aspx?id=41653), az [Azure China](https://www.microsoft.com/download/details.aspx?id=42064)és az [Azure Germany](https://www.microsoft.com/download/details.aspx?id=54770)XML-fájljaiban. Az XML-letöltéseket a rendszer a 2020-es június 30-ig elavult, és a továbbiakban nem lesz elérhető. Az előző szakaszokban leírtaknak megfelelően telepítse át a programot a felderítési API vagy a JSON-fájl letöltésére.

### <a name="tips"></a>Tippek 
- Az egyik kiadvány frissítései a következőre észlelhetők a JSON-fájlban lévő, megnövekedett *changeNumber* értékek alapján. Az egyes alszakaszok (például a **Storage. WestUS**) saját *changeNumber* rendelkeznek, amely a változásoknak megfelelően növekszik. A fájl *changeNumber* legfelső szintje az alszakaszok bármelyikének megváltozásakor növekszik.
- A szolgáltatási címke adatainak elemzésére (például a WestUS-ben az összes címtartomány beszerzésére) vonatkozó példákat a [Service tag felderítési API PowerShell](https://aka.ms/discoveryapi_powershell) dokumentációjában találja.

## <a name="next-steps"></a>Következő lépések
- Megtudhatja, hogyan [hozhat létre hálózati biztonsági csoportot](tutorial-filter-network-traffic.md).
