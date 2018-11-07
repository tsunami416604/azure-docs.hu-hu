---
title: Telepítse a StorSimple Virtual Array 1.0-ás frissítés |} A Microsoft Docs
description: A StorSimple Virtual Array webes felhasználói felület használata az Azure portal és a gyorsjavítások módszerrel frissítés alkalmazása
services: storsimple
documentationcenter: NA
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: 34439268163b4da6ac45af09101f4f609de7e9ee
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51248163"
---
# <a name="install-update-10-on-your-storsimple-virtual-array"></a>Telepítse a StorSimple Virtual Array 1.0-ás frissítés

## <a name="overview"></a>Áttekintés

Ez a cikk a StorSimple virtuális tömb helyi webes felületén keresztül, és az Azure Portalon az 1.0-ás frissítés telepítéséhez szükséges lépéseket ismerteti.

A szoftverfrissítések vagy gyorsjavítások naprakészen tartani a StorSimple Virtual Array alkalmaz. Mielőtt alkalmazná a frissítést, javasoljuk, hogy tegye meg a kötetek vagy megosztások kapcsolat nélküli módban a gazdagépen első és az eszköz majd. Ezzel a lehető legkisebbre csökkenti az adatsérülés esélyét. Miután a kötetek vagy megosztások offline állapotban, akkor kell is manuális biztonsági mentést az eszköz.

> [!IMPORTANT]
> - 1.0-ás frissítés megfelel **10.0.10296.0** szoftververzió az eszközön. Ez a frissítés újdonsága az információk, [kibocsátási megjegyzései frissítése 1.0](storsimple-virtual-array-update-1-release-notes.md).
>
> - Ne feledje, hogy egy frissítés vagy gyorsjavítás telepítése az eszköz újraindul. Tekintve, hogy a StorSimple Virtual Array egy egyetlen csomóponttal rendelkező eszköz, a folyamatban lévő összes i/o megszakad, és a az eszköz állásidő élményhez.
>
> - 1. frissítés az Azure Portalon érhető el, csak akkor, ha a virtuális tömb a 0.6-os frissítés fut-e. A virtuális tömbök frissítés előtti a 0.6-os verziót futtató először telepítse a frissítést a 0.6-os és telepítse az 1. frissítés.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

0,2 és újabb verzióit futtató frissítése, azt javasoljuk, hogy az Azure Portalon keresztül frissítések telepítése. A portál eljárás a felhasználónak vizsgálata, töltse le és telepítse a frissítéseket. A szoftververzió fut. a virtuális tömb készlettől, frissítés telepítése az Azure Portalon keresztül nem egyezik.

 - Ha a virtuális tömb a 0.6-os frissítés fut, az Azure Portalon közvetlenül telepíti az 1. frissítés (10.0.10296.0) az eszközön. Ez az eljárás végrehajtásához körülbelül 7 perc szükséges.
 - Ha a virtuális tömb a 0.6-os frissítés előtti verziót futtat, a frissítés két szakaszban történik. Az Azure Portalon először telepíti a frissítést a 0.6-os (10.0.10293.0) az eszközön. Újraindítja a virtuális tömb, és a portál ezután telepíti a 1. frissítés (10.0.10296.0) az eszközön. Ez az eljárás a végrehajtásához hozzávetőlegesen 15 percet vesz igénybe.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-1.md)]

A telepítés befejeződése után nyissa meg a StorSimple-Eszközkezelő szolgáltatásban. Válassza ki **eszközök** majd válassza ki, és kattintson az imént frissített eszköz. Lépjen a **beállítások > kezelése > Eszközfrissítések**. A megjelenített szoftverfrissítési verziónak kell lennie **10.0.10296.0**.

![Frissítés után szoftververzió](./media/storsimple-virtual-array-install-update-1/azupdate17m1.png)

## <a name="use-the-local-web-ui"></a>A helyi webes felhasználói felület használata

Ha a helyi webes felhasználói felület még két lépésre van:

* A frissítés vagy gyorsjavítás letöltése
* A frissítés vagy gyorsjavítás telepítése

> [!IMPORTANT] 
> **Ez a frissítés folytatásához, csak akkor, ha a frissítés a 0.6-os (10.0.10293.0) futtatja. Ha egy korábbi verziót futtatja [0.6-os frissítés telepítése](storsimple-virtual-array-install-update-06.md) az eszközön első 1. frissítést, majd alkalmazza.**

### <a name="download-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás letöltése

Ha a virtuális tömb a 0.6-os frissítés fut, a következő lépésekkel 1. frissítés letöltése a Microsoft Update katalógusból.

#### <a name="to-download-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás letöltése

