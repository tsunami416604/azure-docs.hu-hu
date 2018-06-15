---
title: A frissítés 0,5 a StorSimple virtuális tömb |} Microsoft Docs
description: A StorSimple virtuális tömb webes felhasználói felület használata az Azure portál és a gyorsjavítások metódussal frissítés alkalmazása
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
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875661"
---
# <a name="install-update-05-on-your-storsimple-virtual-array"></a>A StorSimple virtuális tömb 0,5 frissítés telepítése

## <a name="overview"></a>Áttekintés

Ez a cikk ismerteti a StorSimple virtuális tömb 0,5 frissítés telepítéséhez, és az Azure-portál a helyi webes felhasználói felületen keresztül szükséges lépéseket. Szeretné alkalmazni a szoftverfrissítések vagy gyorsjavítások a StorSimple virtuális tömb naprakész állapotban tartása érdekében.

Egy frissítés alkalmazása előtt javasoljuk, hogy szánjon a kötetek vagy megosztások offline állapotba a gazdagépen első, majd az eszközről. Ez minimalizálja az adatvesztést lehetőségét. Után a kötetek vagy megosztások offline üzemmódban, akkor kell vennie kézi biztonsági mentési eszköz.

> [!IMPORTANT]
> - Frissítés 0,5 megfelel-e **10.0.10290.0** szoftverének verziójával, az eszközön. What's new in a frissítés információkért látogasson el [kibocsátási megjegyzései frissítés 0,5](storsimple-virtual-array-update-05-release-notes.md).
>
> - Ha a frissítés 0,2 futtatja, vagy később, akkor javasoljuk, hogy az Azure-portálon a frissítések telepítése. Ha a frissítés 0,1 vagy GA szoftver verziója fut, a gyorsjavítás módszer a helyi webes felhasználói felületen keresztül frissítés 0,5 kell használnia.
>
> - Ne feledje, hogy egy frissítés vagy gyorsjavítás telepítése az eszköz újraindul. Fényében, hogy a StorSimple virtuális tömb a egycsomópontos eszközről szó, a folyamatban lévő összes i/o megszakad, és az eszköz leállást észlel.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

Ha Update futtatása 0,2 és újabb verziók, azt javasoljuk, hogy telepítse a frissítéseket az Azure portálon keresztül. A portál eljárás a felhasználónak kell vizsgálat, töltse le és telepítse a frissítéseket. Ez az eljárás befejezéséhez körülbelül 7 perc. Hajtsa végre a következő lépésekkel telepíti a frissítést vagy gyorsjavítást.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

A telepítés befejezése után, nyissa meg a StorSimple eszköz Manager szolgáltatáshoz. Válassza ki **eszközök** majd válassza ki, és kattintson az eszköz nemrég frissített. Ugrás a **beállítások > kezelése > Eszközfrissítésekhez**. A megjelenített szoftverfrissítési verziót kell **10.0.10290.0**.

## <a name="use-the-local-web-ui"></a>A helyi webes felhasználói felület használata

Két lépésben történik a helyi webes felhasználói felület használata esetén:

* A frissítés vagy gyorsjavítás letöltése
* A frissítés vagy gyorsjavítás telepítése

### <a name="download-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás letöltése

Hajtsa végre a következő lépéseket a szoftverfrissítés a Microsoft Update katalógusból történő letöltéséhez.

#### <a name="to-download-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás letöltése

1. Indítsa el az Internet Explorert, és keresse fel a [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com) címet.

2. Ha most használja először a Microsoft Update katalógust ezen a számítógépen, kattintson a **Telepítés** gombra, amikor a rendszer a Microsoft Update katalógus beépülő moduljának telepítésére kéri.

3. A keresési mezőbe, a Microsoft Update katalógus adja meg a Tudásbázis (KB) le szeretné tölteni a gyorsjavítást. Adja meg **4021576** frissítési 0,5, és kattintson a **keresési**.
   
    A gyorsjavítás-lista megjelenik, például **StorSimple virtuális tömb frissítés 0,5**.
   
    ![Keresés a katalógusban](./media/storsimple-virtual-array-install-update-05/download1.png)

