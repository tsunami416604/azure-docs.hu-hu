---
title: "StorSimple hardver összetevő cseréje |} Microsoft Docs"
description: "Biztonságosan cserélje le a PCMs, akkumulátor, vezérlő modulok, EBOD vezérlők, meghajtók és a StorSimple eszköz váz ismerteti."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: e8087ba7-0b66-4f59-8988-e53aad52ee21
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2c8945dd06f0372c4260e0553e15627607be6979
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="replace-a-hardware-component-on-your-storsimple-8000-series-device"></a>Cserélje le a StorSimple 8000 series eszközön egy hardverösszetevő
> [!NOTE]
> A klasszikus portál StorSimple elavult. A StorSimple eszköz kezelői automatikusan áthelyezi a érvénytelenítése ütemezés szerint az új Azure-portálon. Kapni fog egy e-mailek és a portál értesítései az áthelyezés. Ez a dokumentum hamarosan is kell vonni. Ez a cikk az új Azure-portálon verziójának megtekintéséhez keresse fel [cserélje le a StorSimple 8000 series eszközön hardverösszetevőt](storsimple-8000-hardware-component-replacement.md). Az áthelyezés kapcsolatos kérdéseivel lásd: [– gyakori kérdések: áthelyezése az Azure-portálon](storsimple-8000-move-azure-portal-faq.md).


## <a name="overview"></a>Áttekintés
A hardver összetevő helyettesítő oktatóanyagok a hardverösszetevők, a Microsoft Azure StorSimple 8000 series eszközt, és távolítsa el, és cserélje le a szükséges lépéseket ismerteti. Ez a cikk ismerteti a biztonsági ikonok, biztosít a részletes oktatóprogramjai mutató hivatkozások, és cserélhető összetevőit tartalmazza.

