---
title: Mi a Azure Availability Zones?
description: Az Azure-ban kiválóan elérhető és rugalmas alkalmazások hozhatók létre, Availability Zones biztosítanak fizikailag különálló helyet, amellyel erőforrásokat futtathat.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 10/17/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: c9f4a418ac05b2618b4641c857e182e73c35d34c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79253467"
---
# <a name="what-are-availability-zones-in-azure"></a>Mi Availability Zones az Azure-ban?
A Availability Zones egy magas rendelkezésre állású ajánlat, amely védelmet nyújt alkalmazásai és adatai számára az adatközpont hibáiból. A rendelkezésre állási zónák egyedi fizikai helyszínek az Azure-régióban. Minden zóna egy vagy több adatközpont független áramellátással, hűtéssel és hálózati található tevődik össze. A rugalmasság biztosításához legalább három különálló zónának kell lennie az összes engedélyezett régióban. Egy régión belüli Availability Zones fizikai elkülönítése megvédi az alkalmazásokat és az adatközpontok meghibásodását. Zóna – a redundáns szolgáltatások az alkalmazások és az adatok replikálását Availability Zones az egypontos meghibásodások elleni védelem érdekében. Az Azure Availability Zones az iparág legjobb 99,99%-os rendelkezésre állását kínálja a virtuális gép számára. Az Azure egészére vonatkozó rendelkezésre állási garancia magyarázata a teljes [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)-ban található.

Az Azure-régiók rendelkezésre állási zónái egy tartalék tartomány és egy frissítési tartomány kombinációja. Ha például három vagy több virtuális gépet hoz létre három zónában egy Azure-régióban, a virtuális gépeket a rendszer gyakorlatilag három tartalék tartományba és három frissítési tartományba terjeszti. Az Azure platform felismeri ezt az eloszlást a frissítési tartományok között, hogy megbizonyosodjon róla, hogy a különböző zónákban lévő virtuális gépek nem frissülnek egyidőben.

Magas rendelkezésre állást építhet ki az alkalmazás-architektúrába a számítási, tárolási, hálózatkezelési és adaterőforrások egy zónán belüli és más zónákban való replikálásával. A Availability Zones támogató Azure-szolgáltatások két kategóriába sorolhatók:

- **Zónákra vonatkozó szolgáltatások** – az erőforrást egy adott zónához (például virtuális gépekhez, felügyelt lemezekhez, szabványos IP-címekhez) rögzítheti, vagy
- **Zóna – redundáns szolgáltatások** – a platform automatikusan replikálja a zónákat (például a zóna redundáns tárolását, SQL Database).

Az Azure-ban elérhető átfogó Üzletmenet-folytonosság érdekében az Azure region Pairs Availability Zones kombinációját használva hozza létre az alkalmazás-architektúrát. Az alkalmazásokat és az adatait szinkron módon replikálhatja az Azure-régión belüli Availability Zones használatával, hogy magas rendelkezésre állást biztosítson, és aszinkron módon replikálja az Azure-régiókat a vész-helyreállítási védelem érdekében.
 
