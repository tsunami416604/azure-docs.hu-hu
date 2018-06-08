---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-windows, azure-resource-manager
author: genlin
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 04/14/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: f403e060859df6d1de96a3c0d478d57df2677eee
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "31531055"
---
Az alábbiakban gyakori foglalási forgatókönyvek, amelyek egy foglalási kérelem rögzítve. Igazolnia kell alaposabban egyes forgatókönyvek a cikk későbbi részében.

- Méretezze át a virtuális gépek vagy virtuális gépekre vagy szerepkörpéldányokra hozzáadása egy meglévő felhőszolgáltatáshoz
- Indítsa újra a részlegesen leállított (felszabadított) virtuális gépek
- Indítsa újra a teljes leállított (felszabadított) virtuális gépek
- Átmeneti és üzemi központi telepítések (csak szolgáltatásként platform)
- Affinitáscsoport (virtuális gép vagy szolgáltatás beléptetőkártyával)
- Virtuális hálózati kapcsolat – csoport-alapú

Amikor foglalási hiba, ellenőrizze, hogy a felsorolt esetekben vonatkoznak-e a hiba. A memóriafoglalási hiba a megfelelő telepítési azonosításához az Azure platform által visszaadott használja. Ha a kérés van rögzítve, távolítson el néhányat a kitűzési korlátozást nyissa meg a további fürtök, növelve a eséllyel sikerül, felosztási vonatkozó kérelmét.
Általában Ha a hiba nem állapotba kerül, hogy "a kért Virtuálisgép-méret nem támogatott", akkor mindig újra egy későbbi időpontban. Ennek az az oka erőforrásokkal előfordulhat, hogy rendelkezik felszabadult a fürt a kérelem befogadásához. Ha a probléma, hogy a kért Virtuálisgép-méret nem támogatott, próbálkozzon egy másik Virtuálisgép-méretet. Ellenkező esetben az egyetlen lehetősége a kitűzési korlátozás távolítható el.

Két gyakori hiba forgatókönyvek affinitáscsoportok kapcsolódnak. A múltban affinitáscsoport használatával adja meg a virtuális gépek és a szolgáltatáspéldány közel vagy való engedélyezése a virtuális hálózat létrehozása. Regionális virtuális hálózatok bevezetésével affinitáscsoportok szükségesek már nem hozhat létre virtuális hálózatot. Azure-infrastruktúra a hálózati késés csökkentésére az ajánlás affinitáscsoportok használó virtuális gépek és a szolgáltatás közelségi kapcsolat megváltozott.

Az alábbi ábra mutatja be, a besorolás (rögzített) foglalás forgatókönyv. 

![Rögzített foglalás besorolás](./media/virtual-machines-common-allocation-failure/Allocation3.png)

## <a name="resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>Méretezze át a virtuális gépek vagy virtuális gépekre vagy szerepkörpéldányokra hozzáadása egy meglévő felhőszolgáltatáshoz
**Hiba történt**

Upgrade_VMSizeNotSupported vagy GeneralError

**A fürt rögzítési OK**

Méretezze át egy virtuális Gépet, vagy egy virtuális gép vagy egy szerepkörpéldányt hozzáadása egy meglévő felhőszolgáltatáshoz kérelem ki lehet megkísérelni, az eredeti fürt, amelyen a meglévő felhőalapú szolgáltatást. Új felhőalapú szolgáltatás létrehozása lehetővé teszi, hogy az Azure platformon található egy másik fürtöt, amely ingyenes erőforrással rendelkezik, és támogatja a kért Virtuálisgép-méretet.

**Megkerülő megoldás**

