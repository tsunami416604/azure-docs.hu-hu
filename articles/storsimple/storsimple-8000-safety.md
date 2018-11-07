---
title: A StorSimple eszköz biztonsági |} A Microsoft Docs
description: Biztonsági egyezmények, irányelvek és szempontokat, és biztonságosan telepítéséhez, és a StorSimple-eszköz üzemeltetéséhez ismerteti.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2017
ms.author: alkohli
ms.openlocfilehash: 428bc3504416f3a99518572f9f5bcb39423295b2
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51261599"
---
# <a name="safely-install-and-operate-your-storsimple-device"></a>Biztonságosan telepítése és üzemeltetése a StorSimple-eszköz
![Figyelmeztetés ikon](./media/storsimple-safety/IC740879.png)
![olvassa el a biztonsági figyelmeztetés ikon](./media/storsimple-safety/IC740885.png) **OLVASÁSI biztonsági és egészségügyi információk**

Olvassa el az összes biztonsági és egészségügyi információt ebben a cikkben, amely a Microsoft Azure StorSimple-eszköz vonatkozik. A nyomtatott útmutatók a StorSimple-eszköz, a későbbiekben rendszerrel szállított tartsa. Hiba történt, kövesse az utasításokat, és megfelelően állítsa be a, használata és a termék növelheti az komoly kárt vagy haláleset, vagy sérülése, az eszköz vagy eszközök számára. A [Ez az útmutató letölthető verziója](https://www.microsoft.com/download/details.aspx?id=44233) is rendelkezésre áll.

## <a name="safety-icon-conventions"></a>Biztonsági ikon konvenciók
Az alábbiakban az ikonokat, amikor áttekinti a biztonsági óvintézkedéseket, figyelembe kell venni, amikor beállíthatja és futtathatja a Microsoft Azure StorSimple-eszköz található.

| Ikon | Leírás |
|:--- |:--- |
| ![Veszély ikon](./media/storsimple-safety/IC740879.png) **veszély!** |Azt jelzi, hogy a veszélyes helyzet eredményező, ha nem kerülni, halála vagy komoly kárt. Ez a jel szó, hogy a rendkívüli helyzetekre korlátozott. |
| ![Figyelmeztetés ikon](./media/storsimple-safety/IC740879.png) **figyelmeztetés!** |Azt jelzi, hogy nem elkerülhető, ha a haláleset vagy komoly kárt okozhat a veszélyes helyzet. |
| ![Figyelmeztetés ikon](./media/storsimple-safety/IC740879.png) **Vigyázat!** |Azt jelzi, hogy nem elkerülhető, ha a kisebb vagy közepes kárt okozhat a veszélyes helyzet. |
| ![Figyelje meg, hogy ikon](./media/storsimple-safety/IC740881.png) **értesítés:** |Azt jelzi, fontos, de nem veszély kapcsolatos információt. |
| ![Elektromos Megváltani ikon](./media/storsimple-safety/IC740882.png) **elektromos Megváltani veszély** |Magas feszültségérzékelő |
| ![Nagy súly ikon](./media/storsimple-safety/IC740883.png) **nagy súly** | |
| ![Felhasználó nem tarthatók karban részeit ikon](./media/storsimple-safety/IC740879.png) **nincsenek felhasználói tarthatók karban részei** |Nem érhető el, ha megfelelően be van tanítva. |
| ![Olvassa el a biztonsági figyelmeztetés ikon](./media/storsimple-safety/IC740885.png)**először olvassa el az összes utasításokat** | |
| ![Tipp veszély ikon](./media/storsimple-safety/IC740886.png) **veszély tipp** | |

## <a name="handling-precautions"></a>Óvintézkedéseket kezelése
![Figyelmeztetés ikon](./media/storsimple-safety/IC740879.png) ![nagy súly ikon](./media/storsimple-safety/IC740883.png) **figyelmeztetés!** 

A kár kockázatának csökkentése érdekében:

* Egy teljes körűen konfigurált ház is tenniük legfeljebb 32 kg (70 lbs); ne próbálja emelje fel saját magának.
* Mielőtt a ház, mindig győződjön meg arról, hogy a két személynek elérhetők súlyát kezelni. Vegye figyelembe, hogy több személy próbál helyezheti át a súly sérülések képes elviselni.
* Nem helyezheti át a ház fogópontja a teljesítmény- és hűtési modulok (PCMs) található a hátsó egység szerint. Ezek nem tervezték, a súly érvénybe.

## <a name="connection-precautions"></a>Kapcsolat óvintézkedéseket
![Figyelmeztetés ikon](./media/storsimple-safety/IC740879.png) ![elektromos Megváltani ikon](./media/storsimple-safety/IC740882.png) **figyelmeztetés!**

Csökkentése érdekében a sérülés, az elektromos megváltani, valamint a haláleset valószínűségét:

* AC több forrásból működteti, válassza le az összes ellátási power a teljes elkülönítést.
* Véglegesen le kell választani a egység mozgatása előtt, vagy ha úgy véli, hogy bármilyen módon megsérült.
* Adja meg a tápkábelek ellátási elektromos earth biztonságos kapcsolatot. Győződjön meg arról, hogy a ház, a résidőalapban kielégíti nemzeti és helyi power alkalmazása előtt.
* Győződjön meg arról, hogy a power kapcsolat mindig le van választva, egy, a ház PCM eltávolítása előtt.
* Tekintve, hogy az ellátás tápkábelét a plug fő válassza le az eszközt, győződjön meg arról, hogy a szoftvercsatorna-kimeneteket a készülék közelében találhatók, és könnyen hozzáférhető.

![Figyelmeztetés ikon](./media/storsimple-safety/IC740879.png) ![elektromos Megváltani ikon](./media/storsimple-safety/IC740882.png) **figyelmeztetés!**

Csökkentheti annak lehetőségét, hogy életbe, vagy a elektromos kapcsolatok aktiválódik:

* Adjon meg egy megfelelő áramforráshoz az elektromos túlterhelési protection részletes műszaki előírásának követelményeinek.
* Ne használjon bifurcated tápkábelek ("Y" érdeklődők).
* Alkalmazandó biztonsági kibocsátási és hőmérsékleti követelmények ahhoz, hogy nem fedezik el kell távolítani, és minden üzemanyagtartály kell lennie töltve beépülő modulok, vagy a meghajtó üres.
* Győződjön meg arról, hogy a berendezés használatban van-e a gyártó által meghatározott módon. Ha ez a berendezés nem gyártója által meghatározott módon használja, a berendezés által biztosított védelem korlátozott lehet.

![Figyelje meg, hogy ikon](./media/storsimple-safety/IC740881.png) **értesítés:**

A megfelelő működéséhez a berendezés és a termék sérülés elkerülése érdekében:

* A RJ45 hátulján az eszközön található portjait egy Ethernet-kapcsolat. Ezek nem kapcsolódnia kell egy távközlési hálózatot.
* Mindenképpen telepítse az eszközt, amely lehetővé teszi egy elölről-hátra hűtési tervezési állványra.
* Minden beépülő modulok és az üres lemezek a rendszerház részét képezik. Ezek csak el kell távolítani a helyettesítő azonnal hozzáadott. A rendszer nem minden modulok vagy üres értékeket eredményez a hely nélkül fog futni.

## <a name="rack-system-precautions"></a>Állvány rendszer óvintézkedéseket
A következő biztonsági követelmények figyelembe kell venni, ha az eszköz egy állványra kabinet csatlakoztatja.

![Figyelmeztetés ikon](./media/storsimple-safety/IC740879.png) ![tipp veszély ikon](./media/storsimple-safety/IC740886.png) **figyelmeztetés!**

Az elágazás tipp: a kár csökkentése keresztül:

* Az állvány tervezési támogatnia kell a telepített házak teljes súlya, és tartalmazniuk kell a megfelelő megakadályozza, hogy az állványra szerelt lerakása vagy telepítését vagy normál használat során keresztüli adatküldés stabilizátort funkciókat.
* Állvány betöltésekor az állványra szerelt alulról felfelé kitölti, és felülről lefelé üres.
* Az állványra szerelt toppling veszélye elkerülése érdekében egyszerre egynél több ház az állványra szerelt kívül nem megnyitáskor.

![Figyelmeztetés ikon](./media/storsimple-safety/IC740879.png) ![elektromos Megváltani ikon](./media/storsimple-safety/IC740882.png) **figyelmeztetés!**

Csökkentése érdekében a sérülés, az elektromos megváltani, valamint a haláleset valószínűségét:

* Az állványra szerelt rendelkeznie kell egy biztonságos elektromos terjesztési rendszer. Ez meg kell adnia a ház túlzott jelenlegi védelmét, és nem kell túlterhelt ház telepítve van a teljes számával. Az elektromos áram használatalapú minősítés látható az adattábla be kell tartani.
* Az elektromos terjesztési rendszer összes rendszerházon, az állvány egyik kiszolgálója egy megbízható alapoktól kell megadnia.
* Az elektromos terjesztési rendszer kialakításától figyelembe kell venni a teljes kiszivárgását az összes házban összes tápegységek aktuális. Ne feledje, hogy az összes rendszerházon egyes tápegység földön kiszivárgását meglévő 1.0-ás mA maximális, 60 Hz 264 v. Az állványra szerelt szükség lehet a címkézés "magas KISZIVÁRGÁSÁT aktuális. Földön (earth) kapcsolat kulcskezelésnek fontos szerepe a forrás-kapcsolat létrejötte előtt."
* Az állványra szerelt, a házak konfigurálásakor meg kell felelnie a UL 60950-1 és 60950 – 1/EN IEC 60950-1 biztonsági követelményeinek.

![Figyelje meg, hogy ikon](./media/storsimple-safety/IC740881.png) **értesítés:**

A megfelelő hűtési a rack rendszer:

* Győződjön meg arról, hogy az állvány tervezési figyelembe veszi a maximális ház működési 35 Celsius-fokban (95 fok Fahrenheit) környezeti hőmérséklet.
* A rendszer a alacsony nyomású, hátsó-kipufogógáz telepítést (nyomása rack ajtók és az akadályok legfeljebb 5 Pascal [0,5 mm víz mérőműszer] által létrehozott) szolgáltatást.

## <a name="power-cooling-module-pcm-precautions"></a>Energiaellátási modul lehűlés (PCM) óvintézkedéseket
Az eszköz két PCMs való működésre tervezték. A PCMs mindegyike rendelkezik egy tápegység és a egy kettős-tengely ventilátor. Egy kritikus fontosságú feltétel során a rendszer lehetővé teszi egy energiaellátás Folytatás a normál működés során hiba. Két PCMs (és ezáltal a készletek power) minden esetben telepíteni kell. Egyetlen PCM nem redundáns áramforrások. Ezért a hiba, akár egy PCM állásidő vagy adatvesztéshez vezethet.

![Figyelmeztetés ikon](./media/storsimple-safety/IC740879.png) ![elektromos Megváltani ikon](./media/storsimple-safety/IC740882.png) **figyelmeztetés!**

Csökkentése érdekében a sérülés, az elektromos megváltani, valamint a haláleset valószínűségét:

* A PCM ne távolítsa el a háttérben. Fennáll a veszélye annak, az elektromos megváltani belül. A PCM adja vissza, és szerezze be a helyettesítő [forduljon a Microsoft Support](storsimple-contact-microsoft-support.md).

![Figyelje meg, hogy ikon](./media/storsimple-safety/IC740881.png) **értesítés:**

A megfelelő működéséhez a berendezés és a termék sérülés elkerülése érdekében:

* 24 órán belül a hibás PCM le kell cserélnie. A PCM cseréje az eltávolítása után a csere eltávolítását követően 10 percen belül kell végezni.
* Ne távolítsa el a PCM, kivéve, ha helyett közvetlenül is telepíthető. A ház nem kell üzemeltetett összes moduljában lévő hely nélkül.

## <a name="electrostatic-discharge-esd-precautions"></a>Óvintézkedéseket kisülés (ESD)
![Figyelje meg, hogy ikon](./media/storsimple-safety/IC740881.png) **értesítés:**

Figyelje meg az alábbi ESD kapcsolatos figyelmeztetéseket.

* Győződjön meg arról, hogy telepítve van, és be van jelölve egy megfelelő antisztatikus wrist vagy bokagyűrűt elé.
* Figyelje meg minden hagyományos ESD óvintézkedéseket, modulok és az összetevők kezelése során.
* Ügyfél-csatlakozópanel meghibásodása összetevők és modul-összekötők elkerülése érdekében.
* ESD kárt jótállás nem jelez.

## <a name="battery-disposal-precautions"></a>Akkumulátor kivezetési óvintézkedéseket
Az áramellátás memória tartalmának védelme során ideiglenes, rövid távú áramkimaradások egy speciális akkumulátor használ. Az akkumulátor a PCM a megfelelőn van. A következő információkat az akkumulátor észben tartani.

![Figyelmeztetés ikon](./media/storsimple-safety/IC740879.png) **figyelmeztetés!**

A sort, fire, explosion, sérülés vagy halála kockázatának csökkentése érdekében:

* Tud megszabadulni használt akkumulátorok országos vagy területi szabályzatának megfelelően.
* Nem fejtheti, teljesítse, vagy a fent 60 Celsius-fokban (140 fok Fahrenheit) melegítsük vagy hamvasszuk. Cserélje le egy megadott akkumulátor a PCM telep. Egy másik akkumulátor használatát kockázatot jelentő fire vagy explosion.
* Használja az elemek a védelmi célú tájékoztatók lezáró, ha ezek el lesznek távolítva a tápegység.

![Figyelje meg, hogy ikon](./media/storsimple-safety/IC740881.png) **értesítés:**

Ha a szállítási címhez tartozó vagy az ellenkező esetben az elemeket a légi kövesse a IATA Lithium akkumulátor dokumentum érhető el [http://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx](http://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx)

Tekintse át ezeket a biztonsági hirdetményeket, a következő lépések, a, állványra szerelésére és bekábelezésére.

## <a name="next-steps"></a>További lépések
* Lépjen egy 8100-as eszköz [telepítse a StorSimple 8100 sorozatú eszköz](storsimple-8100-hardware-installation.md).
* A 8600-as eszközön keresse [telepítse a StorSimple 8600 sorozatú eszköz](storsimple-8600-hardware-installation.md).

