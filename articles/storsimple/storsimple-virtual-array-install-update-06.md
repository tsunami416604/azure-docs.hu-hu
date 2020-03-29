---
title: A 0.6-os frissítés telepítése a StorSimple virtual array-re | Microsoft dokumentumok
description: A storSimple virtuális tömb webes felhasználói felületének használata a frissítések azure portal és gyorsjavítási módszer rel történő alkalmazásához
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/18/2017
ms.author: alkohli
ms.openlocfilehash: 5f0be5d8378cd1640d3052f2e56c8161e2c0b203
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "62116891"
---
# <a name="install-update-06-on-your-storsimple-virtual-array"></a>A 0.6-os frissítés telepítése a StorSimple virtuális tömbre

## <a name="overview"></a>Áttekintés

Ez a cikk ismerteti a 0.6-os frissítés telepítéséhez szükséges lépéseket a StorSimple virtuális tömba a helyi webes felhasználói felületen keresztül és az Azure Portalon keresztül. A szoftverfrissítéseket vagy gyorsjavításokat a StorSimple virtuális tömb naprakészen tartásához kell alkalmazni.

A frissítés alkalmazása előtt javasoljuk, hogy először a köteteket vagy megosztásokat állítsa offline állapotba a gazdagépen, majd az eszközön. Ezzel a lehető legkisebbre csökkenti az adatsérülés esélyét. Miután a kötetek vagy megosztások offline állapotba kerültek, manuális biztonsági másolatot kell készítenie az eszközről.

> [!IMPORTANT]
> - A 0.6-os frissítés a **készülék 10.0.10293.0** szoftververziójának felel meg. A frissítés újdonságairól a [0.6-os frissítéshez kiadott megjegyzések](storsimple-virtual-array-update-06-release-notes.md)című részben talál további információt.
>
> - Ha a 0.2-es vagy újabb frissítést futtatja, javasoljuk, hogy telepítse a frissítéseket az Azure Portalon keresztül. Ha a 0.1-es frissítés vagy a GA szoftververziókat futtatja, a 0.6-os frissítés telepítéséhez a helyi webes felhasználói felületen keresztül kell használnia a gyorsjavítási módszert.
>
> - Ne feledje, hogy egy frissítés vagy gyorsjavítás telepítése újraindítja az eszközt. Tekintettel arra, hogy a StorSimple virtuális tömb egyetlen csomópontú eszköz, minden folyamatban lévő I/O megszakad, és az eszköz leállást tapasztal.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

A 0.2-es és újabb frissítés futtatásaesetén azt javasoljuk, hogy telepítse a frissítéseket az Azure Portalon keresztül. A portáleljárás megköveteli a felhasználótól a frissítések befrissítését, letöltését és telepítését. Ez az eljárás körülbelül 7 percet vesz igénybe. A frissítés vagy gyorsjavítás telepítéséhez hajtsa végre az alábbi lépéseket.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

A telepítés befejezése után nyissa meg a StorSimple Eszközkezelő szolgáltatást. Válassza **az Eszközök lehetőséget,** majd jelölje ki az imént frissített eszközt, és kattintson rájuk. Nyissa meg **a Beállítások > > eszközfrissítések kezelése**lehetőséget. A megjelenített szoftververziónak **10.0.10293.0-nak**kell lennie.

## <a name="use-the-local-web-ui"></a>A helyi webes felhasználói felület használata

A helyi webes felhasználói felület használata két lépésből áll:

* A frissítés vagy a gyorsjavítás letöltése
* A frissítés vagy a gyorsjavítás telepítése

### <a name="download-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás letöltése

Hajtsa végre a következő lépéseket a szoftverfrissítés a Microsoft Update katalógusból történő letöltéséhez.

#### <a name="to-download-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás letöltése

1. Indítsa el az [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com)Internet Explorer t, és keresse meg a programot.

2. Ha először használja a Microsoft Update katalógust ezen a számítógépen, kattintson a **Telepítés** gombra, amikor a microsoft update katalógus bővítmény telepítésére van szüksége.

3. A Microsoft Update katalógus keresőmezőjébe írja be a letölteni kívánt gyorsjavítás Tudásbázisának (KB) számát. Írja be a **4023268** értéket a 0.6-os frissítéshez, majd kattintson a **Keresés**gombra.
   
    Megjelenik a gyorsjavítás-lista, például a **StorSimple Virtual Array Update 0.6**.
   
    ![Keresés a katalógusban](./media/storsimple-virtual-array-install-update-06/download1.png)

4. Kattintson **a Letöltés gombra.**

5. Öt letöltandó fájlnak kell lennie. Töltse le ezeket a fájlokat egy mappába. A mappa átmásolható egy, az eszközről elérhető hálózati megosztásra is.

