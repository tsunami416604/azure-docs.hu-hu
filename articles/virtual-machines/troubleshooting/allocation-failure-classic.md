---
title: Azure-beli virtuális gépek foglalási hibáinak elhárítása a klasszikus üzembehelyezési modellben | Microsoft Docs
description: A klasszikus virtuális gép Azure-beli létrehozásakor, újraindításakor vagy átméretezésével kapcsolatos foglalási hibák elhárítása
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
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77913378"
---
# <a name="troubleshooting-steps-specific-to-allocation-failure-scenarios-in-the-classic-deployment-model"></a>A klasszikus üzemi modellben a foglalási hibákra vonatkozó forgatókönyvekre vonatkozó hibaelhárítási lépések

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

A következő gyakori foglalási forgatókönyvek a kiosztási kérelmek rögzítését okozzák. A jelen cikk későbbi részében ismertetjük az egyes forgatókönyveket.

- Virtuális gép átméretezése vagy virtuális gépek vagy szerepkör-példányok hozzáadása meglévő felhőalapú szolgáltatáshoz
- Részlegesen leállított (felszabadított) virtuális gépek újraindítása
- Teljesen leállított (felszabadított) virtuális gépek újraindítása
- Átmeneti és éles üzembe helyezések (csak szolgáltatásként szolgáló platform)
- Affinitási csoport (virtuális gép vagy szolgáltatás közelsége)
- Affinitás – csoport alapú virtuális hálózat

Ha lefoglalási hibát kap, akkor győződjön meg arról, hogy a felsorolt forgatókönyvek bármelyike a hibára vonatkozik-e. A megfelelő forgatókönyv azonosításához használja az Azure platform által visszaadott foglalási hibát. Ha a kérelem rögzítése megtörtént, távolítson el néhány rögzítési megkötést a kérés több fürtre való megnyitásához, ezzel növelve a foglalás sikerességének esélyét.
Általánosságban elmondható, hogy ha a hiba nem jelenik meg, hogy "a kért VM-méret nem támogatott, akkor a későbbiekben bármikor újrapróbálkozhat. Ennek az az oka, hogy a fürtben elegendő erőforrás szabadult fel a kéréshez. Ha a probléma az, hogy a kért VM-méret nem támogatott, próbálkozzon egy másik virtuálisgép-mérettel. Ellenkező esetben az egyetlen lehetőség, ha el szeretné távolítani a rögzítési korlátozást.

Két gyakori meghibásodási forgatókönyv kapcsolódik az affinitási csoportokhoz. A múltban egy affinitási csoportot használtak a virtuális gépek és a szolgáltatási példányok közelségének biztosítására, vagy egy virtuális hálózat létrehozásának engedélyezésére használták. A regionális virtuális hálózatok bevezetésével az affinitási csoportok már nem szükségesek virtuális hálózat létrehozásához. Az Azure-infrastruktúra hálózati késésének csökkentése révén a virtuális gépekhez vagy a szolgáltatás közelségéhez tartozó affinitási csoportok használatára vonatkozó javaslat megváltozott.

A következő ábra a (rögzített) kiosztási forgatókönyvek besorolását mutatja be. 

![Rögzített kiosztási besorolás](./media/virtual-machines-common-allocation-failure/Allocation3.png)

## <a name="resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>Virtuális gép átméretezése vagy virtuális gépek vagy szerepkör-példányok hozzáadása meglévő felhőalapú szolgáltatáshoz
**Hiba**

Upgrade_VMSizeNotSupported vagy GeneralError

**A fürt kitűzésének oka**

A virtuális gép átméretezésére, vagy egy virtuális gép vagy szerepkör-példány meglévő felhőalapú szolgáltatásba való hozzáadására vonatkozó kérést a meglévő felhőalapú szolgáltatást futtató eredeti fürtön kell megkísérelni. Egy új felhőalapú szolgáltatás létrehozása lehetővé teszi, hogy az Azure platform megkeresse az ingyenes erőforrásokkal rendelkező másik fürtöt, vagy támogatja a kért virtuálisgép-méretet.

