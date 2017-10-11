---
title: "A StorSimple eszköz biztonsági |} Microsoft Docs"
description: "Biztonsági egyezmények, irányelvek és szempontokat, és biztonságosan telepítéséhez, és a StorSimple eszköz üzemeltetéséhez ismerteti."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2017
ms.author: alkohli
ms.openlocfilehash: e45b09a62f33fd9811714febd63a74149784980d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="safely-install-and-operate-your-storsimple-device"></a>Biztonságosan telepítéséhez, és a StorSimple eszköz üzemeltetéséhez
![Figyelmeztetés ikon](./media/storsimple-safety/IC740879.png)
![olvassa el a biztonsági figyelmeztetés ikon](./media/storsimple-safety/IC740885.png) **OLVASÁSI biztonsági és egészségügyi adatokat**

Olvassa el az összes a biztonsági és állapotát a cikkben szereplő információkat, amelyek a Microsoft Azure StorSimple eszköz vonatkozik. A StorSimple eszköz későbbi használatra rendszerrel szállított nyomtatott útmutatók megtartása. Kövesse az utasításokat megfelelően állítsa be, használ, és ügyeljen arra, a termék komoly kárt, vagy a halál növelése, vagy sérülése, az eszközt vagy eszközöket nem sikerült. A [Ez az útmutató letölthető verziója](http://www.microsoft.com/download/details.aspx?id=44233) is rendelkezésre áll.

## <a name="safety-icon-conventions"></a>Biztonsági ikon konvenciók
Az alábbiakban megtalálja a beállíthatja és futtathatja a Microsoft Azure StorSimple eszköz betartandó biztonsági óvintézkedéseket megtekintésekor ikonok.

| Ikon | Leírás |
|:--- |:--- |
| ![Veszély ikon](./media/storsimple-safety/IC740879.png) **veszély!** |Azt jelzi, hogy egy eredményező, nem elkerülhető, ha halál vagy súlyosan veszélyes helyzetben. Ez a jel szó kell korlátozni, a rendkívüli helyzetekben. |
| ![Figyelmeztetés ikon](./media/storsimple-safety/IC740879.png) **figyelmeztetés!** |Azt jelzi, hogy egy veszélyes helyzetben, ha nem elkerülhető, halállal vagy komoly kárt okozhat. |
| ![Figyelmeztetés ikon](./media/storsimple-safety/IC740879.png) **figyelmeztetés!** |Azt jelzi, hogy nem elkerülhető, ha a kisebb vagy mérsékelt kárt okozhat veszélyes helyzet. |
| ![Figyelje meg ikon](./media/storsimple-safety/IC740881.png) **értesítés:** |Azt jelzi, fontos, de nem a veszély kapcsolatos információt. |
| ![Elektromos ütés ikon](./media/storsimple-safety/IC740882.png) **elektromos ütés veszély** |Magas feszültségérzékelő |
| ![Nagy súlyozást ikon](./media/storsimple-safety/IC740883.png) **nehéz súlyozás** | |
| ![Nem használható felhasználó részből ikon](./media/storsimple-safety/IC740879.png) **nincsenek felhasználói használható részei** |Nem érhető el, ha megfelelően be van tanítva. |
| ![Olvassa el a biztonsági figyelmeztetés ikon](./media/storsimple-safety/IC740885.png)**először olvassa el az összes utasításokat** | |
| ![Tipp ikon veszély](./media/storsimple-safety/IC740886.png) **veszély tipp** | |

## <a name="handling-precautions"></a>Óvintézkedéseket kezelése
![Figyelmeztetés ikon](./media/storsimple-safety/IC740879.png) ![nagy súlyozást ikon](./media/storsimple-safety/IC740883.png) **figyelmeztetés!** 

Ezzel csökkenthető a kockázata kár:

* A teljesen konfigurált ház is mérjük legfeljebb 32 kg (70 lbs); ne próbálja meg önállóan emelje fel.
* Mielőtt továbblép a ház, mindig győződjön meg arról, hogy a két személynek elérhetők a súlyozás kezelésére. Vegye figyelembe, hogy egy személy próbál a súlyozás növekedési sérülések képes elviselni.
* Nem a ház növekedési által a leírókon energia- és hűtési modulok (PCMs) a hátsó egység található. A rendszer nem úgy tervezték, hogy a súlyozás.

## <a name="connection-precautions"></a>Kapcsolat óvintézkedéseket
![Figyelmeztetés ikon](./media/storsimple-safety/IC740879.png) ![elektromos ütés ikon](./media/storsimple-safety/IC740882.png) **figyelmeztetés!**

Csökkenteni szeretné annak valószínűségét sérülés, elektromos ütés vagy halál:

* Több AC forrás technológiával, válassza le az összes ellátási töltöttsége a teljes elkülönítési.
* Véglegesen le kell választani a egység mozgatása előtt, vagy ha úgy gondolja, hogy az bármely módon megsérült.
* Adja meg az ellátási tápkábelek biztonságos elektromos earth kapcsolatot. Győződjön meg arról, hogy a ház a résidőalapban megfelel a nemzeti és helyi power alkalmazása előtt.
* Győződjön meg arról, hogy a power kapcsolat mindig le van választva egy PCM ki eltávolítása előtt.
* Tekintve, hogy a forrásokkal tápkábelét plug az elsődleges válassza le az eszközön, győződjön meg arról, hogy a szoftvercsatorna-kimeneteket a készülék közel található, és könnyen hozzáférhető.

![Figyelmeztetés ikon](./media/storsimple-safety/IC740879.png) ![elektromos ütés ikon](./media/storsimple-safety/IC740882.png) **figyelmeztetés!**

Csökkentse a túlmelegedése valószínűségét, vagy az elektromos kapcsolatok tűznek:

* Adjon meg egy megfelelő áramforrásról elektromos túlterhelési védelemmel a műszaki leírás szakaszában részletezett követelmények teljesítéséhez.
* Ne használjon bifurcated tápkábelek ("Y" érdeklődők).
* Alkalmazandó biztonsági kibocsátási és hőmérséklet követelmények ahhoz, hogy el kell távolítani nem vonatkozik, és minden öblök ki kell tölteni a beépülő modulok, vagy a meghajtó üres.
* Győződjön meg arról, hogy a készülék a gyártó által meghatározott módon szolgál. Ha a készülék nem a gyártó által meghatározott módon szerepel, a készülék által biztosított védelmet korlátozott lehet.

![Figyelje meg ikon](./media/storsimple-safety/IC740881.png) **értesítés:**

A megfelelő működéséhez a készülék, és a termék kárt:

* A RJ45 hátulján az eszközön található portjait az Ethernet-kapcsolat. Ezeket nem kell kapcsolódnia kell a távközlő hálózathoz.
* Ne telepítse az eszközt, amely lehetővé teszi az első biztonsági hűtési kialakítást szekrényben.
* A minden beépülő modulok és üres lemez a rendszerház részét képezik. Ezek csak el kell távolítani a helyettesítő azonnal hozzáadásakor. A rendszer nem minden modulok vagy üres értékeket helyen nélkül fog futni.

## <a name="rack-system-precautions"></a>Állvány rendszer óvintézkedéseket
A következő biztonsági követelmények figyelembe veendő kabinet szekrényben az eszköz csatlakoztatásakor.

![Figyelmeztetés ikon](./media/storsimple-safety/IC740879.png) ![tipp veszély ikon](./media/storsimple-safety/IC740886.png) **figyelmeztetés!**

Csökkenteni szeretné annak valószínűségét tipp: a kár keresztül:

* Az állvány terv támogatnia kell a telepített házak teljes súlya, és megakadályozhatja, hogy a az állványra kiengedési, vagy éppen leküldött keresztül telepítését vagy normál használata során a megfelelő stabilizátort szolgáltatások kell tartalmaznia.
* Állvány betöltésekor az állványra aljáról felfelé kitölti és felülről lefelé üres.
* Egynél több ház kívül az állványra elkerülése érdekében az állványra toppling veszélye egyszerre nem csúsztassa be.

![Figyelmeztetés ikon](./media/storsimple-safety/IC740879.png) ![elektromos ütés ikon](./media/storsimple-safety/IC740882.png) **figyelmeztetés!**

Csökkenteni szeretné annak valószínűségét sérülés, elektromos ütés vagy halál:

* Az állványra biztonságos elektromos terjesztési rendszerrel kell rendelkeznie. Biztosítania kell a ház túlzott jelenlegi védelmet, és nem kell túlterhelt által telepített ház teljes száma. Az elektromos fogyasztás energiafogyasztást jelenik meg a adattábla kell figyelembe venni.
* Az elektromos terjesztési rendszer minden egyes ház, az állvány adjon meg egy megbízható ground.
* Az elektromos terjesztési rendszer tervezési figyelembe kell venni a teljes kiszivárgásának aktuális származó összes házban összes áramforrással rendelkezik. Vegye figyelembe, hogy minden egyes szolgáltatással tápegység 1.0-ás, mA maximális ground kiszivárgásának aktuális, 60 Hz 264 v. Az állványra előfordulhat, hogy az "magas KISZIVÁRGÁSÁNAK aktuális címkézés. Ground (föld) kapcsolat elengedhetetlen egy ellátási csatlakozás előtt."
* Az állványra, a ház konfigurálásakor meg kell felelnie a UL 60950-1 és 60950-1/EN IEC 60950-1 biztonsági követelményeinek.

![Figyelje meg ikon](./media/storsimple-safety/IC740881.png) **értesítés:**

A megfelelő hűtési a állvány rendszer:

* Győződjön meg arról, hogy az állvány terv figyelembe veszi a maximális ház működési környezeti hőmérséklet 35 fok Celsius (Fahrenheit 95 fokban megadva).
* A rendszer a alacsony nyomású, hátsó-kipufogógáz telepítést (hátsó nyomás állvány ajtók és legfeljebb 5 Pascal [0,5 mm vízjel mérőműszer] akadályok által létrehozott) működik.

## <a name="power-cooling-module-pcm-precautions"></a>Energiagazdálkodási hűtési modul (PCM) óvintézkedéseket
Az eszköz célja, hogy két PCMs kapnak. A PCMs mindegyikén egy tápegység és a kettős-tengely ventilátor. A kritikus állapot során a rendszer engedélyezi a Folytatás a normál működés során egy Energiaellátás hibája miatt. Két PCMs (és ezáltal a készletek kapcsolja) mindig kell telepíteni. Egyetlen PCM nem biztosít redundáns power. Ezért akár egy PCM hibája állásidő vagy adatvesztéshez vezethet.

![Figyelmeztetés ikon](./media/storsimple-safety/IC740879.png) ![elektromos ütés ikon](./media/storsimple-safety/IC740882.png) **figyelmeztetés!**

Csökkenteni szeretné annak valószínűségét sérülés, elektromos ütés vagy halál:

* Ne távolítsa el a magában foglalja a PCM. Nincs elektromos ütés belül veszélye. Térjen vissza a PCM, és megszerezhetik az helyettesíti, [forduljon a Microsoft Support](storsimple-contact-microsoft-support.md).

![Figyelje meg ikon](./media/storsimple-safety/IC740881.png) **értesítés:**

A megfelelő működéséhez a készülék, és a termék kárt:

* A sikertelen PCM 24 órán belül le kell cserélnie. Egy PCM cserélni eltávolítása után a csere eltávolítását követően 10 percen belül kell végezni.
* Ne távolítsa el a PCM, kivéve, ha egy helyettesítő közvetlenül is telepíthető. A ház kell nem lehet azon műveleteket végezni a hely összes modul nélkül.

## <a name="electrostatic-discharge-esd-precautions"></a>Kisülés (ESD) óvintézkedéseket
![Figyelje meg ikon](./media/storsimple-safety/IC740881.png) **értesítés:**

Vegye figyelembe az alábbi ESD kapcsolatos figyelmeztetéseket.

* Győződjön meg arról, hogy telepítette és be van jelölve egy megfelelő antisztatikus körkörös vagy bokája elé.
* Figyelje meg az összes hagyományos ESD óvintézkedéseket, modulok és az összetevők kezelése során.
* Ne lépjen kapcsolatba a csatlakozópanel összetevőkkel és modul összekötők.
* ESD kárt jótállás nem vonatkozik.

## <a name="battery-disposal-precautions"></a>Akkumulátor kivezetési óvintézkedéseket
A tápegység egy különös akkumulátor az ideiglenes, rövid távú áramkimaradások során memória tartalmát védelmét. Az akkumulátor a PCM a foglal helyet. A következőket vegye figyelembe az akkumulátor kapcsolatban.

![Figyelmeztetés ikon](./media/storsimple-safety/IC740879.png) **figyelmeztetés!**

Ezzel csökkenthető a kockázata, short, tűz, robbanásszerűen növekszik, sérülés vagy halál:

* Eldobásakor használt akkumulátorok nemzeti/regionális szabályzatának megfelelően.
* Nem visszafejtése, őröljük, vagy 60 fok Celsius (Fahrenheit 140 fokban megadva) feletti melegítsük vagy hamvasszuk. Cserélje le a PCM töltöttség esetén csak a megadott elem. Használjon egy másik akkumulátor kockázatot jelentő tűz vagy robbanásszerűen növekszik.
* Ha ezek el lesznek távolítva a tápegység védelmi lezáró használatát az elemeket.

![Figyelje meg ikon](./media/storsimple-safety/IC740881.png) **értesítés:**

Ha szállítási vagy az egyéb az akkumulátorok által vezeték nélkül regisztrálja, kövesse a IATA lítium akkumulátor útmutató letölthető [http://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx](http://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx)

Miután átolvasta ezeket a biztonsági értesítéseket, a következő lépésekre kicsomagolása, állványra és bekábelezésére.

## <a name="next-steps"></a>Következő lépések
* 8100-eszköz esetén nyissa meg a [a StorSimple 8100 eszköz telepítése](storsimple-8100-hardware-installation.md).
* 8600-eszköz esetén nyissa meg a [a StorSimple 8600 eszköz telepítése](storsimple-8600-hardware-installation.md).