4. Kattintson a **Letöltés** gombra. 

5. Két fájlok letöltéséhez, megjelenik egy *.msu* és egy *.cab* fájlt. Töltse le ezeket a fájlokat egy mappát. A mappa átmásolható egy, az eszközről elérhető hálózati megosztásra is.

6. Nyissa meg a fájlokat tartalmazó mappát.
    ![A csomagban lévő fájlok](./media/storsimple-virtual-array-install-update-05/update05folder.png)

    Lásd:
    -  A Microsoft Update önálló csomagfájl `WindowsTH-KB3011067-x64`. Ezt a fájlt az eszköz frissítéséhez használt.
    - Geneva figyelési ügynök csomagfájl `GenevaMonitoringAgentPackageInstaller`. A megfigyelési és diagnosztikai szolgáltatás (MDS) ügynök frissítése a fájllal. Kattintson duplán a cab-fájl. Egy .msi jelenik meg. Válassza ki a fájlt, kattintson a jobb gombbal, majd **kibontása** a fájlt. Használhatja a _.msi_ fájlt az ügynök frissítése.

        ![Az MDS-ügynök frissítése fájl kibontása](./media/storsimple-virtual-array-install-update-05/extract-geneva-monitoring-agent-installer.png)
        
    

### <a name="install-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás telepítése

A frissítés vagy gyorsjavítás telepítése előtt győződjön meg arról, hogy a frissítést, vagy a gyorsjavítás letöltése vagy a gazdagépen helyileg vagy egy hálózati megosztáson keresztül érhető el.

Ezt a módszert egy GA futtató eszközre telepítse a frissítéseket, vagy 0,1 szoftververziók frissítésére. Ez az eljárás kisebb, mint 2 percet is igénybe vehet. Hajtsa végre a következő lépésekkel telepíti a frissítést vagy gyorsjavítást.

#### <a name="to-install-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás telepítése

1. A helyi webes felhasználói felület váltson **karbantartási** > **szoftverfrissítés**.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. A **frissítés fájl elérési útját**, írja be a fájl nevét, a frissítés vagy gyorsjavítás. A frissítés vagy gyorsjavítás telepítési fájl is tallózással, ha egy hálózati megosztáson. Kattintson az **Alkalmaz** gombra.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. A figyelmeztetés akkor jelenik meg. Ez a megadott van egy egycsomópontos eszköz után a frissítés nincs alkalmazva, az eszköz újraindul, és nincs leállás. Kattintson a pipa ikonra.
   
   ![eszköz frissítése](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. A frissítés elindul. Az eszköz sikeres frissítése után újraindul. Ennek az időtartamnak a helyi felhasználói felülete nem érhető el.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. Az újraindítás befejeződése után kattintva megnyílik a **bejelentkezés** lap. Győződjön meg arról, hogy az eszköz szoftvere frissítette, a helyi webes felhasználói felület, keresse fel **karbantartási** > **szoftverfrissítés**. A megjelenített szoftverfrissítési verziót kell **10.0.0.0.0.10290.0** 0,5 frissítés.
   
   > [!NOTE]
   > A szoftver verziójával egy némileg eltérő módon a helyi webes felhasználói felület és az Azure-portálon jelentés azt. Például a helyi webes felhasználói Felületét jelent **10.0.0.0.0.10290** és az Azure portál jelentések **10.0.10290.0** azonos verziójához.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-05/update6m.png)

6. A következő lépés, hogy frissítse az MDS-ügynököt. A a **szoftverfrissítés** lap megnyitásához válassza a **frissítés fájl elérési útját** , és keresse meg a `GenevaMonitoringAgentPackageInstaller.msi` fájl. Ismételje meg a 2 – 4. A virtuális tömb újraindítása után jelentkezzen be a helyi webes felhasználói felületen.

A frissítés most már befejeződött.

## <a name="next-steps"></a>Következő lépések

További információ [felügyelete a StorSimple virtuális tömb](storsimple-ova-web-ui-admin.md).

