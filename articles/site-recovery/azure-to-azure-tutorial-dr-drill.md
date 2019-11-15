---
title: Azure-beli virtuális gépek vész-helyreállítási részletezésének futtatása Azure Site Recovery
description: Megtudhatja, hogyan futtathat vész-helyreállítási gyakorlatot egy másodlagos régióban Azure-beli virtuális gépekhez az Azure Site Recovery szolgáltatással.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 817a220e36ac250b1d5a5aa90d0bddbfb155cc26
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091339"
---
# <a name="run-a-disaster-recovery-drill-to-a-secondary-region-for-azure-vms"></a>Vész-helyreállítási gyakorlat futtatása Azure-beli virtuális gépek másodlagos régiójába 

Az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás működőképes és elérhető állapotban tartja az üzleti alkalmazásokat a tervezett és nem tervezett leállások idején, így segít a vállalatoknak az üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégia megvalósításában. A Site Recovery felügyeli és koordinálja a helyszíni gépek és az Azure-beli virtuális gépek vészhelyreállítását, beleértve a replikálást, a feladatátvételt és a helyreállítást.

Ez az oktatóanyag azt ismerteti, hogy hogyan hajthat végre vészhelyreállítási próbát egy Azure-beli virtuális gép esetében az egyik Azure-régióból a másikba egy feladatátvételi teszt segítségével. A próba adatveszteség és állásidő nélkül ellenőrzi a replikációs stratégiáját, és nincs hatással az éles környezetre. Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az előfeltételek ellenőrzése
> * Feladatátvételi teszt futtatása egyetlen virtuális gép esetén

> [!NOTE]
> Ez az oktatóanyag segítséget nyújt a DR működésének minimális lépéseivel való elvégzéséhez. Ha többet szeretne megtudni a DR működéssel kapcsolatos különböző szempontokról, beleértve a hálózati szempontokat, az automatizálást és a hibaelhárítást, tekintse meg az Azure-beli virtuális gépekhez tartozó "útmutató" című dokumentumot.

## <a name="prerequisites"></a>Előfeltételek

- Javasoljuk, hogy a feladatátvételi teszt futtatása előtt ellenőrizze a virtuális gép tulajdonságait, hogy biztosan minden a várt módon működjön.  Lépjen a virtuális gép tulajdonságaihoz a **Replikált elemek** területen. Az **Alapvető erőforrások** panel megjeleníti a gépek beállításaira és állapotára vonatkozó információkat.
- **Javasoljuk, hogy a feladatátvételi teszthez a replikáció engedélyezésekor beállított alapértelmezett hálózat helyett használjon egy külön Azure-beli virtuálisgép-hálózatot**.
- Az egyes hálózati adapterek forrás-hálózati konfigurációjától függően megadhatja az **alhálózat, az IP-cím, a nyilvános IP-cím, a hálózati biztonsági csoport vagy a belső Load Balancer** is, hogy az egyes hálózati adapterekhez a Dr működést megelőzően a teszt feladatátvételi beállítások alatt & lévő összes hálózati adapterhez társítson.


## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

1. A **Beállítások** > **Replikált elemek** területen kattintson a virtuális gépre, majd a **+Feladatátvételi teszt** ikonra.

2. A **Feladatátvételi teszt** területen válasszon ki egy helyreállítási pontot a feladatátvétel végrehajtásához:

    - **Legutóbb**: feldolgozza site Recovery összes információját, és megadja a legalacsonyabb RTO (helyreállítási idő célkitűzés).
    - **Legutóbb feldolgozott**: A virtuális gép feladatait a Site Recovery által feldolgozott legutóbbi helyreállítási pontnak adja át. Megjelenik az időbélyeg. Ezzel a beállítással a rendszer nem tölt időt az adatok feldolgozásával, így a helyreállítási időre vonatkozó célkitűzés (RTO) alacsony.
   - **Legutóbbi alkalmazáskonzisztens**: Ez a beállítás az összes virtuális gép feladatait a legutóbbi alkalmazáskonzisztens helyreállítási pontnak adja át. Megjelenik az időbélyeg.
   - **Egyéni**: feladatátvétel adott helyreállítási pontra. Az egyéni szolgáltatás csak akkor érhető el, ha egyetlen virtuális gép feladatátvételét hajtja végre, és nem a helyreállítási tervekkel való feladatátvételre.

3. Válassza ki, hogy az Azure-beli virtuális gépek mely másodlagos régióban található cél Azure-beli virtuális hálózathoz csatlakozzanak majd a feladatátvételt követően.

    > [!NOTE]
    > Az Azure Virtual Network kiválasztására szolgáló legördülő lista nem jelenik meg, ha a feladatátvételi teszt beállításai előre konfigurálva vannak a replikált elemhez.

4. A feladatátvétel indításához kattintson az **OK** gombra. A folyamat előrehaladásának megtekintéséhez a virtuális gépre kattintva megnyithatja a tulajdonságait. Vagy kattintson a **Feladatátvételi teszt** feladatra a tároló neve > **Beállítások** > **Feladatok** > **Site Recovery-feladatok** menüpontban.
5. A feladatátvétel befejezését követően az Azure-beli virtuális gép replikája megjelenik az Azure Portal > **Virtuális gépek** területen. Győződjön meg arról, hogy a virtuális gép fut, megfelelő a méretezése és a megfelelő hálózathoz csatlakozik.
6. A feladatátvételi teszt során létrehozott virtuális gépek törléséhez kattintson **Feladatátvételi teszt eltávolítása** elemre a replikált elemen vagy a helyreállítási terven. A **Jegyzetek** területen jegyezheti fel és mentheti a feladatátvételi teszttel kapcsolatos megfigyeléseket.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Éles feladatátvétel futtatása](azure-to-azure-tutorial-failover-failback.md)
