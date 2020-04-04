---
title: Az Azure szolgáltatáscímkéi – áttekintés
titlesuffix: Azure Virtual Network
description: További információ a szolgáltatáscímkékről. A szolgáltatáscímkék segítenek minimalizálni a biztonsági szabályok létrehozásának összetettségét.
services: virtual-network
documentationcenter: na
author: jispar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/12/2020
ms.author: jispar
ms.reviewer: kumud
ms.openlocfilehash: 7da20a0bf87b33f05ea7f1d457157c5b7ee2ec7b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631517"
---
# <a name="virtual-network-service-tags"></a>Virtuális hálózati szolgáltatás címkéi
<a name="network-service-tags"></a>

A szolgáltatáscímke egy adott Azure-szolgáltatás IP-címelőtagjainak csoportját jelöli. A Microsoft kezeli a szolgáltatáscímke által felölelt címelőtagokat, és automatikusan frissíti a szolgáltatáscímkét a címek változásaként, minimalizálva a hálózati biztonsági szabályok gyakori frissítéseinek összetettségét.

A szolgáltatáscímkék segítségével hálózati hozzáférés-vezérlést határozhat meg [a hálózati biztonsági csoportokon](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) vagy az [Azure Firewall-en.](https://docs.microsoft.com/azure/firewall/service-tags) Biztonsági szabályok létrehozásakor a szolgáltatáscímkéket használjon adott IP-címek helyett. Ha megad egy szolgáltatáscímke nevét (például **ApiManagement)** a szabály megfelelő *forrás-* vagy *célmezőjében,* engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát.

A szolgáltatáscímkék segítségével hálózati elkülönítést érhet el, és megvédheti az Azure-erőforrásokat az általános internettől, miközben nyilvános végpontokkal rendelkező Azure-szolgáltatásokhoz fér hozzá. Hozzon létre bejövő/kimenő hálózati biztonsági csoportszabályokat az **internetre/az internetről** érkező forgalom megtagadásához, valamint az AzureCloudba vagy az **azure-szolgáltatások** más [elérhető szolgáltatáscímkéibe](#available-service-tags) irányuló/ onnan érkező forgalom engedélyezéséhez.

## <a name="available-service-tags"></a>Elérhető szolgáltatáscímkék
Az alábbi táblázat a hálózati biztonsági [csoport](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) szabályaiban használható összes szolgáltatáscímkét tartalmazza.

Az oszlopok azt jelzik, hogy a címke:

- Alkalmas olyan szabályokhoz, amelyek a bejövő vagy kimenő forgalmat fedik le.
- Támogatja a [regionális](https://azure.microsoft.com/regions) hatályt.
- Használható az [Azure](https://docs.microsoft.com/azure/firewall/service-tags) Firewall-szabályokban.

Alapértelmezés szerint a szolgáltatáscímkék a teljes felhő tartományait tükrözik. Egyes szolgáltatáscímkék részletesebb vezérlést is lehetővé tesznek azáltal, hogy a megfelelő IP-tartományokat egy adott régióra korlátozzák. Például a szolgáltatáscímke **Storage** az Azure Storage a teljes felhőben, de **Storage.WestUS** szűkíti a tartományt, hogy csak a tárolási IP-cím tartományok a WestUS régióban. Az alábbi táblázat azt mutatja be, hogy az egyes szolgáltatáscímkek támogatják-e az ilyen regionális hatókört.  

| Címke | Cél | Használhatja a bejövő vagy kimenő? | Lehet regionális? | Használhatja az Azure Tűzfal? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **Műveletcsoport** | Akciócsoport. | Bejövő | Nem | Nem |
| **ApiManagement** | Felügyeleti forgalom az Azure API Management által dedikált telepítések. <br/><br/>*Megjegyzés:* Ez a címke az Azure API Management szolgáltatás végpontját jelöli régiónként a vezérlősíkhoz. Ez lehetővé teszi az ügyfelek számára, hogy felügyeleti műveleteket hajtsanak végre az API Management szolgáltatásban konfigurált API-kon, műveleteken, házirendeken, namedvalues-en.  | Bejövő | Igen | Igen |
| **ApplicationInsightsElérhető** | Application Insights rendelkezésre állása. | Bejövő | Nem | Nem |
| **Alkalmazáskonfiguráció** | Alkalmazás konfigurációja. | Kimenő | Nem | Nem |
| **AppService**    | Azure App Service. Ez a címke ajánlott a kimenő biztonsági szabályok webapp előtér-végek. | Kimenő | Igen | Igen |
| **AppServiceManagement szolgáltatás** | Az App Service-környezetnek szánt központi telepítések felügyeleti forgalma. | Mindkettő | Nem | Igen |
| **AzureActiveDirectory** | Azure Active Directory. | Kimenő | Nem | Igen |
| **AzureActiveDirectoryDomainServices** | Az Azure Active Directory tartományi szolgáltatásoknak szánt központi telepítések felügyeleti forgalma. | Mindkettő | Nem | Igen |
| **AzureAdvancedThreatProtection** | Azure komplex veszélyforrások elleni védelem. | Kimenő | Nem | Nem |
| **AzureBackup** |Azure biztonsági mentés.<br/><br/>*Megjegyzés:* Ez a címke a **Storage** és az **AzureActiveDirectory-címkék** től függ. | Kimenő | Nem | Igen |
| **AzureBotService** | Az Azure Bot Szolgáltatás. | Kimenő | Nem | Nem |
| **AzureCloud** | Az összes [adatközpont nyilvános IP-címe](https://www.microsoft.com/download/details.aspx?id=56519). | Kimenő | Igen | Igen |
| **AzureCognitiveSearch** | Azure kognitív keresés. <br/><br/>Ez a címke vagy a címke által lefedett IP-címek segítségével biztonságos hozzáférést biztosíthat az indexelőknek az adatforrásokhoz. További részletekért olvassa el az [indexelő kapcsolatdokumentációját.](https://docs.microsoft.com/azure/search/search-indexer-troubleshooting#connection-errors) <br/><br/> *Megjegyzés:* A keresési szolgáltatás IP-címe nem szerepel a szolgáltatáscímkéhez tartozó IP-tartományok listájában, és hozzá kell adni az adatforrások IP tűzfalához **is.** | Bejövő | Nem | Nem |
| **AzureConnectors** | Azure Logic Apps-összekötők mintavételi/háttér-kapcsolatokhoz. | Bejövő | Igen | Igen |
| **AzureContainerRegistry** | Azure Container Registry. | Kimenő | Igen | Igen |
| **AzureCosmosDB** | Azure Cosmos DB. | Kimenő | Igen | Igen |
| **AzureDatabricks** | Azure Databricks. | Mindkettő | Nem | Nem |
| **Az AzureDataExplorerManagement** | Az Azure Data Explorer Kezelése. | Bejövő | Nem | Nem |
| **AzureDataLake** | Azure Data Lake Storage Gen1. | Kimenő | Nem | Igen |
| **AzureDevSpaces** | Azure fejlesztői terek. | Kimenő | Nem | Nem |
| **AzureEventGrid** | Azure Event Grid. <br/><br/>*Megjegyzés:* Ez a címke az Azure Event Grid végpontjait tartalmazza az USA déli középső részén, az USA keleti részén, az USA keleti részén 2, az USA nyugati 2- és usa-beli központi végpontjait. | Mindkettő | Nem | Nem |
| **AzureFrontDoor.Frontend** <br/> **AzureFrontDoor.Backend** <br/> **AzureFrontDoor.FirstParty**  | Az Azure bejárati ajtaját. | Mindkettő | Nem | Nem |
| **AzureInformationProtection** | Azure-információvédelem.<br/><br/>*Megjegyzés:* Ez a címke az **AzureActiveDirectory**, **Az AzureFrontDoor.Frontend** és az **AzureFrontDoor.FirstParty** címkéktől függ. | Kimenő | Nem | Nem |
| **AzureIoTHub** | Azure IoT Hub. | Kimenő | Nem | Nem |
| **AzureKeyVault** | Azure Key Vault.<br/><br/>*Megjegyzés:* Ez a címke az **AzureActiveDirectory címkétől** függ. | Kimenő | Igen | Igen |
| **AzureLoadBalancer** | Az Azure infrastruktúra terheléselosztó. A címke fordítja a [virtuális IP-címét a gazdagép](security-overview.md#azure-platform-considerations) (168.63.129.16), ahonnan az Azure-állapotmintavételek származnak. Ez nem tartalmazza az Azure Load Balancer erőforrás forgalmat. Ha nem azure load balancer, felülbírálhatja ezt a szabályt. | Mindkettő | Nem | Nem |
| **AzureMachineLearning** | Azure Machine Learning. | Mindkettő | Nem | Igen |
| **AzureMonitor** | Log Analytics, Application Insights, AzMon és egyéni metrikák (GiG-végpontok).<br/><br/>*Megjegyzés:* A Log Analytics esetében ez a címke a **Storage** címkétől függ. | Kimenő | Nem | Igen |
| **AzureOpenDatasets** | Azure Open adatkészletek.<br/><br/>*Megjegyzés:* Ez a címke az **AzureFrontDoor.Frontend** és **storage** címkét függ. | Kimenő | Nem | Nem |
| **AzurePlatformDNS** | Az alapvető infrastruktúra (alapértelmezett) DNS-szolgáltatás.<br/><br>Ezzel a címkével letilthatja az alapértelmezett DNS-t. Legyen elővigyázatos, amikor ezt a címkét használja. Javasoljuk, hogy olvassa el az [Azure platform szempontjait.](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) Azt is javasoljuk, hogy a címke használata előtt végezze el a tesztelést. | Kimenő | Nem | Nem |
| **AzurePlatformIMD-k** | Az Azure Instance metaadat-szolgáltatás (IMDS), amely egy alapvető infrastruktúra-szolgáltatás.<br/><br/>Ezzel a címkével letilthatja az alapértelmezett IMDS-t. Legyen elővigyázatos, amikor ezt a címkét használja. Javasoljuk, hogy olvassa el az [Azure platform szempontjait.](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) Azt is javasoljuk, hogy a címke használata előtt végezze el a tesztelést. | Kimenő | Nem | Nem |
| **AzurePlatformLKM** | Windows-licencelési vagy kulcskezelési szolgáltatás.<br/><br/>Ezzel a címkével letilthatja a licencelés alapértelmezett beállításait. Legyen elővigyázatos, amikor ezt a címkét használja. Javasoljuk, hogy olvassa el az [Azure platform szempontjait.](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)  Azt is javasoljuk, hogy a címke használata előtt végezze el a tesztelést. | Kimenő | Nem | Nem |
| **AzureResourceManager** | Azure Erőforrás-kezelő. | Kimenő | Nem | Nem |
| **AzureSignalR** | Azure-beli signalr. | Kimenő | Nem | Nem |
| **AzureSiteRecovery** | Azure site-helyreállítás.<br/><br/>*Megjegyzés:* Ez a címke az **AzureActiveDirectory**, **Az AzureKeyVault,** az **EventHub,** a**GuestAndHybridManagement** és a **Storage** címkéktől függ. | Kimenő | Nem | Nem |
| **AzureTrafficManager** | Az Azure Traffic Manager mintavételének IP-címei.<br/><br/>A Traffic Manager mintavételi IP-címeiről az [Azure Traffic Manager gyakori kérdések című témakörben talál](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs)további információt. | Bejövő | Nem | Igen |  
| **BatchNodeManagement** | Az Azure Batch-nek szánt központi telepítések felügyeleti forgalma. | Mindkettő | Nem | Igen |
| **CognitiveServicesManagement szolgáltatás** | Az Azure Cognitive Services forgalmának címtartományai. | Mindkettő | Nem | Nem |
| **DataFactory (DataFactory)**  | Azure Data Factory | Mindkettő | Nem | Nem |
| **DataFactoryManagement** | Az Azure Data Factory felügyeleti forgalma. | Kimenő | Nem | Nem |
| **Dynamics365ForMarketingE-mail** | A Dynamics 365 marketing e-mail szolgáltatásának címtartományai. | Kimenő | Igen | Nem |
| **ElasticaFD között** | Rugalmas Azure bejárati ajtó. | Mindkettő | Nem | Nem |
| **EventHub** | Azure Event Hubs. | Kimenő | Igen | Igen |
| **GatewayManager** | Az Azure VPN Gateway és application gateway számára dedikált központi telepítések felügyeleti forgalma. | Bejövő | Nem | Nem |
| **GuestandHybridManagement** | Azure Automation és vendégkonfiguráció. | Kimenő | Nem | Igen |
| **HDInsight** | Azure HDInsight. | Bejövő | Igen | Nem |
| **Internet** | A virtuális hálózaton kívül lévő és a nyilvános interneten elérhető IP-címterület.<br/><br/>A címtartomány tartalmazza az [Azure tulajdonában lévő nyilvános IP-címteret.](https://www.microsoft.com/download/details.aspx?id=41653) | Mindkettő | Nem | Nem |
| **LogicApps** | Logikai alkalmazások. | Mindkettő | Nem | Nem |
| **LogicAppsManagement** | Felügyeleti forgalom a Logic Apps. | Bejövő | Nem | Nem |
| **MicrosoftCloudAppSecurity** | Microsoft Cloud App Security. | Kimenő | Nem | Nem |
| **MicrosoftContainerRegistry** | A Microsoft tárolólemezképek tárolóbeállítás-nyilvántartása. <br/><br/>*Megjegyzés:* Ez a címke az **AzureFrontDoor.FirstParty** címkét függőséget. | Kimenő | Igen | Igen |
| **PowerQueryOnline** | Power Query Online-t. | Mindkettő | Nem | Nem |
| **ServiceBus** | A prémium szolgáltatási szintet használó Azure Service Bus-forgalom. | Kimenő | Igen | Igen |
| **ServiceFabric** | Azure Service Fabric.<br/><br/>*Megjegyzés:* Ez a címke a Service Fabric szolgáltatás végpontját jelöli régiónként a vezérlősíkhoz. Ez lehetővé teszi az ügyfelek számára, hogy felügyeleti műveleteket hajtsanak végre a Service Fabric-fürtjeikhez a virtuális hálózatukból (például végpont. westus.servicefabric.azure.com https://) | Mindkettő | Nem | Nem |
| **Sql** | Azure SQL-adatbázis, Azure Database for MySQL, Azure Database for PostgreSQL és Az Azure SQL Data Warehouse.<br/><br/>*Megjegyzés:* Ez a címke a szolgáltatást jelöli, de a szolgáltatás konkrét példányait nem. Például a címke az Azure SQL Database szolgáltatást jelöli, de nem egy adott SQL-adatbázist vagy -kiszolgálót. Ez a címke nem vonatkozik az SQL által felügyelt példányra. | Kimenő | Igen | Igen |
| **SqlManagement** | Felügyeleti forgalom SQL-dedikált telepítések. | Mindkettő | Nem | Igen |
| **Tárterület** | Azure Storage. <br/><br/>*Megjegyzés:* Ez a címke a szolgáltatást jelöli, de a szolgáltatás konkrét példányait nem. Például a címke az Azure Storage szolgáltatást jelöli, de nem egy adott Azure Storage-fiókot. | Kimenő | Igen | Igen |
| **StorageSyncService** | Tárolási szinkronizálási szolgáltatás. | Mindkettő | Nem | Nem |
| **WindowsVirtualDesktop** | Windows virtuális asztal. | Mindkettő | Nem | Nem |
| **Virtuális hálózat** | A virtuális hálózati címtér (a virtuális hálózathoz definiált összes IP-címtartomány), az összes csatlakoztatott helyszíni címtér, [a társviszonyba helyezett](virtual-network-peering-overview.md) virtuális hálózatok, a [virtuális hálózati átjáróhoz](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json)csatlakoztatott virtuális hálózatok , [az állomás virtuális IP-címe](security-overview.md#azure-platform-considerations)és a [felhasználó által meghatározott útvonalakon](virtual-networks-udr-overview.md)használt címelőtagok. Ez a címke alapértelmezett útvonalakat is tartalmazhat. | Mindkettő | Nem | Nem |

>[!NOTE]
>A klasszikus üzembe helyezési modellben (az Azure Resource Manager előtt) az előző táblázatban felsorolt címkék egy részhalmaza támogatott. Ezek a címkék másképp vannak beírva:
>
>| Klasszikus helyesírás | Egyenértékű Erőforrás-kezelő címke |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| INTERNET | Internet |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Az Azure-szolgáltatások szolgáltatáscímkéi az adott felhőből származó címelőtagokat jelölik. Például az alapul szolgáló IP-tartományok, amelyek megfelelnek az **Sql** tag értéke az Azure nyilvános felhőben eltér az Azure China-felhő ben alapjául szolgáló tartományok.

> [!NOTE]
> Ha egy szolgáltatáshoz, például az Azure Storage-hoz vagy az Azure SQL Database-hez virtuális [hálózati szolgáltatásvégpontot](virtual-network-service-endpoints-overview.md) valósít meg, az Azure hozzáad egy [útvonalat](virtual-networks-udr-overview.md#optional-default-routes) a szolgáltatás virtuális hálózati alhálózatához. Az útvonal címelőrészei ugyanazok a címelőtagok vagy CIDR-tartományok, mint a megfelelő szolgáltatáscímke.

## <a name="service-tags-on-premises"></a>Helyszíni szolgáltatáscímkék  
Az aktuális szolgáltatáscímkét és a tartományadatokat beszerezheti, amelyek a helyszíni tűzfal-konfigurációk részét képezik. Ez az információ az egyes szolgáltatáscímkeinek megfelelő IP-tartományok aktuális időponthoz(0)-időpont listája. Az információkat programozott módon vagy JSON fájlletöltéssel szerezheti be, a következő szakaszokban leírtak szerint.

### <a name="use-the-service-tag-discovery-api-public-preview"></a>A Szolgáltatáscímke felderítése API használata (nyilvános előzetes verzió)
A szolgáltatáscímkék aktuális listáját programozott módon, az IP-címtartomány részleteivel együtt is lekérheti:

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Azure CLI](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> Amíg nyilvános előzetes verzióban van, előfordulhat, hogy a Discovery API olyan információkat ad vissza, amelyek kevésbé aktuálisak, mint a JSON-letöltések által visszaadott információk. (Lásd a következő szakaszt.)


### <a name="discover-service-tags-by-using-downloadable-json-files"></a>Szolgáltatáscímkék felfedezése letölthető JSON-fájlok használatával 
Letöltheti a JSON fájlokat, amelyek tartalmazzák a szolgáltatás címkék aktuális listáját az IP-címtartomány részleteivel együtt. Ezeket a listákat hetente frissítik és közzéteszik. Az egyes felhők helyei a következők:

- [Azure Public](https://www.microsoft.com/download/details.aspx?id=56519)
- [Az Azure Amerikai Egyesült Államok kormánya](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure China](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Germany](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>Ezen információk egy része az Azure Public , [az Azure](https://www.microsoft.com/download/details.aspx?id=41653) [China](https://www.microsoft.com/download/details.aspx?id=42064)és az [Azure Germany](https://www.microsoft.com/download/details.aspx?id=54770)XML-fájljaiban lett közzétéve. Ezek az XML-letöltések 2020. Az előző szakaszokban leírtak szerint át kell térnie a Discovery API- vagy JSON-fájlletöltések használatára.

### <a name="tips"></a>Tippek 
- Az egyik kiadványból a másikba tartozó frissítésekészleléséhez a JSON-fájlban megnövekedett *changeNumber* értékeket kell feljegyzni. Minden alszakasz (például **Storage.WestUS**) saját *changeNumber,* amely a változások bekövetkezése növekszik. A fájl *changeNumber* legfelső szintje növekszik, ha az alszakaszok bármelyike megváltozik.
- Példák a szolgáltatáscímke-információk elemzésével (például a WestUS storage összes címtartományának lekérnie) a [Service Tag Discovery API PowerShell](https://aka.ms/discoveryapi_powershell) dokumentációjában.

## <a name="next-steps"></a>További lépések
- További információ a [hálózati biztonsági csoport létrehozásáról.](tutorial-filter-network-traffic.md)
