---
title: Az Azure virtuális gép foglalási hibáinak hibaelhárítása a klasszikus telepítési modellben| Microsoft dokumentumok
description: Klasszikus virtuális gép létrehozásakor, újraindításakor vagy átméretezésekor a foglalási hibák hibáinak elhárítása az Azure-ban
services: azure-service-management
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: bb939e23-77fc-4948-96f7-5037761c30e8
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 20e64e5225987a8045e406a0e8fcae098c580c61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77913378"
---
# <a name="troubleshooting-steps-specific-to-allocation-failure-scenarios-in-the-classic-deployment-model"></a>A klasszikus üzemi modell foglalási hibáira vonatkozó hibaelhárítási lépések

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Az alábbiakban olyan gyakori felosztási forgatókönyvek láthatók, amelyek a felosztási kérelem rögzítését eredményezik. A cikk későbbi részében minden egyes forgatókönyvet bevethet.

- Virtuális gép átméretezése, virtuális gépek vagy szerepkörpéldányok hozzáadása meglévő felhőszolgáltatáshoz
- Részlegesen leállított (felszabadított) virtuális gépek újraindítása
- Teljesen leállított (felszabadított) virtuális gépek újraindítása
- Átmeneti és éles környezetek (csak platformként)
- Affinitáscsoport (virtuális gép vagy szolgáltatás közelsége)
- Affinitás–csoport alapú virtuális hálózat

Ha foglalási hibát kap, ellenőrizze, hogy a felsorolt esetek bármelyike vonatkozik-e a hibára. Használja az Azure platform által visszaadott foglalási hibát a megfelelő forgatókönyv azonosításához. Ha a kérelem rögzítve van, távolítsa el a rögzítési kényszerek egy részét a kérelem további fürtök számára való megnyitásához, ezáltal növelve a foglalás sikeressításának esélyét.
Általában, ha a hiba nem jelenti azt, hogy "a kért virtuális gép mérete nem támogatott", bármikor újra egy későbbi időpontban. Ennek az az oka, hogy elegendő erőforrás felszabadult a fürtben a kérés teljesítéséhez. Ha a probléma az, hogy a kért virtuális gép mérete nem támogatott, próbálkozzon egy másik virtuális gép méretével. Ellenkező esetben az egyetlen lehetőség a rögzítési megkötés eltávolítása.

Két gyakori hiba forgatókönyvek affinitáscsoportok kapcsolatos. A múltban egy affinitáscsoportot használtak a virtuális gépek és a szolgáltatáspéldányok közvetlen közelségének biztosításához, vagy egy virtuális hálózat létrehozásának engedélyezésére használták. A regionális virtuális hálózatok bevezetésével az affinitáscsoportoknak már nincs szükség virtuális hálózat létrehozásához. A hálózati késés csökkentése az Azure-infrastruktúrában, a virtuális gépek vagy a szolgáltatás közelségének affinitáscsoportjainak használatára vonatkozó javaslat megváltozott.

Az alábbi ábra a (rögzített) felosztási forgatókönyvek rendszerbesorarásját mutatja be. 

![Rögzített felosztási rendszer, amely](./media/virtual-machines-common-allocation-failure/Allocation3.png)

## <a name="resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>Virtuális gép átméretezése, virtuális gépek vagy szerepkörpéldányok hozzáadása meglévő felhőszolgáltatáshoz
**Hiba**

Upgrade_VMSizeNotSupported vagy Általános hiba

**A fürt rögzítésének oka**

A virtuális gép átméretezésére, illetve egy virtuális gép vagy egy szerepkörpéldány meglévő felhőszolgáltatáshoz való hozzáadására vonatkozó kérést meg kell kíséreljen megkísérelni a meglévő felhőszolgáltatást üzemeltető eredeti fürtnél. Egy új felhőszolgáltatás létrehozása lehetővé teszi, hogy az Azure-platform keressen egy másik fürt, amely rendelkezik a szabad erőforrásokat, vagy támogatja a virtuális gép méretét, amit kért.

