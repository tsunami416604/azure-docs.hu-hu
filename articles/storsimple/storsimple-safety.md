---
title: Biztonság a StorSimple eszköz | Microsoft dokumentumok
description: Ez a témakör ismerteti a biztonsági konvenciókat, irányelveket és szempontokat, és ismerteti a StorSimple-eszköz biztonságos telepítését és üzemeltetését.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: dae6d535-1ca2-4d2b-b221-6819043aa068
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: alkohli
ms.openlocfilehash: 4622a8575d7b6a38226ee3a980c05f143c128356
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68963538"
---
# <a name="safely-install-and-operate-your-storsimple-device"></a>A StorSimple eszköz biztonságos telepítése és működtetése

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

![A](./media/storsimple-safety/IC740879.png)
![figyelmeztetésikon](./media/storsimple-safety/IC740885.png) Olvassa el a biztonsági közleményt ikonolvassa **el a biztonsági és egészségügyi információkat**

Olvassa el a cikkben található, a Microsoft Azure StorSimple eszközre vonatkozó összes biztonsági és egészségvédelmi információt. Őrizze meg a StorSimple készülékhez mellékelt összes nyomtatott útmutatót későbbi használatra. Az utasítások be nem tartása és a termék megfelelő beállítása, használata és gondozása növelheti a súlyos sérülés vagy halál, illetve a készülék vagy eszközök károsodásának kockázatát. Az [útmutató letölthető változata](https://www.microsoft.com/download/details.aspx?id=44233) is elérhető.

## <a name="safety-icon-conventions"></a>Biztonsági ikon konvenciók
Az ikonokat a Microsoft Azure StorSimple eszköz beállításakor és futtatásakor betartandó biztonsági óvintézkedések áttekintése során találja.

| Ikon | Leírás |
|:--- |:--- |
| ![Veszély](./media/storsimple-safety/IC740879.png) Icon **veszély!** |Olyan veszélyes helyzetet jelöl, amely, ha nem kerülik el, halálhoz vagy súlyos sérüléshez vezet. Ezt a jelszót a legszélsőségesebb helyzetekre kell korlátozni. |
| ![Figyelmeztető](./media/storsimple-safety/IC740879.png) ikon **figyelmeztetés!** |Olyan veszélyes helyzetet jelöl, amely, ha nem kerülik el, halálhoz vagy súlyos sérüléshez vezethet. |
| ![Figyelem](./media/storsimple-safety/IC740879.png) ikon **VIGYÁZAT!** |Olyan veszélyes helyzetet jelöl, amely, ha nem kerülik el, kisebb vagy közepes sérülést okozhat. |
| ![](./media/storsimple-safety/IC740881.png) **Értesítésikon:** |Fontosnak tekintett, de a veszélyhez kapcsolódó információkat jelöl. |
| ![Elektromos áramütés](./media/storsimple-safety/IC740882.png) icon **áramütés veszély** |Nagyfeszültségű |
| ![Nehéz súlyú](./media/storsimple-safety/IC740883.png) ikon **nehéz súly** | |
| ![Nincs felhasználó által](./media/storsimple-safety/IC740879.png) javítható alkatrészikon: **Nincs felhasználó által javítható alkatrész** |Ne férjen hozzá, kivéve, ha megfelelően van betanítva. |
| ![A Biztonsági nyilatkozat ikon olvasása](./media/storsimple-safety/IC740885.png)**először olvassa el az összes utasítást** | |
| ![Tipp veszély](./media/storsimple-safety/IC740886.png) ikon **tipp veszély** | |

## <a name="handling-precautions"></a>Kezelési óvintézkedések
![Figyelem](./media/storsimple-safety/IC740879.png) ![Icon Nehéz](./media/storsimple-safety/IC740883.png) súly Ikon **FIGYELEM!** 

A sérülés kockázatának csökkentése érdekében:

* Egy teljesen konfigurált ház súlya akár 32 kg (70 lbs); ne próbálja meg egyedül felemelni.
* A burkolat mozgatása előtt mindig győződjön meg arról, hogy két ember áll rendelkezésre a súly kezelésére. Ne feledje, hogy egy személy megpróbálja felemelni ezt a súlyt képes elviselni sérüléseket.
* Ne emelje fel a burkolatot a készülék hátulján található táp- és hűtőmodulok (PCM- ek) fogantyúinál fogva. Ezek nem célja, hogy a tömeg.

## <a name="connection-precautions"></a>Csatlakozási óvintézkedések
![Figyelmeztetés](./media/storsimple-safety/IC740879.png) ![Icon Elektromos](./media/storsimple-safety/IC740882.png) Sokk Icon **FIGYELMEZTETÉS!**

A sérülés, áramütés vagy halál valószínűségének csökkentése érdekében:

* Ha több hálózati áramforrással működik, a teljes elkülönítéshez húzza ki az összes tápellátást.
* Véglegesen húzza ki a készüléket, mielőtt elmozdítaná, vagy ha úgy gondolja, hogy bármilyen módon megsérült.
* Biztosítson biztonságos elektromos földelési csatlakozást a tápkábelhez. A teljesítmény alkalmazása előtt ellenőrizze, hogy a burkolat földelése megfelel-e a nemzeti és helyi követelményeknek.
* A PCM házból való eltávolítása előtt mindig bontsa a hálózati kapcsolatot.
* Mivel a hálózati kábel dugója a fő leválasztó eszköz, győződjön meg arról, hogy a konnektorok a berendezés közelében találhatók, és könnyen hozzáférhetők.

![Figyelmeztetés](./media/storsimple-safety/IC740879.png) ![Icon Elektromos](./media/storsimple-safety/IC740882.png) Sokk Icon **FIGYELMEZTETÉS!**

Az elektromos csatlakozások túlmelegedésének vagy tűzének valószínűségének csökkentése érdekében:

* Biztosítson megfelelő elektromos túlterhelés elleni védelmet biztosító áramforrást, amely megfelel a műszaki leírásban részletezett követelményeknek.
* Ne használjon elágazó tápkábeleket ("Y" vezetékek).
* A vonatkozó biztonsági, kibocsátási és hőkövetelményeknek való megfelelés érdekében nem szabad fedeleket eltávolítani, és minden térközt plug-in modullal vagy meghajtóüres blankkal kell feltölteni.
* Győződjön meg arról, hogy a berendezést a gyártó által meghatározott módon használják. Ha ezt a berendezést a gyártó által nem meghatározott módon használják, a berendezés által nyújtott védelem sérülhet.

![](./media/storsimple-safety/IC740881.png) **Értesítésikon:**

A berendezés megfelelő működéséhez és a termék károsodásának megelőzéséhez:

* Az eszköz hátulján található RJ45 portok csak Ethernet-kapcsolatra szolgálnak. Ezeket nem szabad távközlési hálózathoz csatlakoztatni.
* Ügyeljen arra, hogy a készüléket olyan állványba telepítse, amely elfér egy előre-hátra hűtési tervvel.
* Minden plug-in modul és üres lemez a rendszerház része. Ezeket csak akkor szabad eltávolítani, ha a csere azonnal hozzáadható. A rendszert nem szabad úgy futtatni, hogy ne legyen minden modul vagy üres hely.

## <a name="rack-system-precautions"></a>Rack rendszer óvintézkedések
A következő biztonsági követelményeket kell figyelembe venni, ha a készüléket rackszekrénybe szereli.

![Figyelmeztető](./media/storsimple-safety/IC740879.png) ![ikon](./media/storsimple-safety/IC740886.png) Tipp Hazard Icon **FIGYELMEZTETÉS!**

A sérülés valószínűségének csökkentése a felborulásból:

* A rack kialakításának támogatnia kell a beszerelt burkolatok teljes súlyát, és olyan stabilizáló funkciókat kell tartalmaznia, amelyek alkalmasak arra, hogy megakadályozzák a rack felborulását vagy átlökését a telepítés vagy a normál használat során.
* Rack betöltésekor töltse fel az állványt alulról felfelé és üresen felülről lefelé.
* Ne csúsztasson egyszerre több burkolatot az állványról, hogy elkerülje az állvány felborulásának veszélyét.

![Figyelmeztetés](./media/storsimple-safety/IC740879.png) ![Icon Elektromos](./media/storsimple-safety/IC740882.png) Sokk Icon **FIGYELMEZTETÉS!**

A sérülés, áramütés vagy halál valószínűségének csökkentése érdekében:

* A rack kell egy biztonságos elektromos elosztó rendszer. Túláram elleni védelmet kell biztosítania a burkolat számára, és nem terhelhető túl a beszerelt burkolatok teljes számával. A névtáblán feltüntetett energiafogyasztási besorolást be kell tartani.
* Az elektromos elosztórendszernek megbízható talajt kell biztosítania a rack minden egyes burkolatához.
* Az elektromos elosztórendszer kialakításának figyelembe kell vennie az összes ház összes tápegységéből származó teljes talajszivárgási áramot. Ne feledje, hogy minden egyes ház minden tápegysége legalább 1,0 mA talajszivárgási árammal rendelkezik 60 Hz- en, 264 Volton. Előfordulhat, hogy a racket "MAGAS SZIVÁRGÁSI ÁRAM" felirattal kell címkézni. A földelés (föld) kapcsolat elengedhetetlen a tápellátás csatlakoztatása előtt."
* A házzal konfigurált állványnak meg kell felelnie az UL 60950-1 és az IEC 60950-1/EN 60950-1 biztonsági követelményeinek.

![](./media/storsimple-safety/IC740881.png) **Értesítésikon:**

Az állványrendszer megfelelő hűtéséhez:

* Ügyeljen arra, hogy a rack kialakítása figyelembe vegye a ház maximális működési környezeti hőmérsékletét 35 Celsius fok (95 Fahrenheit fok).
* A rendszert alacsony nyomású, hátsó kipufogóberendezéssel működtetik (az állványajtók által okozott ellennyomás és az 5 Pascal [0,5 mm-es vízmérőt] meg nem haladó akadályok által okozott ellennyomás).

## <a name="power-cooling-module-pcm-precautions"></a>Teljesítményhűtési modul (PCM) óvintézkedések
A készüléket úgy tervezték, hogy két PCM-mel működjön. Mindegyik PCM rendelkezik tápegységrel és kéttengelyes ventilátorsal. Kritikus állapotban a rendszer lehetővé teszi egy tápegység meghibásodását, miközben folytatja a normál működést. Mindig két PCM-et (és így tápegységet) kell telepíteni. Egyetlen PCM nem biztosít redundáns áramot. Ezért a hiba még egy PCM vezethet állásidő vagy esetleges adatvesztés.

![Figyelmeztetés](./media/storsimple-safety/IC740879.png) ![Icon Elektromos](./media/storsimple-safety/IC740882.png) Sokk Icon **FIGYELMEZTETÉS!**

A sérülés, áramütés vagy halál valószínűségének csökkentése érdekében:

* Ne távolítsa el a burkolatokat a PCM-ről. Áramütés veszélye áll fenn. A PCM visszaküldéséhez és a csere beszerzéséhez forduljon a [Microsoft támogatási szolgálatához.](storsimple-contact-microsoft-support.md)

![](./media/storsimple-safety/IC740881.png) **Értesítésikon:**

A berendezés megfelelő működéséhez és a termék károsodásának megelőzéséhez:

* A meghibásodott PCM-et 24 órán belül ki kell cserélnie. A PCM cseréje után a cserét az eltávolítást követő 10 percen belül be kell fejezni.
* Ne távolítsa el a PCM-et, kivéve, ha a csere azonnal beszerelhető. A burkolatot nem szabad az összes modul nélküli működtetésnélkül üzemeltetni.

## <a name="electrostatic-discharge-esd-precautions"></a>Elektrosztatikus kisülés (ESD) óvintézkedések
![](./media/storsimple-safety/IC740881.png) **Értesítésikon:**

Tartsa be az alábbi, ESD-vel kapcsolatos óvintézkedéseket.

* Győződjön meg arról, hogy megfelelő antisztatikus csukló- vagy bokapántot szerelt fel és ellenőrzött.
* A modulok és alkatrészek kezelésekor tartsa be az összes hagyományos ESD-óvintézkedést.
* Kerülje a hátlap-összetevőkkel és a modulcsatlakozókkal való érintkezést.
* Az ESD-károkra nem vonatkozik a garancia.

## <a name="battery-disposal-precautions"></a>Az akkumulátor ártalmatlanítására vonatkozó óvintézkedések
A tápegység egy speciális akkumulátort használ, hogy megvédje a memória tartalmát az ideiglenes, rövid távú áramkimaradások során. Az akkumulátor a PCM-ben található. Tartsa szem előtt az akkumulátorral kapcsolatos alábbi információkat.

![Figyelmeztető](./media/storsimple-safety/IC740879.png) ikon **figyelmeztetés!**

A rövidnadrág, tűz, robbanás, sérülés vagy halál kockázatának csökkentése érdekében:

* A használt elemeket a nemzeti/regionális előírásoknak megfelelően kell megsemmisíteni.
* Ne szerelje szét, ne törje össze vagy ne melegítse 60 Celsius fok (140 Fahrenheit fok) felett, és ne égesse el. Csak a mellékelt akkumulátorra cserélje ki a PCM akkumulátort. Más akkumulátor használata tüzet vagy robbanást okozhat.
* Használjon védővégsapkát az elemeken, ha ezeket eltávolítja a tápegységből.

![](./media/storsimple-safety/IC740881.png) **Értesítésikon:**

Az akkumulátorok légi szállítása vagy más módon történő szállítása esetén kövesse az IATA lítium akkumulátorra vonatkozó útmutatóját, amely a[https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx](https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx)

Miután áttekintette ezeket a biztonsági közleményeket, a következő lépés a készülék kicsomagolása, állványa és kábelezése.

## <a name="next-steps"></a>További lépések
* 8100-as eszköz esetén válassza [a StorSimple 8100 eszköz telepítése című témakört.](storsimple-8100-hardware-installation.md)
* 8600-as eszköz esetén válassza [a StorSimple 8600 eszköz telepítése című témakört.](storsimple-8600-hardware-installation.md)

