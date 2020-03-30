---
title: Telepítse az 1.0-s frissítést a StorSimple virtuális tömbre | Microsoft dokumentumok
description: A storSimple virtuális tömb webes felhasználói felületének használata a frissítések azure portal és gyorsjavítási módszer rel történő alkalmazásához
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254507"
---
# <a name="install-update-10-on-your-storsimple-virtual-array"></a>Az 1.0-s frissítés telepítése a StorSimple virtuális tömbre

## <a name="overview"></a>Áttekintés

Ez a cikk ismerteti az 1.0-s frissítés telepítéséhez szükséges lépéseket a StorSimple virtuális tömba a helyi webes felhasználói felületen keresztül és az Azure Portalon keresztül.

A szoftverfrissítéseket vagy gyorsjavításokat a StorSimple virtuális tömb naprakészen tartásához kell alkalmazni. A frissítés alkalmazása előtt javasoljuk, hogy először a köteteket vagy megosztásokat állítsa offline állapotba a gazdagépen, majd az eszközön. Ezzel a lehető legkisebbre csökkenti az adatsérülés esélyét. Miután a kötetek vagy megosztások offline állapotba kerültek, manuális biztonsági másolatot kell készítenie az eszközről.

> [!IMPORTANT]
> - Az 1.0-s frissítés a **készülék 10.0.10296.0** szoftververziójának felel meg. A frissítés újdonságairól az [1.0-s frissítéshez kiadott megjegyzések](storsimple-virtual-array-update-1-release-notes.md)című részben talál további információt.
>
> - Ne feledje, hogy egy frissítés vagy gyorsjavítás telepítése újraindítja az eszközt. Tekintettel arra, hogy a StorSimple virtuális tömb egyetlen csomópontú eszköz, minden folyamatban lévő I/O megszakad, és az eszköz leállást tapasztal.
>
> - Az 1-es frissítés csak akkor érhető el az Azure Portalon, ha a virtuális tömb 0.6-os frissítést futtat. A 0.6-os frissítés előtti verziókat futtató virtuális tömbök esetében először telepítenie kell a 0.6-os frissítést, majd telepítenie kell az 1.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

A 0.2-es és újabb frissítés futtatásaesetén azt javasoljuk, hogy telepítse a frissítéseket az Azure Portalon keresztül. A portáleljárás megköveteli a felhasználótól a frissítések befrissítését, letöltését és telepítését. Attól függően, hogy a virtuális tömb a virtuális tömb szoftververzióját futtatja, a frissítés alkalmazása az Azure Portalon keresztül eltérő.

 - Ha a virtuális tömb 0.6-os frissítést futtat, az Azure Portal közvetlenül telepíti az 1. Ez az eljárás körülbelül 7 percet vesz igénybe.
 - Ha a virtuális tömb a 0.6-os frissítés előtti verziót futtatja, a frissítés két lépésben történik. Az Azure Portal először telepíti a 0.6-os frissítést (10.0.10293.0) az eszközre. A virtuális tömb újraindul, és a portál ezután telepíti az 1. Ez az eljárás körülbelül 15 percet vesz igénybe.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-1.md)]

A telepítés befejezése után nyissa meg a StorSimple Eszközkezelő szolgáltatást. Válassza **az Eszközök lehetőséget,** majd jelölje ki az imént frissített eszközt, és kattintson rájuk. Nyissa meg **a Beállítások > > eszközfrissítések kezelése**lehetőséget. A megjelenített szoftververziónak **10.0.10296.0-nak**kell lennie.

![Szoftververzió frissítés után](./media/storsimple-virtual-array-install-update-1/azupdate17m1.png)

## <a name="use-the-local-web-ui"></a>A helyi webes felhasználói felület használata

A helyi webes felhasználói felület használata két lépésből áll:

* A frissítés vagy a gyorsjavítás letöltése
* A frissítés vagy a gyorsjavítás telepítése

> [!IMPORTANT] 
> **Csak akkor folytassa a frissítést, ha a 0.6-os frissítést (10.0.10293.0) futtatja. Ha egy korábbi verziót futtat, először telepítse a [0.6-os frissítést](storsimple-virtual-array-install-update-06.md) az eszközére, majd alkalmazza az 1.**

### <a name="download-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás letöltése

Ha a virtuális tömb0.6-os frissítést futtatja, hajtsa végre az 1.

#### <a name="to-download-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás letöltése

1. Indítsa el az [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com)Internet Explorer t, és keresse meg a programot.

2. Ha először használja a Microsoft Update katalógust ezen a számítógépen, kattintson a **Telepítés** gombra, amikor a microsoft update katalógus bővítmény telepítésére van szüksége.

