---
title: A klasszikus üzemi modellben Azure virtuális gépek foglalási hibáinak elhárítása |} A Microsoft Docs
description: Foglalási hibák elhárítása, amikor létrehozása, újraindítása vagy átméretezése egy klasszikus virtuális gép az Azure-ban
services: azure-service-management
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: bb939e23-77fc-4948-96f7-5037761c30e8
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 7cd7897e3a0b940bbc636b2fbc3dbbc13b7cf540
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50748425"
---
# <a name="troubleshooting-steps-specific-to-allocation-failure-scenarios-in-the-classic-deployment-model"></a>Foglalási hibák esetét a klasszikus üzemi modellben meghatározott hibaelhárítási lépéseket

Az alábbiakban gyakori foglalási forgatókönyvek, amelyek egy foglalási kérelemmel, rögzíthetők. Minden forgatókönyvhöz a cikk későbbi részében fogja nekikezdenénk.

- Méretezze át a virtuális Gépet vagy virtuális gépekre vagy szerepkörpéldányokra hozzáadása egy meglévő felhőszolgáltatáshoz
- Részlegesen leállított (felszabadított) virtuális gépek újraindítása
- Teljes körűen leállított (felszabadított) virtuális gépek újraindítása
- Átmeneti és éles üzemelő példányok (csak szolgáltatásként nyújtott platformon)
- Affinitáscsoport (virtuális gép vagy szolgáltatás közelségi)
- Kapcsolat – csoport-alapú virtuális hálózat

Foglalási hibaüzenetet kap, ellenőrizze a listában szereplő esetekben vonatkoznak-e a hiba. A foglalási hiba történt az Azure platform, amely a megfelelő szituáció azonosítása által visszaadott használja. Ha a kérelem rögzítve van, távolítsa el a további fürtökhöz, növelve az esélye, hogy sikeres kérelme megnyitásához a rögzítési korlátozások némelyike.
Általánosságban elmondható Ha a hiba állapot, hogy "a kért Virtuálisgép-méret nem támogatott", mindig újra is egy későbbi időpontban. Ennek oka az, elegendő erőforrással előfordulhat, hogy rendelkezik felszabadult a fürt a kérelem befogadásához. Ha a probléma, hogy a kért Virtuálisgép-méret nem támogatott, próbálkozzon egy eltérő Virtuálisgép-mérettel. Ellenkező esetben az egyetlen lehetőség, hogy távolítsa el a rögzítési korlátozást.

Két gyakori meghibásodási helyzet affinitáscsoportok kapcsolódnak. Múltbeli időpont adja meg a virtuális gépek, illetve szolgáltatáspéldányok hálózatbővítési használt affinitáscsoporthoz, vagy ahhoz, hogy a virtuális hálózat létrehozása használta azt. Regionális virtuális hálózatok bevezetésével affinitás csoportjai már nem szükséges egy virtuális hálózat létrehozásához. Az Azure-infrastruktúra a hálózati késés csökkentése azt javasoljuk, hogy a virtuális gép vagy szolgáltatás közelségi használjon affinitáscsoportokat megváltozott.

Az alábbi ábrán a besorolás, a (rögzített) elosztási forgatókönyveket mutat be. 

![Rögzített felosztás besorolás](./media/virtual-machines-common-allocation-failure/Allocation3.png)

## <a name="resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>Méretezze át a virtuális Gépet vagy virtuális gépekre vagy szerepkörpéldányokra hozzáadása egy meglévő felhőszolgáltatáshoz
**Hiba történt**

Upgrade_VMSizeNotSupported vagy GeneralError

**Fürt rögzítés okát**

Virtuális gép átméretezése, vagy egy virtuális gép vagy szerepkörpéldány hozzáadása egy meglévő felhőszolgáltatáshoz kérést, lehetséges, az eredeti fürthöz, amelyen a meglévő felhőszolgáltatás rendelkezik. Új felhőalapú szolgáltatás létrehozása lehetővé teszi, hogy az Azure platform található egy másik fürtöt, amely ingyenes erőforrással rendelkezik, vagy a kért Virtuálisgép-méretet támogatja.

