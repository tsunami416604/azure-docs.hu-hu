---
title: Mik azok az Azure rendelkezésre állási zónák?
description: Magas rendelkezésre állású és rugalmas alkalmazások létrehozásához az Azure-ban, a rendelkezésre állási zónák fizikailag különálló helyeket biztosítanak, amelyek segítségével futtathatja az erőforrásokat.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 10/17/2019
ms.author: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: e7ba62a96a6fc76ad63960cfe57f5b1f85589c32
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085042"
---
# <a name="what-are-availability-zones-in-azure"></a>Mik azok a rendelkezésre állási zónák az Azure-ban?
A rendelkezésre állási zónák egy magas rendelkezésre állású ajánlat, amely megvédi az alkalmazásokat és az adatokat az adatközponti hibáktól. A rendelkezésreállási zónák fizikailag elkülönített helyek egy Azure-régión belül. Minden rendelkezésreállási zóna egy vagy több, független áramforrással, hűtéssel és hálózatkezelési megoldással ellátott adatközpontból áll. A rugalmasság biztosítása érdekében minden engedélyezett régióban legalább három elkülönített zóna található. A rendelkezésre állási zónák fizikai elkülönítése egy régión belül védi az alkalmazásokat és az adatokat az adatközpont-hibáktól. A zónaredundáns szolgáltatások replikálják az alkalmazásokat és az adatokat a rendelkezésre állási zónák között, hogy megvédjék az egypontosokat. A rendelkezésre állási zónákkal az Azure az iparág legjobb 99,99%-os virtuálisgép-rendelkezésre állási SLA-ját kínálja. Az Azure egészére vonatkozó rendelkezésre állási garancia magyarázata a teljes [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)-ban található.

Egy Azure-régióban rendelkezésre állási zóna egy tartalék tartomány és egy frissítési tartomány kombinációja. Ha például három vagy több virtuális gépet hoz létre egy Azure-régió három zónája között, a virtuális gépek hatékonyan oszlanak el három tartalék tartomány és három frissítési tartomány között. Az Azure platform felismeri ezt a terjesztési frissítési tartományok között, hogy győződjön meg arról, hogy a különböző zónákban lévő virtuális gépek nem frissülnek egyszerre.

Magas rendelkezésre állást építhet be az alkalmazásarchitektúrába a számítási, tárolási, hálózati és adaterőforrások zónán belüli közös helymeghatározásával és más zónákban való replikálásával. A rendelkezésre állási zónákat támogató Azure-szolgáltatások két kategóriába sorolhatók:

- **Zónaszintű szolgáltatások** – az erőforrást egy adott zónához (például virtuális gépekhez, felügyelt lemezekhez, szabványos IP-címekhez) vagy
- **Zónaredundáns szolgáltatások** – a platform automatikusan több zónában replikálódik (például zónaredundáns tárolás és SQL Database).

Az Azure átfogó üzletmenet-folytonosságának elérése érdekében az alkalmazásarchitektúrát a rendelkezésre állási zónák és az Azure régiópárok kombinációjával hozhatja létre. Az azure-régión belüli rendelkezésre állási zónák használatával szinkron módon replikálhatja alkalmazásait és adatait a magas rendelkezésre állás érdekében, és aszinkron módon replikálhatja az Azure-régiók között a vész-helyreállítási védelem érdekében.
 
