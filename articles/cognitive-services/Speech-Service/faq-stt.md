---
title: Gyakran feltett kérdés a Speech to Text szolgáltatás az Azure-ban |} A Microsoft Docs
description: Az alábbiakban a szöveg-hang transzformációs legnépszerűbb kérdésekre adott válaszok.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: 543e8d6fb68a351dfe75c962debaf15eeb080a3f
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223887"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Beszédfelismerés – gyakori kérdések

Ha nem találja a kérdésekre adott válaszok a gyakori kérdések, próbálja meg a Custom Speech Service Közösség kéri a [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) és [uservoice-on](https://cognitive.uservoice.com/)

## <a name="general"></a>Általános kérdések

**Kérdés**: Mi a különbség a szöveg modellek alapkonfiguráció és a custom Speech között?

**Válasz**: kiindulási modelleket alkalmazhatnak rendelkezik betanítva a Microsoft tulajdonában lévő adatok és a már üzembe helyezte a felhőben. Egyéni modellek engedélyezése a felhasználó jobban igazodik egy adott környezetben a meghatározott a zajszint vagy a nyelvi modell igazíthatja. A gyáraktól, autók, zajos utcák igazítani akusztikai modell lenne szükség, mivel a konkrét témakörök biológiai, fizikai, radiológia, termékek neveit és egyéni betűszavakat például egy igazítani, nyelvi modell lenne szükség.

**Kérdés**: Hogyan kezdjek hozzá, ha egy alapkonfiguráció modell használata?

**Válasz**: először le szeretné kérni egy [előfizetési kulcs](get-started.md). Ha azt szeretné, hogy a predeployed kiindulási modelleket alkalmazhatnak REST-hívások, tekintse meg a [részletesen Itt](rest-apis.md). Ha használja a websockets protokoll letöltési szeretné a [SDK](speech-sdk.md)

**Kérdés**: Do I mindig létre kell hoznia egy egyéni beszédmodellel?

**Válasz**: nem, ha az alkalmazás az általános napi nyelv használatával, akkor nem szükséges egy modell testreszabása. Emellett, ha az alkalmazás használatban van egy környezetet, amelyben nincs a háttérzaj alig vagy egyáltalán nem, akkor szabhatja testre, vagy nem kell. A portál lehetővé teszi, hogy a felhasználók alapkonfiguráció és testreszabott modellek üzembe helyezése és pontossági tesztek futtatásához. Felhasználók használhatják ezt a szolgáltatást egy alapkonfiguráció vs pontosságát mérhető egy egyéni modell.

**Kérdés**: Honnan tudom, ha az adatkészlet vagy a modell feldolgozása befejeződött?

**Válasz**: a modell vagy a tábla adatkészlet állapota jelenleg az egyetlen mód arra, hogy.
A feldolgozás befejeződése után az állapot lesz kell "sikeres".

**Kérdés**: hozható létre egynél több modell?

**Válasz**: nem korlátozott, hány modellek szerepelnek a gyűjteményben.

**Kérdés**: vettem hiba. Hogyan megszakítja a saját adatok importálása vagy folyamatban lévő létrehozását modellkezelési? 

**Válasz**: jelenleg nem vonható vissza egy akusztikai és nyelvi betanítás folyamat. Importált adatok és modellek kerül után lehet törölni.

**Kérdés**: Mi a különbség a Keresés & Diktálás modelleket és a természetes nyelvi modellek között?

**Válasz**: nincsenek egynél több kiindulási modelleket alkalmazhatnak a beszédfelismerési szolgáltatás közül választhat. A természetes nyelvi modell megfelelő speech beszélt természetes nyelvi stílus felismerve. Ez a modell ideális keresése közben hívások átírás és Diktálás ideális megoldás alkalmazások indítása hanggal történik. Univerzális egy új modell, amely a célja, hogy mindkét-forgatókönyveket érintenek.

**Kérdés**: is frissíthetők a saját meglévő model (modell rétegezést)?

**Válasz**: létező modell nem frissíthető. Megkerülő megoldásként az új kombinálva a régi adatkészletet, és readapt.

Ha nyelvi adatok egy zip (ha akusztikai adathoz) vagy egy .txt fájl nelze kombinovat a régi és új adatkészletek. Adaptációs van befejezése után az új frissített modell kell lennie a megszüntetéséhez telepített új végpont beszerzése

**Kérdés**: Mi történik, ha az üzembe helyezett modell, mire a portál érhető el, mint a magasabb szintű egyidejűség érdekében kell. 

**Válasz**: a modell 20 egyidejű kérelmek egységnyi növekményekben vertikálisan felskálázhatja. 

Lépjen kapcsolatba velünk, ha nagyobb méreteket van szüksége.

**Kérdés**: I letöltheti futtassam és helyileg futtatja az alkalmazást?

**Válasz**: modellek nem tölthető le és helyben végrehajtott.

**Kérdés**: saját naplózott kérések?

**Válasz**: választhat, hogy a nyomkövetés, onnantól nem hang kikapcsolhatják a központi telepítés létrehozása során, vagy beszédátírás lesz naplózva. Ellenkező esetben kérelmek általában jelentkezett be az Azure-ban biztonságos tárolására. Ha további adatvédelmi megfontolások, amelyek nem engedik a Custom Speech Service segítségével a, forduljon a támogatási csatornáit egyikét.

## <a name="importing-data"></a>Adatok importálása

**Kérdés**: Mi az az adatkészlet méretének korlátját? Hogy miért? 

**Válasz**: az adatkészlet jelenleg legfeljebb 2 GB HTTP feltöltendő fájl mérete a korlátozás miatt nem lehetséges. 

**Kérdés**: is szeretnék zip saját szöveges fájlok annak érdekében, hogy töltsön fel egy nagyobb szöveges fájlt? 

**Válasz**: nem, jelenleg csak a tömörítetlen szöveges fájlok engedélyezettek.

**Kérdés**: az adatok a jelentés szerint a utterances sikertelen volt. Mi a probléma?

**Válasz**: 100 %-a-fájlban megcímkézzen feltöltése sikertelen nem probléma.
Ha egy akusztikai és nyelvi adatok megcímkézzen túlnyomó többsége (például > 95 %-os) sikeresen importálva, az adatkészlet is használható. Ajánlott azonban, hogy próbálja megérteni, hogy megcímkézzen miért volt sikertelen, és hárítsa el a problémákat. Leggyakoribb problémák, például a formázási hibákat, olyan könnyű megoldást kínál. 

## <a name="creating-am"></a>DE létrehozása

**Kérdés**: mennyi akusztikai adatot van szükségem?

**Válasz**: javasoljuk, hogy az egy órás akusztikai adathoz 30 perc alatt.

**Kérdés**: milyen adatokat kell gyűjteni?

**Válasz**: az alkalmazás forgatókönyv legközelebb adatok gyűjtésére, és a használat célját, a lehető.
Az adatgyűjtés meg kell egyeznie a cél alkalmazás és a felhasználók eszköz vagy eszközök, a környezetek és előadói típusú tekintetében. Általában érdemes összegyűjtötte az adatokat a legszélesebb beszélők minél számos. 

**Kérdés**: hogyan kell gyűjteni azt? 

**Válasz**: adatok gyűjtése önálló alkalmazás létrehozása, vagy használhatja a ki a kereskedelmi rendszerhang rögzítésének szoftvert.
Az alkalmazás, amely az audio adatokat naplózza, és használja, amely egy verziója is létrehozhat. 

**Kérdés**: kell átvezeti betanítás magam? 

**Válasz**: Igen! Lefényképezze saját maga, vagy szakmai beszédátírási szolgáltatást használ. Néhány felhasználó szakmai transcribers igény szerint, míg mások közösségi használja, vagy hajtsa végre a beszédátírás magukat.

## <a name="accuracy-testing"></a>Pontosságtesztelés

**Kérdés**: végrehajtható meg, hogy saját egyéni nyelvi modell segítségével importálni akusztikai modell offline tesztelés?

**Válasz**: Igen, csak adja meg, a testreszabott nyelvi modell a listában lévő offline vizsgálat beállításakor

**Kérdés**: végrehajtható meg, hogy saját egyéni nyelvi modell, egy egyéni akusztikai modell használatával offline tesztelés?

**Válasz**: Igen, a importálni akusztikai modell csak válassza a legördülő menüben az offline vizsgálat beállításakor.

**Kérdés**: Mi az a Word hiba sebessége (WER), és hogyan, számított?

**Válasz**: Word hiba sebessége (WER) olyan beszédfelismerési a kiértékelés metrika. A hibák teljes száma, amely tartalmazza a beszúrások, törléseket és helyettesítések továbbá, a hivatkozás beszédátírási szavak száma osztva számítani. További részleteket [itt](https://en.wikipedia.org/wiki/Word_error_rate) talál.

**Kérdés**: Hogyan állapítható meg egy pontossága vizsgálati eredményeket, hogy jó?

**Válasz**: az eredmények megjelenítése a modell és a egy testreszabott összehasonlítását.
A referenciakonfiguráció modellt, hogy a testreszabási adnak a munkánknak beat kell megfelelnek.

**Kérdés**: hogyan do I döntse el, a Word hibák aránya az alap-modellek, így látható, hogy történt-e fokozása? 

**Válasz**: az offline vizsgálat eredményeit mutatja a egyéni modell és a javítása alapkonfiguráció pontosságát pontosságát alapterv.

## <a name="creating-lm"></a>Az LM létrehozása

**Kérdés**: szöveges adatok mennyiségét kell feltölteni?

**Válasz**: attól függ, a különféle beszédstílusok, és az alkalmazásban használt kifejezések a kiindulási nyelvi modellek származnak. Minden új szavak célszerű azokat a szavakat használatát a lehető legtöbb példákat biztosítanak. Az alkalmazásában használt gyakori kifejezések többek között a mondatok a nyelvi adatok akkor is hasznos, ahogy azt jelzi, hogy a rendszer figyelni ezeket a feltételeket, valamint a. Gyakori, hogy legalább száz és általában több száz utterances nyelvi adatkészlet vagy több olyan fontos. Bizonyos típusú lekérdezések gyakoribb a többinél várhatóan, is az adatkészlet is beszúrhat a gyakori lekérdezések több példányát.

**Kérdés**: csak feltölthetek olyan szólistát?

**Válasz**: feltöltése szavak listáját beszédstílusok, a szavakat lekérése azonban tartalmazza a rendszer a szavakat általában használata.
Teljes vagy részleges utterances (mondatokból vagy kifejezésekből vagy a felhasználók valószínűleg övezi hálózata) biztosításával a nyelvi modell tudhat meg az új szavakat, és azok használatát ismerteti. Az egyéni nyelvi modell jó, nem csak az első, a rendszer új szavak, hanem az alkalmazás ismert szavak valószínűségét hangolását. Ismerje meg jobban a rendszer teljes utterances nyújtó segítségével. 

## <a name="next-steps"></a>További lépések

* [hibaelhárítással](troubleshooting.md)
* [Kiadási megjegyzések](releasenotes.md)
