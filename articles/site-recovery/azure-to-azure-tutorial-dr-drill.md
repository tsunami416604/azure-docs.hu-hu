---
title: Azure-beli vész-helyreállítási gyakorlat futtatása az Azure Site Recovery szolgáltatással
description: Ismerje meg, hogyan futtathat vész-helyreállítási gyakorlatot az Azure-beli virtuális gépek másodlagos régiójában az Azure Site Recovery szolgáltatás használatával.
services: site-recovery
ms.topic: tutorial
ms.date: 01/16/2020
ms.custom: mvc
ms.openlocfilehash: b2ce157f0f192135ab0507e4aae4c0a282bda1ea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76166191"
---
# <a name="run-a-disaster-recovery-drill-to-a-secondary-region-for-azure-vms"></a>Vész-helyreállítási gyakorlat futtatása egy másodlagos régióban az Azure virtuális gépek

Az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás működőképes és elérhető állapotban tartja az üzleti alkalmazásokat a tervezett és nem tervezett leállások idején, így segít a vállalatoknak az üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégia megvalósításában. A Site Recovery felügyeli és koordinálja a helyszíni gépek és az Azure-beli virtuális gépek vészhelyreállítását, beleértve a replikálást, a feladatátvételt és a helyreállítást.

Ez az oktatóanyag azt ismerteti, hogy hogyan hajthat végre vészhelyreállítási próbát egy Azure-beli virtuális gép esetében az egyik Azure-régióból a másikba egy feladatátvételi teszt segítségével. A próba adatveszteség és állásidő nélkül ellenőrzi a replikációs stratégiáját, és nincs hatással az éles környezetre. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az előfeltételek ellenőrzése
> * Feladatátvételi teszt futtatása egyetlen virtuális gép esetén

> [!NOTE]
> Ez az oktatóanyag segít a vész-helyreállítási gyakorlat minimális lépésekkel végrehajtásában. Ha többet szeretne megtudni a vész-helyreállítási gyakorlattal kapcsolatos különböző funkciókról, olvassa el az Azure virtuális gépek [replikációjának,](azure-to-azure-how-to-enable-replication.md) [hálózatának,](azure-to-azure-about-networking.md) [automatizálásának](azure-to-azure-powershell.md)és [hibaelhárításának dokumentációját.](azure-to-azure-troubleshoot-errors.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag előtt ellenőrizze a következő elemeket:

- A teszt feladatátvétel futtatása előtt azt javasoljuk, hogy ellenőrizze a virtuális gép tulajdonságait, és győződjön meg arról, hogy a vész-helyreállítási konfigurálva van. Lépjen a virtuális gép**vész-helyreállítási** > **tulajdonságai** a **replikációs** > és feladatátvételi tulajdonságok megtekintéséhez.
- **Javasoljuk, hogy a feladatátvételi teszthez a replikáció engedélyezésekor beállított alapértelmezett hálózat helyett használjon egy külön Azure-beli virtuálisgép-hálózatot**.
- Az egyes hálózati adapterek forráshálózati konfigurációitól függően megadhatja, hogy **az Alhálózat, a** **Magánhálózati IP-cím,** **a Nyilvános IP**, a Hálózati biztonsági **csoport**vagy **a Terheléselosztó** minden hálózati adapterhez csatolható a **számítási és hálózati** tesztfeladat-átvételi beállítások alatt, mielőtt vész-helyreállítási gyakorlatot végezne.

## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

Ez a példa bemutatja, hogyan használhatja a Recovery Services-tároló virtuálisgép-teszt feladatátvétel.

1. Jelöljön ki egy tárolót, és lépjen a **védett elemek** > **replikált elemekre,** és válasszon ki egy virtuális gép.
1. A **Feladatátvétel tesztben**válassza ki a feladatátvételhez használandó helyreállítási pontot:
   - **Legújabb**: Feldolgozza az összes adatot site recovery, és biztosítja a legalacsonyabb RTO (Recovery Time Objective).
   - **Legutóbb feldolgozott**: A virtuális gép feladatait a Site Recovery által feldolgozott legutóbbi helyreállítási pontnak adja át. Megjelenik az időbélyeg. Ezzel a beállítással nem kell időt tölteni az adatok feldolgozásával, így alacsony RTO-t biztosít.
   - **Legutóbbi alkalmazáskonzisztens**: Ez a beállítás az összes virtuális gép feladatait a legutóbbi alkalmazáskonzisztens helyreállítási pontnak adja át. Megjelenik az időbélyeg.
   - **Egyéni**: Feladatátvétel adott helyreállítási pontra. Egyéni csak akkor érhető el, ha feladatátvételegy virtuális gép, és nem a helyreállítási terv feladatátvétel.
1. Válassza ki a cél Azure virtuális hálózat, amely az Azure virtuális gépek a másodlagos régióban csatlakozik a feladatátvétel után.

   > [!NOTE]
   > Ha a teszt feladatátvételi beállítások előre konfigurálva vannak a replikált elemhez, az Azure virtuális hálózat kiválasztásához a legördülő menü nem látható.

1. A feladatátvétel elindításához kattintson az **OK gombra.** A tárolóból származó folyamat nyomon követéséhez nyissa meg a Site Recovery feladatok **figyelése,** > **Site Recovery jobs** és válassza ki a **feladatátvételi feladat tesztelése.**
1. A feladatátvétel befejezése után az Azure Virtuális gép replikája megjelenik az Azure Portal **virtuális gépek.** Győződjön meg arról, hogy a virtuális gép fut, megfelelő a méretezése és a megfelelő hálózathoz csatlakozik.
1. A teszt feladatátvétel során létrehozott virtuális gépek törléséhez válassza a Replikált elem vagy a helyreállítási terv karbantartása teszt **feladatátvétel** lehetőséget. A **Megjegyzések alkalmazásban**rögzítse és mentse a tesztfeladat-átvételhez kapcsolódó megfigyeléseket.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Éles feladatátvétel futtatása](azure-to-azure-tutorial-failover-failback.md)
