---
title: A StorSimple Virtual Array 1.1-es frissítés telepítése |} A Microsoft Docs
description: Ismerteti, hogyan alkalmazza a frissítéseket a StorSimple Virtual Array az Azure portal és a helyi webes felhasználói felület használatával
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/18/2018
ms.author: alkohli
ms.openlocfilehash: 88b903d68e4398b4e30b0b7435279c29bee6cd6b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58005222"
---
# <a name="install-update-11-on-your-storsimple-virtual-array"></a>A StorSimple Virtual Array 1.1-es frissítés telepítése

## <a name="overview"></a>Áttekintés

Ez a cikk azt ismerteti, hogyan 1.1-es frissítés telepítése a StorSimple virtuális tömb helyi webes felületén keresztül, és az Azure Portalon keresztül.

A szoftverfrissítések vagy gyorsjavítások naprakészen tartani a StorSimple Virtual Array alkalmaz. Mielőtt alkalmazná a frissítést, javasoljuk, hogy tegye meg a kötetek vagy megosztások kapcsolat nélküli módban a gazdagépen első és az eszköz majd. Ezzel a lehető legkisebbre csökkenti az adatsérülés esélyét. Miután a kötetek vagy megosztások offline állapotban, akkor kell is manuális biztonsági mentést az eszköz.

> [!IMPORTANT]
> - 1.1-es frissítés megfelel **10.0.10307.0** szoftververzió az eszközön. Ez a frissítés újdonsága az információk, [kibocsátási megjegyzései 1.1-es frissítés](storsimple-virtual-array-update-11-release-notes.md).
>
> - Ne feledje, hogy egy frissítés vagy gyorsjavítás telepítése az eszköz újraindul. Tekintve, hogy a StorSimple Virtual Array egy egyetlen csomóponttal rendelkező eszköz, a folyamatban lévő összes i/o megszakad, és a az eszköz állásidő élményhez.
>
> - 1.1-es frissítés az Azure Portalon érhető el, csak akkor, ha a virtuális tömb 1. frissítés fut-e. A virtuális tömbök 0.6-os frissítés verziót futtató először telepítse az 1.0-ás frissítés és ezután alkalmazza az 1.1-es frissítés.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

0,2 és újabb verzióit futtató frissítése, azt javasoljuk, hogy az Azure Portalon keresztül frissítések telepítése. A portál eljárás a felhasználónak vizsgálata, töltse le és telepítse a frissítéseket. A szoftververzió fut. a virtuális tömb készlettől, frissítés telepítése az Azure Portalon keresztül nem egyezik.

 - Ha a virtuális tömb 1. frissítés fut, az Azure Portalon közvetlenül telepíti a frissítési 1.1-es (10.0.10307.0) az eszközön. Ez az eljárás a befejezéséhez körülbelül 10 percet vesz igénybe.
 - Ha a virtuális tömb a 0.6-os frissítés fut, a frissítés két szakaszban történik. Az Azure Portalon 1.0-ás frissítés (10.0.10296.0) először telepíti az eszközön. Újraindítja a virtuális tömb, és a portál majd telepíti a frissítési 1.1-es (10.0.10307.0) az eszközön. Ez az eljárás a végrehajtásához hozzávetőlegesen 15 percet vesz igénybe.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-11.md)]

A telepítés befejeződése után nyissa meg a StorSimple-Eszközkezelő szolgáltatásban. Válassza ki **eszközök** majd válassza ki, és kattintson az imént frissített eszköz. Lépjen a **beállítások > kezelése > Eszközfrissítések**. A megjelenített szoftverfrissítési verziónak kell lennie **10.0.10307.0**.

![Frissítés után szoftververzió](./media/storsimple-virtual-array-install-update-11/azupdate17m2.png)

## <a name="use-the-local-web-ui"></a>A helyi webes felhasználói felület használata

Ha a helyi webes felhasználói felület még két lépésre van:

* A frissítés vagy gyorsjavítás letöltése
* A frissítés vagy gyorsjavítás telepítése

> [!IMPORTANT] 
> **Ez a frissítés folytatásához, csak akkor, ha Update 1 (10.0.10296.0) futtatja. 0.6-os, frissítés futtatásakor [telepítéséhez 1. frissítés](storsimple-virtual-array-install-update-1.md) az eszközön első 1.1-es frissítést, majd alkalmazza.**

### <a name="download-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás letöltése

A következő lépésekkel 1.1-es frissítés letöltése a Microsoft Update katalógusból.

#### <a name="to-download-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás letöltése

