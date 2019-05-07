---
title: Mik az Azure-beli rendelkezésre állási zónák? | Microsoft Docs
description: Magas rendelkezésre állású és rugalmas alkalmazások létrehozása az Azure-ban, a rendelkezésre állási zónák fizikailag elkülönített helyek használatával futtassa az erőforrások biztosítják.
services: ''
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 31c85c4911642ece588340bd0e4a3a814aae4a20
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65200764"
---
# <a name="what-are-availability-zones-in-azure"></a>Mik azok a rendelkezésre állási zónák az Azure-ban?
A rendelkezésre állási zónák a magas rendelkezésre állást kínál, amely megvédi alkalmazásait és adatait az adatközpontok meghibásodásai. A rendelkezésre állási zónák egyedi fizikai helyszínek az Azure-régióban. Minden zóna egy vagy több adatközpont független áramellátással, hűtéssel és hálózati található tevődik össze. Rugalmasság biztosítása érdekében legalább három különálló zónát, az összes engedélyezett régióban van. Egy adott régión belül a rendelkezésre állási zónák fizikai elválasztása adatközpont meghibásodása ellen védi az alkalmazásokat és adatokat. Zónaredundáns szolgáltatásokat az alkalmazások és adatok replikálása single-pontok-az-hibákkal szembeni rendelkezésre állási zónák között. A rendelkezésre állási zónákban az Azure iparági ajánlott 99,99 %-os virtuális gép ÜZEMIDŐT biztosít. Az Azure egészére vonatkozó rendelkezésre állási garancia magyarázata a teljes [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)-ban található.

Egy rendelkezésre állási zónában az Azure-régióban a tartalék tartomány és a egy frissítési tartományt. Például, ha több Azure-régióban három zónában legalább három virtuális gépet hoz létre, a virtuális gépek hatékony és között elosztott három tartalék tartományt három frissítési tartományt. Az Azure platform felismeri a terjesztéshez győződjön meg arról, hogy a különböző zónában lévő virtuális gépek nem frissülnek egy időben, a frissítési tartományok között.

Magas rendelkezésre állás beépítése az alkalmazásarchitektúra közös helyen való elhelyezése a számítási, tárolási, hálózati és adatok erőforrások zónán belül, és a más zónákban replikálja. Azure-szolgáltatások rendelkezésre állási zónákat támogató két kategóriába sorolhatók:

- **A zónaszintű szolgáltatások** – egy adott zónához (például virtuális gépek, a felügyelt lemezek, IP-címek), az erőforrást rögzítheti, vagy
- **Zónaredundáns szolgáltatások** – platform automatikusan replikál (például a zónaredundáns tárolás, az SQL Database) zónák között.

Az Azure-ban átfogó üzleti folytonossági eléréséhez hozhat létre a rendelkezésre állási zónák együttes használata az Azure-régióban párok architektúra. Is szinkrón replikálása a alkalmazásait és adatait a rendelkezésre állási zónák használatával egy Azure-régióban a magas rendelkezésre állású és vész-helyreállítási védelem az Azure-régióban aszinkron módon replikálja.
 
![egy régióban leáll. egy zóna áttekintése](./media/az-overview/az-graphic-two.png)

## <a name="services-support-by-region"></a>Régiónként szolgáltatások támogatása

Az Azure-szolgáltatások és a rendelkezésre állási zónákat támogató régiók kombinációi a következők:


