---
title: Telepítse az 1.1-es frissítést a StorSimple virtual array-re | Microsoft dokumentumok
description: A frissítések alkalmazása az Azure Portal on és a StorSimple virtual array helyi webes felhasználói felületével
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/18/2018
ms.author: alkohli
ms.openlocfilehash: 88b903d68e4398b4e30b0b7435279c29bee6cd6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254481"
---
# <a name="install-update-11-on-your-storsimple-virtual-array"></a>Az 1.1-es frissítés telepítése a StorSimple virtuális tömbre

## <a name="overview"></a>Áttekintés

Ez a cikk ismerteti az 1.1-es frissítés telepítéséhez szükséges lépéseket a StorSimple virtuális tömb a helyi webes felhasználói felületen keresztül és az Azure Portalon keresztül.

A szoftverfrissítéseket vagy gyorsjavításokat a StorSimple virtuális tömb naprakészen tartásához kell alkalmazni. A frissítés alkalmazása előtt javasoljuk, hogy először a köteteket vagy megosztásokat állítsa offline állapotba a gazdagépen, majd az eszközön. Ezzel a lehető legkisebbre csökkenti az adatsérülés esélyét. Miután a kötetek vagy megosztások offline állapotba kerültek, manuális biztonsági másolatot kell készítenie az eszközről.

> [!IMPORTANT]
> - Az 1.1-es frissítés a **készülék 10.0.10307.0** szoftververziójának felel meg. A frissítés újdonságairól az [1.1-es frissítéshez kiadott megjegyzések](storsimple-virtual-array-update-11-release-notes.md)című részben talál további információt.
>
> - Ne feledje, hogy egy frissítés vagy gyorsjavítás telepítése újraindítja az eszközt. Tekintettel arra, hogy a StorSimple virtuális tömb egyetlen csomópontú eszköz, minden folyamatban lévő I/O megszakad, és az eszköz leállást tapasztal.
>
> - Az 1.1-es frissítés csak akkor érhető el az Azure Portalon, ha a virtuális tömb 1. A 0.6-os verziót futtató virtuális tömbök esetében először telepítenie kell az 1.0-s frissítést, majd telepítenie kell az 1.1-es frissítést.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

A 0.2-es és újabb frissítés futtatásaesetén azt javasoljuk, hogy telepítse a frissítéseket az Azure Portalon keresztül. A portáleljárás megköveteli a felhasználótól a frissítések befrissítését, letöltését és telepítését. Attól függően, hogy a virtuális tömb a virtuális tömb szoftververzióját futtatja, a frissítés alkalmazása az Azure Portalon keresztül eltérő.

 - Ha a virtuális tömb 1. Ez az eljárás körülbelül 10 percet vesz igénybe.
 - Ha a virtuális tömb 0.6-os frissítést futtat, a frissítés két lépésben történik. Az Azure Portal először telepíti az 1.0-s frissítést (10.0.10296.0) az eszközre. A virtuális tömb újraindul, és a portál ezután telepíti az 1.1-es frissítést (10.0.10307.0) az eszközre. Ez az eljárás körülbelül 15 percet vesz igénybe.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-11.md)]

A telepítés befejezése után nyissa meg a StorSimple Eszközkezelő szolgáltatást. Válassza **az Eszközök lehetőséget,** majd jelölje ki az imént frissített eszközt, és kattintson rájuk. Nyissa meg **a Beállítások > > eszközfrissítések kezelése**lehetőséget. A megjelenített szoftververziónak **10.0.10307.0-nak**kell lennie.

![Szoftververzió frissítés után](./media/storsimple-virtual-array-install-update-11/azupdate17m2.png)

## <a name="use-the-local-web-ui"></a>A helyi webes felhasználói felület használata

A helyi webes felhasználói felület használata két lépésből áll:

* A frissítés vagy a gyorsjavítás letöltése
* A frissítés vagy a gyorsjavítás telepítése

> [!IMPORTANT] 
> **Csak akkor folytassa a frissítést, ha az 1. Ha a 0.6-os frissítést futtatja, először telepítse az [1.](storsimple-virtual-array-install-update-1.md)**

### <a name="download-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás letöltése

Az 1.1-es frissítés Microsoft Update katalógusból való letöltéséhez hajtsa végre az alábbi lépéseket.

#### <a name="to-download-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás letöltése

1. Indítsa el az [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com)Internet Explorer t, és keresse meg a programot.

2. Ha először használja a Microsoft Update katalógust ezen a számítógépen, kattintson a **Telepítés** gombra, amikor a microsoft update katalógus bővítmény telepítésére van szüksége.

