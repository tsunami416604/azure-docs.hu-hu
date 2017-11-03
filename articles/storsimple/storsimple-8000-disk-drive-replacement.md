---
title: "Cserélje le a lemezmeghajtó a StorSimple 8000 series eszközön |} Microsoft Docs"
description: "Cserélje le egy meghajtót a StorSimple elsődleges ház vagy egy EBOD ház ismerteti."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 073/2017
ms.author: alkohli
ms.openlocfilehash: a8616eb51b177a9447a7c466c9d934b9139afedf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="replace-a-disk-drive-on-your-storsimple-8000-series-device"></a>Cserélje le a lemezmeghajtó a StorSimple 8000 series eszközön

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag azt ismerteti, hogyan távolítsa el, és cserélje le a hibás vagy nem sikerült merevlemez-meghajtó a Microsoft Azure StorSimple eszközön. A meghajtó cseréjéhez kell:

* A antitamper zárolási működésűeknek
* Távolítsa el a lemezmeghajtó
* Telepítse a helyettesítő lemezmeghajtó

> [!IMPORTANT]
> Mielőtt eltávolítása és cseréje egy meghajtó, tekintse át a biztonsági információk [StorSimple hardver összetevő cseréje](storsimple-8000-hardware-component-replacement.md).
 

## <a name="disengage-the-antitamper-lock"></a>A antitamper zárolási működésűeknek
Ez az eljárás azt ismerteti, hogyan a StorSimple eszköz antitamper zárolásokat is lehet vagy kikapcsolt állapotban a merevlemez-meghajtók cseréjekor. A meghajtó szolgáltatónként Handles antitamper zárolásokat szerelnek, és azokat a leíró zárolás szakaszában egy kis nyílás keresztül érik el. A zárolt pozícióra állítsa be a zárolásokat meghajtók végzik.

#### <a name="to-unlock-the-antitamper-lock"></a>A antitamper zárolás feloldásához
1. A zárolás kulcs ("tamperproof" T10 csavarhúzót Microsoft biztosított) gondosan beszúrása a leíró a nyílás és a szoftvercsatorna. 
   
   A antitamper zárolási aktiválódik, ha a piros jelzőikon nyílás látható.
  
    ![Zárolt meghajtó](./media/storsimple-disk-drive-replacement/IC741056.png)
   
    **1. ábra** részt vevő elleni illetéktelen zárolása
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |Kijelző nyílás |
   | 2 |Antitamper zárolása |
2. Forgassa el anticlockwise irányba a kulcsot, amíg a piros jelző nem látható a fenti a kulcs nyílás.
3. Távolítsa el a kulcsot.
   
    ![Nem zárolt meghajtó](./media/storsimple-disk-drive-replacement/IC741057.png)
   
    **2. ábra** feloldva lemezmeghajtó
4. A meghajtó most eltávolítható.

Kövesse a lépéseket visszafelé végezhetnek a zárolást.

## <a name="remove-the-disk-drive"></a>Távolítsa el a lemezmeghajtó
A StorSimple eszköz támogatja a RAID 10-szerű szóközöket tárkonfiguráció. Ez azt jelenti, hogy azt is megfelelően működik egy meghibásodott lemezt, SSD-meghajtót (SSD), vagy a merevlemez-meghajtó (HDD).

> [!IMPORTANT]
> * Ha a rendszer több mint egy meghibásodott lemezt, távolítsa el egynél több SSD és HDD a rendszer minden ponton időben. Így adatvesztést eredményezhet.
> * Ellenőrizze, hogy egy helyettesítő SSD helyez egy SSD korábban tartalmazó tárhely. Hasonlóképpen helyezze HDD helyettesíti, amely korábban tartalmazott egy HDD tárolóhelyen található.
> * Az Azure-portálon üzembe helyezési ponti számozása 0 – 11. Ezért ha a portál megjeleníti, hogy a lemez tárolóhelye 2 sikertelen, az eszközön, keresse meg a meghibásodott lemezt, a harmadik tárolóhelye a bal felső.
> 
> 