**Workaround**

Ha a hiba Upgrade_VMSizeNotSupported*, próbálkozzon egy másik virtuális gép méretével. Ha egy másik virtuális gép mérete nem egy lehetőség, de ha elfogadható egy másik virtuális IP-cím (VIP), hozzon létre egy új felhőszolgáltatás az új virtuális gép üzemeltetéséhez, és adja hozzá az új felhőszolgáltatás a regionális virtuális hálózat, ahol a meglévő virtuális gépek futnak. Ha a meglévő felhőszolgáltatás nem használ regionális virtuális hálózatot, akkor is létrehozhat egy új virtuális hálózatot az új felhőszolgáltatáshoz, majd csatlakoztathatja a [meglévő virtuális hálózatot az új virtuális hálózathoz.](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/) További információk a [regionális virtuális hálózatokról.](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/)

Ha a hiba GeneralError*, valószínű, hogy a fürt támogatja az erőforrás típusát (például egy adott virtuális gép méretét), de a fürt jelenleg nem rendelkezik szabad erőforrásokkal. A fenti forgatókönyvhöz hasonlóan adja hozzá a kívánt számítási erőforrást egy új felhőszolgáltatás létrehozásával (vegye figyelembe, hogy az új felhőszolgáltatásnak egy másik VIRTUÁLIS VIRTUÁLIS GÉPET kell használnia), és használjon regionális virtuális hálózatot a felhőszolgáltatások csatlakoztatásához.

## <a name="restart-partially-stopped-deallocated-vms"></a>Részlegesen leállított (felszabadított) virtuális gépek újraindítása
**Hiba**

Általános hiba*

**A fürt rögzítésének oka**

Részleges feloldás a szolgáltatás azt jelenti, hogy leállította (felszabadított) egy vagy több, de nem az összes, virtuális gépek egy felhőalapú szolgáltatásban. Amikor leállítja (felszabadítja) a virtuális gép, a kapcsolódó erőforrások felszabadulnak. A leállított (felszabadított) virtuális gép újraindítása ezért új foglalási kérelem. A virtuális gépek újraindítása egy részlegesen felszabadított felhőszolgáltatásban egyenértékű a virtuális gépek meglévő felhőszolgáltatáshoz való hozzáadásával. A foglalási kérelmet meg kell kísérelni az eredeti fürt, amely a meglévő felhőszolgáltatás. Egy másik felhőalapú szolgáltatás létrehozása lehetővé teszi, hogy az Azure-platform keressen egy másik fürt, amely ingyenes erőforrást, vagy támogatja a virtuális gép méretét, amit kért.

**Workaround**

Ha egy másik virtuális ip-cím használata elfogadható, törölje a leállított (felszabadított) virtuális gépeket (de tartsa meg a kapcsolódó lemezeket), és adja hozzá a virtuális gépeket egy másik felhőszolgáltatáson keresztül. A felhőszolgáltatások csatlakoztatásához használjon regionális virtuális hálózatot:

* Ha a meglévő felhőszolgáltatás egy regionális virtuális hálózatot használ, egyszerűen adja hozzá az új felhőszolgáltatást ugyanahhoz a virtuális hálózathoz.
* Ha a meglévő felhőszolgáltatás nem használ regionális virtuális hálózatot, hozzon létre egy új virtuális hálózatot az új felhőszolgáltatáshoz, majd [csatlakoztassa a meglévő virtuális hálózatot az új virtuális hálózathoz.](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/) További információk a [regionális virtuális hálózatokról.](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/)

## <a name="restart-fully-stopped-deallocated-vms"></a>Teljesen leállított (felszabadított) virtuális gépek újraindítása
**Hiba**

Általános hiba*

**A fürt rögzítésének oka**

