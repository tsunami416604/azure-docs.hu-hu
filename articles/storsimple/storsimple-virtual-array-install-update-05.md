---
title: 0.5-ös frissítés telepítése a StorSimple Virtual Array |} A Microsoft Docs
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
ms.date: 05/10/2017
ms.author: alkohli
ms.openlocfilehash: c47da5b90c16e2d5b5709e2a6affc026238b9468
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38704521"
---
# <a name="install-update-05-on-your-storsimple-virtual-array"></a>A StorSimple Virtual Array 0.5-ös frissítés telepítése

## <a name="overview"></a>Áttekintés

Ez a cikk a StorSimple Virtual Array a 0.5-ös frissítés telepítése a helyi webes felhasználói felületen és az Azure Portalon keresztül szükséges lépéseket ismerteti. Szoftverfrissítések vagy gyorsjavítások a StorSimple Virtual Array tartsa naprakészen a alkalmazni kell.

Mielőtt alkalmazná a frissítést, javasoljuk, hogy tegye meg a kötetek vagy megosztások kapcsolat nélküli módban a gazdagépen első és az eszköz majd. Így minimálisra csökkenthető az adatsérülés lehetőségét. Miután a kötetek vagy megosztások offline állapotban, akkor kell is manuális biztonsági mentést az eszköz.

> [!IMPORTANT]
> - 0.5-ös frissítés megfelel **10.0.10290.0** szoftververzió az eszközön. Ez a frissítés újdonsága az információk, [kibocsátási megjegyzései 0.5-ös frissítés](storsimple-virtual-array-update-05-release-notes.md).
>
> - Ha 0.2-es frissítést futtatja, vagy később, akkor javasoljuk, hogy telepítse a frissítéseket az Azure Portalon keresztül. 0.1-es frissítés vagy a GA szoftververziók futtatásakor 0.5-ös frissítés telepítése a helyi webes felhasználói felületen a gyorsjavítás módszert kell használnia.
>
> - Ne feledje, hogy egy frissítés vagy gyorsjavítás telepítése az eszköz újraindul. Tekintve, hogy a StorSimple Virtual Array egy egyetlen csomóponttal rendelkező eszköz, a folyamatban lévő összes i/o megszakad, és a az eszköz állásidő élményhez.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

0,2 és újabb verzióit futtató frissítése, azt javasoljuk, hogy az Azure Portalon keresztül frissítések telepítése. A portál eljárás a felhasználónak vizsgálata, töltse le és telepítse a frissítéseket. Ez az eljárás végrehajtásához körülbelül 7 perc szükséges. A következő lépésekkel telepítse a frissítést vagy gyorsjavítást.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

A telepítés befejeződése után nyissa meg a StorSimple-Eszközkezelő szolgáltatásban. Válassza ki **eszközök** majd válassza ki, és kattintson az imént frissített eszköz. Lépjen a **beállítások > kezelése > Eszközfrissítések**. A megjelenített szoftverfrissítési verziónak kell lennie **10.0.10290.0**.

## <a name="use-the-local-web-ui"></a>A helyi webes felhasználói felület használata

Ha a helyi webes felhasználói felület még két lépésre van:

* A frissítés vagy gyorsjavítás letöltése
* A frissítés vagy gyorsjavítás telepítése

### <a name="download-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás letöltése

Hajtsa végre a következő lépéseket a szoftverfrissítés a Microsoft Update katalógusból történő letöltéséhez.

#### <a name="to-download-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás letöltése