**Megkerülő megoldás**

Ha a hiba Upgrade_VMSizeNotSupported *, próbálkozzon egy eltérő Virtuálisgép-mérettel. Ha egy másik Virtuálisgép-mérettel lehetőség nem, de ha elfogadható, egy másik virtuális IP-cím (VIP) használni, hozzon létre egy új felhőszolgáltatást a új virtuális Gépet üzemelteti, és adja hozzá az új felhőszolgáltatást a regionális virtuális hálózatot, ahol a meglévő virtuális gépek futnak. A már meglévő felhőszolgáltatás nem használja a regionális virtuális hálózat, ha továbbra is létrehozhat egy új virtuális hálózatot az új felhőalapú szolgáltatás, és hogyan csatlakozhat a [az új virtuális hálózat meglévő virtuális hálózat](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Tudjon meg többet [regionális virtuális hálózatok](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Ha a hiba GeneralError *, valószínű, hogy a fürt által támogatott típusú erőforrások (például egy adott virtuális gép mérete), de a fürtnek nincs szabad erőforrás jelenleg. A fenti forgatókönyv adja a kívánt számítási erőforrás egy új felhőszolgáltatást (vegye figyelembe, hogy rendelkezik-e az új felhőalapú szolgáltatás használata egy másik virtuális IP-CÍMEK) létrehozását és a egy regionális virtuális hálózat használatával a cloud services.

## <a name="restart-partially-stopped-deallocated-vms"></a>Részlegesen leállított (felszabadított) virtuális gépek újraindítása
**Hiba történt**

GeneralError *

**Fürt rögzítés okát**

Részleges felszabadítási azt jelenti, hogy leállt-e (felszabadított) egy vagy több, de nem minden virtuális gép egy cloud service-ben. Ha leállítja (szabadítsa fel) egy virtuális Gépet, a kapcsolódó erőforrások jelennek meg. A leállított (felszabadított) virtuális gép újraindítása ezért egy új foglalási kérelemben. Részlegesen felszabadított felhőszolgáltatás a virtuális gépek újraindításával megegyezik a virtuális gépeket ad hozzá egy meglévő felhőszolgáltatáshoz. A foglalási kérelemben fel kell kísérlet a következő időpontban az eredeti fürthöz, amelyen a meglévő felhőszolgáltatás. Másik felhőalapú szolgáltatás létrehozása lehetővé teszi, hogy az Azure platform található egy másik fürtöt, amely ingyenes erőforrás vagy a kért Virtuálisgép-méretet támogatja.

**Megkerülő megoldás**

Ha elfogadható, használjon egy másik virtuális IP-CÍMEK, törölje a leállított (felszabadított) virtuális gépek (de tartsa a társított lemezek), és adja hozzá a virtuális gépek biztonsági másolatot egy másik felhőalapú szolgáltatás segítségével. Regionális virtuális hálózat használatával csatlakozzon a cloud Services szolgáltatások:

* A meglévő felhőalapú szolgáltatást használ egy regionális virtuális hálózat, az új felhőszolgáltatásra egyszerűen adja hozzá ugyanahhoz a virtuális hálózathoz.
* Ha a meglévő felhőszolgáltatás nem használja a regionális virtuális hálózat, hozzon létre egy új virtuális hálózatot az új felhőalapú szolgáltatás, majd [a meglévő virtuális hálózat csatlakoztatása az új virtuális hálózat](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Tudjon meg többet [regionális virtuális hálózatok](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

## <a name="restart-fully-stopped-deallocated-vms"></a>Teljes körűen leállított (felszabadított) virtuális gépek újraindítása
**Hiba történt**

GeneralError *

**Fürt rögzítés okát**

Teljes felszabadítási azt jelenti, hogy az Ön által leállítva (felszabadítva) minden virtuális gép egy felhőalapú szolgáltatásából. Ezek a virtuális gépek újraindítását a foglalási kérések kell az eredeti fürthöz, amelyen a felhőszolgáltatást, lehetséges. Új felhőalapú szolgáltatás létrehozása lehetővé teszi, hogy az Azure platform található egy másik fürtöt, amely ingyenes erőforrással rendelkezik, vagy a kért Virtuálisgép-méretet támogatja.

**Megkerülő megoldás**

Ha elfogadható, használjon egy másik virtuális IP-CÍMEK, törli az eredeti leállított (felszabadított) virtuális gépek (de tartsa a társított lemezek), és a megfelelő felhőalapú szolgáltatás törlése (a kapcsolódó számítási erőforrások már kiadott, leállított (felszabadított) virtuális gépek). Vissza a virtuális gépek hozzáadása új felhőszolgáltatás hozható létre.

## <a name="stagingproduction-deployments-platform-as-a-service-only"></a>Átmeneti és éles környezetekben üzemelő példányok (csak szolgáltatásként nyújtott platformon)
**Hiba történt**

New_General * vagy New_VMSizeNotSupported *

**Fürt rögzítés okát**

Az átmeneti üzembe helyezés és a egy felhőszolgáltatás éles üzembe helyezése az azonos fürtön üzemelnek. Amikor hozzáadja a második üzembe helyezést, a megfelelő foglalási kérelemben ugyanazon a fürtön, amelyen az első üzembe helyezés a lehetséges.

**Megkerülő megoldás**

Törölje az első üzembe helyezés és az eredeti felhőszolgáltatást, és telepítse újra a felhőalapú szolgáltatást. Ez a művelet lehetséges, hogy megnyitja az első üzembe helyezés, egy fürtben, amely elegendő szabad erőforrás mindkét központi telepítés megfelelően vagy egy fürtben, amely támogatja a kért Virtuálisgép-méretekkel.

## <a name="affinity-group-vmservice-proximity"></a>Az affinitáscsoport (virtuális gép vagy szolgáltatás közelségi)
**Hiba történt**

New_General * vagy New_VMSizeNotSupported *

**Fürt rögzítés okát**

Minden számítási affinitáscsoporthoz hozzárendelt erőforrás egy fürt van kötve. Új számítási erőforrás kéri, hogy affinitáscsoportban van kísérlet a meglévő erőforrások üzemeltető ugyanazon fürt. Ez az e az új erőforrások jönnek létre egy új felhőszolgáltatást vagy egy meglévő felhőalapú szolgáltatás segítségével.

**Megkerülő megoldás**

Ha affinitáscsoport nem szükséges, nem használhatja az affinitáscsoporthoz, vagy több affinitáscsoportok csoportosíthatja a számítási erőforrásokat.

## <a name="affinity-group-based-virtual-network"></a>Kapcsolat-csoport-alapú virtuális hálózat
**Hiba történt**

New_General * vagy New_VMSizeNotSupported *

**Fürt rögzítés okát**

Regionális virtuális hálózatok jelentek meg, mielőtt kellett egy virtuális hálózat társítása az affinitáscsoportot. Ennek eredményeképpen számítási erőforrásokat helyez el az affinitáscsoportot nézve kötelezőnek ismeri az azonos megkötések leírtak szerint a "foglalási forgatókönyv: affinitáscsoport (virtuális gép vagy szolgáltatás közelségi)" című fenti szakaszban. A számítási erőforrásokat egy fürtben vannak társítva.

**Megkerülő megoldás**

Ha nem szükséges affinitáscsoport, hozzon létre egy új regionális virtuális hálózatot az új erőforrások hozzáadása esetén, majd [a meglévő virtuális hálózat csatlakoztatása az új virtuális hálózat](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Tudjon meg többet [regionális virtuális hálózatok](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Lehetőségként [áttelepítése a kapcsolat-csoport-alapú virtuális hálózat regionális virtuális hálózat](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/), majd adja hozzá újra a kívánt erőforrást.