Ha a hiba Upgrade_VMSizeNotSupported *, próbálkozzon egy másik Virtuálisgép-méretet. Ha egy másik Virtuálisgép-méretet használó lehetőség nem érhető el, de a másik virtuális IP-cím (VIP) elfogadható, az új virtuális gép és az új felhőalapú szolgáltatás hozzáadása a regionális virtuális hálózatot, amelyen a meglévő virtuális gépek futnak a új felhőalapú szolgáltatás létrehozása. Ha a meglévő felhőalapú szolgáltatást használja a regionális virtuális hálózatot, továbbra is létrehozhat egy új virtuális hálózat az új felhőalapú szolgáltatás, és csatlakoztassa a [az új virtuális hálózat már meglévő virtuális hálózatot](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). További tudnivalók [regionális virtuális hálózatokba](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Ha a hiba GeneralError *, valószínű, hogy típusú erőforrások (például egy adott virtuális gép méretét) támogatja a fürt, de a fürt nem rendelkezik szabad erőforrást jelenleg. Hasonló a fenti esetben vegye fel a kívánt számítási erőforrással keresztül új felhőalapú szolgáltatás (vegye figyelembe, hogy rendelkezik-e az új felhőalapú szolgáltatás használatára egy különböző VIP) létrehozásához, és a felhőszolgáltatások eléréséhez regionális virtuális hálózatot használja.

## <a name="restart-partially-stopped-deallocated-vms"></a>Indítsa újra a részlegesen leállított (felszabadított) virtuális gépek
**Hiba történt**

GeneralError *

**A fürt rögzítési OK**

Részleges felszabadítás azt jelenti, hogy a felhőszolgáltatás (felszabadított) egy vagy több, de nem minden virtuális gépe leállt. Amikor leállít (felszabadítása) a virtuális gép, a kapcsolódó erőforrások kiadásakor. A leállított (felszabadított) virtuális gép újraindítása ezért egy új memóriafoglalási kérelem. Virtuális gépek újraindításával részben felszabadított felhőszolgáltatásban megegyezik virtuális gépek hozzáadása egy meglévő felhőszolgáltatáshoz. A memóriafoglalási kérelem ki lehet megkísérelni, az eredeti fürt, amelyen a meglévő felhőalapú szolgáltatást. Másik felhőalapú szolgáltatás létrehozása lehetővé teszi, hogy az Azure platformon található egy másik fürtöt, amely ingyenes erőforrással rendelkezik, és támogatja a kért Virtuálisgép-méretet.

**Megkerülő megoldás**

Elfogadható használja másik virtuális IP-címhez, törölje a leállított (felszabadított) virtuális gépek (de tartsa a kapcsolódó lemezek), és adja hozzá a virtuális gépek biztonsági keresztül másik felhőalapú szolgáltatást. Regionális virtuális hálózatot használja a felhőszolgáltatások eléréséhez:

* Ha a meglévő felhőalapú szolgáltatást használ egy regionális virtuális hálózatot, vegyük fel az új felhőalapú szolgáltatás ugyanazt a virtuális hálózatot.
* Ha a meglévő felhőalapú szolgáltatást használja a regionális virtuális hálózat, az új felhőszolgáltatás, új virtuális hálózat létrehozása, majd [csatlakozzon a meglévő virtuális hálózatot az új virtuális hálózat](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). További tudnivalók [regionális virtuális hálózatokba](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

## <a name="restart-fully-stopped-deallocated-vms"></a>Indítsa újra a teljes leállított (felszabadított) virtuális gépek
**Hiba történt**

GeneralError *

**A fürt rögzítési OK**

Teljes felszabadítás azt jelenti, hogy leállította (felszabadítva) összes virtuális gépet egy felhőalapú szolgáltatás. Indítsa újra a virtuális gépeken foglalási kérelmeket kell kísérli meg a következő az eredeti fürt, amelyen a felhőalapú szolgáltatás. Új felhőalapú szolgáltatás létrehozása lehetővé teszi, hogy az Azure platformon található egy másik fürtöt, amely ingyenes erőforrással rendelkezik, és támogatja a kért Virtuálisgép-méretet.

**Megkerülő megoldás**

Ha az elfogadható használja másik virtuális IP-címhez, törli az eredeti leállított (felszabadított) virtuális gépek (de tartsa a kapcsolódó lemezek), és törölje a megfelelő felhőszolgáltatást (a tartozó számítási erőforrásokat már kiadott, (felszabadított) leállításakor a virtuális gépek). Hozzon létre egy új felhőalapú szolgáltatás hozzáadása a virtuális gépek vissza.

## <a name="stagingproduction-deployments-platform-as-a-service-only"></a>Átmeneti vagy üzemi környezetek (csak szolgáltatásként platform)
**Hiba történt**

New_General * vagy New_VMSizeNotSupported *

**A fürt rögzítési OK**

Az átmeneti üzembe helyezési és az éles környezet egy felhőalapú szolgáltatás ugyanazon fürt üzemelteti. Amikor a második telepítést, akkor a megfelelő memóriafoglalási kérelem ugyanazon fürt, amelyen az első telepített történt kísérlet.

**Megkerülő megoldás**

Törli a első és az eredeti felhőalapú szolgáltatás, és telepítse újra a felhőalapú szolgáltatáshoz. Ez a művelet lehet, hogy az első telepített megnyílik, elég szabad erőforrást mindkét központi telepítés módosításokkal rendelkező fürtben, vagy a fürt, amely támogatja a kért Virtuálisgép-méretek.

## <a name="affinity-group-vmservice-proximity"></a>Az affinitáscsoport (virtuális gép vagy szolgáltatás beléptetőkártyával)
**Hiba történt**

New_General * vagy New_VMSizeNotSupported *

**A fürt rögzítési OK**

A számítási erőforrás egy affinitáscsoporthoz van kötve egy fürt. Új számítási erőforrás-kérelmek abban a affinitáscsoport ugyanabban a fürtben, a meglévő erőforrásokat a rendszer hol tárolja a rendszer megkísérelte. Ez érvényét veszti, hogy az új erőforrások jönnek létre egy új felhőalapú szolgáltatás vagy egy meglévő felhőszolgáltatáshoz keresztül.

**Megkerülő megoldás**

Az affinitáscsoportokban nincs szükség, ha nem használható affinitáscsoport, vagy csoportosíthatja a számítási erőforrásokat több affinitáscsoportok.

## <a name="affinity-group-based-virtual-network"></a>Kapcsolat-csoport-alapú virtuális hálózat
**Hiba történt**

New_General * vagy New_VMSizeNotSupported *

**A fürt rögzítési OK**

Regionális virtuális hálózatokba jelentek meg, mielőtt rendelje hozzá a virtuális hálózat affinitáscsoporttal kellett. Ennek eredményeképpen az affinitáscsoport csoportosítson erőforrásokat kötik azonos korlátait leírtak számítási a "foglalási forgatókönyv: affinitáscsoport (virtuális gép vagy szolgáltatás beléptetőkártyával)" szakaszban. A számítási erőforrásokat egy fürt vannak társítva.

**Megkerülő megoldás**

Az affinitáscsoportokban nem szükséges, ha az új erőforrások hozzáadása esetén új regionális virtuális hálózat létrehozása, majd [csatlakozzon a meglévő virtuális hálózatot az új virtuális hálózat](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). További tudnivalók [regionális virtuális hálózatokba](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Alternatív megoldásként, [a kapcsolat-csoport-alapú virtuális hálózat regionális virtuális hálózat áttelepítése](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/), majd újra adja hozzá a kívánt erőforrást.
