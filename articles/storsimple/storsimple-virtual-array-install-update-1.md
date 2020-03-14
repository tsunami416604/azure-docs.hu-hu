---
title: Az 1,0-es frissítés telepítése a StorSimple virtuális tömbön | Microsoft Docs
description: Ismerteti, hogyan használhatók a StorSimple virtuális tömb webes felhasználói felülete a frissítések alkalmazásához a Azure Portal és a gyorsjavítási módszer használatával
services: storsimple
documentationcenter: NA
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: fa53213e577028628d48db91704578e23888f2a8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79254507"
---
# <a name="install-update-10-on-your-storsimple-virtual-array"></a>Az 1,0-es frissítés telepítése a StorSimple virtuális tömbön

## <a name="overview"></a>Áttekintés

Ez a cikk azokat a lépéseket ismerteti, amelyek szükségesek a 1,0-es frissítés telepítéséhez a StorSimple virtuális tömbön a helyi webes felületen és a Azure Portal keresztül.

A szoftverfrissítések vagy gyorsjavítások alkalmazása a StorSimple virtuális tömb naprakészen tartásához. A frissítés alkalmazása előtt javasoljuk, hogy a köteteket vagy a megosztásokat a gazdagépen, majd az eszközön offline állapotba helyezze. Ezzel a lehető legkisebbre csökkenti az adatsérülés esélyét. A kötetek vagy a megosztások offline állapotba helyezése után manuálisan kell biztonsági másolatot készíteni az eszközről.

> [!IMPORTANT]
> - Az 1,0-es frissítés megfelel az eszközön **10.0.10296.0** szoftver verziójának. A frissítéssel kapcsolatos újdonságokról a 1,0-es [frissítés kibocsátási megjegyzései](storsimple-virtual-array-update-1-release-notes.md)című témakörben olvashat bővebben.
>
> - Ne feledje, hogy frissítés vagy gyorsjavítás telepítése újraindítja az eszközt. Mivel a StorSimple virtuális tömb egyetlen csomópontos eszköz, a folyamatban lévő I/O-műveletek megszakadnak, és az eszköz leállást tapasztal.
>
> - Az 1. frissítés csak akkor érhető el a Azure Portalban, ha a virtuális tömb a 0,6-es frissítést futtatja. A 0,6-es frissítés előtti verziót futtató virtuális tömbök esetében először telepítenie kell a 0,6-es frissítést, majd telepíteni kell az 1. frissítést.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

Ha a 0,2-es vagy újabb frissítést futtatja, javasoljuk, hogy telepítse a frissítéseket a Azure Portal. A portál eljárása megköveteli a felhasználótól a frissítések vizsgálatát, letöltését és telepítését. Attól függően, hogy a virtuális tömb melyik szoftververzió fut, a frissítés alkalmazása a Azure Portal eltérő.

 - Ha a virtuális tömb a 0,6-es frissítést futtatja, a Azure Portal közvetlenül telepíti az 1. frissítést (10.0.10296.0) az eszközre. Az eljárás végrehajtása körülbelül 7 percet vesz igénybe.
 - Ha a virtuális tömb verziója a 0,6-es frissítés előtt fut, akkor a frissítés két fázisban történik. A Azure Portal először a 0,6 (10.0.10293.0) frissítést telepíti az eszközön. A virtuális tömb újraindul, és a portál telepíti az 1. frissítést (10.0.10296.0) az eszközre. Az eljárás végrehajtása körülbelül 15 percet vesz igénybe.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-1.md)]

A telepítés befejezése után lépjen a StorSimple Eszközkezelő szolgáltatásra. Válassza az **eszközök** lehetőséget, majd válassza ki, majd kattintson az imént frissített eszközre. Válassza a **beállítások > > eszköz frissítéseinek kezelése lehetőséget**. A megjelenített szoftververzió **10.0.10296.0**kell lennie.

![Szoftverfrissítés utáni szoftververzió](./media/storsimple-virtual-array-install-update-1/azupdate17m1.png)

## <a name="use-the-local-web-ui"></a>A helyi webes felhasználói felület használata

A helyi webes felhasználói felület használata két lépésből áll:

* A frissítés vagy a gyorsjavítás letöltése
* A frissítés vagy a gyorsjavítás telepítése

> [!IMPORTANT] 
> **Csak akkor folytassa ezt a frissítést, ha az 0,6-es (10.0.10293.0) frissítést futtatja. Ha egy korábbi verziót futtat, először [telepítse a 0,6](storsimple-virtual-array-install-update-06.md) -es frissítést az eszközön, majd alkalmazza az 1. frissítést.**

### <a name="download-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás letöltése

Ha a virtuális tömb a 0,6-es frissítést futtatja, hajtsa végre a következő lépéseket az 1. frissítés letöltéséhez az Microsoft Update-katalógusból.

#### <a name="to-download-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás letöltése

1. Indítsa el az Internet Explorert, és navigáljon [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com).

2. Ha első alkalommal használja a Microsoft Update-katalógust ezen a számítógépen, kattintson a **telepítés** gombra, amikor a rendszer felszólítja az Microsoft Update Catalog bővítmény telepítésére.