6. Nyissa meg azt a mappát, amelyben a fájlok találhatók.
    ![Fájlok a csomagban](./media/storsimple-virtual-array-install-update-06/update06folder.png)

    A következőt fogja látni:
    -  A Microsoft Update önálló `WindowsTH-KB3011067-x64`csomagfájlja . Ez a fájl az eszköz szoftverének frissítésére szolgál.
    - A genfi monitoring `GenevaMonitoringAgentPackageInstaller`ügynök csomag fájl . Ez a fájl a figyelési és diagnosztikai szolgáltatás (MDS) ügynökének frissítésére szolgál. Kattintson duplán a fülkefájlra. Egy _.msi jelenik_ meg. Jelölje ki a fájlt, kattintson a jobb gombbal, majd **bontsa ki** a fájlt. Az _.msi_ fájl segítségével frissítheti az ügynököt.

        ![MDS-ügynökfrissítési fájl kibontása](./media/storsimple-virtual-array-install-update-06/extract-geneva-monitoring-agent-installer.png)

        > [!IMPORTANT]
        > A StorSimple 0.5-ös (0.0.10293.0) frissítése esetén nem kell frissítenie az MDS-ügynököt.

    - Három fájl, amelyek a `windows8.1-kb4012213-x64`Windows`windows8.1-kb3205400-x64`kritikus `windows8.1-kb4019213-x64`biztonsági frissítéseit , , és .


### <a name="install-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás telepítése

A frissítés vagy a gyorsjavítás telepítése előtt győződjön meg arról, hogy a frissítést vagy a gyorsjavítást helyileg töltötte le a gazdagépen, vagy elérhető egy hálózati megosztáson keresztül.

Ezzel a módszerrel ga vagy Update 0.1 szoftververziókat futtató eszközökön telepíthet frissítéseket. Ez az eljárás körülbelül 12 percet vesz igénybe. A frissítés vagy gyorsjavítás telepítéséhez hajtsa végre az alábbi lépéseket.

#### <a name="to-install-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás telepítése

1. A helyi internetes felhasználói felületen nyissa meg a **Karbantartási** > **szoftverfrissítés című webhelyet.** Jegyezze fel a futtatott szoftververziót. Ha a **10.0.10290.0-t**futtatja, nem kell frissítenie az MDS-ügynököt a 6.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. A **Fájl elérési útjának frissítése**mezőbe írja be a frissítés vagy a gyorsjavítás fájlnevét. A frissítési vagy gyorsjavítás-telepítési fájlba is tallózhat, ha hálózati megosztásra helyezi. Kattintson az **Alkalmaz** gombra.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. Figyelmeztetés jelenik meg. Mivel a virtuális tömb egyetlen csomóponteszköz, a frissítés alkalmazása után az eszköz újraindul, és van állásidő. Kattintson az ellenőrzés ikonra.
   
   ![eszköz frissítése](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. A frissítés elindul. Az eszköz sikeres frissítése után újraindul. A helyi felhasználói felület nem érhető el ebben az időtartamban.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. Az újraindítás befejezése után a **bejelentkezési** lapra kerül. Az eszközszoftver frissítésének ellenőrzéséhez a helyi webes felhasználói felületen nyissa meg a **Karbantartási** > **szoftverfrissítés című**részt. A megjelenített szoftververziónak **10.0.0.0.0.10293-nak** kell lennie a 0.6-os frissítéshez.
   
   > [!NOTE]
   > A szoftververziókat némileg eltérő módon jelentjük a helyi webes felhasználói felületen és az Azure Portalon. A helyi webes felhasználói felület például **a 10.0.0.0.0.10293-at** jelenti, az Azure Portal pedig **a 10.0.10293.0-t** ugyanahhoz a verzióhoz.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-06/update6m.png)

6. A frissítés alkalmazása előtt hagyja ki ezt a lépést, ha a StorSimple Virtual Array Update 0.5 (**10.0.10290.0**) frissítést futtatta. A frissítés megkezdése előtt az 1. Ha a 0.5-ös frissítést futtatta, az MDS-ügynök már naprakész.

    Ha a 0.5-ös frissítés előtt szoftververziót futtat, a következő lépés az MDS-ügynök frissítése. A **Szoftverfrissítés** lapon nyissa meg a Fájl frissítése `GenevaMonitoringAgentPackageInstaller.msi` elérési **útját,** és keresse meg a fájlt. Ismételje meg a 2-4. A virtuális tömb újraindítása után jelentkezzen be a helyi webes felhasználói felületre.

7. A Windows biztonsági javításának fájlok `windows8.1-kb4012213-x64``windows8.1-kb3205400-x64`használatával történő telepítéséhez ismételje meg a `windows8.1-kb4019213-x64`2-4. A virtuális tömb minden telepítés után újraindul, és be kell jelentkeznie a helyi webes felhasználói felületre.

## <a name="next-steps"></a>További lépések

További információ [a StorSimple virtuális tömb felügyeletéről.](storsimple-ova-web-ui-admin.md)

