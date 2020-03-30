---
title: Lemezmeghajtó cseréje StorSimple 8000 sorozatú eszközön | Microsoft dokumentumok
description: Bemutatja, hogyan cserélhető le egy lemezmeghajtó egy StorSimple elsődleges ház vagy egy EBOD ház.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267910"
---
# <a name="replace-a-disk-drive-on-your-storsimple-8000-series-device"></a>Lemezmeghajtó cseréje StorSimple 8000 sorozatú eszközökön

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag bemutatja, hogyan távolíthat el és cserélhet le egy hibásan működő vagy meghibásodott merevlemez-meghajtót egy Microsoft Azure StorSimple-eszközön. A lemezmeghajtó cseréjéhez a következőket kell tenni:

* Oldja ki az antitamper zárat
* A lemezmeghajtó eltávolítása
* A cserelemez-meghajtó telepítése

> [!IMPORTANT]
> A lemezmeghajtó eltávolítása és cseréje előtt tekintse át a [StorSimple hardverösszetevő-csere](storsimple-8000-hardware-component-replacement.md)biztonsági tudnivalóit.
 

## <a name="disengage-the-antitamper-lock"></a>Oldja ki az antitamper zárat
Ez az eljárás bemutatja, hogy a StorSimple-eszközön lévő antitamper zárolások hogyan kapcsolhatók be vagy kapcsolhatók ki a lemezmeghajtók cseréjekor. Az antitamper zárak vannak felszerelve a meghajtó hordozó fogantyúk, és ezek keresztül érhető el egy kis nyílás a zár részben a fogantyú. A meghajtókat a reteszelt reteszelésű zárakkal szállítjuk.

#### <a name="to-unlock-the-antitamper-lock"></a>Az antitamper zár feloldása
1. Óvatosan helyezze be a zárkulcsot (a Microsoft által biztosított "szabotázsbiztos" T10 csavarhúzót) a fogantyúnyílásba és a foglalatba. 
   
   Ha az antitamper zár aktiválva van, a piros kijelző látható a rekeszben.
  
    ![Zárolt lemezmeghajtó](./media/storsimple-disk-drive-replacement/IC741056.png)
   
    **1. ábra** Szabotázselleni zár bekapcsolva
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |Kijelző rekesznyílása |
   | 2 |Antitamper zár |
2. Forgassa el a kulcsot az óramutató járásával ellentétes irányban, amíg a piros jelzőfény nem látható a kulcs feletti nyílásban.
3. Vedd le a kulcsot.
   
    ![Feloldott lemezmeghajtó](./media/storsimple-disk-drive-replacement/IC741057.png)
   
    **2. ábra** Feloldott lemezmeghajtó
4. A lemezmeghajtó most már eltávolítható.

A zár bekapcsolásához kövesse a hátrameneti lépéseket.

## <a name="remove-the-disk-drive"></a>A lemezmeghajtó eltávolítása
A StorSimple eszköz támogatja a RAID 10-szerű tárolóhelyek konfigurációját. Ez azt jelenti, hogy egy meghibásodott lemezzel, SSD-meghajtóval vagy merevlemez-meghajtóval (HDD) képes normálisan működni.

> [!IMPORTANT]
> * Ha a rendszer egynél több meghibásodott lemezzel rendelkezik, ne távolítson el egynél több SSD-t vagy HDD-t a rendszerből. Ez adatvesztéshez vezethet.
> * Győződjön meg arról, hogy egy csere SSD-t helyezett el egy olyan foglalatban, amely korábban SSD-t tartalmazott. Hasonlóképpen helyezzen egy csere HDD-t egy olyan foglalatba, amely korábban hdd-t tartalmazott.
> * Az Azure Portalon a tárolóhelyek 0 és 11 közötti számmal vannak számozva. Ezért ha a portál azt mutatja, hogy a 2-es tárolóhely egy lemeze meghibásodott, az eszközön keresse meg a meghibásodott lemezt a harmadik tárolóhelyen a bal felső sarokból.
> 
> 

