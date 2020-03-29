---
title: 0.5 frissítés telepítése a StorSimple virtuális tömbre | Microsoft dokumentumok
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
ms.date: 05/10/2017
ms.author: alkohli
ms.openlocfilehash: e09ff4bcbc141b1a1f80bc278918a291639c1885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61445420"
---
# <a name="install-update-05-on-your-storsimple-virtual-array"></a>A 0.5-ös frissítés telepítése a StorSimple virtuális tömbre

## <a name="overview"></a>Áttekintés

Ez a cikk ismerteti a 0.5-ös frissítés telepítéséhez szükséges lépéseket a StorSimple virtuális tömba a helyi webes felhasználói felületen keresztül és az Azure Portalon keresztül. A StorSimple virtuális tömb naprakészen tartásához szoftverfrissítéseket vagy gyorsjavításokat kell alkalmaznia.

A frissítés alkalmazása előtt javasoljuk, hogy először a köteteket vagy megosztásokat állítsa offline állapotba a gazdagépen, majd az eszközön. Ezzel a lehető legkisebbre csökkenti az adatsérülés esélyét. Miután a kötetek vagy megosztások offline állapotba kerültek, manuális biztonsági másolatot kell készítenie az eszközről.

> [!IMPORTANT]
> - A 0.5-ös frissítés a **készülék 10.0.10290.0** szoftververziójának felel meg. A frissítés újdonságairól a [0.5-ös frissítéshez kiadott megjegyzések](storsimple-virtual-array-update-05-release-notes.md)című részben talál további információt.
>
> - Ha a 0.2-es vagy újabb frissítést futtatja, javasoljuk, hogy telepítse a frissítéseket az Azure Portalon keresztül. Ha a 0.1-es frissítés vagy a GA szoftververziókat futtatja, a 0.5-ös frissítés telepítéséhez a helyi webes felhasználói felületen keresztül kell használnia a gyorsjavítási módszert.
>
> - Ne feledje, hogy egy frissítés vagy gyorsjavítás telepítése újraindítja az eszközt. Tekintettel arra, hogy a StorSimple virtuális tömb egyetlen csomópontú eszköz, minden folyamatban lévő I/O megszakad, és az eszköz leállást tapasztal.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

A 0.2-es és újabb frissítés futtatásaesetén azt javasoljuk, hogy telepítse a frissítéseket az Azure Portalon keresztül. A portáleljárás megköveteli a felhasználótól a frissítések befrissítését, letöltését és telepítését. Ez az eljárás körülbelül 7 percet vesz igénybe. A frissítés vagy gyorsjavítás telepítéséhez hajtsa végre az alábbi lépéseket.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

A telepítés befejezése után nyissa meg a StorSimple Eszközkezelő szolgáltatást. Válassza **az Eszközök lehetőséget,** majd jelölje ki az imént frissített eszközt, és kattintson rájuk. Nyissa meg **a Beállítások > > eszközfrissítések kezelése**lehetőséget. A megjelenített szoftververziónak **10.0.10290.0-nak**kell lennie.

## <a name="use-the-local-web-ui"></a>A helyi webes felhasználói felület használata

A helyi webes felhasználói felület használata két lépésből áll:

* A frissítés vagy a gyorsjavítás letöltése
* A frissítés vagy a gyorsjavítás telepítése

### <a name="download-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás letöltése

Hajtsa végre a következő lépéseket a szoftverfrissítés a Microsoft Update katalógusból történő letöltéséhez.

#### <a name="to-download-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás letöltése

1. Indítsa el az [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com)Internet Explorer t, és keresse meg a programot.

2. Ha most használja először a Microsoft Update katalógust ezen a számítógépen, kattintson a **Telepítés** gombra, amikor a rendszer a Microsoft Update katalógus beépülő moduljának telepítésére kéri.

3. A Microsoft Update katalógus keresőmezőjébe írja be a letölteni kívánt gyorsjavítás Tudásbázisának (KB) számát. Írja be a **4021576 értéket** a 0.5-ös frissítéshez, majd kattintson a **Keresés**gombra.
   
    Megjelenik a gyorsjavítás-lista, például a **StorSimple Virtual Array Update 0.5**.
   
    ![Keresés a katalógusban](./media/storsimple-virtual-array-install-update-05/download1.png)

