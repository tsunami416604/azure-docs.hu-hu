---
title: "A frissítés 0,6 a StorSimple virtuális tömb |} Microsoft Docs"
description: "A StorSimple virtuális tömb webes felhasználói felület használata az Azure portál és a gyorsjavítások metódussal frissítés alkalmazása"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/18/2017
ms.author: alkohli
ms.openlocfilehash: 111976cd684561f5bc63b92f09a20470fe3036d7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="install-update-06-on-your-storsimple-virtual-array"></a>A StorSimple virtuális tömb 0,6 frissítés telepítése

## <a name="overview"></a>Áttekintés

Ez a cikk ismerteti a StorSimple virtuális tömb 0,6 frissítés telepítéséhez, és az Azure-portál a helyi webes felhasználói felületen keresztül szükséges lépéseket. A szoftverfrissítések vagy gyorsjavítások segítségével nyomon követheti a StorSimple virtuális tömbjét alkalmaz.

Egy frissítés alkalmazása előtt javasoljuk, hogy szánjon a kötetek vagy megosztások offline állapotba a gazdagépen első, majd az eszközről. Ez minimalizálja az adatvesztést lehetőségét. Után a kötetek vagy megosztások offline üzemmódban, akkor kell vennie kézi biztonsági mentési eszköz.

> [!IMPORTANT]
> - Frissítés 0,6 megfelel-e **10.0.10293.0** szoftverének verziójával, az eszközön. What's new in a frissítés információkért látogasson el [kibocsátási megjegyzései frissítés 0,6](storsimple-virtual-array-update-06-release-notes.md).
>
> - Ha a frissítés 0,2 futtatja, vagy később, akkor javasoljuk, hogy az Azure-portálon a frissítések telepítése. Ha a frissítés 0,1 vagy GA szoftver verziója fut, a gyorsjavítás módszer a helyi webes felhasználói felületen keresztül frissítés 0,6 kell használnia.
>
> - Ne feledje, hogy egy frissítés vagy gyorsjavítás telepítése az eszköz újraindul. Fényében, hogy a StorSimple virtuális tömb a egycsomópontos eszközről szó, a folyamatban lévő összes i/o megszakad, és az eszköz leállást észlel.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

Ha Update futtatása 0,2 és újabb verziók, azt javasoljuk, hogy telepítse a frissítéseket az Azure portálon keresztül. A portál eljárás a felhasználónak kell vizsgálat, töltse le és telepítse a frissítéseket. Ez az eljárás befejezéséhez körülbelül 7 perc. Hajtsa végre a következő lépésekkel telepíti a frissítést vagy gyorsjavítást.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

A telepítés befejezése után, nyissa meg a StorSimple eszköz Manager szolgáltatáshoz. Válassza ki **eszközök** majd válassza ki, és kattintson az eszköz nemrég frissített. Ugrás a **beállítások > kezelése > Eszközfrissítésekhez**. A megjelenített szoftverfrissítési verziót kell **10.0.10293.0**.

## <a name="use-the-local-web-ui"></a>A helyi webes felhasználói felület használata

Két lépésben történik a helyi webes felhasználói felület használata esetén:

* A frissítés vagy gyorsjavítás letöltése
* A frissítés vagy gyorsjavítás telepítése

### <a name="download-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás letöltése

Hajtsa végre a következő lépéseket a szoftverfrissítés a Microsoft Update katalógusból történő letöltéséhez.

#### <a name="to-download-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás letöltése

1. Indítsa el az Internet Explorert, és keresse fel a [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com) címet.

2. Ha először a számítógépen a Microsoft Update katalógus használ, kattintson a **telepítése** amikor a rendszer kéri a Microsoft Update katalógus bővítmény telepítése.

3. A keresési mezőbe, a Microsoft Update katalógus adja meg a Tudásbázis (KB) le szeretné tölteni a gyorsjavítást. Adja meg **4023268** frissítési 0,6, és kattintson a **keresési**.
   
    A gyorsjavítás-lista megjelenik, például **StorSimple virtuális tömb frissítés 0,6**.
   
    ![Keresés a katalógusban](./media/storsimple-virtual-array-install-update-06/download1.png)

4. Kattintson a **Letöltés** gombra.

5. Meg kell jelennie öt fájlok letöltéséhez. Töltse le ezeket a fájlokat egy mappát. A mappa átmásolható egy, az eszközről elérhető hálózati megosztásra is.

