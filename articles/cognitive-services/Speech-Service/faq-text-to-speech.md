---
title: Gyakran ismételt kérdések a beszédfelismerés szöveg szolgáltatás az Azure-on |} Microsoft Docs
description: Az alábbiakban a beszédfelismerés szöveg legnépszerűbb kérdésekre adott válaszok.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: fa25f16d3ef7ee8c7ec4c0c9ee17ccc89e8c52a2
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321854"
---
# <a name="custom-speech-service-frequently-asked-questions"></a>Egyéni beszéd szolgáltatás gyakran ismételt kérdések

Ha ez a GYIK a kérdésekre adott válaszok nem található, próbálja meg az egyéni beszéd Service Közösség kérése a [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) és [UserVoice](https://cognitive.uservoice.com/)

## <a name="general"></a>Általános kérdések

**Kérdés**: szöveg modellekhez alapterv és egyéni beszéd átalakítás közötti különbségek?

**Válasz**: eredeti modellek rendelkezik betanítva a Microsoft tulajdonában lévő adatokat, és már található, telepítve van a felhőben. Egyéni modellek engedélyezése a felhasználó számára egy modell jobban megfelelően egy adott környezetben meghatározott zajszint vagy nyelvi alkalmazkodáshoz. Gyári padlók, autók, zajos utca igényelnének igazítani akusztikus modell, mivel például biológia, a fizikai, a radiológia, a termékek neveit és egyéni mozaikszavak meg adott témákat igényelnének nyelvi modell.

**Kérdés**: Hogyan kezdjek hozzá, ha a modell használni kívánt?

**Válasz**: először be kell szereznie egy [előfizetés kulcs](get-started.md). Ha a kívánt REST-hívások predeployed alapterv modellekkel, tekintse meg a [részletesen Itt](rest-apis.md). Ha szeretné használni a websocket elemek letöltése a [SDK](speech-sdk.md)

**Kérdés**: i. tegye mindig kell egy egyéni beszéd modell létrehozása?

**Válasz**: nem, ha az alkalmazás általános napi nyelvi egyéni szóhasználatának és, hogy esetleg ritka majd nélkül nem kell egy modell testreszabásához. Ezen felül olyan környezetben, ahol kevéssé vagy egyáltalán ne háttérzaj van, akkor nem használható testre kell szabnia vagy alkalmazás esetén. A portál lehetővé teszi, hogy a felhasználók alapterv és egyéni modellek telepítéséhez, és pontossága tesztek futtatásához őket. Felhasználók Ez a szolgáltatás segítségével mérheti egy alapkonfiguráció vs pontosságát egy egyéni modell.

**Kérdés**: hogyan fogja tudni, ha az adatok vagy a modell feldolgozása befejeződött?

**Válasz**: a modell vagy a tábla adatkészlet állapota jelenleg tudja csak szeretne.
Amikor a feldolgozás befejeződik, az állapota lesz "Kész".

**Kérdés**: hozható létre egynél több modell egyszerre?

**Válasz**: nincs korlátozva, hogy hány modellek a gyűjteményben, de minden oldalon egyszerre csak egy hozhatók létre.
Például egy nyelvi modell létrehozási folyamat nem indítható, ha jelenleg a nyelvi modell folyamat szakaszában.
Is, azonban rendelkezik egy akusztikus modell és a nyelvi modell feldolgozása egy időben. 

**Kérdés**: I is tudjuk, hogy végzett a hibát. Hogyan szakítsa meg az adatok importálását, illetve a modell létrehozása folyamatban? 

**Válasz**: jelenleg nem állítható vissza egy akusztikus vagy nyelvi kiigazítása folyamat.
Az importálás után törölheti importált adatok

**Kérdés**: a keresési & Diktálás modellek és a Conversational modellek közötti különbségek?

**Válasz**: nincsenek két talál akusztikus & nyelvi modellek az egyéni beszéd szolgáltatásban választhat.
keresési lekérdezések, vagy Diktálás # témakör. A Microsoft Conversational AM megfelelő conversational Style szóbeli beszéd ismer fel.
Az ilyen típusú beszéd általában irányul másik személynek, többek között a telefonos ügyfélszolgálatok vagy értekezletek.

**Kérdés**: frissíthetők a meglévő model (modell halmozott)?

**Válasz**: meglévő modellek nem lehet frissíteni. Megkerülő megoldásként az új egyesíthető a régi adatkészletet, és readapt.

A régi és új adatkészletek kapcsolóval együtt kell használni egy egyetlen .zip (ha akusztikus adatok) vagy egy .txt fájl nyelvi adatok esetén. Egyszer kiigazítása történik, az új frissített modell rendelkeznie kell deszerializálni telepített új végpont beszerzése

**Kérdés**: Mi történik, ha az alapértelmezett értéknél nagyobb feldolgozási kell, vagy hogy mit nyújt a portálon. 

**Válasz**: legfeljebb 20 egyidejű kérelmek lépésekben a modell. 

Lépjen kapcsolatba velünk, ha nagyobb méretezéshez van szüksége.

**Kérdés**: is szeretnék a modell letöltése és Futtatás helyben?

**Válasz**: modellek nem lehet letölti és helyben végrehajtott.

**Kérdés**: a rendszer naplózza a kérelem?

**Válasz**: megválaszthatja, hogy kikapcsolja a nyomkövetés, amikor nincsenek hang központi telepítés létrehozásakor vagy transcriptions bekerülnek a naplóba. Ellenkező esetben kérelmek általában jelentkezett be az Azure-ban biztonságos tárolására. Ha további adatvédelmi megfontolások, amelyek nem engedik az egyéni beszéd szolgáltatással a, forduljon a támogatási csatornáit egyikét.

## <a name="importing-data"></a>Adatok importálása

**Kérdés**: Mi az, hogy az adatkészlet méretének korlátot? Hogy miért? 

**Válasz**: az aktuális adatkészlet határa 2 GB-ot, HTTP feltöltésének fájl mérete a korlátozás miatt. 

**Kérdés**: is szeretnék a zip-saját szövegfájlok ahhoz, hogy nagyobb szöveges fájl feltöltése? 

**Válasz**: nem, jelenleg csak a tömörítetlen szövegfájlok használata engedélyezett.

**Kérdés**: A jelentés szerint a utterances sikertelen volt. Mi az a probléma?

**Válasz**: nem sikerült feltölteni egy fájlban utterences 100 %-os nincs probléma.
Ha az adatok akusztikus vagy nyelvi utterances többsége (például > 95 %-os) sikeresen importálva, az adatkészlet is használható. Ajánlott azonban, hogy megpróbálja megállapítani, hogy a utterances miért bukott, és hárítsa el a problémákat. Leggyakoribb problémák, például formázási hibákat, amelyek könnyen javítsa ki. 

## <a name="creating-am"></a>DE létrehozása

**Kérdés**: akusztikus adatok van szükségem?

**Válasz**: javasoljuk akusztikus adatok egy óra 30 perc

**Kérdés**: milyen adatokat kell gyűjteni?

**Válasz**: adatokat gyűjthet, amelyek a forgatókönyvet közel van, és a lehető nagybetűhasználattal.
Az adatgyűjtés meg kell felelnie a célalkalmazás és a felhasználók eszközt vagy eszközöket, környezetek és hangszórók típusú. Általában akkor kell amikből adatgyűjtést legszélesebb hangszórók lehető széles. 

**Kérdés**: hogyan kell gyűjteni az? 

**Válasz**: hozzon létre egy önálló adatok gyűjtemény alkalmazást, vagy bizonyos ki a forgalomban hangrögzítés programot.
Az alkalmazás, amely a hangadatok naplózza, és használja, amely egy verziója is létrehozhat. 

**Kérdés**: kell átírni kiigazítása adatok magam megírni? 

**Válasz**: az adatok kért kell lennie. Saját kezűleg átvezeti, vagy használjon szakmai írjanak elő szolgáltatást. Közösségi ezek használatát szakmai transcribers, míg mások egyes használja.

**Kérdés**: mennyi időt vesz igénybe, létrehozhat egy egyéni akusztikus modellt?

**Válasz**: A feldolgozási ideje létrehozni egy egyéni akusztikus modell tárgya ugyanaz, mint a akusztikus adatkészlet hosszát.
Igen, öt órás adatkészlet alapján létrehozott testreszabott akusztikus modell órát fog igénybe venni körülbelül öt folyamathoz. 

## <a name="offline-testing"></a>Offline tesztelés

**Kérdés**: végezheti meg, hogy egy egyéni nyelvi modell használatával saját egyéni akusztikus modell offline vizsgálat?

**Válasz**: Igen, egyszerűen jelölje ki a egyéni nyelvi modellt a legördülő lista az offline vizsgálat beállításakor

**Kérdés**: végezheti meg, hogy saját egyéni akusztikus modell segítségével egyéni nyelvi modell offline vizsgálat?

**Válasz**: Igen, egyszerűen jelölje ki a egyéni akusztikus modellt a legördülő menü az offline vizsgálat beállítása során.

**Kérdés**: Mi Word Hibaarány, és hogyan azt számított?

**Válasz**: Word hibaarány a beszédfelismerés értékelési metrikát. A hibák összesített számát, beleértve a beszúrások, törléseket és helyettesítések hivatkozás írjanak elő szavak száma osztva számítanak.

**Kérdés**: Hogyan állapítható meg az pontossága vizsgálati eredményeket, hogy helyes?

**Válasz**: az eredmények megjelenítése a modell, és a testreszabott összehasonlítása.
Meg kell célja, hogy a modell létrehozásához a testreszabási megfelelőbb járulhatnak

**Kérdés**: hogyan tegye I mérje fel, a WER alap modell, ezért láthatom, ha hiba történt a fokozása? 

**Válasz**: A kapcsolat nélküli vizsgálati eredmények megjelenítése az egyéni és javítása alapterv pontosságát pontosságát alapterv keresztül

## <a name="creating-lm"></a>Az LM létrehozása

**Kérdés**: mennyi szöveges adatot kell feltölteni?

**Válasz**: attól függ, hogyan különböző szóhasználatának és az alkalmazásban használt kifejezések a kiindulási nyelvi modelleket. Minden új szó akkor célszerű listázzák használata a lehető legtöbb példákat. A közös az alkalmazásban használt kifejezések többek között a mondatok nyelvi adatok akkor is hasznos, azt jelzi, hogy a rendszer ezeket a feltételeket, valamint a figyelésére. Gyakori, hogy legalább egy 100 és általában több száz utterances a nyelvi adatkészlet vagy több olyan is. Is lekérdezések bizonyos típusú kellene lennie, mint a többi gyakori, ha beszúrásához közös lekérdezések több példánya a következő adatkészletben.

**Kérdés**: I csak feltöltheti szavak listáját?

**Válasz**: szavakat feltöltése fogja lekérni a Szójegyzék a szavakat azonban tanítja meg a rendszer a szavakat általában használata.
Teljes vagy részleges utterances (mondat vagy felhasználók várhatóan mondja ki a műveleteket kifejezések) megadásával a nyelvi modell megtudhatja a szavakat, valamint, hogy használhatók. Az egyéni nyelvi modell helyes, nem csak az első, a rendszer új szavakat, hanem az alkalmazás ismert szavak valószínűségét hangolását. A rendszer további ebtter teljes utterances biztosító segítségével. 

## <a name="next-steps"></a>További lépések

* [hibaelhárítással](troubleshooting.md)
* [Kiadási megjegyzések](releasenotes.md)