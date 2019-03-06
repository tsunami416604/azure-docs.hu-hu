---
title: A StorSimple 8000 sorozat hardvercseréhez összetevő |} A Microsoft Docs
description: Ismerteti, hogyan lehet biztonságosan cserélje le a PCMs, akkumulátor, vezérlő modulok, EBOD tartományvezérlők, meghajtók, és a StorSimple-eszköz váz.
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
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433654"
---
# <a name="replace-a-hardware-component-on-your-storsimple-8000-series-device"></a>A StorSimple 8000 sorozatú eszköz a hardverkomponensek cseréje

## <a name="overview"></a>Áttekintés
A hardver összetevő helyettesítő oktatóanyagok ismertetik a hardverösszetevők, a Microsoft Azure StorSimple 8000 sorozatú eszköz és távolítsa el, és cserélje le a szükséges lépéseket. Ez a cikk ismerteti a biztonsági ikonok, mutató hivatkozások a részletes oktatóanyagok segítségével biztosít, és cserélhető összetevőit tartalmazza.

> [!IMPORTANT]
> Távolítsa el vagy cserélje le valamelyik StorSimple összetevő megkísérlése előtt győződjön meg arról, hogy tekintse át a [biztonsági ikon konvenciók](#safety-icon-conventions) és egyéb [biztonsági óvintézkedések](storsimple-safety.md).


### <a name="safety-icon-conventions"></a>Biztonsági ikon konvenciók
A következő táblázat ismerteti az ezekben az oktatóanyagokban használt biztonsági ikonokat. Figyeljen biztonsági ikonok távolítsa el, és cserélje le a alkatrészek lépésein.

| Ikon | Szöveg | További információ |
|:--- |:--- |:--- |
| ![Figyelmeztetés ikon](./media/storsimple-hardware-component-replacement/Warning.png) |**DANGER!** |Azt jelzi, hogy a veszélyes helyzet eredményező, ha nem kerülni, halála vagy komoly kárt. Ez a jel szó a rendkívüli helyzetekben korlátozódik. |
| ![Figyelmeztetés ikon](./media/storsimple-hardware-component-replacement/Warning.png) |**FIGYELMEZTETÉS!** |Azt jelzi, hogy nem elkerülhető, ha a haláleset vagy komoly kárt okozhat a veszélyes helyzet. |
| ![Figyelmeztetés ikon](./media/storsimple-hardware-component-replacement/Caution.png) |**FIGYELEM!** |Azt jelzi, hogy nem elkerülhető, ha a kisebb vagy közepes kárt okozhat a veszélyes helyzet. |
| ![Figyelmeztetés ikon](./media/storsimple-hardware-component-replacement/NoticeIcon.png) |**FIGYELMEZTETÉS:** |Azt jelzi, fontos, de nem veszély kapcsolatos információt. |
| ![Elektromos megváltani ikon](./media/storsimple-hardware-component-replacement/Electric.png) |**Elektromos Megváltani veszély** |Azt jelzi, hogy magas feszültségérzékelő. |
| ![Nagy súly ikon](./media/storsimple-hardware-component-replacement/Weight.png) |**Nagy súly** | |
| ![Nincs felhasználó tarthatók karban részek ikon](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png) |**Nincsenek felhasználói tarthatók karban részei** |Nem érhető el, ha megfelelően be van tanítva. |
| ![Olvasható utasítások ikon](./media/storsimple-hardware-component-replacement/ReadInstructions.png) |**Először olvassa el az összes utasításokat** | |
| ![Tipp veszély ikon](./media/storsimple-hardware-component-replacement/TipHazard.png) |**Veszély tipp** | |

### <a name="before-you-begin"></a>Előkészületek
Ismerje meg az eszköz és a biztonsági ikonok a jelen oktatóanyagban használt biztonsági információ. Lépjen a [biztonságosan telepítéséhez, és a StorSimple-eszköz üzemeltetéséhez](storsimple-safety.md) teljes körű információkat. Ne feledje el áttekinteni a [biztonsági óvintézkedések](storsimple-safety.md#handling-precautions) előtt, a StorSimple-eszköz kezeléséhez.

Mielőtt megkísérli cserélje le egy összetevő, vegye figyelembe a következőket.

![Figyelmeztetés ikon](./media/storsimple-hardware-component-replacement/Warning.png) ![elektromos Megváltani ikon](./media/storsimple-hardware-component-replacement/Electric.png) **figyelmeztetés!**

* Szabad saját kezűleg megfelelően kisülés vagy antisztatikus mat használatával modulok és a StorSimple eszköz összetevők kezelése során.
* Bármely áramkört nem használja ezen. Használja a megadott kezeli és útmutatók összetevőket, előfordulhat, hogy közzétette a áramkört kezelése során.

![Figyelmeztetés ikon](./media/storsimple-hardware-component-replacement/Warning.png) ![figyelje meg, hogy ikon](./media/storsimple-hardware-component-replacement/NoticeIcon.png) **értesítés:**

Ha egy modul cserél **SOSEM hagyják el az a ház hátsó egy üres bay**. Helyett vagy annak üres modul beszerzése előtt a probléma eltávolításával.

## <a name="hardware-component-replacement-procedures"></a>Hardver összetevő helyettesítő eljárások
A StorSimple 8000 sorozatú eszköz áll a több beépülő modult a modulok az elsődleges és/vagy EBOD ház. A 8100-as rendelkezik egy önálló elsődleges ház, mivel a 8600-as elsődleges ház és a egy EBOD ház kettős ház eszköz.

Az eszköz fő hardverösszetevők listáját az alábbi táblázat foglalja össze. Kattintson a hivatkozásra a **helyettesítő eljárás** oszlop nyissa meg a kapcsolódó oktatóanyagot.

| Összetevők | # Jelen van | Beépülő modul? | Csere eljárás |
|:--- |:--- |:--- |:--- |
| Váz |1 |Nem |[A StorSimple eszközön a ház cseréje](storsimple-8000-chassis-replacement.md) |
| Elsődleges vezérlő |2 |Igen |[Cserélje le a StorSimple eszköz vezérlő modul](storsimple-8000-controller-replacement.md) |
| 764W tápellátáshoz és modulok (PCMs) |2 |Igen |[Az energiaellátási és hűtési rendszer cseréje a StorSimple-eszközön](storsimple-8000-power-cooling-module-replacement.md) |
| A biztonsági mentési akkumulátor |2 |Igen |[A tartalék akkumulátor modul cseréje a StorSimple-eszközön](storsimple-8000-battery-replacement.md) |
| Lemezmeghajtók |12 |Igen |[A StorSimple eszközön a lemezmeghajtó cseréje](storsimple-8000-disk-drive-replacement.md) |

**1. táblázat** az elsődleges ház a hardverösszetevő

Az elsődleges ház és a EBOD ház eltérőek az i/o-modulokkal. Ezenkívül a PCMs kell különböző teljesítményt. Az elsődleges helyiségben PCMs 764 W, míg a EBOD ház lévőket 580 l. Az elsődleges helyiségben PCMs egy biztonsági mentési akkumulátor modult is tartalmaznak.

| Összetevők | # Jelen van | Beépülő modul? | Csere eljárás |
|:--- |:--- |:--- |:--- |
| Váz |1 |Nem |[A StorSimple eszközön a ház cseréje](storsimple-8000-chassis-replacement.md) |
| Az EBOD-vezérlő |2 |Igen |[A StorSimple eszköz az EBOD-vezérlő cseréje](storsimple-8000-ebod-controller-replacement.md) |
| 580W tápellátáshoz és modulok (PCMs) |2 |Igen |[Az energiaellátási és hűtési rendszer cseréje a StorSimple-eszközön](storsimple-8000-power-cooling-module-replacement.md) |
| Lemezmeghajtók |12 |Igen |[A StorSimple eszközön a lemezmeghajtó cseréje](storsimple-8000-disk-drive-replacement.md) |

**2. táblázat** a EBOD ház a hardverösszetevő

Az eszközön a beépülő modulok ki vannak emelve az alábbi első és hátsó diagramokat. Ezek a diagramok segítségével a különféle beépülő modulok helyének meghatározásához, ha egy helyettesítő megadása kötelező. Az első ábra bemutatja a merevlemez-meghajtók, valamint a hátsó az a EBOD ház és az elsődleges ház megjelenítése a beépülő modulok.

![Lemezmeghajtók eszköz előlapi panel](./media/storsimple-hardware-component-replacement/IC741028.png)

**1. ábra** az eszköz első

| Címke | Leírás |
|:--- |:--- |
| 0 - 11 |Lemezmeghajtók (12 összesen) |

Az elsődleges ház és a EBOD ház is rendelkezik a meghajtó szolgáltatója modulok. A váz Kezelőkód tizenkét 3.5-ös "lemezmeghajtók 3 x 4 formátum szerint rendezve.

![Az eszköz elsődleges ház modulok csatlakozópanel meghibásodása](./media/storsimple-hardware-component-replacement/IC740994.png)

**2. ábra** oldalán az elsődleges ház

| Címke | Leírás |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |0. vezérlő |
| 4 |1. vezérlő |

![Az eszköz EBOD ház beépülő modulok csatlakozópanel meghibásodása](./media/storsimple-hardware-component-replacement/IC769599.png)

**3. ábra** oldalán a EBOD ház

| Címke | Leírás |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |Az EBOD-vezérlő 0 |
| 4 |Az EBOD-vezérlő 1 |

## <a name="field-replaceable-units"></a>A mező telepen cserélhető egység
A StorSimple-eszköz a következő mező telepen cserélhető egység (részegysége) érhetők el:

* Váz (beleértve az integrált műveletek panel)
* 764 W AC PCM
* 580 W AC PCM
* Merevlemez-meghajtó meghajtó szolgáltatója modullal
* Vezérlő modul
* Az EBOD-vezérlő modul
* A biztonsági mentési akkumulátor modul
* Állvány sín kit csatlakoztatása

Adjon [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) sorrendjének ezen helyettesítő egységek bármelyikét.

## <a name="next-steps"></a>További lépések
Tekintse át az összes [biztonsági információ](storsimple-safety.md) StorSimple hardverkomponensek cseréje előtt.

