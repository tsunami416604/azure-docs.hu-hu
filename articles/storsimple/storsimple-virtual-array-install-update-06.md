---
title: 0.6-os frissítés telepítése a StorSimple Virtual Array |} A Microsoft Docs
description: A StorSimple Virtual Array webes felhasználói felület használata az Azure portal és a gyorsjavítások módszerrel frissítés alkalmazása
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
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58002791"
---
# <a name="install-update-06-on-your-storsimple-virtual-array"></a>A StorSimple virtuális tömb a 0.6-os frissítés telepítése

## <a name="overview"></a>Áttekintés

Ez a cikk a StorSimple virtuális tömb a 0.6-os frissítés telepítése a helyi webes felhasználói felületen és az Azure Portalon keresztül szükséges lépéseket ismerteti. A szoftverfrissítések vagy gyorsjavítások naprakészen tartani a StorSimple Virtual Array alkalmaz.

Mielőtt alkalmazná a frissítést, javasoljuk, hogy tegye meg a kötetek vagy megosztások kapcsolat nélküli módban a gazdagépen első és az eszköz majd. Ezzel a lehető legkisebbre csökkenti az adatsérülés esélyét. Miután a kötetek vagy megosztások offline állapotban, akkor kell is manuális biztonsági mentést az eszköz.

> [!IMPORTANT]
> - 0.6-os frissítés megfelel **10.0.10293.0** szoftververzió az eszközön. Ez a frissítés újdonsága az információk, [kibocsátási megjegyzések a 0.6-os frissítés](storsimple-virtual-array-update-06-release-notes.md).
>
> - Ha 0.2-es frissítést futtatja, vagy később, akkor javasoljuk, hogy telepítse a frissítéseket az Azure Portalon keresztül. 0.1-es frissítés vagy a GA szoftververziók futtatja, ha a gyorsjavítás metódus a helyi webes felhasználói felületen kell használnia a 0.6-os frissítés telepítése.
>
> - Ne feledje, hogy egy frissítés vagy gyorsjavítás telepítése az eszköz újraindul. Tekintve, hogy a StorSimple Virtual Array egy egyetlen csomóponttal rendelkező eszköz, a folyamatban lévő összes i/o megszakad, és a az eszköz állásidő élményhez.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

0,2 és újabb verzióit futtató frissítése, azt javasoljuk, hogy az Azure Portalon keresztül frissítések telepítése. A portál eljárás a felhasználónak vizsgálata, töltse le és telepítse a frissítéseket. Ez az eljárás végrehajtásához körülbelül 7 perc szükséges. A következő lépésekkel telepítse a frissítést vagy gyorsjavítást.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

A telepítés befejeződése után nyissa meg a StorSimple-Eszközkezelő szolgáltatásban. Válassza ki **eszközök** majd válassza ki, és kattintson az imént frissített eszköz. Lépjen a **beállítások > kezelése > Eszközfrissítések**. A megjelenített szoftverfrissítési verziónak kell lennie **10.0.10293.0**.

## <a name="use-the-local-web-ui"></a>A helyi webes felhasználói felület használata

Ha a helyi webes felhasználói felület még két lépésre van:

* A frissítés vagy gyorsjavítás letöltése
* A frissítés vagy gyorsjavítás telepítése

### <a name="download-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás letöltése

Hajtsa végre a következő lépéseket a szoftverfrissítés a Microsoft Update katalógusból történő letöltéséhez.

#### <a name="to-download-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás letöltése

