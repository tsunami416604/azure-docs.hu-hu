---
title: Cserélje le a lemezmeghajtó a StorSimple 8000 sorozatú eszköz |} A Microsoft Docs
description: Cserélje le egy meghajtót egy StorSimple elsődleges ház- és a egy EBOD ház ismerteti.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 8/25/2017
ms.author: alkohli
ms.openlocfilehash: 3d6ef22e4df36996d68194589f43ea0f57def22c
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673923"
---
# <a name="replace-a-disk-drive-on-your-storsimple-8000-series-device"></a>A StorSimple 8000 sorozatú eszköz az a lemezmeghajtó cseréje

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag azt ismerteti, hogyan távolítsa el, és cserélje le a hibás vagy sikertelen merevlemez-meghajtó a Microsoft Azure StorSimple eszközön. A meghajtó cseréjéhez kell tennie:

* A antitamper zárolást működésűeknek
* A meghajtó eltávolítása
* Telepítse a helyettesítő-meghajtó

> [!IMPORTANT]
> Mielőtt eltávolítása és cseréje egy meghajtó, tekintse át a biztonsági információk [StorSimple összetevő hardvercseréhez](storsimple-8000-hardware-component-replacement.md).
 

## <a name="disengage-the-antitamper-lock"></a>A antitamper zárolást működésűeknek
Ez az eljárás bemutatja, hogyan antitamper szüntetni a StorSimple-eszköz is lehet vagy kikapcsolt állapotban a merevlemez-meghajtók cseréjekor. A antitamper zárolása fel vannak szerelve, az a meghajtó-szolgáltató kezeli, és ezek a leíró reteszes szakaszában egy kis rekesze és keresztül érhetők el. A zárolt pozíció beállítása a zárolások meghajtók végzik.

#### <a name="to-unlock-the-antitamper-lock"></a>A antitamper zárolás feloldásához
1. A zárolás kulcs ("tamperproof" T10 csavarhúzót a Microsoft által biztosított) gondosan beszúrása a rekesze és a leíró a és a hozzá tartozó szoftvercsatorna. 
   
   Antitamper zárolási aktiválódik, ha a piros jelzőikon nyílás látható.
  
    ![Zárolt lemezmeghajtó](./media/storsimple-disk-drive-replacement/IC741056.png)
   
    **1. ábra** lesz a víruskereső illetéktelen zárolása
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |Kijelző rekesze és |
   | 2 |Antitamper zárolása |
2. Kulcsrotálás az óramutató járásával ellenkezően irányba addig nem jelenik meg a fenti a kulcs rekesze és a piros jelző.
3. Távolítsa el a kulcsot.
   
    ![Meghajtó zárolását](./media/storsimple-disk-drive-replacement/IC741057.png)
   
    **2. ábra** feloldva lemezmeghajtó
4. A meghajtó már távolíthatja el.

A zárolás végezhetnek fordított kövesse.

## <a name="remove-the-disk-drive"></a>A meghajtó eltávolítása
A StorSimple-eszköz támogatja a RAID 10-hez hasonló storage spaces konfiguráció. Ez azt jelenti, hogy azt egy meghibásodott lemezt, tartós állapotú meghajtót (SSD), a szokásos módon működhet, vagy a merevlemez-meghajtó (HDD).

> [!IMPORTANT]
> * Ha a rendszer egynél több meghibásodott lemezt, távolítsa el több SSD vagy HDD bármikor a rendszer időben. Ez azt eredményezheti, hogy az adatvesztés.
> * Győződjön meg arról, hogy egy SSD-t korábban forráselérésiútja tárhely helyett SSD helyez. Hasonlóképpen helyezze, amelyek korábban egy HDD-bővítőhely HDD helyett.
> * Az Azure Portalon tárhelyek számozása 0 – 11. Ezért ha a portál megjeleníti, hogy egy lemezt a 2-es bővítőhelyen sikertelen, az eszközön, keresse meg a meghibásodott lemezt, a harmadik tárolóhelyben bal oldalt felül.
> 
> 