1. Indítsa el az Internet Explorert és keresse meg [ http://catalog.update.microsoft.com ](https://catalog.update.microsoft.com).

2. Ha a Microsoft Update katalógust ezen a számítógépen először használ, kattintson a **telepítése** , amikor a Microsoft Update katalógus beépülő moduljának telepítésére kéri.

3. A keresőmezőbe, a Microsoft Update katalógus adja meg a letölteni kívánt gyorsjavítás Tudásbázis (KB) száma. Adja meg **4047203** az 1.0-s frissíteni, és kattintson **keresési**.
   
    A gyorsjavítás-lista megjelenik, például **StorSimple Virtual Array 1.0-ás frissítés**.
   
    ![Keresés a katalógusban](./media/storsimple-virtual-array-install-update-1/download1.png)

4. Kattintson a **Letöltés** gombra.

5. Töltse le a két fájlt egy mappába. A mappa, amely az eszközről elérhető hálózati megosztásra is másolhatja.

6. Nyissa meg a fájlokat tartalmazó mappát.

    ![A csomagban lévő fájlok](./media/storsimple-virtual-array-install-update-1/update01folder.png)

    Két fájlt láthat:
    -  A Microsoft Update önálló csomagfájl `WindowsTH-KB3011067-x64`. Ez a fájl frissítése az eszközhöz használatos.
    - Augusztus összegző frissítései tartalmazó fájl `windows8.1-kb4034681-x64`. Mit tartalmaz az összegző további információért látogasson el [augusztus havi a biztonság összegzése](https://support.microsoft.com/help/4034681/windows-8-1-windows-server-2012-r2-update-kb40346810).

### <a name="install-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás telepítése

A frissítés vagy gyorsjavítás telepítése előtt győződjön meg arról, hogy:

 - Hogy a frissítés vagy a gyorsjavítás letöltése helyileg a gazdagépen vagy egy hálózati megosztáson keresztül érhető el.
 - A virtuális tömb a 0.6-os (10.0.10293.0) frissítés fut. Ha a 0.6-os, frissítés előtti verziót futtatja [0.6-os frissítés telepítése](storsimple-virtual-array-install-update-06.md) első, és telepítse az 1. frissítés.

Ez az eljárás végrehajtásához szükséges körülbelül 4 perc. A következő lépésekkel telepítse a frissítést vagy gyorsjavítást.

#### <a name="to-install-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás telepítése

1. A helyi webes felhasználói felületen váltson **karbantartási** > **szoftverfrissítés**. Jegyezze fel az Ön által futtatott szoftververzió. **Ez a frissítés folytatásához, csak akkor, ha a frissítés a 0.6-os (10.0.10293.0) futtatja. Ha egy korábbi verziót futtatja [0.6-os frissítés telepítése](storsimple-virtual-array-install-update-06.md) az eszközön első 1. frissítést, majd alkalmazza.**
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-1/update1m.png)

2. A **frissítési fájl elérési útja**, adja meg a fájl nevét, a frissítés vagy gyorsjavítás. A frissítés vagy gyorsjavítás telepítési fájl is tallózással, ha egy hálózati megosztáson. Kattintson az **Alkalmaz** gombra.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-1/update2m.png)

3. Egy figyelmeztetés jelenik meg. A virtuális tömb a megadott van egy egyetlen csomóponttal rendelkező eszköz, miután a frissítés nincs alkalmazva, az eszköz újraindul, és üzemkimaradást okoz. Kattintson a pipa ikonra.
   
   ![eszköz frissítése](./media/storsimple-virtual-array-install-update-1/update3m.png)

4. A frissítés elindításakor. Az eszköz sikeres frissítése után újraindul. A helyi felhasználói felületen Ez az időtartam nem érhető el.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-1/update5m.png)

5. Az újraindítás befejeződése után megnyílik a **jelentkezzen be a** lapot. Győződjön meg arról, hogy az eszköz szoftverének frissítette, a helyi webes felhasználói felületen, lépjen a **karbantartási** > **szoftverfrissítés**. A megjelenített szoftverfrissítési verziónak kell lennie **10.0.0.0.0.10296** az 1.0-ás frissítés.
   
   > [!NOTE]
   > A helyi webes felhasználói felület kissé másféle és az Azure Portalon a szoftver verziójával azt jelentést. Például a helyi webes felületén jelentések **10.0.0.0.0.10296** és az Azure portal jelentések **10.0.10296.0** esetében ugyanazt a verziót.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-1/update6m.png)

6. Ismételje meg a 2 – 4 a Windows biztonsági javítás fájllal telepítendő `windows8.1-kb4012213-x64`. A telepítést követően újraindítja a virtuális tömböt, és jelentkezzen be a helyi webes felületén kell.

> [!NOTE]
> Ha közvetlenül alkalmazott 0.6-os frissítés előtti verziót futtató eszközről egy 1. frissítés, hiányzik néhány frissítést. További lépésekért forduljon a Microsoft Support.

## <a name="next-steps"></a>További lépések

Tudjon meg többet [felügyelete a StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).
