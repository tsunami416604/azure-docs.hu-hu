---
title: Az Availability Zonest támogató Azure-szolgáltatások
description: Az Azure-ban kiválóan elérhető és rugalmas alkalmazások hozhatók létre, Availability Zones biztosítanak fizikailag különálló helyet, amellyel erőforrásokat futtathat.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 12/17/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: 0365a60317538ba31f39928cd30a57e2c969c832
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2020
ms.locfileid: "97723038"
---
# <a name="azure-services-that-support-availability-zones"></a>Az Availability Zonest támogató Azure-szolgáltatások

A Availability Zones magas rendelkezésre állású ajánlat, amely védelmet nyújt alkalmazásai és adatai számára az adatközpont hibáiból. Az Availability Zonest támogató meglévő és közelgő régiók listáját lásd: [régiók és Availability Zones az Azure-ban](az-overview.md).  

Ez a szakasz felsorolja a Availability Zones támogató Azure-szolgáltatásokat. 

Az egyes régiókban elérhető szolgáltatások, valamint a rendelkezésre állásra vonatkozó közelgő ütemterv a [régiók által elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/)között található.

Az összes Azure-beli felügyeleti szolgáltatás a régió szintű hibáktól való ellenálló képességnek számít. A hibák spektrumában egy vagy több rendelkezésre állási zónának a régión belüli meghibásodása kisebb meghibásodási sugarú, mint a teljes régió meghibásodása. Az Azure helyreállíthatja a régión belüli felügyeleti szolgáltatások zóna szintű meghibásodását. Az Azure a régión belül egyszerre végzi el a kritikus karbantartást, így megakadályozva, hogy az adott régión belül üzembe helyezett felhasználói erőforrásokkal kapcsolatos hibák ne legyenek Availability Zones.


![3 zónával rendelkező Azure-régió fogalmi nézete](./media/az-region/azure-region-availability-zones.png)


A Availability Zones támogató **Azure-szolgáltatások** három kategóriába sorolhatók: a többtényezős, a **zóna-redundáns** és a **nem regionális** szolgáltatásokra. Az ügyfelek számítási feladatait kategorizálhatja az alkalmazások teljesítményének és tartósságának kielégítése érdekében.

- **Zónákra** kiterjedő szolgáltatások – egy erőforrást egy meghatározott, saját maga által kiválasztott rendelkezésre állási zónába helyezhet üzembe, amely szigorúbb késési vagy teljesítménybeli követelményeket is elérhet.  A rugalmasságot úgy kell megtervezni, hogy az alkalmazásokat és az adatforrásokat egy vagy több, a régión belüli zónába replikálja.  Az erőforrások rögzíthető egy adott zónához. Például a virtuális gépek, a felügyelt lemezek vagy a standard IP-címek egy adott zónába rögzíthetők, ami nagyobb rugalmasságot tesz lehetővé azáltal, hogy az erőforrások egy vagy több példánya több zónában is elterjedt.

- **Zóna – redundáns szolgáltatások** – az Azure platform replikálja az erőforrást és az adatzónákat.  A Microsoft kezeli a magas rendelkezésre állást, mivel az Azure automatikusan replikálja és elosztja a régión belüli példányokat.  A ZRS például három zónában replikálja az adatforrást, így a zóna meghibásodása nem befolyásolja az adott adatforrást. 

- **Nem regionális szolgáltatások** – olyan szolgáltatások, amelyek nem rendelkeznek egy adott Azure-régiótól való függőséggel, így rugalmasak lehetnek a zónákra kiterjedő kimaradások, valamint az egész régióra kiterjedő kimaradások terén.


