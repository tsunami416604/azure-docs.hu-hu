---
title: Telepítse a frissítéseket a Microsoft Azure StorSimple virtuális tömb |} Microsoft Docs
description: A StorSimple virtuális tömb webes felhasználói felület használata a portál és a gyorsjavítások metódussal frissítés alkalmazása
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 9997a97b-9382-43ed-b56e-61369335c987
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c2d081604c0ca01f47c3ff2aab7477859d280963
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876025"
---
# <a name="install-updates-on-your-storsimple-virtual-array---azure-portal"></a>Telepítse a frissítéseket a StorSimple virtuális tömb – az Azure-portálon

## <a name="overview"></a>Áttekintés

Ez a cikk azt ismerteti, hogyan frissítések települnek arra a StorSimple virtuális tömb és az Azure-portál a helyi webes felhasználói felületen keresztül. Szeretné alkalmazni a szoftverfrissítések vagy gyorsjavítások a StorSimple virtuális tömb naprakész állapotban tartása érdekében. 

Ne feledje, hogy egy frissítés vagy gyorsjavítás telepítése az eszköz újraindul. Fényében, hogy a StorSimple virtuális tömb a egycsomópontos eszközről szó, a folyamatban lévő összes i/o megszakad, és az eszköz leállást észlel. 

Egy frissítés alkalmazása előtt javasoljuk, hogy szánjon a kötetek vagy megosztások offline állapotba a gazdagépen első, majd az eszközről. Ez minimalizálja az adatvesztést lehetőségét.

> [!IMPORTANT]
> Ha a frissítés 0,1 vagy GA szoftver verziója fut, a gyorsjavítás módszer a helyi webes felhasználói felületen keresztül frissítés 0,3 kell használnia. Ha futtatja a frissítési 0,2, azt javasoljuk, hogy telepítse a frissítéseket, a klasszikus Azure portálon keresztül.
 

## <a name="use-the-local-web-ui"></a>A helyi webes felhasználói felület használata

Két lépésben történik a helyi webes felhasználói felület használata esetén:

* A frissítés vagy gyorsjavítás letöltése
* A frissítés vagy gyorsjavítás telepítése

### <a name="download-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás letöltése

Hajtsa végre a következő lépéseket a szoftverfrissítés a Microsoft Update katalógusból történő letöltéséhez.

#### <a name="to-download-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás letöltése

1. Indítsa el az Internet Explorert, és keresse fel a [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com) címet.

2. Ha most használja először a Microsoft Update katalógust ezen a számítógépen, kattintson a **Telepítés** gombra, amikor a rendszer a Microsoft Update katalógus beépülő moduljának telepítésére kéri.

3. A keresési mezőbe, a Microsoft Update katalógus adja meg a Tudásbázis (KB) le szeretné tölteni a gyorsjavítást. Adja meg **3182061** frissítési 0,3, és kattintson a **keresési**.
   
    A gyorsjavítás-lista megjelenik, például **StorSimple virtuális tömb frissítés 0,3**.
   
    ![Keresés a katalógusban](./media/storsimple-virtual-array-install-update/download1.png)

4. Kattintson az **Add** (Hozzáadás) parancsra. Ezzel a frissítést hozzáadja a kosárhoz.

5. Kattintson a **Kosár megtekintése** gombra.

6. Kattintson a **Letöltés** gombra. Adja meg vagy **tallózással** válassza ki a helyet, ahová a fájlokat le szeretné tölteni. A frissítések a megadott helyre lesznek letöltve, azon belül az egyes frissítések nevével egyező nevű almappákba. A mappa átmásolható egy, az eszközről elérhető hálózati megosztásra is.

7. Nyissa meg a másolt mappát, a Microsoft Update önálló csomagfájl kell megjelennie `WindowsTH-KB3011067-x64`. A frissítés vagy gyorsjavítás telepítése a fájllal.

### <a name="install-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás telepítése

A frissítés vagy gyorsjavítás telepítése előtt győződjön meg arról, hogy a frissítést, vagy a gyorsjavítás letöltése vagy a gazdagépen helyileg vagy egy hálózati megosztáson keresztül érhető el. 

Ezt a módszert egy GA futtató eszközre telepítse a frissítéseket, vagy 0,1 szoftververziók frissítésére. Ez az eljárás kisebb, mint 2 percet is igénybe vehet. Hajtsa végre a következő lépésekkel telepíti a frissítést vagy gyorsjavítást.

#### <a name="to-install-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás telepítése

1. A helyi webes felhasználói felület váltson **karbantartási** > **szoftverfrissítés**.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update/update1m.png)

2. A **frissítés fájl elérési útját**, írja be a fájl nevét, a frissítés vagy gyorsjavítás. A frissítés vagy gyorsjavítás telepítési fájl is tallózással, ha egy hálózati megosztáson. Kattintson az **Alkalmaz** gombra.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update/update2m.png)

3. A figyelmeztetés akkor jelenik meg. Ez a megadott van egy egycsomópontos eszköz után a frissítés nincs alkalmazva, az eszköz újraindul, és nincs leállás. Kattintson a pipa ikonra.
   
   ![eszköz frissítése](./media/storsimple-virtual-array-install-update/update3m.png)

4. A frissítés elindul. Az eszköz sikeres frissítése után újraindul. Ennek az időtartamnak a helyi felhasználói felülete nem érhető el.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update/update5m.png)

5. Az újraindítás befejeződése után kattintva megnyílik a **bejelentkezés** lap. Győződjön meg arról, hogy az eszköz szoftvere frissítette, a helyi webes felhasználói felület, keresse fel **karbantartási** > **szoftverfrissítés**. A megjelenített szoftverfrissítési verziót kell **10.0.0.0.0.10288.0** 0,3 frissítés.
   
   > [!NOTE]
   > A szoftver verziójával egy némileg eltérő módon a helyi webes felhasználói felület és az Azure-portálon jelentés azt. Például a helyi webes felhasználói Felületét jelent **10.0.0.0.0.10288** és az Azure portál jelentések **10.0.10288.0** azonos verziójához.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update/update6m.png)

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

Ha frissítés 0,2 fut, javasoljuk, hogy telepítse a frissítéseket az Azure portálon keresztül. A portál eljárás a felhasználónak kell vizsgálat, töltse le és telepítse a frissítéseket. Ez az eljárás befejezéséhez körülbelül 7 perc. Hajtsa végre a következő lépésekkel telepíti a frissítést vagy gyorsjavítást.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal.md)]

A telepítés befejezése után (a feladatok állapota a 100 %-os), nyissa meg a StorSimple eszköz Manager szolgáltatáshoz. Válassza ki **eszközök** majd jelölje ki, és kattintson az eszközre, ezt a szolgáltatást a csatlakoztatott eszközök közül frissíti. Az a **beállítások** paneljén lépjen **kezelése** válassza ki azt **eszközfrissítésekhez**. A megjelenített szoftverfrissítési verziót kell **10.0.10288.0**.


## <a name="next-steps"></a>Következő lépések

További információ [felügyelete a StorSimple virtuális tömb](storsimple-ova-web-ui-admin.md).

