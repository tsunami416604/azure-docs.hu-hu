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
ms.date: 08/31/2018
ms.author: cynthn
ms.custom: mvc I am an ITPro and application developer, and I want to protect (use Availability Zones) my applications and data against data center failure (to build Highly Available applications).
ms.openlocfilehash: 665db66bc7302710ed73ae46aad0e9afcfd44400
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258458"
---
# <a name="what-are-availability-zones-in-azure"></a>Mik azok a rendelkezésre állási zónák az Azure-ban?
A rendelkezésre állási zónák a magas rendelkezésre állást kínál, amely megvédi alkalmazásait és adatait az adatközpontok meghibásodásai. A rendelkezésre állási zónák egyedi fizikai helyszínek az Azure-régióban. Minden zóna egy vagy több adatközpont független áramellátással, hűtéssel és hálózati található tevődik össze. Rugalmasság biztosítása érdekében legalább három különálló zónát, az összes engedélyezett régióban van. Egy adott régión belül a rendelkezésre állási zónák fizikai elválasztása adatközpont meghibásodása ellen védi az alkalmazásokat és adatokat. Zónaredundáns szolgáltatásokat az alkalmazások és adatok replikálása single-pontok-az-hibákkal szembeni rendelkezésre állási zónák között. A rendelkezésre állási zónákban az Azure iparági ajánlott 99,99 %-os virtuális gép ÜZEMIDŐT biztosít. Az Azure egészére vonatkozó rendelkezésre állási garancia magyarázata a teljes [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)-ban található.

Egy rendelkezésre állási zónában az Azure-régióban a tartalék tartomány és a egy frissítési tartományt. Például, ha több Azure-régióban három zónában legalább három virtuális gépet hoz létre, a virtuális gépek hatékony és között elosztott három tartalék tartományt három frissítési tartományt. Az Azure platform felismeri a terjesztéshez győződjön meg arról, hogy a különböző zónában lévő virtuális gépek nem frissülnek egy időben, a frissítési tartományok között.

Magas rendelkezésre állás beépítése az alkalmazásarchitektúra közös helyen való elhelyezése a számítási, tárolási, hálózati és adatok erőforrások zónán belül, és a más zónákban replikálja. Azure-szolgáltatások rendelkezésre állási zónákat támogató két kategóriába sorolhatók:

- **A zónaszintű szolgáltatások** – egy adott zónához (például virtuális gépek, a felügyelt lemezek, IP-címek), az erőforrást rögzítheti, vagy
- **Zónaredundáns szolgáltatások** – platform automatikusan replikál (például a zónaredundáns tárolás, az SQL Database) zónák között.

Az Azure-ban átfogó üzleti folytonossági eléréséhez hozhat létre a rendelkezésre állási zónák együttes használata az Azure-régióban párok architektúra. Is szinkrón replikálása a alkalmazásait és adatait a rendelkezésre állási zónák használatával egy Azure-régióban a magas rendelkezésre állású és vész-helyreállítási védelem az Azure-régióban aszinkron módon replikálja.
 
![egy régióban leáll. egy zóna áttekintése](./media/az-overview/az-graphic-two.png)

## <a name="regions-that-support-availability-zones"></a>Rendelkezésre állási zónákat támogató régiók

- USA középső régiója
- USA 2. keleti régiója
- Közép-Franciaország
- Észak-Európa
- Délkelet-Ázsia 
- Nyugat-Európa
- USA nyugati régiója, 2.



## <a name="services-that-support-availability-zones"></a>Rendelkezésre állási zónákat támogató szolgáltatások
Az Azure-szolgáltatások, amelyek támogatják a rendelkezésre állási zónák a következők:

- Linux rendszerű virtuális gépek
- Windows rendszerű virtuális gépek
- Virtual Machine Scale Sets
- Felügyelt lemezek
- Load Balancer
- Nyilvános IP-cím
- Zónaredundáns tárolás
- SQL Database
- Event Hubs
- A Service Bus (prémium szintű csomag esetében)
- VPN Gateway
- ExpressRoute
- Az Application Gateway (előzetes verzió)


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
- [Event Hubs georeplikációs vészhelyreállítás](../event-hubs/event-hubs-geo-dr.md#availability-zones-preview)
- [Service Bus georeplikációs vészhelyreállítás](../service-bus-messaging/service-bus-geo-dr.md#availability-zones-preview)
- [Zónaredundáns virtuális hálózati átjáró létrehozása](../vpn-gateway/create-zone-redundant-vnet-gateway.md)


## <a name="next-steps"></a>További lépések
- [Gyorsindítási sablonok](https://aka.ms/azqs)