Az Azure-ban elérhető átfogó Üzletmenet-folytonosság érdekében az Azure region Pairs Availability Zones kombinációját használva hozza létre az alkalmazás-architektúrát. Az alkalmazásokat és az adatait szinkron módon replikálhatja az Azure-régión belüli Availability Zones használatával, hogy magas rendelkezésre állást biztosítson, és aszinkron módon replikálja az Azure-régiókat a vész-helyreállítási védelem érdekében. További információért olvassa el a [Availability Zones használatával történő magas rendelkezésre állású megoldások kiépítése](https://docs.microsoft.com/azure/architecture/high-availability/building-solutions-for-high-availability)című témakört. 


### <a name="azure-services-supporting-availability-zones"></a>Availability Zones támogató Azure-szolgáltatások

- A régebbi generációs virtuális gépek nem szerepelnek az alábbi listában. További információ: [a virtuális gépek méreteinek előző generációi](../virtual-machines/sizes-previous-gen.md).

- Egyes szolgáltatások nem regionálisak, további információért lásd: [régiók és Availability Zones az Azure-ban](az-overview.md) . Ezek a szolgáltatások nem függnek egy adott Azure-régiótól, így rugalmasak lehetnek a zónákra kiterjedő kimaradások és az egész régióra kiterjedő kimaradások terén.  A nem regionális szolgáltatások listáját a [régió által elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/)között találja.



## <a name="americas"></a>Észak-, Dél- és Közép-Amerika

| **Termékek** | **USA középső régiója** | **USA keleti régiója** | **USA 2. keleti régiója** | **USA 2. nyugati régiója** | **Közép-Kanada** |
|--|--|--|--|--|--|
| **Számítás** |  |  |  |  |  |
| [App Service környezetek (ILB)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines méretezési csoportok](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 
| **Containers** |  |  |  |
| [Azure Kubernetes Service (AKS)](../aks/availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Container Registry](../container-registry/zone-redundancy.md) |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  | 
| **Storage** |  |  |  |  |  |
| [Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Premium Files Storage](../storage/files/storage-files-planning.md) |  | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Blob Storage](../storage/blobs/storage-blobs-introduction.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Managed Disks](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Hálózat** |  |  |  |  |  |
| [Application Gateway v2](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Express-útvonal](../expressroute/designing-for-high-availability-with-expressroute.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Firewall](../firewall/deploy-availability-zone-powershell.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Szabványos IP-cím](../virtual-network/public-ip-addresses.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Load Balancer](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network](../vpn-gateway/create-zone-redundant-vnet-gateway.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network NAT](../virtual-network/nat-gateway-resource.md#availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual WAN](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN Gateway](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Adatbázisok** |  |  |  |  |  |
| [Azure Cache for Redis](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Database for MySQL – rugalmas kiszolgáló](../mysql/flexible-server/concepts-high-availability.md) | x | x | :heavy_check_mark: | :heavy_check_mark: | x |
| [Azure Database for PostgreSQL – rugalmas kiszolgáló](../postgresql/flexible-server/overview.md) | x | x | :heavy_check_mark: | :heavy_check_mark: | x |
| [Azure SQL Database (általános célú szintű)](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | x | : heavy_check_mark: (előzetes verzió) | : heavy_check_mark: (előzetes verzió) | : heavy_check_mark: (előzetes verzió) | x |
| [Azure SQL Database (prémium szintű & üzletileg kritikus szinten)](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Elemzés** |  |  |  |  |  |
| [Event Hubs](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integráció** |  |  |  |  |  |
| [Event Grid](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Szolgáltatásbusz](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Felügyelet és irányítás** |  |  |  |  |  |
| [Network Watcher](../network-watcher/frequently-asked-questions.md) | x | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | x |
| **Biztonság** |  |  |  |  |  |
| [Azure Active Directory tartományi szolgáltatások](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="europe"></a>Európa

| **Termékek** | **Közép-Franciaország** | **Észak-Európa** | **Az Egyesült Királyság déli régiója** | **Nyugat-Európa** |
|--|--|--|--|--|
| **Számítás** |  |  |  |  |
| [App Service környezetek (ILB)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Kubernetes Service (AKS)](../aks/availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines méretezési csoportok](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Storage** |  |  |  |  |
| [Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Premium Files Storage](../storage/files/storage-files-planning.md) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Blob Storage](../storage/blobs/storage-blobs-introduction.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Managed Disks](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Hálózat** |  |  |  |  |
| [Application Gateway v2](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Express-útvonal](../expressroute/designing-for-high-availability-with-expressroute.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Firewall](../firewall/deploy-availability-zone-powershell.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Szabványos IP-cím](../virtual-network/public-ip-addresses.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Load Balancer](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network](../vpn-gateway/create-zone-redundant-vnet-gateway.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network NAT](../virtual-network/nat-gateway-resource.md#availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual WAN](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN Gateway](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Adatbázisok** |  |  |  |  |
| [Azure Cache for Redis](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Database for MySQL – rugalmas kiszolgáló](../mysql/flexible-server/concepts-high-availability.md) | x | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Database for PostgreSQL – rugalmas kiszolgáló](../postgresql/flexible-server/overview.md) | x | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure SQL Database (általános célú szintű)](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | x | : heavy_check_mark: (előzetes verzió) | x | : heavy_check_mark: (előzetes verzió) |
| [Azure SQL Database (prémium szintű & üzletileg kritikus szinten)](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Elemzés** |  |  |  |  |
| [Event Hubs](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integráció** |  |  |  |  |
| [Event Grid](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Szolgáltatásbusz](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Felügyelet és irányítás** |  |  |  |  |
| [Network Watcher](../network-watcher/frequently-asked-questions.md) | :heavy_check_mark: | :heavy_check_mark: | x | :heavy_check_mark: |
| **Biztonság** |  |  |  |  |
| [Azure Active Directory tartományi szolgáltatások](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="asia-pacific"></a>Ázsia és a Csendes-óceáni térség



| **Termékek** | **Kelet-Japán** | **Délkelet-Ázsia** | **Kelet-Ausztrália** |
|--|--|--|--|
| **Számítás** |  |  |  |
| [App Service környezetek (ILB)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Kubernetes Service (AKS)](../aks/availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines méretezési csoportok](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Storage** |  |  |  |
| [Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Premium Files Storage](../storage/files/storage-files-planning.md) |  | :heavy_check_mark: | :heavy_check_mark: |
| [Blob Storage](../storage/blobs/storage-blobs-introduction.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Managed Disks](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Hálózat** |  |  |  |
| [Application Gateway v2](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Express-útvonal](../expressroute/designing-for-high-availability-with-expressroute.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Firewall](../firewall/deploy-availability-zone-powershell.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Szabványos IP-cím](../virtual-network/public-ip-addresses.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Load Balancer](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network](../vpn-gateway/create-zone-redundant-vnet-gateway.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network NAT](../virtual-network/nat-gateway-resource.md#availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual WAN](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN Gateway](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Adatbázisok** |  |  |  |
| [Azure Cache for Redis](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Database for MySQL – rugalmas kiszolgáló](../mysql/flexible-server/concepts-high-availability.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Database for PostgreSQL – rugalmas kiszolgáló](../postgresql/flexible-server/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure SQL Database (általános célú szintű)](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | x | : heavy_check_mark: (előzetes verzió) | : heavy_check_mark: (előzetes verzió) |
| [Azure SQL Database (prémium szintű & üzletileg kritikus szinten)](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Elemzés** |  |  |  |
| [Event Hubs](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integráció** |  |  |  |
| [Event Grid](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Szolgáltatásbusz](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Felügyelet és irányítás** |  |  |  |
| [Network Watcher](../network-watcher/frequently-asked-questions.md) | :heavy_check_mark: | x | x |
| **Biztonság** |  |  |  |
| [Azure Active Directory tartományi szolgáltatások](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: |  |


## <a name="upcoming-availability-zones"></a>Közelgő Availability Zones 

Az Azure Availability Zones támogatást nyújt a következő régiókban:
- USA-beli államigazgatás – Virginia
- Dél-Afrika északi régiója
- USA déli középső régiója
- Középnyugat-Németország

Az Availability Zonest támogató meglévő és közelgő régiók listája [itt](https://azure.microsoft.com/global-infrastructure/geographies/)található.    

Ha többet szeretne megtudni a Availability Zones támogatásáról ezekben a régiókban, lépjen kapcsolatba a Microsoft értékesítési vagy ügyfélszolgálati képviselőjével.


## <a name="pricing-for-vms-in-availability-zones"></a>A Availability Zones-beli virtuális gépek díjszabása

A rendelkezésre állási zónában üzembe helyezett virtuális gépekhez nem kell további díjat fizetni. További információkért tekintse át a [sávszélesség díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="get-started-with-availability-zones"></a>Ismerkedés a Availability Zones

- [Virtuális gép létrehozása](../virtual-machines/windows/create-portal-availability-zone.md)
- [Felügyelt lemez hozzáadása a PowerShell használatával](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Zónában redundáns virtuálisgép-méretezési csoport létrehozása](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Virtuális gépek terheléselosztása zónák között egy olyan standard Load Balancer használatával, amely zóna nélküli előtérbeli felületet használ](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-cli?tabs=option-1-create-load-balancer-standard)
- [Virtuális gépek terheléselosztása zónán belül egy standard Load Balancer és egy zóna-előtérbeli felület használatával](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-cli?tabs=option-1-create-load-balancer-standard)
- [Zónaredundáns tárolás](../storage/common/storage-redundancy.md)
- [Általános célú SQL Database](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Event Hubs georeplikációs vészhelyreállítás](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Service Bus georeplikációs vészhelyreállítás](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Zónaredundáns virtuális hálózati átjáró létrehozása](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Zóna redundáns régiójának hozzáadása Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Azure cache Első lépések Redis Availability Zones](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [Azure Active Directory Domain Services-példány létrehozása](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)
- [Availability Zonest használó Azure Kubernetes-szolgáltatásbeli (ak-beli) fürt létrehozása](../aks/availability-zones.md)


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Régiók és rendelkezésre állási zónák az Azure-ban](az-overview.md)