![Egy régióban lehaladó zóna fogalmi nézete](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> A rendelkezésre állási zóna azonosítók (a fenti képen 1, 2 és 3 szám) logikailag le vannak képezve az egyes előfizetések tényleges fizikai zónáihoz egymástól függetlenül. Ez azt jelenti, hogy egy adott előfizetés 1. rendelkezésre állási zóna előfordulhat, hogy egy másik fizikai zóna, mint a rendelkezésre állási zóna 1 egy másik előfizetésben. Ennek következtében azt javasoljuk, hogy ne támaszkodjon a rendelkezésre állási zóna azonosítók különböző előfizetések virtuális gépek elhelyezése között.

## <a name="services-support-by-region"></a>Szolgáltatások támogatása régiónként

Az Azure-szolgáltatások és a rendelkezésre állási zónákat támogató régiók kombinációi a következők:


|                                 |Amerika |              |           |           | Európa |              |          |              | Ázsia és a Csendes-óceáni térség |                 |                |
|----------------------------|----------|----------|---------|---------|--------------|------------|--------|----------|----------|-------------|-------------|
|          |USA középső régiója|USA keleti régiója|USA 2. keleti régiója|USA nyugati régiója, 2.|Közép-Franciaország|Észak-Európa|Az Egyesült Királyság déli régiója|Nyugat-Európa|Kelet-Japán|Délkelet-Ázsia|Kelet-Ausztrália|
| **Compute**                         |            |              |           |           |                |              |          |             |            |                |                |
| Linux rendszerű virtuális gépek          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Windows rendszerű virtuális gépek        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Virtual Machine Scale Sets      | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Az Azure App Service-környezetek ILB | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Azure Kubernetes Service        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| **Storage**   |            |              |           |           |                |              |          |             |            |                |                |
| Felügyelt lemezek                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Zónaredundáns tárolás          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| **Hálózat**                     |            |              |           |           |                |              |          |             |            |                |                |
| Normál IP-cím        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Standard terheléselosztó     | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| VPN Gateway            | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |                |
| ExpressRoute-átjáró   | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |                |
| Alkalmazásátjáró(V2)    | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |                |
| Azure Firewall           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |                |
| **Adatbázisok**                     |            |              |           |           |                |              |          |             |            |                |                |
| Azure Data Explorer                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;        | &#10003;       |                |
| SQL Database                    | &#10003;   | &#10003;     | &#10003;  | &#10003;(Előzetes verzió) | &#10003;       | &#10003;(Előzetes verzió)     | &#10003; | &#10003;    | &#10003;       | &#10003;       |&#10003;        |
| Azure Cache for Redis           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |                |
| Azure Cosmos DB                    | &#10003;   |  &#10003;  |  &#10003; | &#10003; |       |     | &#10003; |  &#10003;   |            | &#10003;       |                |
| **Elemzés**                       |            |              |           |           |                |              |          |             |            |                |                |
| Event Hubs                      | &#10003;   |   &#10003; | &#10003;  | &#10003;  | &#10003; | &#10003; | &#10003; | &#10003; | &#10003; | &#10003;       |                |
| **Integráció**                     |            |              |           |           |                |              |          |             |            |                |                |
| Service Bus (csak prémium szint) | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |                |
| Event Grid | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |                |
| **Identitás**                     |            |              |           |           |                |              |          |             |            |                |                |
| Azure AD Domain Services | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |                |

## <a name="services-resiliency"></a>Szolgáltatások rugalmassága
Minden Azure felügyeleti szolgáltatások úgy vannak kitervezve, hogy rugalmas a régiószintű hibák. A hibák spektrumában egy vagy több rendelkezésre állási zóna hibák egy régión belül egy kisebb hibasugarat, mint egy teljes régió hiba. Az Azure a régión belüli vagy egy másik Azure-régióban lévő felügyeleti szolgáltatások zónaszintű meghibásodása után helyreállítható. Az Azure kritikus karbantartást végez egy zónában egy régión belül, hogy megakadályozza a régión belüli rendelkezésre állási zónákban üzembe helyezett ügyfél-erőforrásokat befolyásoló hibákat.

## <a name="pricing"></a>Díjszabás
A rendelkezésre állási zónában üzembe helyezett virtuális gépek nem járnak többletköltséggel. A virtuális gépek 99,99%-os rendelkezésre állási sla-ja akkor érhető el, ha két vagy több virtuális gép van telepítve két vagy több rendelkezésre állási zónában egy Azure-régióban. Lesz további rendelkezésre állási zóna virtuális gép-virtuális gép adatátviteli díjakat. További információért tekintse át a [Sávszélesség díjszabási](https://azure.microsoft.com/pricing/details/bandwidth/) oldalát.


## <a name="get-started-with-availability-zones"></a>A rendelkezésre állási zónák – első lépések
- [Virtuális gép létrehozása](../virtual-machines/windows/create-portal-availability-zone.md)
- [Felügyelt lemez hozzáadása a PowerShell használatával](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Zónaredundáns virtuálisgép-méretezési csoport létrehozása](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Terheléselosztási virtuális gépek zónák között, zónaredundáns előtérrel rendelkező standard terheléselosztó használatával](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [Zónaszintű előtér-előtér-előtér-előtérrel rendelkező standard terheléselosztót használó virtuális gépek terheléselosztási virtuális gépek](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [Zónaredundáns tárolás](../storage/common/storage-redundancy-zrs.md)
- [SQL Database](../sql-database/sql-database-high-availability.md#zone-redundant-configuration)
- [Event Hubs georeplikációs vészhelyreállítás](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Service Bus georeplikációs vészhelyreállítás](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Zónaredundáns virtuális hálózati átjáró létrehozása](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Zónaredundáns régió hozzáadása az Azure Cosmos DB-hez](../cosmos-db/high-availability.md#availability-zone-support)
- [Első lépések az Azure-gyorsítótára a Redis rendelkezésre állási zónáihoz](https://aka.ms/redis/az/getstarted)
- [Azure Active Directory Domain Services-példány létrehozása](../active-directory-domain-services/tutorial-create-instance.md)
- [Azure Kubernetes-szolgáltatás (AKS) fürt létrehozása, amely rendelkezésre állási zónákat használ](../aks/availability-zones.md)

## <a name="next-steps"></a>További lépések
- [Gyorsindítási sablonok](https://aka.ms/azqs)