Meghajtók eltávolítható, és közben a rendszer lecseréli.

#### <a name="to-remove-a-drive"></a>A meghajtó eltávolítása
1. Azonosítsa a meghibásodott lemezt, az Azure Portalon nyissa meg az eszköz **beállítások > hardverállapot**. Egy lemezt a elsődleges ház és/vagy egy EBOD ház (Ha használja a 8600-as modell) sikertelen, mert tekintse meg a lemezek állapotát **Shared components** és **EBOD közös összetevők**. Egy meghibásodott lemez vagy ház vörös állapottal jelenik meg.
2. Keresse meg az elsődleges ház- és a EBOD ház található meghajtókat. 
3. Ha a lemez nem oldják a zárolást, folytassa a következő lépéssel. Ha a lemez zárolva van, a zárolás feloldásához leírt [antitamper zárolási működésűeknek](#disengage-the-antitamper-lock).
4. Nyomja le a fekete zárolás a meghajtó szolgáltatója modulról, és kérje le a meghajtó szolgáltatója leíró azonnal ki és a váz elejéhez.
   
    ![Lemezmeghajtó leíró felszabadítása](./media/storsimple-disk-drive-replacement/IC741051.png)
   
    **3. ábra** a meghajtó leíró felszabadítása
5. Ha a meghajtó szolgáltatója leíró teljes mértékben ki van bővítve, húzza az ujját a meghajtó szolgáltatója, a váz kívül. 
   
    ![Késleltetett lemezmeghajtó ki lemez](./media/storsimple-disk-drive-replacement/IC741052.png)
   
    **4. ábra** késleltetett a lemezmeghajtó a szállítmányozó kívül

## <a name="install-the-replacement-disk-drive"></a>Telepítse a helyettesítő-meghajtó
Miután egy meghajtót az a StorSimple-eszköz nem sikerült, és törölte, az alábbi eljárás segítségével cserélje le az új meghajtó.

#### <a name="to-insert-a-drive"></a>A meghajtó beszúrása
1. Győződjön meg arról, a meghajtó szolgáltatója leíró teljes mértékben ki van bővítve, az alábbi képen látható módon.
   
    ![A kiterjesztett leíró lemezmeghajtó](./media/storsimple-disk-drive-replacement/IC741044.png)
   
    **5. ábra** kiterjesztett leíró meghajtó
2. Húzza az ujját a meghajtó szolgáltatója egészen a váz be.
   
    ![Késleltetett lemez be lemezmeghajtó szolgáltatója](./media/storsimple-disk-drive-replacement/IC741045.png)
   
    **6. ábra** meghajtó leltárelemet késleltetett be a váz
3. Az a meghajtó szolgáltatói beszúrt zárja be a meghajtó szolgáltatója leíró miközben továbbra is a meghajtó szolgáltatója leküldése a váz, amíg a meghajtó szolgáltatója leíró illeszti be a zárolt helyzetben.
4. Használja a szolgáltató leíró biztonságos (tamperproof Torx csavarhúzóra) a Microsoft által biztosított zárolási kulcsot helyen bekapcsolásával a zárolás csavart negyedév kapcsolja elforgatása jobbra.
5. Győződjön meg arról, hogy a csere sikeres volt, és a meghajtó működik. Az Azure portal eléréséhez, és keresse meg a **eszközbeállítások** > **hardverállapot**. A **Shared components** vagy **EBOD közös összetevők**, a meghajtó állapota zöld, jelezve, hogy kifogástalan állapotú legyen.

   
   > [!NOTE]
   > A lemez állapotát, amely után a helyettesítő zöld színűre néhány óráig is eltarthat.
  
## <a name="next-steps"></a>További lépések
Tudjon meg többet [StorSimple összetevő hardvercseréhez](storsimple-8000-hardware-component-replacement.md).