|                                 |Amerika |              |           |           | Európa |              |          |              | Ázsia és a Csendes-óceáni térség |                 |
|----------------------------|----------|----------|---------|---------|--------------|------------|--------|----------|----------|-------------|
|          |USA középső régiója|USA keleti régiója|USA 2. keleti régiója|USA nyugati régiója, 2.|Közép-Franciaország|Észak-Európa|Az Egyesült Királyság déli régiója|Nyugat-Európa|Kelet-Japán|Délkelet-Ázsia|
| **Számítás**                         |            |              |           |           |                |              |          |             |            |                |
| Linux rendszerű virtuális gépek          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Windows rendszerű virtuális gépek        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Virtual Machine Scale Sets      | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Tárolás**   |            |              |           |           |                |              |          |             |            |                |
| Felügyelt lemezek                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Zone-redundant Storage          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Hálózat**                     |            |              |           |           |                |              |          |             |            |                |
| Standard IP Address        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Standard Load Balancer     | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| VPN Gateway                     | &#10003;   |              | &#10003;  | &#10003;  | &#10003;       | &#10003;     |          | &#10003;    |            | &#10003;       |
| ExpressRoute                    | &#10003;   |              | &#10003;  | &#10003;  | &#10003;       | &#10003;     |          | &#10003;    |            | &#10003;       |
| Application Gateway   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     |          | &#10003;    |            | &#10003;       |
| **Adatbázisok**                     |            |              |           |           |                |              |          |             |            |                |
| SQL Database                    | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |            | &#10003;       |
| **Elemzés**                       |            |              |           |           |                |              |          |             |            |                |
| Event Hubs                      | &#10003;   |              | &#10003;  | &#10003;  | &#10003;       | &#10003;     |          | &#10003;    |            | &#10003;       |
| **Integráció**                     |            |              |           |           |                |              |          |             |            |                |
| A Service Bus (prémium szintű csomag esetében) | &#10003;   |              | &#10003;  | &#10003;  | &#10003;       | &#10003;     |          | &#10003;    |            | &#10003;       |



## <a name="services-resiliency"></a>Szolgáltatások rugalmasság
Minden Azure management szolgáltatás kihasználására tervezett is ellenáll a terület-szint hibák után. Hibák a különböző rendelkezésre állási zónában egy vagy több hiba egy adott régión belül egy teljes régióra hibát képest kisebb hiba radius rendelkezik. Az Azure felügyeleti szolgáltatások a régión belül a zónaszintű meghibásodása vagy egy másik Azure-régióból állíthatja helyre. Az Azure legfontosabb karbantartási egy zóna ugyanabban a régióban, egy adott régión belül a rendelkezésre állási zónában üzembe helyezett ügyfélerőforrásait érintő hibák elkerülése érdekében egyszerre hajtja végre.

## <a name="pricing"></a>Díjszabás
Nincs rendelkezésre állási zónában üzembe helyezett virtuális gépeken további költség nélkül. 99,99 %-os virtuális gép SLA érhető el, ha két vagy több virtuális gép két vagy több rendelkezésre állási zónában az Azure-régióban vannak telepítve. További közötti rendelkezésre állási zóna virtuális Gépeken adatátviteli díjak lesznek. További információkért tekintse át a [sávszélesség díjszabás](https://azure.microsoft.com/pricing/details/bandwidth/) lapot.


## <a name="get-started-with-availability-zones"></a>A rendelkezésre állási zónák használatának első lépései
- [Virtuális gép létrehozása](../virtual-machines/windows/create-portal-availability-zone.md)
- [Adjon hozzá egy felügyelt lemezt PowerShell-lel](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Egy zóna zónaredundáns virtuálisgép-méretezési csoport létrehozása](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Virtuális gépek terheléselosztása zónákban a zónaredundáns előtér egy Standard Load Balancer használatával](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [Virtuális gépek terheléselosztása Standard Load Balancer használatával a zónaszintű előtérbeli zónán belül](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [Zónaredundáns tárolás](../storage/common/storage-redundancy-zrs.md)
- [SQL Database](../sql-database/sql-database-high-availability.md#zone-redundant-configuration)
- [Event Hubs georeplikációs vészhelyreállítás](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Service Bus georeplikációs vészhelyreállítás](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Zónaredundáns virtuális hálózati átjáró létrehozása](../vpn-gateway/create-zone-redundant-vnet-gateway.md)


## <a name="next-steps"></a>További lépések
- [Gyorsindítási sablonok](https://aka.ms/azqs)