4. Kattintson **a Letöltés gombra.** 

5. Két letöltandó fájlnak kell lennie, egy *.msu* és egy *.cab* fájlnak. Töltse le ezeket a fájlokat egy mappába. A mappa átmásolható egy, az eszközről elérhető hálózati megosztásra is.

6. Nyissa meg azt a mappát, amelyben a fájlok találhatók.
    ![Fájlok a csomagban](./media/storsimple-virtual-array-install-update-05/update05folder.png)

    A következőt fogja látni:
    -  A Microsoft Update önálló `WindowsTH-KB3011067-x64`csomagfájlja . Ez a fájl az eszköz szoftverének frissítésére szolgál.
    - A genfi monitoring `GenevaMonitoringAgentPackageInstaller`ügynök csomag fájl . Ez a fájl a figyelési és diagnosztikai szolgáltatás (MDS) ügynökének frissítésére szolgál. Kattintson duplán a fülkefájlra. Egy .msi jelenik meg. Jelölje ki a fájlt, kattintson a jobb gombbal, majd **bontsa ki** a fájlt. Az _.msi_ fájl segítségével frissítheti az ügynököt.

        ![MDS-ügynökfrissítési fájl kibontása](./media/storsimple-virtual-array-install-update-05/extract-geneva-monitoring-agent-installer.png)
        
    

### <a name="install-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás telepítése

A frissítés vagy a gyorsjavítás telepítése előtt győződjön meg arról, hogy a frissítést vagy a gyorsjavítást helyileg töltötte le a gazdagépen, vagy elérhető egy hálózati megosztáson keresztül.

Ezzel a módszerrel ga vagy Update 0.1 szoftververziókat futtató eszközökön telepíthet frissítéseket. Ez az eljárás kevesebb, mint 2 percet vesz igénybe. A frissítés vagy gyorsjavítás telepítéséhez hajtsa végre az alábbi lépéseket.

#### <a name="to-install-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás telepítése

1. A helyi internetes felhasználói felületen nyissa meg a **Karbantartási** > **szoftverfrissítés című webhelyet.**
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. A **Fájl elérési útjának frissítése**mezőbe írja be a frissítés vagy a gyorsjavítás fájlnevét. A frissítési vagy gyorsjavítás-telepítési fájlba is tallózhat, ha hálózati megosztásra helyezi. Kattintson az **Alkalmaz** gombra.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. Figyelmeztetés jelenik meg. Mivel ez egy csomópontos eszköz, a frissítés alkalmazása után az eszköz újraindul, és van állásidő. Kattintson az ellenőrzés ikonra.
   
   ![eszköz frissítése](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. A frissítés elindul. Az eszköz sikeres frissítése után újraindul. A helyi felhasználói felület nem érhető el ebben az időtartamban.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. Az újraindítás befejezése után a **bejelentkezési** lapra kerül. Az eszközszoftver frissítésének ellenőrzéséhez a helyi webes felhasználói felületen nyissa meg a **Karbantartási** > **szoftverfrissítés című**részt. A megjelenített szoftververziónak **10.0.0.0.0.10290.0-nak** kell lennie a 0.5-ös frissítéshez.
   
   > [!NOTE]
   > A szoftververziókat némileg eltérő módon jelentjük a helyi webes felhasználói felületen és az Azure Portalon. A helyi webes felhasználói felület például **a 10.0.0.0.0.10290** jelentést, az Azure Portal on **10.0.10290.0** ugyanazért a verzióért.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-05/update6m.png)

6. A következő lépés az MDS-ügynök frissítése. A **Szoftverfrissítés** lapon nyissa meg a Fájl frissítése `GenevaMonitoringAgentPackageInstaller.msi` elérési **útját,** és keresse meg a fájlt. Ismételje meg a 2-4. A virtuális tömb újraindítása után jelentkezzen be a helyi webes felhasználói felületre.

A frissítés befejeződött.

## <a name="next-steps"></a>További lépések

További információ [a StorSimple virtuális tömb felügyeletéről.](storsimple-ova-web-ui-admin.md)

