---
title: Gyakran ismételt kérdések a egyéni beszéd szolgáltatás az Azure-on |} Microsoft Docs
description: Az alábbiakban a kérdésekre adott válaszok a legnépszerűbb az egyéni beszéd szolgáltatásról.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 11/21/2016
ms.author: panosper
ms.openlocfilehash: a929869b36387b3257b672308ceca36c84ff8cae
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347190"
---
# <a name="custom-speech-service-frequently-asked-questions"></a>Egyéni beszéd szolgáltatás gyakran ismételt kérdések

Ha ez a GYIK a kérdésekre adott válaszok nem található, próbálja meg az egyéni beszéd Service Közösség kérése a [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) és [UserVoice](https://cognitive.uservoice.com/)

## <a name="general"></a>Általános kérdések

**Kérdés**: hogyan fogja tudni, ha az adatok vagy a modell feldolgozása befejeződött?

**Válasz**: a modell vagy a tábla adatkészlet állapota jelenleg tudja csak szeretne.
Amikor a feldolgozás befejeződik, az állapota lesz "Kész".
Továbbfejlesztett feldolgozás állapota, például az e-mailben értesítést kommunikációs módszerek jelenleg is dolgozunk.

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

**Válasz**: jelenleg nem lehetővé teszik a meglévő modell frissítéséhez az új adatokat.
Ha egy új adatkészlet rendelkezik, és szeretné testre szabni a meglévő modell kell újra alakítani az új adatok és a használt régi adatkészlettel.
A régi és új adatkészletek kapcsolóval együtt kell használni a egyetlen .zip (ha akusztikus adatok) vagy egy .txt fájlt, ha egyszer kiigazítása nyelvi adatok történik, az új frissített modell rendelkeznie kell deszerializálni telepített új végpont beszerzése

**Kérdés**: Mi történik, ha az alapértelmezett értéknél nagyobb feldolgozási van szükség. 

**Válasz**: legfeljebb 5 egyidejű kérelmek méretezési egységek telepítésnek lépésekben a modell. Minden egyes méretezési egység biztosítja, hogy az, hogy a modell lehet egyidejűleg feldolgozni 5 hangadatfolyam. 100 méretezési egységeket (vagy 500 egyidejű kérelmek) vásárolhatja meg.

Lépjen kapcsolatba velünk Ha magasabb, mint az, hogy van szüksége.

**Kérdés**: is szeretnék a modell letöltése és Futtatás helyben?

**Válasz**: nem engedélyezzük letölti és helyben végrehajtott modellek.

**Kérdés**: a rendszer naplózza a kérelem?

**Válasz**: megválaszthatja, hogy kikapcsolja a nyomkövetés, amikor nincsenek hang központi telepítés létrehozásakor vagy transcriptions bekerülnek a naplóba. Ellenkező esetben kérelmek általában jelentkezett be az Azure-ban biztonságos tárolására. Ha további adatvédelmi megfontolások, amelyek nem engedik a szolgáltatással az egyéni beszéd adja, lépjen velünk kapcsolatba.

## <a name="importing-data"></a>Adatok importálása

**Kérdés**: Mi az, hogy az adatkészlet méretének korlátot? Hogy miért? 

**Válasz**: az aktuális adatkészlet határa 2 GB-ot, HTTP feltöltésének fájl mérete a korlátozás miatt. 

**Kérdés**: is szeretnék a zip-saját szövegfájlok ahhoz, hogy nagyobb szöveges fájl feltöltése? 

**Válasz**: nem, jelenleg csak a tömörítetlen szövegfájlok használata engedélyezett.

**Kérdés**: A jelentés szerint a utterances sikertelen volt. Az ezt a problémát?

**Válasz**: Ha csak néhány utterances nem sikerült importálása sikertelen volt, de ez nem probléma.
Ha az adatok akusztikus vagy nyelvi utterances többsége (pl. > 95 %-os) sikeresen importálva, az adatkészlet is használható. Ajánlott azonban, hogy megpróbálja megállapítani, hogy a utterances miért bukott, és hárítsa el a problémákat.
Leggyakoribb problémák, például formázási hibákat, amelyek könnyen javítsa ki. 

## <a name="creating-am"></a>DE létrehozása

**Kérdés**: akusztikus adatok van szükségem?

**Válasz**: javasoljuk akusztikus adatok egy óra 30 perc

**Kérdés**: az adatok milyen rendezési kell gyűjteni?

**Válasz**: adatokat gyűjthet, amelyek a forgatókönyvet közel van és a lehető és nagybetűhasználattal kell.
Ez azt jelenti, hogy az adatgyűjtés meg kell egyeznie a cél alkalmazás és a felhasználók eszközt vagy eszközöket, környezetek és hangszórók típusú. Általában akkor kell amikből adatgyűjtést legszélesebb hangszórók lehető széles. 

**Kérdés**: hogyan kell gyűjteni az? 

**Válasz**: hozzon létre egy önálló adatok gyűjtemény alkalmazást, vagy bizonyos ki a forgalomban hangrögzítés programot.
Az alkalmazás, amely a hangadatok naplózza, és használja, amely egy verziója is létrehozhat. 

**Kérdés**: kell átírni kiigazítása adatok magam megírni? 

**Válasz**: az adatok kért kell lennie. Saját kezűleg átvezeti, vagy használjon szakmai írjanak elő szolgáltatást. Közösségi ezek használatát szakmai transcribers, míg mások egyes használja. Azt is javasoljuk, hogy kérésre írjanak elő szolgáltatás.

**Kérdés**: mennyi időt vesz igénybe, létrehozhat egy egyéni akusztikus modellt?

**Válasz**: A feldolgozási ideje létrehozni egy egyéni akusztikus modell tárgya ugyanaz, mint a akusztikus adatkészlet hosszát.
Igen, öt órát adatkészlet alapján létrehozott testreszabott akusztikus modell órát fog igénybe venni körülbelül öt folyamathoz. 

## <a name="offline-testing"></a>Offline tesztelés

**Kérdés**: végezheti meg, hogy egy egyéni nyelvi modell használatával saját egyéni akusztikus modell offline vizsgálat?

**Válasz**: Igen, csak az egyéni nyelvi modellek válassza ki a legördülő lista az offline vizsgálat beállításakor

**Kérdés**: végezheti meg, hogy saját egyéni akusztikus modell segítségével egyéni nyelvi modell offline vizsgálat?

**Válasz**: Igen, egyszerűen jelölje ki a egyéni akusztikus modellt a legördülő menü az offline vizsgálat beállítása során.

**Kérdés**: Mi Word Hibaarány, és hogyan azt számított?

**Válasz**: Word hibaarány a beszédfelismerés értékelési metrikát. A hibák összesített számát, beleértve a beszúrások, törléseket és helyettesítések hivatkozás írjanak elő szavak száma osztva számítanak.

**Kérdés**: I most már tudja saját egyéni modell a vizsgálati eredmények, a ez helyes, vagy hibás számnak?

**Válasz**: az eredmények megjelenítése a modell, és a testreszabott összehasonlítása.
Meg kell célja, hogy a modell létrehozásához a testreszabási megfelelőbb járulhatnak

**Kérdés**: hogyan tegye I mérje fel, a WER alap modell, ezért láthatom, ha hiba történt a fokozása? 

**Válasz**: A kapcsolat nélküli vizsgálati eredmények megjelenítése az egyéni és javítása alapterv pontosságát pontosságát alapterv keresztül

## <a name="creating-lm"></a>Az LM létrehozása

**Kérdés**: mennyi szöveges adatot kell feltölteni?

**Válasz**: Ez a pontos választ adhat nehéz kérdést, attól függ, hogy különböző szóhasználatának és az alkalmazásban használt kifejezések során a rendszer a kiindulási nyelvi modelleket. Minden új szó akkor célszerű listázzák használata a lehető legtöbb példákat. A közös az alkalmazásban használt kifejezések a nyelvi adatok lévőket is beleértve olyan esetekben is hasznos, azt jelzi, hogy a rendszer ezeket a feltételeket, valamint a figyelésére.
Gyakori, hogy legalább száz és általában több száz utterances a nyelvi adatkészlet vagy több olyan is.
Emellett ha bizonyos * a lekérdezések kellene lennie, mint a többi gyakori, a következő adatkészletben közös lekérdezések több példányát is beszúrhat.

**Kérdés**: I csak feltöltheti szavak listáját?

**Válasz**: szavakat feltöltése fogja lekérni a Szójegyzék a szavakat azonban tanítja meg a rendszer a szavakat általában használata.
Teljes vagy részleges utterances (mondat vagy felhasználók várhatóan mondja ki a műveleteket kifejezések) biztosításával a nyelvi modell megtudhatja, az új szavak és azok használata. Az egyéni nyelvi modell helyes, nem csak az első, a rendszer új szavakat, hanem az alkalmazás ismert szavak valószínűségét hangolását. Ismerje meg, ez a rendszer teljes utterances biztosító segítségével. 

-----

 * [Áttekintés](cognitive-services-custom-speech-home.md)
 * [Megkezdődött](cognitive-services-custom-speech-get-started.md)
 * [Szószedet](cognitive-services-custom-speech-glossary.md)