3. A Microsoft Update-katalógus keresőmező mezőjébe írja be a letölteni kívánt gyorsjavítás tudásbázisának (KB) számát. Adja meg a **4047203** -es frissítést a 1,0-es frissítéshez, majd kattintson a **Keresés**gombra.
   
    Megjelenik a gyorsjavítások listája, például: **StorSimple Virtual Array Update 1,0**.
   
    ![Keresés a katalógusban](./media/storsimple-virtual-array-install-update-1/download1.png)

4. Kattintson a **Letöltés** gombra.

5. Töltse le a két fájlt egy mappába. A mappát átmásolhatja egy olyan hálózati megosztásra is, amely elérhető az eszközről.

6. Nyissa meg azt a mappát, ahol a fájlok találhatók.

    ![A csomagban található fájlok](./media/storsimple-virtual-array-install-update-1/update01folder.png)

    Két fájl jelenik meg:
    -  Egy önálló csomagfájl Microsoft Update `WindowsTH-KB3011067-x64`. Ez a fájl az eszköz szoftverének frissítésére szolgál.
    - Az augusztusi `windows8.1-kb4034681-x64`összesítő frissítéseit tartalmazó fájl. További információ a jelen összesítésben található adatokról: [augusztus havi biztonsági összesítés](https://support.microsoft.com/help/4034681/windows-8-1-windows-server-2012-r2-update-kb40346810).

### <a name="install-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás telepítése

A frissítés vagy a gyorsjavítás telepítése előtt győződjön meg az alábbiakról:

 - A frissítés vagy a gyorsjavítás helyileg, a gazdagépen vagy egy hálózati megosztáson keresztül érhető el.
 - A virtuális tömb a 0,6-es (10.0.10293.0) frissítést futtatja. Ha a 0,6-es frissítés előtti verziót futtat, először [telepítse a 0,6](storsimple-virtual-array-install-update-06.md) -es frissítést, majd telepítse az 1. frissítést.

Az eljárás végrehajtása körülbelül 4 percet vesz igénybe. A frissítés vagy a gyorsjavítás telepítéséhez hajtsa végre a következő lépéseket.

#### <a name="to-install-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás telepítése

1. A helyi webes KEZELŐFELÜLETen lépjen a **karbantartás** > **szoftverfrissítés**elemre. Jegyezze fel a futtatott szoftver verzióját. **Csak akkor folytassa ezt a frissítést, ha az 0,6-es (10.0.10293.0) frissítést futtatja. Ha egy korábbi verziót futtat, először [telepítse a 0,6](storsimple-virtual-array-install-update-06.md) -es frissítést az eszközön, majd alkalmazza az 1. frissítést.**
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-1/update1m.png)

2. A **frissítési fájl elérési útja**mezőben adja meg a frissítés vagy a gyorsjavítás fájlnevét. Ha hálózati megosztásra helyezi, tallózással is megkeresheti a frissítés vagy a gyorsjavítás telepítési fájlját. Kattintson az **Alkalmaz** gombra.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-1/update2m.png)

3. A rendszer figyelmeztetést jelenít meg. Mivel a virtuális tömb egyetlen csomópontos eszköz, a frissítés alkalmazása után az eszköz újraindul, és leállási idő van. Kattintson a pipa ikonra.
   
   ![eszköz frissítése](./media/storsimple-virtual-array-install-update-1/update3m.png)

4. A frissítés elindul. Az eszköz sikeres frissítése után újraindul. A helyi felhasználói felület ebben az időtartamban nem érhető el.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-1/update5m.png)

5. Az újraindítás után a rendszer a **bejelentkezési** oldalra kerül. Annak ellenőrzéséhez, hogy az eszköz szoftvere frissítve lett-e, a helyi webes KEZELŐFELÜLETen lépjen a **karbantartás** > **szoftverfrissítés**elemre. A megjelenített szoftververzió **10.0.0.0.0.10296** kell lennie az 1,0-es frissítéshez.
   
   > [!NOTE]
   > A szoftver-verziókat a helyi webes KEZELŐFELÜLETen és a Azure Portal némileg eltérő módon jelentik be. A helyi webes felhasználói felület például a **10.0.0.0.0.10296** és a Azure Portal jelentések **10.0.10296.0** ugyanazt a verziót jelenti.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-1/update6m.png)

6. Ismételje meg a 2-4-es lépéseket a Windows biztonsági javítás a fájl `windows8.1-kb4012213-x64`használatával történő telepítéséhez. A virtuális tömb a telepítés után újraindul, és be kell jelentkeznie a helyi webes felhasználói felületre.

> [!NOTE]
> Ha közvetlenül az 1. frissítést alkalmazza egy olyan eszközre, amely a 0,6-es frissítés előtt fut, akkor hiányoznak néhány frissítés. A következő lépésekhez forduljon Microsoft ügyfélszolgálatahoz.

## <a name="next-steps"></a>Következő lépések

További információ [a StorSimple virtuális tömb felügyeletéről](storsimple-ova-web-ui-admin.md).