> [!IMPORTANT]
> Távolítsa el vagy cserélje le a StorSimple összetevők közül bármelyik megkísérlése előtt győződjön meg arról, hogy tekintse át a [biztonsági ikon egyezmények](#safety-icon-conventions) és egyéb [biztonsági óvintézkedéseket](storsimple-safety.md).
> 
> 

### <a name="safety-icon-conventions"></a>Biztonsági ikon konvenciók
Az alábbi táblázatban szereplő használt biztonsági ikonokat. Figyelmesen elolvassa a biztonsági ikonok eltávolítja és pótolja az eszköz összetevők lépéseit követve.

| Ikon | Szöveg | További információ |
|:--- |:--- |:--- |
| ![Figyelmeztetés ikon](./media/storsimple-hardware-component-replacement/Warning.png) |**VESZÉLY!** |Azt jelzi, hogy egy eredményező, nem elkerülhető, ha halál vagy súlyosan veszélyes helyzetben. Ez a jel szó a rendkívüli helyzetek korlátozódik. |
| ![Figyelmeztetés ikon](./media/storsimple-hardware-component-replacement/Warning.png) |**FIGYELMEZTETÉS!** |Azt jelzi, hogy egy veszélyes helyzetben, ha nem elkerülhető, halállal vagy komoly kárt okozhat. |
| ![Figyelmeztetés ikon](./media/storsimple-hardware-component-replacement/Caution.png) |**FIGYELEM!** |Azt jelzi, hogy nem elkerülhető, ha a kisebb vagy mérsékelt kárt okozhat veszélyes helyzet. |
| ![Figyelmeztetés ikon](./media/storsimple-hardware-component-replacement/NoticeIcon.png) |**FIGYELMEZTETÉS:** |Azt jelzi, fontos, de nem a veszély kapcsolatos információt. |
| ![Elektromos ütés ikon](./media/storsimple-hardware-component-replacement/Electric.png) |**Elektromos ütés veszély** |Azt jelzi, hogy magas feszültségérzékelő. |
| ![Nagy súlyozást ikon](./media/storsimple-hardware-component-replacement/Weight.png) |**Nehéz súlyozás** | |
| ![Nincs felhasználói használható kijelzők ikonja](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png) |**Nincsenek felhasználói használható részei** |Nem érhető el, ha megfelelően be van tanítva. |
| ![Olvasható utasításokat ikon](./media/storsimple-hardware-component-replacement/ReadInstructions.png) |**Először olvassa el az összes utasításokat** | |
| ![Tipp veszély ikon](./media/storsimple-hardware-component-replacement/TipHazard.png) |**Tipp veszély** | |

### <a name="before-you-begin"></a>Előkészületek
Ismerkedjen meg a jelen oktatóanyagban használt eszköz és a biztonsági ikonok biztonsági információt. Ugrás a [biztonságosan telepítéséhez, és a StorSimple eszköz üzemeltetéséhez](storsimple-safety.md) kapcsolatos részletes információkért. Ne feledje el áttekinteni a [biztonsági óvintézkedéseket](storsimple-safety.md#handling-precautions) előtt a StorSimple eszközt kezeli. 

Mielőtt megkísérli egy, vegye figyelembe a következőket.

![Figyelmeztetés ikon](./media/storsimple-hardware-component-replacement/Warning.png) ![elektromos ütés ikon](./media/storsimple-hardware-component-replacement/Electric.png) **figyelmeztetés!** 

* Szabad saját kezűleg megfelelően kisülés vagy antisztatikus mat használatával modulok és a StorSimple eszköz összetevői kezelésekor.
* Bármely áramkört nem használja. Használja a megadott leírók és útmutatók összetevők, előfordulhat, hogy rendelkezik kitett áramkört kezelése közben.

![Figyelmeztetés ikon](./media/storsimple-hardware-component-replacement/Warning.png) ![ikont láthatja](./media/storsimple-hardware-component-replacement/NoticeIcon.png) **értesítés:**

Ha egy modul **SOSEM hagyják el a a ház hátsó egy üres bay**. A probléma rész eltávolítása előtt szerezze be a helyett vagy annak üres modul.

## <a name="hardware-component-replacement-procedures"></a>Hardver összetevő helyettesítő eljárások
A StorSimple 8000 series eszköz több beépülő modult a modulok az elsődleges és/vagy EBOD ház áll. A 8100 rendelkezik egy önálló elsődleges ház, mivel a 8600 rendelkező elsődleges ház és egy EBOD ház kettős ház eszköz.

A fő hardverösszetevőinek az eszközt az alábbi táblázatok foglalja össze. Kattintson a hivatkozásra a **helyettesítő eljárás** Ugrás a kapcsolódó oktatóanyag alapját képező oszlop.

| Összetevők | # Jelen van | Beépülő modult? | Csere eljárás |
|:--- |:--- |:--- |:--- |
| Készülékház |1 |Nem |[Cserélje le a StorSimple eszköz készülékház](storsimple-chassis-replacement.md) |
| Elsődleges tartományvezérlők |2 |Igen |[Cserélje le a StorSimple eszköz vezérlő modul](storsimple-controller-replacement.md) |
| 764W energia- és hűtési modulok (PCMs) |2 |Igen |[Cserélje le a energia- és hűtési modul a StorSimple eszköz](storsimple-power-cooling-module-replacement.md) |
| Biztonsági mentési töltöttség esetén |2 |Igen |[Cserélje le a biztonsági mentési akkumulátor modul a StorSimple eszköz](storsimple-battery-replacement.md) |
| Lemezmeghajtó |12 |Igen |[Cserélje le a lemezmeghajtó a StorSimple eszköz](storsimple-disk-drive-replacement.md) |

**1. táblázat** hardverösszetevők az elsődleges szolgáltatással

Az elsődleges ház és a EBOD ház különböznek az i/o-modulok. Ezenkívül a PCMs kell különböző teljesítményt. Az elsődleges szolgáltatással PCMs 764 W, mivel azok a EBOD szolgáltatással 580 w Az elsődleges szolgáltatással PCMs is tartalmazhat, egy biztonsági mentési akkumulátor modul.

| Összetevők | # Jelen van | Beépülő modult? | Csere eljárás |
|:--- |:--- |:--- |:--- |
| Készülékház |1 |Nem |[Cserélje le a StorSimple eszköz készülékház](storsimple-chassis-replacement.md) |
| EBOD tartományvezérlők |2 |Igen |[Cserélje le az EBOD vezérlőhöz a StorSimple eszköz](storsimple-ebod-controller-replacement.md) |
| 580W energia- és hűtési modulok (PCMs) |2 |Igen |[Cserélje le a energia- és hűtési modul a StorSimple eszköz](storsimple-power-cooling-module-replacement.md) |
| Lemezmeghajtó |12 |Igen |[Cserélje le a lemezmeghajtó a StorSimple eszköz](storsimple-disk-drive-replacement.md) |

**2. táblázat** hardverösszetevők a EBOD szolgáltatással

A beépülő modulok az eszközön az alábbi első és hátsó diagramok vannak kiemelve. Ezek a diagramok segítségével különböző a beépülő modulok helyének meghatározásához, ha egy helyettesítő szükség. Az első látható a merevlemez-meghajtók, valamint a hátsó az a EBOD ház és elsődleges ház megjelenik a beépülő modulok.

![Az eszköz lemezmeghajtók Frontplane](./media/storsimple-hardware-component-replacement/IC741028.png)

**1. ábra** az eszköz első

| Címke | Leírás |
|:--- |:--- |
| 0 - 11 |Lemezmeghajtók (összesen 12) |

Az elsődleges ház- és a EBOD ház meghajtó szolgáltatónként modulok vannak. A váz Kezelőkód tizenkét 3.5-ös "lemezmeghajtó rendezett 3 által 4 formátumban.

![Az eszköz elsődleges ház modulok Csatlakozópanel](./media/storsimple-hardware-component-replacement/IC740994.png)

**2. ábra** hátsó az elsődleges ház

| Címke | Leírás |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |A vezérlő 0 |
| 4 |1. vezérlő |

![Az eszköz EBOD ház beépülő modulok Csatlakozópanel](./media/storsimple-hardware-component-replacement/IC769599.png)

**3. ábra** oldalán a EBOD ház

| Címke | Leírás |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |EBOD vezérlő 0 |
| 4 |1. EBOD vezérlő |

## <a name="field-replaceable-units"></a>A mező egységek
A StorSimple eszközt a következő mező egységek (részegysége) érhetők el:

* Váz (beleértve a beépített műveletek panel)
* 764 W AC PCM
* 580 W AC PCM
* Merevlemez-meghajtó meghajtó szolgáltatónként modullal
* A tartományvezérlő modul
* EBOD vezérlő modul
* Biztonsági mentési akkumulátor modul
* Állvány oldalon kit csatlakoztatása

Adjon [forduljon a Microsoft Support](storsimple-contact-microsoft-support.md) rendezése a csere egységeket bármelyikét.

## <a name="next-steps"></a>Következő lépések
Tekintse át az összes [biztonsági információk](storsimple-safety.md) előtt cserélje le a StorSimple hardverösszetevő.

