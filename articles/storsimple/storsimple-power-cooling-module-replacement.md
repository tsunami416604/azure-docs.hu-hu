---
title: "Cserélje le a StorSimple eszköz egy PCM |} Microsoft Docs"
description: "Ismerteti, hogyan eltávolítja és pótolja az energia- és hűtési modul (PCM) a StorSimple eszköz"
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 24a158cb-0b79-4908-bb5a-431e48760f6a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 7031aff9d4797e99e6523a65ded7495c88aff282
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>Cserélje le a energia- és hűtési modul a StorSimple eszköz
> [!NOTE]
> A klasszikus portál StorSimple elavult. A StorSimple eszköz kezelői automatikusan áthelyezi a érvénytelenítése ütemezés szerint az új Azure-portálon. Kapni fog egy e-mailek és a portál értesítései az áthelyezés. Ez a dokumentum hamarosan is kell vonni. Ez a cikk az új Azure-portálon verziójának megtekintéséhez keresse fel [energia- és hűtési modul lecseréli a StorSimple eszköz](storsimple-8000-power-cooling-module-replacement.md). Az áthelyezés kapcsolatos kérdéseivel lásd: [– gyakori kérdések: áthelyezése az Azure-portálon](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Áttekintés
A energia- és hűtési modul (PCM) a Microsoft Azure StorSimple eszközt a áll egy tápegység- és hűtési az elsődleges és a EBOD ház által szabályozott ventilátor. Csak egy modellje, amely minden ház minősítéssel PCM van. Az elsődleges ház egy 764 W PCM minősítéssel, és a EBOD ház egy 580 W PCM minősítéssel. Annak ellenére, hogy az elsődleges ház és a EBOD ház PCMs különböző, a csere eljárás megegyezik.

Ez az oktatóanyag azt ismerteti, hogyan:

* Távolítsa el a PCM
* A helyettesítő PCM telepítése

> [!IMPORTANT]
> Mielőtt eltávolítása és cseréje egy PCM, tekintse át a biztonsági információk [StorSimple hardver összetevő cseréje](storsimple-hardware-component-replacement.md).
> 
> 

## <a name="before-you-replace-a-pcm"></a>Mielőtt lecseréli a PCM
Vegye figyelembe a következő fontos problémák a PCM cseréje előtt:

* Ha a PCM tápegység. meghibásodik, a hibás modul telepítve hagyja, de a tápkábel eltávolítása. A ventilátor power kapnak a ház, és továbbra is biztosítani a megfelelő hűtési továbbra is. Ha nem sikerül a ventilátor, a PCM kell azonnal le kell cserélni.
* Mielőtt eltávolítaná a PCM, válassza le a teljesítmény a PCM (ha van ilyen) a fő kapcsoló kikapcsolásával vagy a tápkábel fizikailag eltávolításával. Így lehetővé teszi a rendszer figyelmeztetést, hogy egy power leállítás rövidesen.
* Győződjön meg arról, hogy a többi PCM működési folyamatos rendszer működéséhez a hibás PCM cseréje előtt. Hibás PCM kell helyettesíteni egy teljesen működőképes PCM lehető legrövidebb időn belül.
* PCM modul helyettesítő csak néhány percet is igénybe vehet, de túlmelegedése megelőzése érdekében sikertelen PCM eltávolításával 10 percen belül kell végezni.
* Vegye figyelembe, hogy a csere 764 W PCM modulok gyártól szállított tartalmaz a biztonsági mentési akkumulátor modul. Szüksége lesz az akkumulátor eltávolítása a hibás PCM, majd szúrja be a csere modulba váltja végrehajtása előtt. További információkért lásd: hogyan [távolítsa el, majd szúrja be a biztonsági mentési akkumulátor modul](storsimple-battery-replacement.md).

## <a name="remove-a-pcm"></a>Távolítsa el a PCM
Kövesse ezeket az utasításokat, ha készen áll egy Power és hűtési modul (PCM) eltávolítása a Microsoft Azure StorSimple eszközt.

> [!NOTE]
> A PCM eltávolítása előtt győződjön meg arról, hogy rendelkezik-e (az elsődleges ház W 764) vagy 580 W a EBOD ház a megfelelő helyettesíti.
> 
> 

#### <a name="to-remove-a-pcm"></a>Egy PCM eltávolítása
1. A klasszikus Azure portálon kattintson **eszközök** > **karbantartási** > **hardverállapot**. Tekintse meg a PCM összetevők alatt **megosztott összetevők** azonosításához, amely PCM sikertelen volt:
   
   * Ha a tápegységet PCM 0 sikertelen volt, állapotának **tápegység PCM 0 a** pedig piros színűvé változik.
   * Ha egy tápegység PCM az 1. sikertelen volt, állapotának **PCM az 1. tápegység** pedig piros színűvé változik.
   * Ha a ventilátor PCM az 1. sikertelen volt, vagy állapotának **0 hűtési PCM 0 a** vagy **PCM 0 1 hűtési** pedig piros színűvé változik.
2. Keresse meg a sikertelen PCM az elsődleges ház hátulján olvasható. Ha futtat egy 8600 modell, azonosítsa az elsődleges ház alapján a rendszer egység azonosítószámának a előlapján LED megjelenítendő látható. Az alapértelmezett érték a egység azonosítója: jelenik meg a elsődleges ház **00**, mivel az alapértelmezett egység azonosítója: jelenik meg a EBOD ház **01**. A következő ábra és táblázat magyarázza el, a LED megjelenítési előlapja.
   
    ![Előlapján OPS azonosító](./media/storsimple-power-cooling-module-replacement/IC740991.png)
   
     **1. ábra** az eszköz első panel  
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |Némító gomb |
   | 2 |Rendszer energiagazdálkodási |
   | 3 |A modul hiba |
   | 4 |Logikai hiba |
   | 5 |Egység azonosító megjelenítése |
3. A figyelési kijelző LED hátulján az elsődleges ház is használható a hibás PCM azonosításához. Tekintse meg a következő ábra és táblázat segít megérteni, hogyan keresse meg a hibás PCM a LED segítségével. Például ha a LED megfelelő a **ventilátor sikertelen** van bekapcsolásával; a ventilátor sikertelen volt. Hasonlóképpen ha a LED megfelelő **AC sikertelen** van bekapcsolásával, az áramellátás sikertelen volt. 
   
    ![Eszköz PCM figyelési kijelző LED Csatlakozópanel](./media/storsimple-power-cooling-module-replacement/IC740992.png)
   
     **2. ábra** vissza a PCM a kijelző LED
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |AC áramszünet esetén |
   | 2 |Hiba ventilátor |
   | 3 |Akkumulátor hiba |
   | 4 |PCM OK |
   | 5 |DC áramszünet esetén |
   | 6 |Kifogástalan akkumulátor |
4. Tekintse meg a következő ábra a háttér a StorSimple eszköz keresse meg a sikertelen PCM modul. PCM 0 a bal oldali és a jobb oldalon pedig PCM 1. Az alábbi táblázat ismerteti a modulokat.
   
     ![Az eszköz elsődleges ház modulok Csatlakozópanel](./media/storsimple-power-cooling-module-replacement/IC740994.png)
   
     **3. ábra** oldalán a beépülő modulok eszköz 
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |A vezérlő 0 |
   | 4 |1. vezérlő |
5. Kapcsolja ki a hibás PCM, és húzza ki a forrás. Most már eltávolíthatja a PCM.
6. A zárolás és az USB és mutatóujj között a PCM leíró oldalán megfogható, és nyomja össze együtt, hogy a leírót megnyitni.
   
    ![Nyitó PCM leíró](./media/storsimple-power-cooling-module-replacement/IC740995.png)
   
    **4. ábra** a PCM leíró megnyitása
7. A leíró fogja túl, és távolítsa el a PCM.
   
    ![PCM-eszközök eltávolítása](./media/storsimple-power-cooling-module-replacement/IC740996.png)
   
    **5. ábra** a PCM eltávolítása

## <a name="install-a-replacement-pcm"></a>A helyettesítő PCM telepítése
Kövesse ezeket az utasításokat egy PCM a StorSimple eszköz telepítése. Győződjön meg arról, hogy a csere PCM (764 W PCMs vonatkozik) telepítése előtt a biztonsági mentési akkumulátor modul beszúrt. További információkért lásd: hogyan [távolítsa el, majd szúrja be a biztonsági mentési akkumulátor modul](storsimple-battery-replacement.md).

#### <a name="to-install-a-pcm"></a>Egy PCM telepítése
1. Győződjön meg arról, hogy rendelkezik-e a ház megfelelő PCM helyettesítője. Az elsődleges ház kell egy 764 W PCM, és a EBOD ház kell egy 580 W PCM. Ne próbáljon a 580 W PCM az elsődleges szolgáltatással, illetve a 764 W PCM a EBOD szolgáltatással. A következő kép bemutatja, hol határozza meg az adatokat, amelyek a PCM dobozának a címkén.
   
    ![Eszköz PCM címke](./media/storsimple-power-cooling-module-replacement/IC740973.png)
   
    **6. ábra** PCM címke
2. Ellenőrizze, hogy a ház, különös tekintettel az összekötők való megosztása kárt. 
   
   > [!NOTE]
   > **A modul nem telepítése, ha bármely összekötő elgörbülve.**
   > 
   > 
3. A nyitott állapotban PCM leíró, és csúsztassa be a modul a ház be.
   
    ![PCM eszköz telepítése](./media/storsimple-power-cooling-module-replacement/IC740975.png)
   
    **7. ábra** a PCM telepítése
4. Kézzel zárja be a PCM leíró. Egy kattintással kell hall, mivel a leíró zárolás kapcsolatba lép. 
   
   > [!NOTE]
   > Győződjön meg arról, hogy folytat rendelkezik-e az összekötő PIN-kód, hogy akkor is óvatosan tug a leírón a zárolás feloldása nélkül. A PCM diák ki, ha azt feltételezi, hogy a zárolás bezárult, mielőtt az összekötők részt vevő.
   > 
   > 
5. A tápkábelek csatlakoztatja a áramforrásról és a PCM.
6. A törzs mentesség bálákban biztonságos. 
7. Kapcsolja be a PCM.
8. Győződjön meg arról, hogy sikeres volt-e a helyettesítő: a StorSimple Manager szolgáltatás klasszikus Azure portálon lépjen a **eszközök** > **karbantartási**  >   **Hardverállapot**. A **megosztott összetevők**, a PCM állapota zöld kell lennie. 
   
   > [!NOTE]
   > A csere PCM teljesen inicializálni a néhány percig is eltarthat.
   > 
   > 

## <a name="next-steps"></a>Következő lépések
További információ [StorSimple hardver összetevő cseréje](storsimple-hardware-component-replacement.md).