1. Indítsa el az Internet Explorert és keresse meg [ http://catalog.update.microsoft.com ](http://catalog.update.microsoft.com).

2. Ha most használja először a Microsoft Update katalógust ezen a számítógépen, kattintson a **Telepítés** gombra, amikor a rendszer a Microsoft Update katalógus beépülő moduljának telepítésére kéri.

3. A keresőmezőbe, a Microsoft Update katalógus adja meg a letölteni kívánt gyorsjavítás Tudásbázis (KB) száma. Adja meg **4021576** a 0.5-ös frissítés, és kattintson **keresési**.
   
    A gyorsjavítás-lista megjelenik, például **StorSimple Virtual Array 0.5-ös frissítés**.
   
    ![Keresés a katalógusban](./media/storsimple-virtual-array-install-update-05/download1.png)

4. Kattintson a **Letöltés** gombra. 

5. Két fájlok letöltéséhez, megjelenik egy *.msu* és a egy *.cab* fájlt. Töltse le ezeket a fájlokat egy mappában. A mappa átmásolható egy, az eszközről elérhető hálózati megosztásra is.

6. Nyissa meg a fájlokat tartalmazó mappát.
    ![A csomagban lévő fájlok](./media/storsimple-virtual-array-install-update-05/update05folder.png)

    Lásd:
    -  A Microsoft Update önálló csomagfájl `WindowsTH-KB3011067-x64`. Ez a fájl frissítése az eszközhöz használatos.
    - Geneva Monitoring Agent csomagfájl `GenevaMonitoringAgentPackageInstaller`. Ez a fájl a Monitorozási és diagnosztikai szolgáltatás (MDS) ügynök frissítése szolgál. Kattintson duplán a cab-fájl. Egy .msi jelenik meg. Válassza ki a fájlt, kattintson a jobb gombbal, majd **kinyerése** a fájlt. Használhatja a _.msi_ fájlt az ügynök frissítése.

        ![Bontsa ki a fájlt MDS-ügynök frissítése](./media/storsimple-virtual-array-install-update-05/extract-geneva-monitoring-agent-installer.png)
        
    

### <a name="install-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás telepítése

A frissítés vagy gyorsjavítás telepítése előtt győződjön meg arról, hogy a frissítést, vagy a gyorsjavítás letöltése vagy helyileg, a gazdagépen vagy egy hálózati megosztáson keresztül érhető el.

Ez a módszer használatával egy GA futtató eszközre telepítse a frissítéseket, vagy 0,1 szoftververziók frissítése. Ez az eljárás végrehajtásához kevesebb mint 2 percet vesz igénybe. A következő lépésekkel telepítse a frissítést vagy gyorsjavítást.

#### <a name="to-install-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás telepítése

1. A helyi webes felhasználói felületen váltson **karbantartási** > **szoftverfrissítés**.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. A **frissítési fájl elérési útja**, adja meg a fájl nevét, a frissítés vagy gyorsjavítás. A frissítés vagy gyorsjavítás telepítési fájl is tallózással, ha egy hálózati megosztáson. Kattintson az **Alkalmaz** gombra.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. Egy figyelmeztetés jelenik meg. Ez a megadott van egy egyetlen csomóponttal rendelkező eszköz, miután a frissítés nincs alkalmazva, az eszköz újraindul, és üzemkimaradást okoz. Kattintson a pipa ikonra.
   
   ![eszköz frissítése](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. A frissítés elindításakor. Az eszköz sikeres frissítése után újraindul. A helyi felhasználói felületen Ez az időtartam nem érhető el.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. Az újraindítás befejeződése után megnyílik a **jelentkezzen be a** lapot. Győződjön meg arról, hogy az eszköz szoftverének frissítette, a helyi webes felhasználói felületen, lépjen a **karbantartási** > **szoftverfrissítés**. A megjelenített szoftverfrissítési verziónak kell lennie **10.0.0.0.0.10290.0** 0.5-ös frissítés számára.
   
   > [!NOTE]
   > A helyi webes felhasználói felület kissé másféle és az Azure Portalon a szoftver verziójával azt jelentést. Például a helyi webes felületén jelentések **10.0.0.0.0.10290** és az Azure portal jelentések **10.0.10290.0** esetében ugyanazt a verziót.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-05/update6m.png)

6. A következő lépés, hogy frissítse az MDS-ügynököt. Az a **szoftverfrissítés** lapon kattintson a **frissítési fájl elérési útja** , és keresse meg a `GenevaMonitoringAgentPackageInstaller.msi` fájlt. Ismételje meg a 2 – 4. A virtuális tömb újraindítása után jelentkezzen be a helyi webes felületén.

A frissítés befejeződött.

## <a name="next-steps"></a>További lépések

Tudjon meg többet [felügyelete a StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