1. Indítsa el az Internet Explorert és keresse meg [ https://catalog.update.microsoft.com ](https://catalog.update.microsoft.com).

2. Ha a Microsoft Update katalógust ezen a számítógépen először használ, kattintson a **telepítése** , amikor a Microsoft Update katalógus beépülő moduljának telepítésére kéri.

3. A keresőmezőbe, a Microsoft Update katalógus adja meg a letölteni kívánt gyorsjavítás Tudásbázis (KB) száma. Adja meg **4023268** esetében frissítse a 0.6-os, és kattintson **keresési**.
   
    A gyorsjavítás-lista megjelenik, például **StorSimple Virtual Array 0.6-os frissítés**.
   
    ![Keresés a katalógusban](./media/storsimple-virtual-array-install-update-06/download1.png)

4. Kattintson a **Letöltés** gombra.

5. Megtekintheti az öt fájl letöltéséhez. Töltse le ezeket a fájlokat egy mappában. A mappa átmásolható egy, az eszközről elérhető hálózati megosztásra is.

6. Nyissa meg a fájlokat tartalmazó mappát.
    ![A csomagban lévő fájlok](./media/storsimple-virtual-array-install-update-06/update06folder.png)

    A következőt fogja látni:
    -  A Microsoft Update önálló csomagfájl `WindowsTH-KB3011067-x64`. Ez a fájl frissítése az eszközhöz használatos.
    - Geneva Monitoring Agent csomagfájl `GenevaMonitoringAgentPackageInstaller`. Ez a fájl a Monitorozási és diagnosztikai szolgáltatás (MDS) ügynök frissítése szolgál. Kattintson duplán a cab-fájl. A _.msi_ jelenik meg. Válassza ki a fájlt, kattintson a jobb gombbal, majd **kinyerése** a fájlt. Használja a _.msi_ fájlt az ügynök frissítése.

        ![Bontsa ki a fájlt MDS-ügynök frissítése](./media/storsimple-virtual-array-install-update-06/extract-geneva-monitoring-agent-installer.png)

        > [!IMPORTANT]
        > Nem kell az MDS-ügynök frissítése, ha futtatja a StorSimple Update 0.5-ös (0.0.10293.0).

    - Három fontos Windows biztonsági frissítéseket tartalmazó fájlokat `windows8.1-kb4012213-x64`,`windows8.1-kb3205400-x64`, és `windows8.1-kb4019213-x64`.


### <a name="install-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás telepítése

A frissítés vagy gyorsjavítás telepítése előtt győződjön meg arról, hogy a frissítést, vagy a gyorsjavítás letöltése vagy helyileg, a gazdagépen vagy egy hálózati megosztáson keresztül érhető el.

Ez a módszer használatával egy GA futtató eszközre telepítse a frissítéseket, vagy 0,1 szoftververziók frissítése. Ez az eljárás végrehajtásához hozzávetőlegesen 12 percet vesz igénybe. A következő lépésekkel telepítse a frissítést vagy gyorsjavítást.

#### <a name="to-install-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás telepítése

1. A helyi webes felhasználói felületen váltson **karbantartási** > **szoftverfrissítés**. Jegyezze fel az Ön által futtatott szoftververzió. Ha futtatja **10.0.10290.0**, nem kell frissíteni a 6. lépés az MDS-ügynökhöz.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. A **frissítési fájl elérési útja**, adja meg a fájl nevét, a frissítés vagy gyorsjavítás. A frissítés vagy gyorsjavítás telepítési fájl is tallózással, ha egy hálózati megosztáson. Kattintson az **Alkalmaz** gombra.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. Egy figyelmeztetés jelenik meg. A virtuális tömb a megadott van egy egyetlen csomóponttal rendelkező eszköz, miután a frissítés nincs alkalmazva, az eszköz újraindul, és üzemkimaradást okoz. Kattintson a pipa ikonra.
   
   ![eszköz frissítése](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. A frissítés elindításakor. Az eszköz sikeres frissítése után újraindul. A helyi felhasználói felületen Ez az időtartam nem érhető el.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. Az újraindítás befejeződése után megnyílik a **jelentkezzen be a** lapot. Győződjön meg arról, hogy az eszköz szoftverének frissítette, a helyi webes felhasználói felületen, lépjen a **karbantartási** > **szoftverfrissítés**. A megjelenített szoftverfrissítési verziónak kell lennie **10.0.0.0.0.10293** a 0.6-os frissítés.
   
   > [!NOTE]
   > A helyi webes felhasználói felület kissé másféle és az Azure Portalon a szoftver verziójával azt jelentést. Például a helyi webes felületén jelentések **10.0.0.0.0.10293** és az Azure portal jelentések **10.0.10293.0** esetében ugyanazt a verziót.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-06/update6m.png)

6. Ezt a lépést kihagyhatja, ha az a StorSimple Virtual Array 0.5-ös frissítés (**10.0.10290.0**) Ez a frissítés alkalmazása előtt. Jegyezze fel a szoftververzió végzett az 1. lépésben a frissítés megkezdése előtt. 0.5-ös frissítés futtatna, akkor az MDS-ügynök már naprakész.

    0.5-ös frissítés előtti szoftvert verziót futtatja, ha az Ön számára a következő lépés az MDS-ügynök frissítése. Az a **szoftverfrissítés** lapon kattintson a **frissítési fájl elérési útja** , és keresse meg a `GenevaMonitoringAgentPackageInstaller.msi` fájlt. Ismételje meg a 2 – 4. A virtuális tömb újraindítása után jelentkezzen be a helyi webes felületén.

7. Ismételje meg a műveletet. lépés 2 – 4 a Windows biztonsági javításokat fájlok `windows8.1-kb4012213-x64`,`windows8.1-kb3205400-x64`, és `windows8.1-kb4019213-x64`. A virtuális tömb minden egyes telepítése után újraindul, és, be kell jelentkeznie a helyi webes felületén.

## <a name="next-steps"></a>További lépések

Tudjon meg többet [felügyelete a StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