3. A Microsoft Update katalógus keresőmezőjébe írja be a letölteni kívánt gyorsjavítás Tudásbázisának (KB) számát. Írja be a **4337628** értéket az 1.1-es frissítéshez, majd kattintson a **Keresés**gombra.
   
    Megjelenik a gyorsjavítás-lista, például a **StorSimple Virtual Array Update 1.1**.
   
    ![Keresés a katalógusban](./media/storsimple-virtual-array-install-update-11/download1.png)

4. Kattintson **a Letöltés gombra.**

5. Töltse le a két fájlt egy mappába. A mappát az eszközről elérhető hálózati megosztásra is átmásolhatja.

6. Nyissa meg azt a mappát, amelyben a fájlok találhatók.

    ![Fájlok a csomagban](./media/storsimple-virtual-array-install-update-11/update01folder.png)

    Két fájl jelenik meg:
    -  A Microsoft Update önálló `WindowsTH-KB3011067-x64`csomagfájlja . Ez a fájl az eszköz szoftverének frissítésére szolgál.
    - A júniusi `Windows8.1-KB4284815-x64`összesítő frissítéseket tartalmazó fájl. Ha többet szeretne tudni arról, hogy mi szerepel ebben az összesítésben, látogasson el a [júniusi havi biztonsági összesítésre.](https://support.microsoft.com/help/4284815/windows-81-update-kb4284815)

### <a name="install-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás telepítése

A frissítés vagy a gyorsjavítás telepítése előtt győződjön meg arról, hogy:

 - A frissítést vagy a gyorsjavítást helyileg töltötte le a gazdagépen, vagy egy hálózati megosztáson keresztül érhető el.
 - A virtuális tömb az 1. Ha a 0.6-os frissítést futtatja, először [telepítse az 1.1-es frissítést,](storsimple-virtual-array-install-update-1.md) majd telepítse az 1.1-es frissítést.

Ez az eljárás körülbelül 4 percet vesz igénybe. A frissítés vagy gyorsjavítás telepítéséhez hajtsa végre az alábbi lépéseket.

#### <a name="to-install-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás telepítése

1. A helyi internetes felhasználói felületen nyissa meg a **Karbantartási** > **szoftverfrissítés című webhelyet.** Jegyezze fel a futtatott szoftververziót. **Csak akkor folytassa a frissítést, ha az 1. Ha a 0.6-os frissítést futtatja, először telepítse az [1.](storsimple-virtual-array-install-update-1.md)**
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-11/update1m.png)

2. A **Fájl elérési útjának frissítése**mezőbe írja be a frissítés vagy a gyorsjavítás fájlnevét. A frissítési vagy gyorsjavítás-telepítési fájlba is tallózhat, ha hálózati megosztásra helyezi. Kattintson az **Alkalmaz** gombra.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-11/update2m.png)

3. Figyelmeztetés jelenik meg. Mivel a virtuális tömb egyetlen csomóponteszköz, a frissítés alkalmazása után az eszköz újraindul, és van állásidő. Kattintson az ellenőrzés ikonra.
   
   ![eszköz frissítése](./media/storsimple-virtual-array-install-update-11/update3m.png)

4. A frissítés elindul. Az eszköz sikeres frissítése után újraindul. A helyi felhasználói felület nem érhető el ebben az időtartamban.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-11/update5m.png)

5. Az újraindítás befejezése után a **bejelentkezési** lapra kerül. Az eszközszoftver frissítésének ellenőrzéséhez a helyi webes felhasználói felületen nyissa meg a **Karbantartási** > **szoftverfrissítés című**részt. A megjelenített szoftververziónak **10.0.0.0.10307-nek** kell lennie az 1.1-es frissítéshez.
   
   > [!NOTE]
   > A szoftververziókat némileg eltérő módon jelentjük a helyi webes felhasználói felületen és az Azure Portalon. A helyi webes felhasználói felület például **a 10.0.0.0.0.10307-et** jelenti, az Azure Portal pedig ugyanahhoz a verzióhoz a **10.0.10307.0-t.**
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-11/update6m.png)

6. Ismételje meg a `Windows8.1-KB4284815-x64`2-4. A virtuális tömb a telepítés után újraindul, és be kell jelentkeznie a helyi webes felhasználói felületre.


## <a name="next-steps"></a>További lépések

További információ [a StorSimple virtuális tömb felügyeletéről.](storsimple-ova-web-ui-admin.md)
