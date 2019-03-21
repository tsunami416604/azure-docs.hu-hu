---
title: Telepítse a frissítéseket a Microsoft Azure StorSimple Virtual Array |} A Microsoft Docs
description: Ismerteti, hogyan alkalmazza a frissítéseket, a portál és a gyorsjavítások metódussal a StorSimple Virtual Array webes felhasználói felület használatával
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
ms.openlocfilehash: 7bf064ff01693f7a65c756a99c435d7f1a39840e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58010134"
---
# <a name="install-updates-on-your-storsimple-virtual-array---azure-portal"></a>Telepítse a frissítéseket a StorSimple Virtual Array – az Azure Portalon

## <a name="overview"></a>Áttekintés

Ez a cikk a StorSimple virtuális tömb helyi webes felületén keresztül, és az Azure Portalon a frissítések telepítéséhez szükséges lépéseket ismerteti. Szoftverfrissítések vagy gyorsjavítások a StorSimple Virtual Array tartsa naprakészen a alkalmazni kell. 

Ne feledje, hogy egy frissítés vagy gyorsjavítás telepítése az eszköz újraindul. Tekintve, hogy a StorSimple Virtual Array egy egyetlen csomóponttal rendelkező eszköz, a folyamatban lévő összes i/o megszakad, és a az eszköz állásidő élményhez. 

Mielőtt alkalmazná a frissítést, javasoljuk, hogy tegye meg a kötetek vagy megosztások kapcsolat nélküli módban a gazdagépen első és az eszköz majd. Ezzel a lehető legkisebbre csökkenti az adatsérülés esélyét.

> [!IMPORTANT]
> 0.1-es frissítés vagy a GA szoftververziók futtatja, ha a gyorsjavítás metódus a helyi webes felhasználói felületen a 0.3 telepítéséhez kell használnia. 0.2-es frissítést futtatja, azt javasoljuk, hogy telepíti a frissítéseket, a klasszikus Azure portálon keresztül.
 

## <a name="use-the-local-web-ui"></a>A helyi webes felhasználói felület használata

Ha a helyi webes felhasználói felület még két lépésre van:

* A frissítés vagy gyorsjavítás letöltése
* A frissítés vagy gyorsjavítás telepítése

### <a name="download-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás letöltése

Hajtsa végre a következő lépéseket a szoftverfrissítés a Microsoft Update katalógusból történő letöltéséhez.

#### <a name="to-download-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás letöltése

1. Indítsa el az Internet Explorert és keresse meg [ https://catalog.update.microsoft.com ](https://catalog.update.microsoft.com).

2. Ha most használja először a Microsoft Update katalógust ezen a számítógépen, kattintson a **Telepítés** gombra, amikor a rendszer a Microsoft Update katalógus beépülő moduljának telepítésére kéri.

3. A keresőmezőbe, a Microsoft Update katalógus adja meg a letölteni kívánt gyorsjavítás Tudásbázis (KB) száma. Adja meg **3182061** a 0.3-as, és kattintson **keresési**.
   
    A gyorsjavítás-lista megjelenik, például **StorSimple Virtual Array a 0.3**.
   
    ![Keresés a katalógusban](./media/storsimple-virtual-array-install-update/download1.png)

4. Kattintson a **Hozzáadás** parancsra. Ezzel a frissítést hozzáadja a kosárhoz.

5. Kattintson a **Kosár megtekintése** gombra.

6. Kattintson a **Letöltés** gombra. Adja meg vagy **tallózással** válassza ki a helyet, ahová a fájlokat le szeretné tölteni. A frissítések a megadott helyre lesznek letöltve, azon belül az egyes frissítések nevével egyező nevű almappákba. A mappa átmásolható egy, az eszközről elérhető hálózati megosztásra is.

7. Nyissa meg a másolt mappa kell, és láthatja, a Microsoft Update önálló csomagfájl `WindowsTH-KB3011067-x64`. Ez a fájl segítségével telepítheti a frissítést vagy gyorsjavítást.

### <a name="install-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás telepítése

A frissítés vagy gyorsjavítás telepítése előtt győződjön meg arról, hogy a frissítést, vagy a gyorsjavítás letöltése vagy helyileg, a gazdagépen vagy egy hálózati megosztáson keresztül érhető el. 

Ez a módszer használatával egy GA futtató eszközre telepítse a frissítéseket, vagy 0,1 szoftververziók frissítése. Ez az eljárás végrehajtásához kevesebb mint 2 percet vesz igénybe. A következő lépésekkel telepítse a frissítést vagy gyorsjavítást.

#### <a name="to-install-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás telepítése

1. A helyi webes felhasználói felületen váltson **karbantartási** > **szoftverfrissítés**.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update/update1m.png)

2. A **frissítési fájl elérési útja**, adja meg a fájl nevét, a frissítés vagy gyorsjavítás. A frissítés vagy gyorsjavítás telepítési fájl is tallózással, ha egy hálózati megosztáson. Kattintson az **Alkalmaz** gombra.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update/update2m.png)

3. Egy figyelmeztetés jelenik meg. Ez a megadott van egy egyetlen csomóponttal rendelkező eszköz, miután a frissítés nincs alkalmazva, az eszköz újraindul, és üzemkimaradást okoz. Kattintson a pipa ikonra.
   
   ![eszköz frissítése](./media/storsimple-virtual-array-install-update/update3m.png)

4. A frissítés elindításakor. Az eszköz sikeres frissítése után újraindul. A helyi felhasználói felületen Ez az időtartam nem érhető el.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update/update5m.png)

5. Az újraindítás befejeződése után megnyílik a **jelentkezzen be a** lapot. Győződjön meg arról, hogy az eszköz szoftverének frissítette, a helyi webes felhasználói felületen, lépjen a **karbantartási** > **szoftverfrissítés**. A megjelenített szoftverfrissítési verziónak kell lennie **10.0.0.0.0.10288.0** a 0.3 számára.
   
   > [!NOTE]
   > A helyi webes felhasználói felület kissé másféle és az Azure Portalon a szoftver verziójával azt jelentést. Például a helyi webes felületén jelentések **10.0.0.0.0.10288** és az Azure portal jelentések **10.0.10288.0** esetében ugyanazt a verziót.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update/update6m.png)

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

0.2-es frissítést futtatja, azt javasoljuk, hogy az Azure Portalon keresztül frissítések telepítése. A portál eljárás a felhasználónak vizsgálata, töltse le és telepítse a frissítéseket. Ez az eljárás végrehajtásához körülbelül 7 perc szükséges. A következő lépésekkel telepítse a frissítést vagy gyorsjavítást.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal.md)]

A telepítés befejezése után (a feladat állapotát a 100 %-os), nyissa meg a StorSimple-Eszközkezelő szolgáltatásban. Válassza ki **eszközök** majd válassza ki, és kattintson az eszköz frissíti a szolgáltatáshoz csatlakozó eszközök listájából. Az a **beállítások** panelen lépjen a **kezelés** szakaszt, és válassza **Eszközfrissítések**. A megjelenített szoftverfrissítési verziónak kell lennie **10.0.10288.0**.


## <a name="next-steps"></a>További lépések

Tudjon meg többet [felügyelete a StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