Meghajtók távolítva, és amíg a rendszer lecseréli.

#### <a name="to-remove-a-drive"></a>A meghajtó eltávolítása
1. Azonosítsa a meghibásodott lemezt, az Azure portálon keresse fel az eszköz **beállítások > hardver állapotának**. Lemez sikertelen lehet az elsődleges ház és/vagy egy EBOD ház (Ha egy 8600 modellt használ), mert a lemez állapotát megtekinthetik **összetevők megosztott** és a **EBOD megosztott összetevők**. A hibás lemez vagy szolgáltatással vörös állapottal jelenik meg.
2. Keresse meg a meghajtók elején található az elsődleges vagy a a EBOD ház. 
3. Ha a lemez oldva, folytassa a következő lépéssel. Ha a lemez le van tiltva, oldhatja fel azt az eljárást követve [antitamper zárolás működésűeknek](#disengage-the-antitamper-lock).
4. A fekete zárolás nyomja meg a meghajtó szolgáltatónként modul, és a meghajtó szolgáltatónként leíró számítógépnél ki és lekéréses elölről kell elhelyezni.
   
    ![Lemezmeghajtó leíró feloldása](./media/storsimple-disk-drive-replacement/IC741051.png)
   
    **3. ábra** a meghajtó leíró feloldása
5. Ha a meghajtó szolgáltatónként leíró teljesen ki van bővítve, csúsztassa be a meghajtó szolgáltatói a váz kívül. 
   
    ![A késleltetett lemez Lemezmeghajtó kívül](./media/storsimple-disk-drive-replacement/IC741052.png)
   
    **4. ábra** késleltetett a lemezmeghajtó a szolgáltatói kívül

## <a name="install-the-replacement-disk-drive"></a>Telepítse a helyettesítő lemezmeghajtó
Miután egy meghajtót a StorSimple eszközt a sikertelen volt, és törölte, az alábbi eljárás segítségével cserélje le egy új meghajtót.

#### <a name="to-insert-a-drive"></a>A meghajtó beszúrása
1. Győződjön meg arról, a meghajtó szolgáltatónként leíró teljesen ki van bővítve, a következő ábrán látható módon.
   
    ![A kiterjesztett leíró meghajtó](./media/storsimple-disk-drive-replacement/IC741044.png)
   
    **5. ábra** kiterjesztett leíró rendelkező meghajtóra
2. Csúsztassa be a meghajtó szolgáltatói egészen a váz be.
   
    ![A meghajtó szolgáltatónként mozgó lemez](./media/storsimple-disk-drive-replacement/IC741045.png)
   
    **6. ábra** a meghajtó szolgáltatói késleltetett azokat a váz
3. Az a meghajtó szolgáltatói beszúrni zárja be a meghajtó szolgáltatónként leíró miközben továbbra is a meghajtó szolgáltatói leküldéses azokat a váz, mindaddig, amíg a meghajtó szolgáltatónként leíró zárolt helyzetbe illesztése.
4. Microsoft (tamperproof Torx csavarhúzóra) biztonságossá tétele a vivőjel-leíró által biztosított zárolási kulcs használata helyen által, hogy a zárolás csavart negyedéves kapcsolja jobbra.
5. Győződjön meg arról, hogy a csere befejeződött, és a meghajtó nem működik. Hozzáférés az Azure-portálon, és keresse meg **eszközbeállítások** > **hardver állapotának**. A **összetevők megosztott** vagy **EBOD megosztott összetevők**, a meghajtó állapotát legyen zöld, jelezve, hogy kifogástalan.

   
   > [!NOTE]
   > A lemez állapota zöld színűre a csere utáni több órát is igénybe vehet.
  
## <a name="next-steps"></a>Következő lépések
További információ [StorSimple hardver összetevő cseréje](storsimple-8000-hardware-component-replacement.md).

