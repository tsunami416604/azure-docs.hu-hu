---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/10/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 1582f61befb9b6d71adbfda9482175d67874ffb9
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850274"
---
# <a name="availability-options-for-virtual-machines-in-azure"></a>Az Azure-beli virtuális gépek rendelkezésre állási lehetőségei
Ez a cikk áttekintést nyújt az Azure Virtual Machines (VM) rendelkezésre állási funkcióiról.


## <a name="availability-sets"></a>Rendelkezésre állási csoportok
A rendelkezésre állási csoport a virtuális gépek logikai csoportosítása az adatközponton belül, amely lehetővé teszi az Azure számára, hogy megtudja, hogyan épülnek fel az alkalmazás a redundancia és a rendelkezésre állás érdekében. Javasoljuk, hogy legalább két virtuális gépet hozzon létre egy rendelkezésre állási csoporton belül, hogy egy magasan elérhető alkalmazást biztosítson, és kielégítse a [99,95%-os Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)-t. Magának a rendelkezésre állási csoportnak nincs díja, csak a létrehozott virtuálisgép-példányért kell fizetnie. Ha egyetlen virtuális gép használja az [Azure Premium SSD](../articles/virtual-machines/windows/disks-types.md#premium-ssd)-ket, az Azure SLA a nem tervezett karbantartási eseményekre vonatkozik.

A rendelkezésre állási csoport két további, a hardverhiba elleni védelemhez és a frissítések biztonságos alkalmazásához (tartalék) és frissítési tartományokhoz (frissítési) való hozzáférésének engedélyezése. Tudjon meg többet a [Linux-alapú virtuális gépek](../articles/virtual-machines/linux/manage-availability.md), illetve a [Windows-alapú virtuális gépek](../articles/virtual-machines/windows/manage-availability.md) rendelkezésre állásának kezeléséről.

### <a name="fault-domains"></a>Tartalék tartományok
A tartalék tartomány a mögöttes hardverelemek logikus csoportja, ahol az áramforrás és a hálózati kapcsoló közös, a helyszíni adatközpontok állványaihoz hasonlóan. Amikor rendelkezésre állási csoporton belül hoz létre virtuális gépeket, az Azure platform automatikusan elosztja ezeket a tartalék tartományok között. Ez a módszer korlátozza a potenciális hardvermeghibásodások, hálózatkimaradások vagy a tápellátás megszakadásának hatását.

### <a name="update-domains"></a>Frissítési tartományok
A frissítési tartomány a mögöttes hardverelemek logikus csoportja, amelyen egyszerre végezhető karbantartás vagy újraindítás. Amikor rendelkezésre állási csoporton belül hoz létre virtuális gépeket, az Azure platform automatikusan elosztja ezeket a frissítési tartományok között. Ez lehetővé teszi, hogy az alkalmazás legalább egy példánya mindig fusson akkor is, ha az Azure platformon épp rendszeres karbantartás folyik. A frissítési tartományok újraindítása nem haladhat szekvenciálisan a tervezett karbantartás során, hanem csak egyetlen frissítési tartományt lehet újraindítani egyszerre.

![Rendelkezésre állási csoportok](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

### <a name="managed-disk-fault-domains"></a>Felügyelt lemezes tartalék tartományok
Az [Azure Managed Disks](../articles/virtual-machines/windows/faq-for-disks.md) használatával létrehozott virtuális gépek felügyelt rendelkezésre állási csoportok használata esetén felügyelt lemezes tartalék tartományokra vannak elosztva. Ez a kiosztás biztosítja, hogy a virtuális géphez csatolt mindegyik felügyelt lemez ugyanabban a felügyelt lemezes tartalék tartományban legyen. Kizárólag felügyelt lemezeken futó virtuális gépek hozhatók létre felügyelt rendelkezésre állási csoportokban. A felügyelt lemezes tartalék tartományok száma régiónként eltérő – régiónként kettő vagy három felügyelt lemezes tartalék tartomány lehet. Ezekről a felügyelt lemezes tartalék tartományokról a [Linux rendszerű virtuális gépek](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) vagy a [Windows rendszerű virtuális gépek](../articles/virtual-machines/windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set)esetében olvashat bővebben.

![Felügyelt rendelkezésre állási csoport](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

## <a name="availability-zones"></a>Rendelkezésre állási zónák

[Rendelkezésre állási zónák](../articles/availability-zones/az-overview.md), a rendelkezésre állási csoportok alternatívája, a vezérlési szint kibontásával a virtuális gépeken elérhető alkalmazások és adatmennyiségek rendelkezésre állását kell fenntartania. A rendelkezésre állási zónák egy Azure-régió fizikailag elkülönített zónáit jelentik. Egy támogatott Azure-régióban három Availability Zones érhető el. Mindegyik rendelkezésre állási zóna különálló áramforrással, hálózattal és hűtéssel rendelkezik. Ha a megoldásait a zónákban lévő replikált virtuális gépek használatára kívánja használni, az alkalmazások és az adatok az adatközpont elvesztése miatt is védhetők. Ha egy zóna biztonsága sérül, a replikált alkalmazások és az adatszolgáltatások azonnal elérhetők lesznek egy másik zónában. 

![Rendelkezésre állási zónák](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

További információ a [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) vagy [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) rendszerű virtuális gépek rendelkezésre állási zónában való üzembe helyezéséről.


## <a name="next-steps"></a>További lépések
Mostantól a saját Azure-környezetében is használhatja ezeket a rendelkezésre állási és redundanciával kapcsolatos szolgáltatásokat. Javasoljuk, hogy tájékozódjon [az Azure rendelkezésre állásával kapcsolatos ajánlott eljárásokról](../articles/best-practices-availability-checklist.md).

