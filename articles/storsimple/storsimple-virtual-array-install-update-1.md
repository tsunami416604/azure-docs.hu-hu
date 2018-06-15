---
title: A frissítés 1.0 a StorSimple virtuális tömb |} Microsoft Docs
description: A StorSimple virtuális tömb webes felhasználói felület használata az Azure portál és a gyorsjavítások metódussal frissítés alkalmazása
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
ms.openlocfilehash: a85290f3f56eb1e1bf57524c43c6d4fea36129f7
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
ms.locfileid: "24012077"
---
# <a name="install-update-10-on-your-storsimple-virtual-array"></a>A StorSimple virtuális tömb 1.0 frissítés telepítése

## <a name="overview"></a>Áttekintés

Ez a cikk ismerteti a StorSimple virtuális tömb és az Azure-portál a helyi webes felhasználói felületen keresztül 1.0-s frissítésében telepítéséhez szükséges lépéseket.

A szoftverfrissítések vagy gyorsjavítások segítségével nyomon követheti a StorSimple virtuális tömbjét alkalmaz. Egy frissítés alkalmazása előtt javasoljuk, hogy szánjon a kötetek vagy megosztások offline állapotba a gazdagépen első, majd az eszközről. Ez minimalizálja az adatvesztést lehetőségét. Után a kötetek vagy megosztások offline üzemmódban, akkor kell vennie kézi biztonsági mentési eszköz.

> [!IMPORTANT]
> - Frissítés 1.0 megfelel-e **10.0.10296.0** szoftverének verziójával, az eszközön. What's new in a frissítés információkért látogasson el [kibocsátási megjegyzései frissítése 1.0](storsimple-virtual-array-update-1-release-notes.md).
>
> - Ne feledje, hogy egy frissítés vagy gyorsjavítás telepítése az eszköz újraindul. Fényében, hogy a StorSimple virtuális tömb a egycsomópontos eszközről szó, a folyamatban lévő összes i/o megszakad, és az eszköz leállást észlel.
>
> - 1. frissítés áll rendelkezésre az Azure-portálon, csak akkor, ha a virtuális tömb fut-e frissítés 0,6. A frissítés előtti 0,6 verzióit futtató virtuális tömbök először telepítse a frissítést 0,6 és telepítse az 1. frissítést.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

Ha Update futtatása 0,2 és újabb verziók, azt javasoljuk, hogy telepítse a frissítéseket az Azure portálon keresztül. A portál eljárás a felhasználónak kell vizsgálat, töltse le és telepítse a frissítéseket. Attól függően, a szoftver verziója fut-e a virtuális tömb, frissítés telepítése az Azure-portálon nem egyezik.

 - Ha a virtuális tömb frissítés 0,6 fut, az Azure-portálon közvetlenül telepíti az 1. frissítést (10.0.10296.0) az eszközön. Ez az eljárás befejezéséhez körülbelül 7 perc.
 - Ha a virtuális tömb 0,6 frissítés előtti verziót futtat, a frissítés két szakaszban történik. Az Azure-portálon frissítés 0,6 (10.0.10293.0) először telepíti az eszközön. A virtuális tömb újraindul, és a portálon, majd telepíti 1. frissítést (10.0.10296.0) az eszközön. Ez az eljárás befejezéséhez körülbelül 15 perc.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-1.md)]

A telepítés befejezése után, nyissa meg a StorSimple eszköz Manager szolgáltatáshoz. Válassza ki **eszközök** majd válassza ki, és kattintson az eszköz nemrég frissített. Ugrás a **beállítások > kezelése > Eszközfrissítésekhez**. A megjelenített szoftverfrissítési verziót kell **10.0.10296.0**.

![Ügyfélszoftverének verziójára frissítése után](./media/storsimple-virtual-array-install-update-1/azupdate17m1.png)

## <a name="use-the-local-web-ui"></a>A helyi webes felhasználói felület használata

Két lépésben történik a helyi webes felhasználói felület használata esetén:

* A frissítés vagy gyorsjavítás letöltése
* A frissítés vagy gyorsjavítás telepítése

> [!IMPORTANT] 
> **A frissítés folytatásához, csak akkor, ha a frissítés 0,6 (10.0.10293.0) futtatja. Ha egy korábbi verzióját futtatja [0,6. számú frissítés](storsimple-virtual-array-install-update-06.md) az eszközön első 1. frissítést, majd alkalmazza.**

### <a name="download-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás letöltése

Ha a virtuális tömb frissítés 0,6 fut, a következő lépésekkel 1. frissítés letöltése a Microsoft Update katalógusból.

#### <a name="to-download-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás letöltése

1. Indítsa el az Internet Explorert, és keresse fel a [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com) címet.

2. Ha először a számítógépen a Microsoft Update katalógus használ, kattintson a **telepítése** amikor a rendszer kéri a Microsoft Update katalógus bővítmény telepítése.