A meghajtók eltávolíthatók és cserélhetők a rendszer működése közben.

#### <a name="to-remove-a-drive"></a>Meghajtó eltávolítása
1. A meghibásodott lemez azonosításához az Azure Portalon nyissa meg az eszköz **beállításai > hardver állapota**. Mivel egy lemez meghibásodhat az elsődleges ház és/vagy egy EBOD ház (ha egy 8600-as modellt használ), nézd meg a lemezek állapotát a **Megosztott összetevők** és az **EBOD megosztott összetevők**. A rendszer mindkét ház meghibásodott lemeze piros állapotú lesz.
2. Keresse meg a meghajtók az elsődleges ház vagy az EBOD ház. 
3. Ha a lemez zárolása nincs feloldva, folytassa a következő lépéssel. Ha a lemez zárolva van, oldja fel a zárolást a [Szabotázselleni zár kioldása](#disengage-the-antitamper-lock)című részben található eljárás sal.
4. Nyomja meg a fekete reteszt a meghajtóhordozó modulon, és húzza ki a meghajtóhordozó fogantyúját, és távolodjon el az alváz elejétől.
   
    ![Lemezmeghajtó-leíró felengedése](./media/storsimple-disk-drive-replacement/IC741051.png)
   
    **3. ábra** A meghajtó fogantyújának felengedése
5. Ha a meghajtótartó fogantyúja teljesen ki van nyújtva, csúsztassa ki a meghajtótartót a házból. 
   
    ![Csúszó lemez a lemezmeghajtóból](./media/storsimple-disk-drive-replacement/IC741052.png)
   
    **4.** A lemezmeghajtó kicsúsztatása a hordozóból

## <a name="install-the-replacement-disk-drive"></a>A cserelemez-meghajtó telepítése
Miután egy meghajtó meghibásodott a StorSimple-eszközben, és eltávolította, kövesse az alábbi eljárást, ha új meghajtóra cserélheti.

#### <a name="to-insert-a-drive"></a>Meghajtó beszúrása
1. Győződjön meg arról, hogy a meghajtóhordozó fogantyúja teljesen ki van nyújtva, ahogy az az alábbi képen látható.
   
    ![Lemezmeghajtó kiterjesztett fogantyúval](./media/storsimple-disk-drive-replacement/IC741044.png)
   
    **5. ábra** Meghajtó fogantyúval bővített
2. Csúsztassa a meghajtótartót egészen az alvázba.
   
    ![Lemez csúsztatása a lemezmeghajtó-hordozóba](./media/storsimple-disk-drive-replacement/IC741045.png)
   
    **6. ábra**  A meghajtóhordozó becsúszása az alvázba
3. A meghajtótartó behelyezése után csukja le a meghajtóhordozó fogantyúját, miközben továbbra is benyomja a meghajtótartót az alvázba, amíg a meghajtótartó fogantyúja zárt helyzetbe nem pattan.
4. Használja a Microsoft által biztosított zárkulcsot (szabotázsbiztos Torx csavarhúzó) a hordozófogantyú rögzítéséhez úgy, hogy a zárcsavart negyedfordulattal elfordítja az óramutató járásával megegyező irányban.
5. Ellenőrizze, hogy a csere sikeres volt-e, és hogy a meghajtó működőképes-e. Nyissa meg az Azure Portalt, és keresse meg az **Eszközbeállítások** > **hardverállapota**lapot. A **Megosztott összetevők** vagy az **EBOD megosztott összetevői**alatt a meghajtó állapotának zöldnek kell lennie, jelezve, hogy kifogástalan állapotú.

   
   > [!NOTE]
   > A csere után több órába is beletelhet, amíg a lemez állapota zöldre vált.
  
## <a name="next-steps"></a>További lépések
További információ a [StorSimple hardverösszetevő-cseréjéről.](storsimple-8000-hardware-component-replacement.md)