A teljes feloldás azt jelenti, hogy leállította (felszabadította) az összes virtuális gépet egy felhőszolgáltatásból. A virtuális gépek újraindítására irányuló foglalási kérelmeket meg kell kísérelni a felhőszolgáltatást üzemeltető eredeti fürtben. Egy új felhőszolgáltatás létrehozása lehetővé teszi, hogy az Azure-platform keressen egy másik fürt, amely rendelkezik a szabad erőforrásokat, vagy támogatja a virtuális gép méretét, amit kért.

**Workaround**

Ha egy másik virtuális ip-cím használata elfogadható, törölje az eredeti leállított (felszabadított) virtuális gépeket (de tartsa meg a kapcsolódó lemezeket), és törölje a megfelelő felhőszolgáltatást (a kapcsolódó számítási erőforrások már fel voltak szabadítva, amikor leállította (felszabadította) a virtuális gépeket). Hozzon létre egy új felhőszolgáltatást a virtuális gépek visszaadásához.

## <a name="stagingproduction-deployments-platform-as-a-service-only"></a>Átmeneti/éles környezetek (csak szolgáltatásként platform)
**Hiba**

New_General* vagy New_VMSizeNotSupported*

**A fürt rögzítésének oka**

Az átmeneti központi telepítés és a felhőszolgáltatás éles telepítése ugyanabban a fürtben található. A második központi telepítés hozzáadásakor a megfelelő foglalási kérelem megkísérli az első központi telepítést tartalmazó fürtben.

**Workaround**

Törölje az első üzembe helyezést és az eredeti felhőszolgáltatást, és telepítse újra a felhőszolgáltatást. Ez a művelet az első üzembe helyezésegy olyan fürtben, amely elegendő szabad erőforrásokkal rendelkezik ahhoz, hogy mindkét központi telepítéshez illeszkedjen, vagy egy olyan fürtben, amely támogatja a virtuális gép által kért méretet.

## <a name="affinity-group-vmservice-proximity"></a>Affinitáscsoport (Virtuálisgép/szolgáltatás közelsége)
**Hiba**

New_General* vagy New_VMSizeNotSupported*

**A fürt rögzítésének oka**

Az affinitáscsoporthoz rendelt számítási erőforrások egy fürthöz vannak kötve. Az adott affinitáscsoportban lévő új számítási erőforrás-kérelmeket ugyanabban a fürtben kísérelik meg, ahol a meglévő erőforrások találhatók. Ez igaz, hogy az új erőforrások egy új felhőszolgáltatás vagy egy meglévő felhőszolgáltatás keresztül jönnek létre.

**Workaround**

Ha nincs szükség affinitáscsoportra, ne használjon affinitáscsoportot, és ne csoportosítsa a számítási erőforrásokat több affinitáscsoportba.

## <a name="affinity-group-based-virtual-network"></a>Affinitáscsoport-alapú virtuális hálózat
**Hiba**

New_General* vagy New_VMSizeNotSupported*

**A fürt rögzítésének oka**

A regionális virtuális hálózatok bevezetése előtt egy virtuális hálózatot kellett társítania egy affinitási csoporthoz. Ennek eredményeképpen az affinitási csoportba helyezett számítási erőforrásokat ugyanazok a korlátozások kötik, mint a fenti "Foglalási forgatókönyv: Affinitáscsoport (Virtuálisgép/szolgáltatás közelsége)" című szakaszban leírtak. A számítási erőforrások egy fürthöz vannak kötve.

**Workaround**

Ha nincs szüksége affinitáscsoportra, hozzon létre egy új regionális virtuális hálózatot a hozzáadni kívánt új erőforrásokhoz, majd [csatlakoztassa a meglévő virtuális hálózatot az új virtuális hálózathoz.](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/) További információk a [regionális virtuális hálózatokról.](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/)

Másik lehetőségként [áttelepítheti az affinitáscsoport-alapú virtuális hálózatot egy regionális virtuális hálózatra,](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)majd újra hozzáadhatja a kívánt erőforrásokat.


