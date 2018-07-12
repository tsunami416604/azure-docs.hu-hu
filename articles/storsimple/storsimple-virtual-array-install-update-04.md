---
title: Telepítse a frissítéseket a StorSimple Virtual Array |} A Microsoft Docs
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
ms.date: 02/07/2017
ms.author: alkohli
ms.openlocfilehash: 3fb246b1515e7a637e6cff6499bf324c3f80dd45
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38232189"
---
# <a name="install-update-04-on-your-storsimple-virtual-array"></a>A 0.4 telepítse a StorSimple Virtual Array

## <a name="overview"></a>Áttekintés

Ez a cikk a helyi webes felhasználói felületen és az Azure Portalon a StorSimple Virtual Array az a 0.4 telepítendő szükséges lépéseket ismerteti. Szoftverfrissítések vagy gyorsjavítások a StorSimple Virtual Array tartsa naprakészen a alkalmazni kell. 

Ne feledje, hogy egy frissítés vagy gyorsjavítás telepítése az eszköz újraindul. Tekintve, hogy a StorSimple Virtual Array egy egyetlen csomóponttal rendelkező eszköz, a folyamatban lévő összes i/o megszakad, és a az eszköz állásidő élményhez. 

Mielőtt alkalmazná a frissítést, javasoljuk, hogy tegye meg a kötetek vagy megosztások kapcsolat nélküli módban a gazdagépen első és az eszköz majd. Így minimálisra csökkenthető az adatsérülés lehetőségét.

> [!IMPORTANT]
> 0.1-es frissítés vagy a GA szoftververziók futtatja, ha a gyorsjavítás metódus a helyi webes felhasználói felületen a 0.3 telepítéséhez kell használnia. Ha 0.2-es frissítést futtatja, vagy később, akkor javasoljuk, hogy telepítse a frissítéseket az Azure Portalon keresztül.
 

## <a name="use-the-local-web-ui"></a>A helyi webes felhasználói felület használata

Ha a helyi webes felhasználói felület még két lépésre van:

* A frissítés vagy gyorsjavítás letöltése
* A frissítés vagy gyorsjavítás telepítése

### <a name="download-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás letöltése

Hajtsa végre a következő lépéseket a szoftverfrissítés a Microsoft Update katalógusból történő letöltéséhez.

#### <a name="to-download-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás letöltése

1. Indítsa el az Internet Explorert és keresse meg [ http://catalog.update.microsoft.com ](http://catalog.update.microsoft.com).

2. Ha most használja először a Microsoft Update katalógust ezen a számítógépen, kattintson a **Telepítés** gombra, amikor a rendszer a Microsoft Update katalógus beépülő moduljának telepítésére kéri.

3. A keresőmezőbe, a Microsoft Update katalógus adja meg a letölteni kívánt gyorsjavítás Tudásbázis (KB) száma. Adja meg **3216577** Update 0,4, és kattintson **keresési**.
   
    A gyorsjavítás-lista megjelenik, például **StorSimple Virtual Array a 0.4**.
   
    ![Keresés a katalógusban](./media/storsimple-virtual-array-install-update-04/download1.png)

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

5. Az újraindítás befejeződése után megnyílik a **jelentkezzen be a** lapot. Győződjön meg arról, hogy az eszköz szoftverének frissítette, a helyi webes felhasználói felületen, lépjen a **karbantartási** > **szoftverfrissítés**. A megjelenített szoftverfrissítési verziónak kell lennie **10.0.0.0.0.10289.0** a 0.4 számára.
   
   > [!NOTE]
   > A helyi webes felhasználói felület kissé másféle és az Azure Portalon a szoftver verziójával azt jelentést. Például a helyi webes felületén jelentések **10.0.0.0.0.10289** és az Azure portal jelentések **10.0.10289.0** esetében ugyanazt a verziót.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update/update6m.png)

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

0,2 és újabb verzióit futtató frissítése, azt javasoljuk, hogy az Azure Portalon keresztül frissítések telepítése. A portál eljárás a felhasználónak vizsgálata, töltse le és telepítse a frissítéseket. Ez az eljárás végrehajtásához körülbelül 7 perc szükséges. A következő lépésekkel telepítse a frissítést vagy gyorsjavítást.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

A telepítés befejezése után (a feladat állapotát a 100 %-os), nyissa meg a StorSimple-Eszközkezelő szolgáltatásban. Válassza ki **eszközök** majd válassza ki, és kattintson az eszköz frissíti a szolgáltatáshoz csatlakozó eszközök listájából. Az a **beállítások** panelen lépjen a **kezelés** szakaszt, és válassza **Eszközfrissítések**. A megjelenített szoftverfrissítési verziónak kell lennie **10.0.10289.0**.


## <a name="next-steps"></a>További lépések

Tudjon meg többet [felügyelete a StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