![egy régió egy zónájának elméleti nézete](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> A rendelkezésre állási zónák azonosítói (a fenti képen az 1, 2 és 3 számok) logikai módon vannak leképezve az egyes előfizetések tényleges fizikai zónájához, egymástól függetlenül. Ez azt jelenti, hogy egy adott előfizetéshez tartozó rendelkezésre állási 1. zóna eltérő fizikai zónára hivatkozhatnak, mint a rendelkezésre állás 1. zóna egy másik előfizetésben. Ezért javasoljuk, hogy a rendelkezésre állási zónák azonosítóit ne használja a virtuális gépek elhelyezésére szolgáló különböző előfizetések között.

## <a name="services-support-by-region"></a>Szolgáltatások támogatása régiónként

A Availability Zones támogató Azure-szolgáltatások és-régiók kombinációi a következők:


|                                 |Amerika |              |           |           | Európa |              |          |              | Ázsia és a Csendes-óceáni térség |                 |
|----------------------------|----------|----------|---------|---------|--------------|------------|--------|----------|----------|-------------|
|          |USA középső régiója|USA keleti régiója|USA 2. keleti régiója|USA nyugati régiója, 2.|Közép-Franciaország|Észak-Európa|Az Egyesült Királyság déli régiója|Nyugat-Európa|Kelet-Japán|Délkelet-Ázsia|
| **Számítás**                         |            |              |           |           |                |              |          |             |            |                |
| Linux rendszerű virtuális gépek          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Windows Virtual Machines        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Virtual Machine Scale Sets      | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Azure App Service környezetek ILB | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Azure Kubernetes Service        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Storage**   |            |              |           |           |                |              |          |             |            |                |
| Felügyelt lemezek                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Zóna – redundáns tárolás          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Hálózat**                     |            |              |           |           |                |              |          |             |            |                |
| Standard IP Address        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| standard Load Balancer     | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| VPN Gateway            | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| ExpressRoute-átjáró   | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| Application Gateway (v2)    | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| Azure Firewall           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |
| **Adatbázisok**                     |            |              |           |           |                |              |          |             |            |                |
| Azure Data Explorer                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;        | &#10003;       |
| SQL-adatbázis                    | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;Előnézet      | &#10003;       |
| Azure Cache for Redis           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |
| Azure Cosmos DB                    | &#10003;   |  &#10003;  |  &#10003; | &#10003; |       |     | &#10003; |  &#10003;   |            | &#10003;       |
| **Elemzés**                       |            |              |           |           |                |              |          |             |            |                |
| Event Hubs                      | &#10003;   |   &#10003; | &#10003;  | &#10003;  | &#10003; | &#10003; | &#10003; | &#10003; | &#10003; | &#10003;       |
| **Integrációs**                     |            |              |           |           |                |              |          |             |            |                |
| Service Bus (csak prémium szint esetén) | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |
| Event Grid | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |
| **Identitáskezelés**                     |            |              |           |           |                |              |          |             |            |                |
| Azure AD Domain Services | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |

## <a name="services-resiliency"></a>Szolgáltatások rugalmassága
Az összes Azure-beli felügyeleti szolgáltatás a régió szintű hibáktól való ellenálló képességnek számít. A hibák spektrumában egy vagy több rendelkezésre állási zónának a régión belüli meghibásodása kisebb meghibásodási sugarú, mint a teljes régió meghibásodása. Az Azure a régióban vagy egy másik Azure-régión belüli felügyeleti szolgáltatások zóna szintű meghibásodásával állítható helyre. Az Azure a régión belül egyszerre végzi el a kritikus karbantartást, így megakadályozva, hogy az adott régión belül üzembe helyezett felhasználói erőforrásokkal kapcsolatos hibák ne legyenek Availability Zones.

## <a name="pricing"></a>Díjszabás
A rendelkezésre állási zónában üzembe helyezett virtuális gépekhez nem kell további díjat fizetni. 99,99%-os virtuálisgép-üzemidőt biztosító SLA-t akkor ajánljuk, ha legalább két virtuális GÉPET telepítenek két vagy több Availability Zones egy Azure-régión belül. A virtuális gépek és a virtuális gépek közötti adatátviteli díjak további rendelkezésre állási zónában lesznek. További információkért tekintse át a [sávszélesség díjszabását](https://azure.microsoft.com/pricing/details/bandwidth/) ismertető oldalt.


## <a name="get-started-with-availability-zones"></a>Ismerkedés a Availability Zones
- [Virtuális gép létrehozása](../virtual-machines/windows/create-portal-availability-zone.md)
- [Felügyelt lemez hozzáadása a PowerShell használatával](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Zónában redundáns virtuálisgép-méretezési csoport létrehozása](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Virtuális gépek terheléselosztása zónák között egy olyan standard Load Balancer használatával, amely zóna nélküli előtérbeli felületet használ](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [Virtuális gépek terheléselosztása zónán belül egy standard Load Balancer és egy zóna-előtérbeli felület használatával](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [Zónaredundáns tárolás](../storage/common/storage-redundancy-zrs.md)
- [SQL Database](../sql-database/sql-database-high-availability.md#zone-redundant-configuration)
- [Event Hubs georeplikációs vészhelyreállítás](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Service Bus georeplikációs vészhelyreállítás](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Zónaredundáns virtuális hálózati átjáró létrehozása](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Zóna redundáns régiójának hozzáadása Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Azure cache Első lépések Redis Availability Zones](https://aka.ms/redis/az/getstarted)
- [Azure Active Directory Domain Services példány létrehozása](../active-directory-domain-services/tutorial-create-instance.md)
- [Availability Zonest használó Azure Kubernetes-szolgáltatásbeli (ak-beli) fürt létrehozása](../aks/availability-zones.md)

## <a name="next-steps"></a>További lépések
- [Gyorsindítási sablonok](https://aka.ms/azqs)
