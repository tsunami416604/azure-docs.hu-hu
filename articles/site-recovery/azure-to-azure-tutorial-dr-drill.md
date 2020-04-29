---
title: Azure-beli virtuális gépek vész-helyreállítási részletezésének futtatása Azure Site Recovery
description: Megtudhatja, hogyan futtathat vész-helyreállítási gyakorlatot egy másodlagos régióban Azure-beli virtuális gépekhez az Azure Site Recovery szolgáltatással.
services: site-recovery
ms.topic: tutorial
ms.date: 01/16/2020
ms.custom: mvc
ms.openlocfilehash: b2ce157f0f192135ab0507e4aae4c0a282bda1ea
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "76166191"
---
# <a name="run-a-disaster-recovery-drill-to-a-secondary-region-for-azure-vms"></a>Vész-helyreállítási gyakorlat futtatása Azure-beli virtuális gépek másodlagos régiójába

Az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás működőképes és elérhető állapotban tartja az üzleti alkalmazásokat a tervezett és nem tervezett leállások idején, így segít a vállalatoknak az üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégia megvalósításában. A Site Recovery felügyeli és koordinálja a helyszíni gépek és az Azure-beli virtuális gépek vészhelyreállítását, beleértve a replikálást, a feladatátvételt és a helyreállítást.

Ez az oktatóanyag azt ismerteti, hogy hogyan hajthat végre vészhelyreállítási próbát egy Azure-beli virtuális gép esetében az egyik Azure-régióból a másikba egy feladatátvételi teszt segítségével. A próba adatveszteség és állásidő nélkül ellenőrzi a replikációs stratégiáját, és nincs hatással az éles környezetre. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az előfeltételek ellenőrzése
> * Feladatátvételi teszt futtatása egyetlen virtuális gép esetén

> [!NOTE]
> Ez az oktatóanyag segítséget nyújt a vész-helyreállítási gyakorlat végrehajtásához minimális lépésekkel. Ha többet szeretne megtudni a vész-helyreállítási gyakorlattal kapcsolatos különböző funkciókról, tekintse meg az Azure-beli virtuális gépek [replikálásának](azure-to-azure-how-to-enable-replication.md), [hálózatkezelésének](azure-to-azure-about-networking.md), [automatizálásának](azure-to-azure-powershell.md)és [hibaelhárításának](azure-to-azure-troubleshoot-errors.md)dokumentációját.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzése előtt tekintse át a következő elemeket:

- A feladatátvételi teszt futtatása előtt javasoljuk, hogy ellenőrizze a virtuális gép tulajdonságait, és győződjön meg róla, hogy az a vész-helyreállításra van konfigurálva. A replikálási és feladatátvételi tulajdonságok megtekintéséhez lépjen a virtuális gép **műveleteinek** > vész-**helyreállítási** > **tulajdonságaira** .
- **Javasoljuk, hogy a feladatátvételi teszthez a replikáció engedélyezésekor beállított alapértelmezett hálózat helyett használjon egy külön Azure-beli virtuálisgép-hálózatot**.
- Az egyes hálózati adapterek forrás-hálózati konfigurációjától függően megadhatja az **alhálózatot**, a **magánhálózati IP-címet**, a **nyilvános IP**-címet, a **hálózati biztonsági csoportot**vagy a **Load balancert** úgy, hogy az egyes hálózati adapterekhez a **számítás és a hálózat** feladatátvételi beállításai alatt, a vész-helyreállítási gyakorlat végrehajtása előtt is csatolni lehessen.

## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

Ez a példa bemutatja, hogyan használható egy Recovery Services tároló a virtuális gép feladatátvételi tesztje során.

1. Válasszon ki egy tárolót, és lépjen a **védett elemek** > **replikált elemek elemre** , és válasszon ki egy virtuális gépet.
1. A feladatátvételi **teszt**területen válassza ki a feladatátvételhez használandó helyreállítási pontot:
   - **Legutóbb**: feldolgozza site Recovery összes információját, és megadja a legalacsonyabb RTO (helyreállítási idő célkitűzés).
   - **Legutóbb feldolgozott**: A virtuális gép feladatait a Site Recovery által feldolgozott legutóbbi helyreállítási pontnak adja át. Megjelenik az időbélyeg. Ezzel a beállítással a rendszer nem tölti le az adatfeldolgozási időt, így alacsony RTO biztosít.
   - **Legutóbbi alkalmazáskonzisztens**: Ez a beállítás az összes virtuális gép feladatait a legutóbbi alkalmazáskonzisztens helyreállítási pontnak adja át. Megjelenik az időbélyeg.
   - **Egyéni**: feladatátvétel adott helyreállítási pontra. Az egyéni szolgáltatás csak akkor érhető el, ha egyetlen virtuális gép feladatátvételét hajtja végre, és nem a helyreállítási tervekkel való feladatátvételre.
1. Válassza ki azt a célként megadott Azure-beli virtuális hálózatot, amelyet a másodlagos régióban található Azure-beli virtuális gépek a feladatátvétel után fognak csatlakozni.

   > [!NOTE]
   > Ha a feladatátvételi teszt beállításai előre konfigurálva vannak a replikált elemhez, az Azure-beli virtuális hálózat kiválasztására szolgáló legördülő menü nem látható.

1. A feladatátvétel elindításához kattintson **az OK gombra**. A tár előrehaladásának nyomon követéséhez lépjen a **figyelés** > **site Recovery feladatok** elemre, és válassza ki a **feladatátvételi teszt** feladatot.
1. A feladatátvétel befejeződése után a replika Azure-beli virtuális gép megjelenik a Azure Portal **Virtual Machines**. Győződjön meg arról, hogy a virtuális gép fut, megfelelő a méretezése és a megfelelő hálózathoz csatlakozik.
1. A feladatátvételi teszt során létrehozott virtuális gépek törléséhez válassza a **feladatátvételi teszt** törlése elemet a replikált elemen vagy a helyreállítási terven. A **jegyzetek**területen jegyezze fel és mentse a feladatátvételi teszttel kapcsolatos megfigyeléseket.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Éles feladatátvétel futtatása](azure-to-azure-tutorial-failover-failback.md)