3. A Microsoft Update katalógus keresőmezőjébe írja be a letölteni kívánt gyorsjavítás Tudásbázisának (KB) számát. Írja be a **4047203-as értéket** az 1.0-s frissítéshez, majd kattintson a **Keresés**gombra.
   
    Megjelenik a gyorsjavítás-lista, például a **StorSimple Virtual Array Update 1.0**.
   
    ![Keresés a katalógusban](./media/storsimple-virtual-array-install-update-1/download1.png)

4. Kattintson **a Letöltés gombra.**

5. Töltse le a két fájlt egy mappába. A mappát az eszközről elérhető hálózati megosztásra is átmásolhatja.

6. Nyissa meg azt a mappát, amelyben a fájlok találhatók.

    ![Fájlok a csomagban](./media/storsimple-virtual-array-install-update-1/update01folder.png)

    Két fájl jelenik meg:
    -  A Microsoft Update önálló `WindowsTH-KB3011067-x64`csomagfájlja . Ez a fájl az eszköz szoftverének frissítésére szolgál.
    - Az augusztusi `windows8.1-kb4034681-x64`összesítő frissítéseket tartalmazó fájl. Ha többet szeretne tudni arról, hogy mi szerepel ebben az összesítésben, látogasson el az [augusztusi havi biztonsági összesítésre.](https://support.microsoft.com/help/4034681/windows-8-1-windows-server-2012-r2-update-kb40346810)

### <a name="install-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás telepítése

A frissítés vagy a gyorsjavítás telepítése előtt győződjön meg arról, hogy:

 - A frissítést vagy a gyorsjavítást helyileg töltötte le a gazdagépen, vagy egy hálózati megosztáson keresztül érhető el.
 - A virtuális tömb 0.6-os frissítése fut (10.0.10293.0). Ha a 0.6-os frissítést megelőzőverziót futtatja, először [telepítse a 0.6-os frissítést,](storsimple-virtual-array-install-update-06.md) majd telepítse az 1.

Ez az eljárás körülbelül 4 percet vesz igénybe. A frissítés vagy gyorsjavítás telepítéséhez hajtsa végre az alábbi lépéseket.

#### <a name="to-install-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás telepítése

1. A helyi internetes felhasználói felületen nyissa meg a **Karbantartási** > **szoftverfrissítés című webhelyet.** Jegyezze fel a futtatott szoftververziót. **Csak akkor folytassa a frissítést, ha a 0.6-os frissítést (10.0.10293.0) futtatja. Ha egy korábbi verziót futtat, először telepítse a [0.6-os frissítést](storsimple-virtual-array-install-update-06.md) az eszközére, majd alkalmazza az 1.**
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-1/update1m.png)

2. A **Fájl elérési útjának frissítése**mezőbe írja be a frissítés vagy a gyorsjavítás fájlnevét. A frissítési vagy gyorsjavítás-telepítési fájlba is tallózhat, ha hálózati megosztásra helyezi. Kattintson az **Alkalmaz** gombra.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-1/update2m.png)

3. Figyelmeztetés jelenik meg. Mivel a virtuális tömb egyetlen csomóponteszköz, a frissítés alkalmazása után az eszköz újraindul, és van állásidő. Kattintson az ellenőrzés ikonra.
   
   ![eszköz frissítése](./media/storsimple-virtual-array-install-update-1/update3m.png)

4. A frissítés elindul. Az eszköz sikeres frissítése után újraindul. A helyi felhasználói felület nem érhető el ebben az időtartamban.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-1/update5m.png)

5. Az újraindítás befejezése után a **bejelentkezési** lapra kerül. Az eszközszoftver frissítésének ellenőrzéséhez a helyi webes felhasználói felületen nyissa meg a **Karbantartási** > **szoftverfrissítés című**részt. A megjelenített szoftververziónak **10.0.0.0.0.10296-nak** kell lennie az 1.0-s frissítéshez.
   
   > [!NOTE]
   > A szoftververziókat némileg eltérő módon jelentjük a helyi webes felhasználói felületen és az Azure Portalon. A helyi webes felhasználói felület például **a 10.0.0.0.0.10296-ot** jelenti, az Azure Portal pedig **a 10.0.10296.0-t** ugyanahhoz a verzióhoz.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-1/update6m.png)

6. Ismételje meg a `windows8.1-kb4012213-x64`2-4. A virtuális tömb a telepítés után újraindul, és be kell jelentkeznie a helyi webes felhasználói felületre.

> [!NOTE]
> Ha közvetlenül alkalmazta az 1. A következő lépésekért forduljon a Microsoft támogatási szolgálatához.

## <a name="next-steps"></a>További lépések

További információ [a StorSimple virtuális tömb felügyeletéről.](storsimple-ova-web-ui-admin.md)
