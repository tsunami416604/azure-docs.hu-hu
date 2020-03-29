---
title: StorSimple 8000 sorozatú hardverkomponens cseréje | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogy miként cserélhetők le biztonságosan a PCM-ek, az akkumulátor, a vezérlőmodulok, az EBOD-vezérlők, a lemezmeghajtók és a StorSimple-eszközök háza.
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
ms.date: 06/02/2017
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: e05a37122647d4979089f0ba00b1fc15f9b84b0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60321732"
---
# <a name="replace-a-hardware-component-on-your-storsimple-8000-series-device"></a>Hardverösszetevő cseréje a StorSimple 8000 sorozatú eszközön

## <a name="overview"></a>Áttekintés
A hardverösszetevő-csere oktatóanyag a Microsoft Azure StorSimple 8000 sorozatú eszköz hardverösszetevőit és az eltávolításukhoz és cseréjéhez szükséges lépéseket ismerteti. Ez a cikk ismerteti a biztonsági ikonokat, a részletes oktatóanyagokra mutató mutatókat, valamint felsorolja a cserélhető összetevőket.

> [!IMPORTANT]
> Mielőtt megpróbálna eltávolítani vagy kicserélni a StorSimple bármely összetevőjét, ellenőrizze, hogy áttekinti-e a [biztonsági ikonra vonatkozó konvenciókat](#safety-icon-conventions) és egyéb [biztonsági óvintézkedéseket.](storsimple-safety.md)


### <a name="safety-icon-conventions"></a>Biztonsági ikon konvenciók
Az alábbi táblázat az alábbi útmutatókban használt biztonsági ikonokat ismerteti. Ügyeljen ezekre a biztonsági ikonokra, miközben végigmegy az eszköz összetevőinek eltávolításához és cseréjéhez szükséges lépéseken.

| Ikon | Szöveg | További információ |
|:--- |:--- |:--- |
| ![Figyelmeztetés ikon](./media/storsimple-hardware-component-replacement/Warning.png) |**Veszély!** |Olyan veszélyes helyzetet jelöl, amely, ha nem kerülik el, halálhoz vagy súlyos sérüléshez vezet. Ez a jelszó a legszélsőségesebb helyzetekre korlátozódik. |
| ![Figyelmeztetés ikon](./media/storsimple-hardware-component-replacement/Warning.png) |**Figyelmeztetés!** |Olyan veszélyes helyzetet jelöl, amely, ha nem kerülik el, halálhoz vagy súlyos sérüléshez vezethet. |
| ![Vigyázat ikon](./media/storsimple-hardware-component-replacement/Caution.png) |**Figyelem!** |Olyan veszélyes helyzetet jelöl, amely, ha nem kerülik el, kisebb vagy közepes sérülést okozhat. |
| ![Értesítés ikon](./media/storsimple-hardware-component-replacement/NoticeIcon.png) |**Észrevesz:** |Fontosnak tekintett, de a veszélyhez kapcsolódó információkat jelöl. |
| ![Az áramütés ikonja](./media/storsimple-hardware-component-replacement/Electric.png) |**Áramütés veszélye** |Magas feszültséget jelez. |
| ![A Nehéz súly ikonja](./media/storsimple-hardware-component-replacement/Weight.png) |**Nehéz súly** | |
| ![Nincs felhasználó által javítható alkatrészikon](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png) |**Nincs enek javítható alkatrész a felhasználó számára** |Ne férjen hozzá, kivéve, ha megfelelően van betanítva. |
| ![Utasítások olvasása ikon](./media/storsimple-hardware-component-replacement/ReadInstructions.png) |**Először olvassa el az összes utasítást** | |
| ![Tippveszély ikon](./media/storsimple-hardware-component-replacement/TipHazard.png) |**Tipp veszély** | |

### <a name="before-you-begin"></a>Előkészületek
Ismerkedjen meg az oktatóeszközbiztonsági információkkal és az oktatóanyagban használt biztonsági ikonokkal. A teljes körű információkért [keresse fel és működtesse biztonságosan a StorSimple-eszközt.](storsimple-safety.md) A StorSimple-eszköz kezelése előtt mindenképpen tekintse át a [biztonsági óvintézkedéseket.](storsimple-safety.md#handling-precautions)

Mielőtt megpróbálna lecserélni egy összetevőt, vegye figyelembe az alábbi információkat.

![Figyelmeztetés](./media/storsimple-hardware-component-replacement/Warning.png) ![Icon Elektromos](./media/storsimple-hardware-component-replacement/Electric.png) Sokk Icon **FIGYELMEZTETÉS!**

* A StorSimple készülék moduljainak és alkatrészeinek kezelésekor elektrosztatikus kisülés vagy antisztatikus mat segítségével megfelelően földelje magát.
* Ne érintsen meg semmilyen áramkört. Használja a mellékelt fogantyúkat és vezetőket, miközben olyan alkatrészeket kezel, amelyek ki vannak téve az áramköröknek.

![Figyelmeztetési](./media/storsimple-hardware-component-replacement/Warning.png) ![ikon értesítési](./media/storsimple-hardware-component-replacement/NoticeIcon.png) ikonja **ÉRTESÍTÉS:**

Amikor kicserél egy modult, **SOHA ne hagyjon üres rekeszt a ház hátsó részén**. A problémás rész eltávolítása előtt szerezzen be egy csere- vagy üres modult.

## <a name="hardware-component-replacement-procedures"></a>Hardverösszetevők cseréjére vonatkozó eljárások
A StorSimple 8000 sorozatú eszköz több beépülő modulból áll az elsődleges és/vagy EBOD házakban. A 8100 egyetlen elsődleges házzal rendelkezik, míg a 8600 egy kettős ház eszköz elsődleges házzal és EBOD házzal.

Az eszköz fő hardverösszetevőit az alábbi táblázatok foglalják össze. Kattintson a **csereeljárás** oszlopban lévő hivatkozásra a társított oktatóanyag hoz.

| Összetevők | # Jelen | Beépülő modul? | Helyettesítési eljárás |
|:--- |:--- |:--- |:--- |
| Váz |1 |Nem |[Cserélje ki a storSimple eszköz házát](storsimple-8000-chassis-replacement.md) |
| Elsődleges vezérlők |2 |Igen |[Vezérlőmodul cseréje a StorSimple eszközön](storsimple-8000-controller-replacement.md) |
| 764 W-os táp- és hűtőmodulok (PCM-ek) |2 |Igen |[Az energiaellátási és hűtési rendszer cseréje a StorSimple-eszközön](storsimple-8000-power-cooling-module-replacement.md) |
| Tartalék akkumulátor |2 |Igen |[A tartalék akkumulátor modul cseréje a StorSimple-eszközön](storsimple-8000-battery-replacement.md) |
| Lemezmeghajtók |12 |Igen |[Lemezmeghajtó cseréje a StorSimple eszközön](storsimple-8000-disk-drive-replacement.md) |

**1.** Hardverösszetevők az elsődleges burkolatban

Az elsődleges ház és az EBOD ház különböznek az I/O-modulokban. Továbbá, a PCMs különböző teljesítmény. Az elsődleges ház PCM-jei 764 W, míg az EBOD ház580 W- os. Az elsődleges házpcm-jei egy tartalék akkumulátormodult is tartalmaznak.

| Összetevők | # Jelen | Beépülő modul? | Helyettesítési eljárás |
|:--- |:--- |:--- |:--- |
| Váz |1 |Nem |[Cserélje ki a storSimple eszköz házát](storsimple-8000-chassis-replacement.md) |
| EBOD-vezérlők |2 |Igen |[EBOD-vezérlő cseréje a StorSimple eszközön](storsimple-8000-ebod-controller-replacement.md) |
| 580 W-os táp- és hűtőmodulok (PCM-ek) |2 |Igen |[Az energiaellátási és hűtési rendszer cseréje a StorSimple-eszközön](storsimple-8000-power-cooling-module-replacement.md) |
| Lemezmeghajtók |12 |Igen |[Lemezmeghajtó cseréje a StorSimple eszközön](storsimple-8000-disk-drive-replacement.md) |

**2.** Hardverösszetevők az EBOD házban

A készülék beépülő moduljai az alábbi első és hátsó ábrákon jelennek meg. Ezekkel a diagramokkal meghatározhatja a különböző beépülő modulok helyét, ha cserére van szükség. Az elülső ábra a lemezmeghajtókat, az EBOD ház és az elsődleges ház hátsó diagramjait mutatja a beépülő modulokat.

![Az eszköz előlapján, lemezmeghajtókkal](./media/storsimple-hardware-component-replacement/IC741028.png)

**1. ábra** A készülék eleje

| Címke | Leírás |
|:--- |:--- |
| 0 - 11 |Lemezmeghajtók (összesen 12) |

Mind az elsődleges ház, mind az EBOD ház meghajtóvivő modulokkal rendelkezik. A ház 12 3,5"-os lemezmeghajtónak ad otthont, 3:4 formátumban elrendezve.

![Az eszköz elsődleges házmoduljainak hátlapja](./media/storsimple-hardware-component-replacement/IC740994.png)

**2. ábra** Az elsődleges ház háttekon

| Címke | Leírás |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |Vezérlő 0 |
| 4 |Vezérlő 1 |

![Az EBOD ház beépített moduljainak hátsíkja](./media/storsimple-hardware-component-replacement/IC769599.png)

**3. ábra** Az EBOD ház hátulja

| Címke | Leírás |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |EBOD vezérlő 0 |
| 4 |EBOD vezérlő 1 |

## <a name="field-replaceable-units"></a>Mezőcserélhető egységek
A StorSimple eszközhöz a következő cserélhető egységek (FRUs- k) érhetők el:

* Alváz (beleértve az integrált műveleti panelt is)
* 764 W AC PCM
* 580 W AC PCM
* Merevlemez-meghajtó meghajtóvivő modullal
* Vezérlő modul
* EBOD vezérlőmodul
* Tartalék akkumulátor modul
* Rack szerelő sínkészlet

A csereegységek megrendeléséhez [forduljon](storsimple-8000-contact-microsoft-support.md) a Microsoft támogatási szolgálatához.

## <a name="next-steps"></a>További lépések
A StorSimple hardverösszetevő cseréjének megkísérlése előtt tekintse át az összes [biztonsági információt.](storsimple-safety.md)

