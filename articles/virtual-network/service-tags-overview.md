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
ms.openlocfilehash: 33ee7351e547ee5ef57ef07f67ba6f5f4410b57f
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384145"
---
# <a name="virtual-network-service-tags"></a>Virtuális hálózati szolgáltatás címkéi 
<a name="network-service-tags"></a>

A szolgáltatás címkéje egy adott Azure-szolgáltatás IP-címeinek egy csoportját jelöli. Ez segít a hálózati biztonsági szabályok gyakori frissítéseinek összetettségének minimalizálásában. A szolgáltatás-címkék használatával hálózati [biztonsági csoportokon](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) vagy [Azure Firewallon](https://docs.microsoft.com/azure/firewall/service-tags)is meghatározhat hálózati hozzáférés-vezérlést. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha egy szabály megfelelő *forrás* vagy *cél* mezőjében megadja a szolgáltatási címke nevét (például **ApiManagement**), akkor engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával. 

## <a name="available-service-tags"></a>Elérhető szolgáltatás címkéi
A következő táblázat tartalmazza a [hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) szabályaiban használható összes szolgáltatási címkét.

Az oszlopok azt jelzik, hogy a címke:

- A bejövő vagy kimenő forgalomra vonatkozó szabályokra alkalmas
- [Regionális](https://azure.microsoft.com/regions) hatókör támogatása 
- Használható [Azure Firewall](https://docs.microsoft.com/azure/firewall/service-tags) -szabályokban

Alapértelmezés szerint a szolgáltatás címkéi a teljes felhő tartományait tükrözik.  Egyes szolgáltatási címkék a megfelelő IP-tartományok adott régióra való korlátozásával is finomabb gabona-szabályozást tesznek lehetővé.  Ha például a szolgáltatás címkéi **tárolója** az Azure Storage-t jelöli a teljes felhőhöz, a **Storage. WestUS** leszűkíti, hogy csak a WestUS-régióból származó tárolási IP-címtartományok.  Az alábbi szolgáltatási címkék leírása azt jelzi, hogy támogatják-e az ilyen regionális hatókört.  



| Címke | Cél | Használhat bejövő vagy kimenő adatforgalmat? | Lehet regionális? | Használható a Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ApiManagement** | Felügyeleti forgalom a APIM dedikált központi telepítések esetén. | Mindkettő | Nem | Igen |
| **AppService**    | App Service szolgáltatás. Ez a címke javasolt a kimenő biztonsági szabályokhoz az WebApp-előtérben. | Kimenő | Igen | Igen |
| **AppServiceManagement** | Felügyeleti forgalom App Service Environment dedikált üzemelő példányokhoz. | Mindkettő | Nem | Igen |
| **AzureActiveDirectory** | Azure Active Directory szolgáltatás. | Kimenő | Nem | Igen |
| **AzureActiveDirectoryDomainServices** | Felügyeleti forgalom Azure Active Directory Domain Services dedikált üzemelő példányokhoz. | Mindkettő | Nem | Igen |
| **AzureBackup** |Azure Backup szolgáltatás.<br/><br/>*Megjegyzés:* Ez a címke függőséget tartalmaz a **Storage** és a **AzureActiveDirectory** címkével. | Kimenő | Nem | Igen |
| **AzureCloud** | Minden [adatközpont nyilvános IP-címe](https://www.microsoft.com/download/details.aspx?id=41653). | Kimenő | Igen | Igen |
| **AzureConnectors** | Logic Apps összekötők mintavételi vagy háttérbeli kapcsolatokhoz. | Bejövő | Igen | Igen |
| **AzureContainerRegistry** | Azure Container Registry szolgáltatás. | Kimenő | Igen | Igen |
| **AzureCosmosDB** | Azure Cosmos Database szolgáltatás. | Kimenő | Igen | Igen |
| **AzureDataLake** | Azure Data Lake szolgáltatás. | Kimenő | Nem | Igen |
| **AzureIoTHub** | Azure IoT Hub szolgáltatás. | Kimenő | Nem | Nem |
| **AzureKeyVault** | Azure kulcstartó szolgáltatás.<br/><br/>*Megjegyzés:* Ez a címke függőséget tartalmaz a **AzureActiveDirectory** címkével. | Kimenő | Igen | Igen |
| **AzureLoadBalancer** | Azure infrastruktúra-terheléselosztó. A címkét a rendszer lefordítja a [gazdagép azon virtuális IP-címére](security-overview.md#azure-platform-considerations) (168.63.129.16), ahonnan az Azure állapot-mintavételei származnak. Ha nem az Azure Load Balancert használja, ezt a szabályt felül lehet bírálni. | Mindkettő | Nem | Nem |
| **AzureMachineLearning** | Azure Machine Learning szolgáltatás. | Kimenő | Nem | Igen |
| **AzureMonitor** | Log Analytics, alkalmazás-felismerések, AzMon és egyéni metrikák (koncert-végpontok).<br/><br/>*Megjegyzés:* Log Analytics esetén ez a címke függőséget tartalmaz a **tárolási** címkével. | Kimenő | Nem | Igen |
| **AzurePlatformDNS** | Az alapszintű infrastruktúra (alapértelmezett) DNS szolgáltatás.<br/><br>Ezt a címkét használhatja az alapértelmezett DNS letiltásához. Körültekintően járjon el a címke használatával. Az [Azure platformmal kapcsolatos megfontolások](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) beolvasása ajánlott. A címke használata előtt javasolt a tesztelés. | Kimenő | Nem | Nem |
| **AzurePlatformIMDS** | IMDS, amely egy alapszintű infrastruktúra-szolgáltatás.<br/><br/>Ezt a címkét használhatja az alapértelmezett IMDS letiltásához.  Körültekintően járjon el a címke használatával. Az [Azure platformmal kapcsolatos megfontolások](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) beolvasása ajánlott. A címke használata előtt javasolt a tesztelés. | Kimenő | Nem | Nem |
| **AzurePlatformLKM** | Windows licencelés vagy kulcskezelő szolgáltatás.<br/><br/>Ezt a címkét használhatja a licencelés alapértelmezett értékének letiltásához. Körültekintően járjon el a címke használatával.  Az [Azure platformmal kapcsolatos megfontolások](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) beolvasása ajánlott. A címke használata előtt javasolt a tesztelés. | Kimenő | Nem | Nem |
| **AzureTrafficManaged** | Azure Traffic Manager mintavétel IP-címei.<br/><br/>További információk a Traffic Manager mintavételezési IP-címeiről: [Azure Traffic Manager – Gyakori kérdések](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). | Bejövő | Nem | Igen |  
| **BatchNodeManagement** | Felügyeleti forgalom Azure Batch dedikált üzemelő példányokhoz. | Mindkettő | Nem | Igen |
| **CognitiveServicesManagement** | A Cognitive Services adatforgalmához tartozó címtartományok | Kimenő | Nem | Nem |
| **Dynamics365ForMarketingEmail** | A Dynamics 365 marketing e-mail-szolgáltatásának címtartományt. | Kimenő | Igen | Nem |
| **EventHub** | Azure EventHub szolgáltatás. | Kimenő | Igen | Igen |
| **GatewayManager** | Felügyeleti forgalom a VPN/app Gateway dedikált központi telepítések esetén. | Bejövő | Nem | Nem |
| **Internet** | Az IP-címtartomány a virtuális hálózaton kívül esik, és a nyilvános Internet elérhető.<br/><br/>A címtartományba beletartozik az [Azure tulajdonában lévő nyilvános IP-címtér](https://www.microsoft.com/download/details.aspx?id=41653) is. | Mindkettő | Nem | Nem |
| **MicrosoftContainerRegistry** | Microsoft Container Registry szolgáltatás. | Kimenő | Igen | Igen |
| **ServiceBus** | Azure Service Bus szolgáltatás a prémium szintű szolgáltatási szinttel. | Kimenő | Igen | Igen |
| **ServiceFabric** | Service Fabric szolgáltatás. | Kimenő | Nem | Nem |
| **SQL** | Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL és Azure SQL Data Warehouse szolgáltatások.<br/><br/>*Megjegyzés:* Ez a címke a szolgáltatást jelöli, de a szolgáltatás adott példányai nem. Például a címke az Azure SQL Database szolgáltatást jelöli, de nem egy adott SQL-adatbázist vagy -kiszolgálót. | Kimenő | Igen | Igen |
| **SqlManagement** | Felügyeleti forgalom az SQL dedikált üzemelő példányokhoz. | Mindkettő | Nem | Igen |
| **Tárolás** | Azure Storage szolgáltatás. <br/><br/>*Megjegyzés:* A címke a szolgáltatást jelöli, de a szolgáltatás adott példányai nem. Például a címke az Azure Storage szolgáltatást jelöli, de nem egy adott Azure Storage-fiókot. | Kimenő | Igen | Igen |
| **VirtualNetwork** | A virtuális hálózati címtartomány (a virtuális hálózathoz meghatározott összes IP-címtartomány), a virtuális hálózati [átjáróhoz](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json)csatlakoztatott összes helyszíni címterület, egymással [összekapcsolt virtuális hálózat](virtual-network-peering-overview.md) vagy virtuális hálózat, a virtuális [IP-cím a](security-overview.md#azure-platform-considerations) [felhasználó által megadott útvonalakon](virtual-networks-udr-overview.md)használt gazdagép és címek előtagjainak címe. Vegye figyelembe, hogy ez a címke tartalmazhatja az alapértelmezett útvonalakat is. | Mindkettő | Nem | Nem |

>[!NOTE]
>A *klasszikus* (előzetes Azure Resource Manager) környezetben végzett munka esetén a fenti címkék közül választhat.  Ezek a következő alternatív helyesírást használják:

| Klasszikus helyesírás | Egyenértékű Resource Manager-címke |
|---|---|
| AZURE_LOADBALANCER | AzureLoadBalancer |
| INTERNET | Internet |
| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Az Azure-szolgáltatások szolgáltatási címkéi a használt felhőből származó előtagokat jelzik. például az **SQL** -címke értékének megfelelő mögöttes IP-címtartományok eltérőek lesznek az Azure nyilvános felhő és az Azure China Cloud között.

> [!NOTE]
> Ha egy [virtuális hálózati szolgáltatásvégpontot](virtual-network-service-endpoints-overview.md) implementál egy szolgáltatáshoz, például az Azure Storage-hoz vagy az Azure SQL Database-hez, az Azure hozzáad egy [útvonalat](virtual-networks-udr-overview.md#optional-default-routes) a szolgáltatás virtuális hálózatának alhálózatához. Az útvonal címelőtagjai ugyanazok a címelőtagok vagy CIDR-tartományok lesznek, mint a megfelelő szolgáltatáscímke.



## <a name="service-tags-in-on-premises"></a>Szolgáltatások címkéi a helyszínen  
A szolgáltatáshoz tartozó aktuális címke és tartomány információ beszerzésével a helyszíni tűzfal konfigurációjának részeként is felvehető.  Ez az információ az egyes szolgáltatási címkéknek megfelelő IP-címtartományok aktuális időpontra vonatkozó listája.  Az információk programozott módon vagy a JSON-fájl letöltésével szerezhetők be az alábbiak szerint.

### <a name="service-tag-discovery-api-public-preview"></a>Service tag Discovery API (nyilvános előzetes verzió)
Programozott módon lekérheti a szolgáltatási címkék aktuális listáját az IP-címtartomány részleteivel:

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Azure CLI](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> A nyilvános előzetes verzióban a felderítési API a JSON-letöltéseket (alább) nem aktuálisként megadott adatokat adhat vissza.


### <a name="discover-service-tags-using-downloadable-json-files"></a>Szolgáltatási címkék felderítése a letölthető JSON-fájlokkal 
A szolgáltatási címkék aktuális listáját tartalmazó JSON-fájlok az IP-címtartomány részleteivel tölthetők le. Ezeket a rendszer hetente frissíti és közzéteszi.  Az egyes felhő helyei a következők:

- [Nyilvános Azure](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure China](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Germany](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>Az adatok egy részhalmaza korábban már közzé lett téve az [Azure Public](https://www.microsoft.com/download/details.aspx?id=41653), az [Azure China](https://www.microsoft.com/download/details.aspx?id=42064) és az [Azure Germany](https://www.microsoft.com/download/details.aspx?id=54770)XML-fájljaiban. Az XML-letöltéseket a rendszer a 2020-es június 30-ig elavult, és a továbbiakban nem lesz elérhető. A fentiekben leírtak szerint telepítse át a felderítési API vagy a JSON-fájl letöltését.

### <a name="tips"></a>Tippek 
- Az egyik közzététel frissítései a következőre észlelhetők a JSON-fájlon belüli megnövekedett *changeNumber* -értékek használatával. Az egyes alszakaszok (például a **Storage. WestUS**) saját *changeNumber* rendelkeznek, amely a változásoknak megfelelően növekszik.  A fájl *changeNumber* legfelső szintje növekszik, ha az alszakaszok bármelyike megváltozott.
- A szolgáltatás címkéjén található információk elemzését (például a WestUS lévő összes címtartomány beolvasását) ismertető témakörben talál példákat a [Service tag Discovery API PowerShell](https://aka.ms/discoveryapi_powershell) dokumentációjában.

## <a name="next-steps"></a>Következő lépések
- Ismerje meg [a hálózati biztonsági csoportok létrehozását](tutorial-filter-network-traffic.md).