**Áthidaló megoldás**

Ha a hiba Upgrade_VMSizeNotSupported *, próbálkozzon egy másik virtuálisgép-mérettel. Ha eltérő virtuálisgép-méretet használ, de ha más virtuális IP-cím (VIP) használatát szeretné használni, hozzon létre egy új felhőalapú szolgáltatást az új virtuális gép üzemeltetéséhez, és adja hozzá az új felhőalapú szolgáltatást arra a regionális virtuális hálózatra, ahol a meglévő virtuális gépek futnak. Ha a meglévő felhőalapú szolgáltatás nem használ regionális virtuális hálózatot, akkor is létrehozhat egy új virtuális hálózatot az új felhőalapú szolgáltatáshoz, majd a meglévő virtuális hálózatot összekapcsolhatjuk [az új virtuális hálózattal](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). További információ a [regionális virtuális hálózatokról](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Ha a hiba a GeneralError *, akkor valószínű, hogy az erőforrás típusát (például egy adott virtuálisgép-méretet) a fürt támogatja, de a fürt jelenleg nem rendelkezik ingyenes erőforrásokkal. A fenti forgatókönyvhöz hasonlóan vegye fel a kívánt számítási erőforrást egy új felhőalapú szolgáltatás létrehozásával (vegye figyelembe, hogy az új felhőalapú szolgáltatásnak egy másik VIP-t kell használnia), és egy regionális virtuális hálózatot kell használnia a felhőalapú szolgáltatások összekapcsolásához.

## <a name="restart-partially-stopped-deallocated-vms"></a>Részlegesen leállított (felszabadított) virtuális gépek újraindítása
**Hiba**

GeneralError*

**A fürt kitűzésének oka**

A részleges felszabadítás azt jelenti, hogy egy vagy több, de nem az összes virtuális gép leállt (felszabadítása) egy felhőalapú szolgáltatásban. Ha leállítja (felszabadítja) a virtuális gépet, a rendszer kibocsátja a kapcsolódó erőforrásokat. A leállított (felszabadítási) virtuális gép újraindítása ezért új foglalási kérelem. Egy részlegesen lefoglalt felhőalapú szolgáltatásban lévő virtuális gépek újraindítása egyenértékű a virtuális gépek meglévő felhőalapú szolgáltatásba való hozzáadásával. A foglalási kérelmet a meglévő felhőalapú szolgáltatást futtató eredeti fürtön kell megkísérelni. Egy másik felhőalapú szolgáltatás létrehozása lehetővé teszi az Azure platform számára, hogy megkeresse az ingyenes erőforrással rendelkező másik fürtöt, vagy támogatja a kért virtuálisgép-méretet.

**Áthidaló megoldás**

Ha egy másik virtuális IP-cím használatára van lehetőség, törölje a leállított (lefoglalt) virtuális gépeket (de tartsa meg a kapcsolódó lemezeket), és adja hozzá a virtuális gépeket egy másik felhőalapú szolgáltatáshoz. Regionális virtuális hálózat használata a Cloud Services összekapcsolásához:

* Ha a meglévő felhőalapú szolgáltatás regionális virtuális hálózatot használ, egyszerűen adja hozzá az új felhőalapú szolgáltatást ugyanahhoz a virtuális hálózathoz.
* Ha a meglévő felhőalapú szolgáltatás nem használ regionális virtuális hálózatot, hozzon létre egy új virtuális hálózatot az új felhőalapú szolgáltatáshoz, majd [kapcsolja össze meglévő virtuális hálózatát az új virtuális hálózattal](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). További információ a [regionális virtuális hálózatokról](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

## <a name="restart-fully-stopped-deallocated-vms"></a>Teljesen leállított (felszabadított) virtuális gépek újraindítása
**Hiba**

GeneralError*

**A fürt kitűzésének oka**

A teljes felszabadítás azt jelenti, hogy az összes virtuális gép leállt (felszabadítása) egy felhőalapú szolgáltatásból. A virtuális gépek újraindítására vonatkozó foglalási kérelmeket a felhőalapú szolgáltatást futtató eredeti fürtön kell megkísérelni. Egy új felhőalapú szolgáltatás létrehozása lehetővé teszi, hogy az Azure platform megkeresse az ingyenes erőforrásokkal rendelkező másik fürtöt, vagy támogatja a kért virtuálisgép-méretet.

**Áthidaló megoldás**

Ha más virtuális IP-címet szeretne használni, törölje az eredeti leállított (felszabadított) virtuális gépeket (de tartsa meg a kapcsolódó lemezeket), és törölje a megfelelő felhőalapú szolgáltatást (a társított számítási erőforrások már fel lettek szabadítva a virtuális gépek leállításakor (felszabadítva). Hozzon létre egy új felhőalapú szolgáltatást a virtuális gépek újbóli hozzáadásához.

## <a name="stagingproduction-deployments-platform-as-a-service-only"></a>Előkészítési/éles üzembe helyezések (csak szolgáltatásként szolgáló platform)
**Hiba**

New_General * vagy New_VMSizeNotSupported *

**A fürt kitűzésének oka**

Egy felhőalapú szolgáltatás átmeneti üzembe helyezése és éles üzembe helyezése ugyanabban a fürtben történik. A második központi telepítés hozzáadásakor a rendszer a megfelelő foglalási kérelmet ugyanazon a fürtön kísérli meg, amely az első telepítést üzemelteti.

**Áthidaló megoldás**

Törölje az első üzembe helyezést és az eredeti felhőalapú szolgáltatást, majd telepítse újra a felhőalapú szolgáltatást. Ez a művelet egy olyan fürt első üzembe helyezését teszi lehetővé, amely elegendő szabad erőforrással rendelkezik ahhoz, hogy mindkét központi telepítéshez vagy olyan fürthöz illeszkedjen, amely támogatja a kért virtuálisgép-méreteket.

## <a name="affinity-group-vmservice-proximity"></a>Affinitási csoport (virtuális gép/szolgáltatás közelsége)
**Hiba**

New_General * vagy New_VMSizeNotSupported *

**A fürt kitűzésének oka**

Az affinitási csoporthoz rendelt számítási erőforrások egy fürthöz vannak kötve. Az adott affinitási csoportban lévő új számítási erőforrás-kérelmeket ugyanabban a fürtben kísérli meg, amelyben a meglévő erőforrások futnak. Ez igaz, hogy az új erőforrások egy új felhőalapú szolgáltatáson vagy egy meglévő felhőalapú szolgáltatáson keresztül jönnek létre.

**Áthidaló megoldás**

Ha nincs szükség affinitási csoportra, ne használjon affinitási csoportot, vagy csoportosítsa a számítási erőforrásokat több affinitási csoportba.

## <a name="affinity-group-based-virtual-network"></a>Affinitás – csoport alapú virtuális hálózat
**Hiba**

New_General * vagy New_VMSizeNotSupported *

**A fürt kitűzésének oka**

A regionális virtuális hálózatok bevezetése előtt hozzá kell rendelnie egy affinitási csoporttal rendelkező virtuális hálózatot. Ennek eredményeképpen az affinitási csoportba helyezett számítási erőforrásokra ugyanazok a korlátozások vonatkoznak, mint a fenti "kiosztási forgatókönyv: affinitási csoport (virtuális gép/szolgáltatás közelsége)" című szakaszban leírtak szerint. A számítási erőforrások egy fürthöz vannak kötve.

**Áthidaló megoldás**

Ha nincs szüksége affinitási csoportra, hozzon létre egy új regionális virtuális hálózatot a felvenni kívánt új erőforrásokhoz, majd [kapcsolja össze meglévő virtuális hálózatát az új virtuális hálózattal](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). További információ a [regionális virtuális hálózatokról](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Azt is megteheti, [hogy áttelepítheti az affinitás-csoport alapú virtuális hálózatot egy regionális virtuális hálózatra](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/), majd újra felveszi a kívánt erőforrásokat.


