---
title: Mik az Azure rendelkezésre állási zónák? | Microsoft Docs
description: Magas rendelkezésre állású és rugalmas alkalmazások létrehozása az Azure-ban, a rendelkezésre állási zónák segítségével a fizikailag különböző helyeken az erőforrások futtatásához használhatja.
services: ''
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/08/2018
ms.author: iainfou
ms.custom: mvc I am an ITPro and application developer, and I want to protect (use Availability Zones) my applications and data against data center failure (to build Highly Available applications).
ms.openlocfilehash: 7cfd3fba459f409079cb0c621edab3197070285e
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232347"
---
# <a name="what-are-availability-zones-in-azure"></a>Mik azok a rendelkezésre állási zónák az Azure-ban?
Rendelkezésre állási zónák magas rendelkezésre állású ajánlat, amely megakadályozza az alkalmazások és adatok datacenter hiba. Rendelkezésre állási zónák egyedi fizikai helyen belül egy Azure-régióban. Az egyes zónák felszerelt független power hűtési, és a hálózat egy vagy több adatközpontok épül fel. Rugalmasság biztosítása érdekében legalább három különálló zónákra minden engedélyezett régióban van. A fizikailag szét van választva a rendelkezésre állási zónák régión belül alkalmazások és adatok datacenter hibák védelmet nyújt. Zónaredundáns szolgáltatások rendelkezésre állásának zónák nyújt védelmet a single-pontok-az-szemben az alkalmazások és adatok replikálásához. A rendelkezésre állási zónák Azure iparág ajánlott 99,99 % VM hasznos üzemidő SLA-t kínál. Az Azure egészére vonatkozó rendelkezésre állási garancia magyarázata a teljes [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)-ban található.

Egy rendelkezésre állási zóna Azure-régióban egy tartalék tartomány és a frissítési tartomány kombinációja. Például különböző Azure-régióban három zónák három vagy több virtuális gépet hoz létre, ha a virtuális gépek hatékony és között elosztott három tartalék tartományok három frissítési tartomány. Az Azure platformon felismeri a terjesztéshez győződjön meg arról, hogy a különböző szintű zónákban levő virtuális gépek nem frissülnek a egyszerre frissítési tartományok között.

Kibővített kialakítást hozhat létre magas rendelkezésre állású be az alkalmazás architektúrák együttes elhelyezésével a zónán belül a számítási, tárolási, hálózatkezelési és adatok erőforrások és a többi replikálásához. Azure-szolgáltatásokat, amely támogatja a rendelkezésre állási zónák két kategóriába sorolhatók:

- **Zonal szolgáltatások** – rögzíti a megadott zónában (például virtuális gépek, kezelt lemezeken, IP-címek), az erőforrás vagy
- **Zónaredundáns szolgáltatások** – platform automatikusan zónák (például zónaredundáns tárolás, az SQL-adatbázis) keresztül replikálásra.

Átfogó üzleti folytonosságot az Azure-on, hogy a rendelkezésre állási zónák használatával az Azure-régió párokkal alkalmazásarchitektúra felépítéséhez. Szinkron replikálása az alkalmazásokat és adatokat, rendelkezésre állási zónák belül egy Azure-régió, magas rendelkezésre állású, és aszinkron módon vész-helyreállítási védelem Azure-régiók közötti replikáció.
 
![egy zóna régióban fog konceptuális ábrázolása](./media/az-overview/az-graphic-two.png)

## <a name="regions-that-support-availability-zones"></a>Rendelkezésre állási zónák támogató régiók

- USA középső régiója
- Közép-Franciaország
- USA keleti régiója 2 (előzetes verzió)
- Nyugat-Európában (előzetes verzió)
- Délkelet-Ázsia (előzetes verzió)


## <a name="services-that-support-availability-zones"></a>Rendelkezésre állási zónák támogató szolgáltatások
Azure-szolgáltatás, amely támogatja a rendelkezésre állási zónák a következők:

- Linux rendszerű virtuális gépek
- Windows rendszerű virtuális gépek
- Virtual Machine Scale Sets
- Felügyelt lemezek
- Load Balancer
- Nyilvános IP-cím
- Zónaredundáns tárolás
- SQL Database


## <a name="pricing"></a>Díjszabás
Nincs további költség nélkül üzembe helyezett egy rendelkezésre állási zóna virtuális gépekhez. 99,99 % VM hasznos üzemidő SLA tartományregisztráció két vagy több virtuális gépet egy Azure-régiót belül két vagy több rendelkezésre állási zóna között telepítésekor. További nagyfokú rendelkezésre állás zóna VM-VM adatok adatátviteli díjakkal lesz. További információkért tekintse át a [sávszélesség árképzési](https://azure.microsoft.com/pricing/details/bandwidth/) lap.


## <a name="get-started-with-availability-zones"></a>Ismerkedés a rendelkezésre állási zónák
- [Virtuális gép létrehozása](../virtual-machines/windows/create-portal-availability-zone.md)
- [Adjon hozzá egy felügyelt lemezt PowerShell használatával](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Egy zóna redundáns virtuálisgép-méretezési csoport létrehozása](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Terheléselosztás virtuális gépek a szabványos terheléselosztó zónaredundáns időtúllépést a zónák között](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [Terheléselosztás virtuális gépek a szabványos terheléselosztó zonal időtúllépést a zónában](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [Zónaredundáns tárolás](../storage/common/storage-redundancy-zrs.md)
- [SQL Database](../sql-database/sql-database-high-availability.md#zone-redundant-configuration-preview)


## <a name="next-steps"></a>További lépések
- [Gyorsindítási sablonok](http://aka.ms/azqs)