6. Nyissa meg a fájlokat tartalmazó mappát.
    ![A csomagban lévő fájlok](./media/storsimple-virtual-array-install-update-06/update06folder.png)

    Lásd:
    -  A Microsoft Update önálló csomagfájl `WindowsTH-KB3011067-x64`. Ezt a fájlt az eszköz frissítéséhez használt.
    - Geneva figyelési ügynök csomagfájl `GenevaMonitoringAgentPackageInstaller`. A megfigyelési és diagnosztikai szolgáltatás (MDS) ügynök frissítése a fájllal. Kattintson duplán a cab-fájl. A _.msi_ jelenik meg. Válassza ki a fájlt, kattintson a jobb gombbal, majd **kibontása** a fájlt. Használja a _.msi_ fájlt az ügynök frissítése.

        ![Az MDS-ügynök frissítése fájl kibontása](./media/storsimple-virtual-array-install-update-06/extract-geneva-monitoring-agent-installer.png)

        > [!IMPORTANT]
        > Nem kell az MDS-ügynök frissítése, ha a StorSimple frissítés 0,5 (0.0.10293.0) futtatja.

    - Három fontos Windows biztonsági frissítéseket tartalmazó fájlokat `windows8.1-kb4012213-x64`,`windows8.1-kb3205400-x64`, és `windows8.1-kb4019213-x64`.


### <a name="install-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás telepítése

A frissítés vagy gyorsjavítás telepítése előtt győződjön meg arról, hogy a frissítést, vagy a gyorsjavítás letöltése vagy a gazdagépen helyileg vagy egy hálózati megosztáson keresztül érhető el.

Ezt a módszert egy GA futtató eszközre telepítse a frissítéseket, vagy 0,1 szoftververziók frissítésére. Ez az eljárás körülbelül 12 percet is igénybe vehet. Hajtsa végre a következő lépésekkel telepíti a frissítést vagy gyorsjavítást.

#### <a name="to-install-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás telepítése

1. A helyi webes felhasználói felület váltson **karbantartási** > **szoftverfrissítés**. Jegyezze fel az Ön által futtatott szoftverének verziójával. Ha futtatja **10.0.10290.0**, nem kell frissíteni az MDS-ügynök, a 6. lépés.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. A **frissítés fájl elérési útját**, írja be a fájl nevét, a frissítés vagy gyorsjavítás. A frissítés vagy gyorsjavítás telepítési fájl is tallózással, ha egy hálózati megosztáson. Kattintson az **Alkalmaz** gombra.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. A figyelmeztetés akkor jelenik meg. A virtuális tömb a megadott van egy egycsomópontos eszköz után a frissítés nincs alkalmazva, az eszköz újraindul, és nincs leállás. Kattintson a pipa ikonra.
   
   ![eszköz frissítése](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. A frissítés elindul. Az eszköz sikeres frissítése után újraindul. Ennek az időtartamnak a helyi felhasználói felülete nem érhető el.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. Az újraindítás befejeződése után kattintva megnyílik a **bejelentkezés** lap. Győződjön meg arról, hogy az eszköz szoftvere frissítette, a helyi webes felhasználói felület, keresse fel **karbantartási** > **szoftverfrissítés**. A megjelenített szoftverfrissítési verziót kell **10.0.0.0.0.10293** 0,6 frissítés.
   
   > [!NOTE]
   > A szoftver verziójával egy némileg eltérő módon a helyi webes felhasználói felület és az Azure-portálon jelentés azt. Például a helyi webes felhasználói Felületét jelent **10.0.0.0.0.10293** és az Azure portál jelentések **10.0.10293.0** azonos verziójához.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-06/update6m.png)

6. Kihagyhatja ezt a lépést, ha a StorSimple virtuális tömb frissítés 0,5 futtatása során (**10.0.10290.0**) a frissítés alkalmazása előtt. A szoftververzió jegyezze fel az 1. lépésben végzett, a frissítés megkezdése előtt. Ha 0,5 frissítés futtatásakor, az MDS-ügynök már naprakész.

    Ha futtatja a szoftververzió 0,5 frissítés előtt, a következő lépés az Ön frissítése érdekében szükség az MDS-ügynök. A a **szoftverfrissítés** lap megnyitásához válassza a **frissítés fájl elérési útját** , és keresse meg a `GenevaMonitoringAgentPackageInstaller.msi` fájl. Ismételje meg a 2 – 4. A virtuális tömb újraindítása után jelentkezzen be a helyi webes felhasználói felületen.

7. Ismétlődő lépés 2 – 4 a Windows biztonsági javítások fájlokkal `windows8.1-kb4012213-x64`,`windows8.1-kb3205400-x64`, és `windows8.1-kb4019213-x64`. A virtuális tömb minden telepítése után újraindul, és jelentkezzen be a helyi webes felhasználói felület kell.

## <a name="next-steps"></a>Következő lépések

További információ [felügyelete a StorSimple virtuális tömb](storsimple-ova-web-ui-admin.md).