1. Indítsa el az Internet Explorert és keresse meg [ https://catalog.update.microsoft.com ](https://catalog.update.microsoft.com).

2. Ha a Microsoft Update katalógust ezen a számítógépen először használ, kattintson a **telepítése** , amikor a Microsoft Update katalógus beépülő moduljának telepítésére kéri.

3. A keresőmezőbe, a Microsoft Update katalógus adja meg a letölteni kívánt gyorsjavítás Tudásbázis (KB) száma. Adja meg **4337628** az 1.1-es frissítés, és kattintson a **keresési**.
   
    A gyorsjavítás-lista megjelenik, például **StorSimple Virtual Array frissítés 1.1-es**.
   
    ![Keresés a katalógusban](./media/storsimple-virtual-array-install-update-11/download1.png)

4. Kattintson a **Letöltés** gombra.

5. Töltse le a két fájlt egy mappába. A mappa, amely az eszközről elérhető hálózati megosztásra is másolhatja.

6. Nyissa meg a fájlokat tartalmazó mappát.

    ![A csomagban lévő fájlok](./media/storsimple-virtual-array-install-update-11/update01folder.png)

    Két fájlt láthat:
    -  A Microsoft Update önálló csomagfájl `WindowsTH-KB3011067-x64`. Ez a fájl frissítése az eszközhöz használatos.
    - Június összegző frissítései tartalmazó fájl `Windows8.1-KB4284815-x64`. Mit tartalmaz az összegző további információért látogasson el [június havi a biztonság összegzése](https://support.microsoft.com/help/4284815/windows-81-update-kb4284815).

### <a name="install-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás telepítése

A frissítés vagy gyorsjavítás telepítése előtt győződjön meg arról, hogy:

 - Hogy a frissítés vagy a gyorsjavítás letöltése helyileg a gazdagépen vagy egy hálózati megosztáson keresztül érhető el.
 - A virtuális tömb fut az 1. frissítés (10.0.10296.0). 0.6-os, frissítés futtatásakor [telepítéséhez 1. frissítés](storsimple-virtual-array-install-update-1.md) első, majd telepítse a frissítést 1.1.

Ez az eljárás végrehajtásához szükséges körülbelül 4 perc. A következő lépésekkel telepítse a frissítést vagy gyorsjavítást.

#### <a name="to-install-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás telepítése

1. A helyi webes felhasználói felületen váltson **karbantartási** > **szoftverfrissítés**. Jegyezze fel az Ön által futtatott szoftververzió. **Ez a frissítés folytatásához, csak akkor, ha Update 1 (10.0.10296.0) futtatja. 0.6-os, frissítés futtatásakor [telepítéséhez 1. frissítés](storsimple-virtual-array-install-update-1.md) az eszközön első 1.1-es frissítést, majd alkalmazza.**
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-11/update1m.png)

2. A **frissítési fájl elérési útja**, adja meg a fájl nevét, a frissítés vagy gyorsjavítás. A frissítés vagy gyorsjavítás telepítési fájl is tallózással, ha egy hálózati megosztáson. Kattintson az **Alkalmaz** gombra.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-11/update2m.png)

3. Egy figyelmeztetés jelenik meg. A virtuális tömb a megadott van egy egyetlen csomóponttal rendelkező eszköz, miután a frissítés nincs alkalmazva, az eszköz újraindul, és üzemkimaradást okoz. Kattintson a pipa ikonra.
   
   ![eszköz frissítése](./media/storsimple-virtual-array-install-update-11/update3m.png)

4. A frissítés elindításakor. Az eszköz sikeres frissítése után újraindul. A helyi felhasználói felületen Ez az időtartam nem érhető el.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-11/update5m.png)

5. Az újraindítás befejeződése után megnyílik a **jelentkezzen be a** lapot. Győződjön meg arról, hogy az eszköz szoftverének frissítette, a helyi webes felhasználói felületen, lépjen a **karbantartási** > **szoftverfrissítés**. A megjelenített szoftverfrissítési verziónak kell lennie **10.0.0.0.0.10307** a frissítés 1.1.
   
   > [!NOTE]
   > A helyi webes felhasználói felület kissé másféle és az Azure Portalon a szoftver verziójával azt jelentést. Például a helyi webes felületén jelentések **10.0.0.0.0.10307** és az Azure portal jelentések **10.0.10307.0** esetében ugyanazt a verziót.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-11/update6m.png)

6. Ismételje meg a 2 – 4 a Windows biztonsági javítás fájllal telepítendő `Windows8.1-KB4284815-x64`. A telepítést követően újraindítja a virtuális tömböt, és jelentkezzen be a helyi webes felületén kell.


## <a name="next-steps"></a>További lépések

Tudjon meg többet [felügyelete a StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).