3. A keresési mezőbe, a Microsoft Update katalógus adja meg a Tudásbázis (KB) le szeretné tölteni a gyorsjavítást. Adja meg **4047203** a 1.0 frissítése, és kattintson a **keresési**.
   
    A gyorsjavítás-lista megjelenik, például **StorSimple virtuális tömb frissítés 1.0-s**.
   
    ![Keresés a katalógusban](./media/storsimple-virtual-array-install-update-1/download1.png)

4. Kattintson a **Letöltés** gombra.

5. Töltse le a két fájlt egy mappába. A mappa, amely az eszköz elérhető megosztott hálózati helyre is másolhatja.

6. Nyissa meg a fájlokat tartalmazó mappát.

    ![A csomagban lévő fájlok](./media/storsimple-virtual-array-install-update-1/update01folder.png)

    Két fájlt láthat:
    -  A Microsoft Update önálló csomagfájl `WindowsTH-KB3011067-x64`. Ezt a fájlt az eszköz frissítéséhez használt.
    - Augusztus összesítő frissítéseinek tartalmazó fájl `windows8.1-kb4034681-x64`. Ez a kumulatív tartalmának további információkért látogasson el [augusztus havi biztonsági kumulatív](https://support.microsoft.com/help/4034681/windows-8-1-windows-server-2012-r2-update-kb40346810).

### <a name="install-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás telepítése

A frissítés vagy gyorsjavítás telepítése előtt győződjön meg arról, hogy:

 - Hogy a frissítés vagy a gyorsjavítás letöltése helyileg a gazdagépen vagy egy hálózati megosztáson keresztül érhető el.
 - A virtuális tömb frissítés 0,6 (10.0.10293.0) fut. Ha egy verzióját 0,6, frissítés előtt [0,6. számú frissítés](storsimple-virtual-array-install-update-06.md) első és telepítse az 1. frissítést.

Ez az eljárás befejezéséhez körülbelül 4 percig. Hajtsa végre a következő lépésekkel telepíti a frissítést vagy gyorsjavítást.

#### <a name="to-install-the-update-or-the-hotfix"></a>A frissítés vagy gyorsjavítás telepítése

1. A helyi webes felhasználói felület váltson **karbantartási** > **szoftverfrissítés**. Jegyezze fel az Ön által futtatott szoftverének verziójával. **A frissítés folytatásához, csak akkor, ha a frissítés 0,6 (10.0.10293.0) futtatja. Ha egy korábbi verzióját futtatja [0,6. számú frissítés](storsimple-virtual-array-install-update-06.md) az eszközön első 1. frissítést, majd alkalmazza.**
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-1/update1m.png)

2. A **frissítés fájl elérési útját**, írja be a fájl nevét, a frissítés vagy gyorsjavítás. A frissítés vagy gyorsjavítás telepítési fájl is tallózással, ha egy hálózati megosztáson. Kattintson az **Alkalmaz** gombra.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-1/update2m.png)

3. A figyelmeztetés akkor jelenik meg. A virtuális tömb a megadott van egy egycsomópontos eszköz után a frissítés nincs alkalmazva, az eszköz újraindul, és nincs leállás. Kattintson a pipa ikonra.
   
   ![eszköz frissítése](./media/storsimple-virtual-array-install-update-1/update3m.png)

4. A frissítés elindul. Az eszköz sikeres frissítése után újraindul. Ennek az időtartamnak a helyi felhasználói felülete nem érhető el.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-1/update5m.png)

5. Az újraindítás befejeződése után kattintva megnyílik a **bejelentkezés** lap. Győződjön meg arról, hogy az eszköz szoftvere frissítette, a helyi webes felhasználói felület, keresse fel **karbantartási** > **szoftverfrissítés**. A megjelenített szoftverfrissítési verziót kell **10.0.0.0.0.10296** az 1.0-s frissítésében.
   
   > [!NOTE]
   > A szoftver verziójával egy némileg eltérő módon a helyi webes felhasználói felület és az Azure-portálon jelentés azt. Például a helyi webes felhasználói Felületét jelent **10.0.0.0.0.10296** és az Azure portál jelentések **10.0.10296.0** azonos verziójához.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update-1/update6m.png)

6. Ismételje meg a 2 – 4 a Windows biztonsági javítás fájllal telepítendő `windows8.1-kb4012213-x64`. A telepítést követően újraindítja a virtuális tömb, és jelentkezzen be a helyi webes felhasználói felületen szeretné.

> [!NOTE]
> 1. frissítés előtt frissítés 0,6 verziót futtat eszközre közvetlenül alkalmazható, ha hiányzik néhány frissítést. Forduljon a Microsoft Support további lépéseket.

## <a name="next-steps"></a>Következő lépések

További információ [felügyelete a StorSimple virtuális tömb](storsimple-ova-web-ui-admin.md).
