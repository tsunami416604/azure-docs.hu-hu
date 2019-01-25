---
title: – Gyakran ismételt kérdések a Speech to Text szolgáltatás az Azure-ban
titleSuffix: Azure Cognitive Services
description: Szolgáltatás szöveg-hang transzformációs legnépszerűbb kérdésekre adott válaszok.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: 21f7733f3f63fd4d7567830ab4c98ea1b04bf32e
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2019
ms.locfileid: "54904131"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Beszédfelismerés – gyakori kérdések

Ha nem találja a kérdésekre adott válaszok a gyakori kérdések, tekintse meg az [más támogatási lehetőségeket](support.md).

## <a name="general"></a>Általános kérdések

**K: Mi a különbség egy alapkonfiguráció modell és a egy egyéni beszéd szöveges modellre?**

**A**: A modell rendelkezik betanítva Microsoft tulajdonú adatok használatával, és már üzembe helyezte a felhőben.  Használhatja egy egyéni modell jobban igazodik egy adott környezetben, amely rendelkezik az adott a zajszint vagy nyelvi modell igazíthatja. A gyáraktól, autókból vagy zajos utcák megköveteli az igazítani akusztikai modell. Témakörökben, mint biológiai, fizika, radiológia, a termék nevét, és egyéni betűszavakat egy igazítani, nyelvi modell lenne szükség.

**K: Hogyan kezdjek hozzá, ha egy alapkonfiguráció modell használata?**

**A**: Először kérje le egy [előfizetési kulcs](get-started.md). Ha azt szeretné, hogy a predeployed kiindulási modelleket alkalmazhatnak REST-hívások, tekintse meg a [REST API-k](rest-apis.md). Ha azt szeretné, hogy használja a websockets protokollt, [az SDK letöltéséhez](speech-sdk.md).

**K: Mindig meg kell hozhat létre egy egyéni beszédmodellel?**

**A**: Nem. Ha az alkalmazás általános, napi nyelv, nem kell egy modell testreszabása. Ha az alkalmazás használatban van egy környezet alig vagy egyáltalán nem háttérzaj esetén nem kell egy modell testreszabása.

Alapkonfiguráció és a portálon testreszabott modellek üzembe helyezése, és futtassa a pontossági tesztek őket. Ez a funkció segítségével mérheti a modell és a egy egyéni modell pontosságát.

**K: Honnan tudom, ha az adatkészletet vagy a modell feldolgozása befejeződött?**

**A**: A modell vagy a tábla adatkészlet állapota jelenleg az egyetlen mód arra, hogy. A feldolgozás befejeződése után az állapot értéke **sikeres**.

**K: Több modellt is létrehozható?**

**A**: Nincs megszabva, hogy a gyűjtemények rendelkezhet modellek számát.

**K: Vettem hiba. Hogyan megszakítja a saját adatok importálása vagy folyamatban lévő létrehozását modellkezelési?**

**A**: Jelenleg nem vonható vissza egy akusztikai és nyelvi betanítás folyamat. Ha kerül a törölheti az importált adatok és modellek.

**K: Mi a különbség a Keresés és a Diktálás modell és a természetes nyelvi modell között?**

**A**: A beszédfelismerési szolgáltatás több modell közül választhat. A természetes nyelvi modell, természetes nyelvi stílusú elhangzik hatékonysággal hasznos. Ez a modell ideális átírás telefonhívásokat. A Keresés és a Diktálás modell ideális voice-eseményindítóval aktivált alkalmazásokat. Az univerzális modellje új modell, amely a célja, hogy mindkét-forgatókönyveket érintenek. Az univerzális modell jelenleg a természetes nyelvi modell, a legtöbb helyen a minőségi szintű vagy afeletti.

**K: Frissítheti a saját meglévő model (modell rétegezést)?**

**A**: Egy meglévő modell nem frissíthető. Egy megoldás kombinálja a régi adatkészletet, hogy az új adatkészletet és readapt.

A régi adatkészletet, és az új adatkészletet egy zip-fájlba (az akusztikai adathoz) vagy egy .txt fájlban (a nyelvi adatok) nelze kombinovat. Adaptációs befejeződése után az új, frissített modell kell új végpont beszerzése újratelepítése

**K: Amikor alapkonfigurációt új verziója érhető el az üzembe helyezés automatikusan frissülnek?**

**A**: Üzemelő példányok nem automatikusan frissül.

Ha módosítani, és a telepített alapkonfiguráció 1.0-s verziójú modell, a telepítés marad. Ügyfelek decommision is az üzembe helyezett modell, újra alkalmazkodjon az alapkonfiguráció újabb verzióját használja, és helyezze újra üzembe.

**K: Mi történik, ha van szükség magasabb szintű egyidejűség érdekében, mire a portál érhető el, mint az üzembe helyezett modell?**

**A**: A modell 20 egyidejű kérelmek egységnyi növekményekben vertikálisan felskálázhatja.

Lépjen kapcsolatba velünk, ha szüksége van egy nagyobb méreteket.

**K: A modell letöltheti és helyileg futtatja az alkalmazást?**

**A**: Modellek nem tölthető le és helyben végrehajtott.

**K: Bejelentkezett saját kérések?**

**A**: Választhat, hogy egy nyomkövetést kikapcsolhatják a központi telepítés létrehozásakor. Ezen a ponton nincs hang- vagy beszédátírás bekerülnek a naplóba. Ellenkező esetben kérelmek általában jelentkezett be az Azure-ban biztonságos tárolására.

**K: Saját kérések szabályozott?**

**A**: A REST API-25, 5 másodpercenként kérelmek korlátozza. Részletek tekintheti meg az oldalak [beszédfelismerés](speech-to-text.md).

Ha további adatvédelmi megfontolások, amelyek nem engedik a custom Speech service használatával, lépjen kapcsolatba a támogatási csatornáit egyikét.

## <a name="importing-data"></a>Adatok importálása

**K: Mi az adatkészlet méretének korlátját, és miért van rájuk a korlátot?**

**A**: Az adatkészlet jelenleg legfeljebb 2 GB-os. A korlát HTTP feltöltendő fájl mérete a korlátozás miatt van. 

**K: Is szeretnék zip saját szöveges fájlok, nagyobb szövegfájl töltök?** 

**A**: Nem. Jelenleg csak a tömörítetlen szövegfájlok használata engedélyezett.

**K: Az adatok a jelentés szerint utterances sikertelen volt. Mi a probléma?**

**A**: Egy fájlban megcímkézzen 100 %-os feltöltése sikertelen nem probléma. Ha az az akusztikai és nyelvi-adatkészletek (a példában, több mint 95 %-os) megcímkézzen túlnyomó többsége importálása sikeresen megtörtént, az adatkészlet is lesz használható. Azt javasoljuk azonban, próbálja megérteni, hogy megcímkézzen miért volt sikertelen, és hárítsa el a problémákat. Leggyakoribb problémák, például a formázási hibákat, olyan könnyű megoldást kínál. 

## <a name="creating-an-acoustic-model"></a>Az akusztikai modell létrehozása

**K: Mennyi akusztikai adatot van szükségem?**

**A**: Javasoljuk, hogy a 30 perc és egy órányi akusztikai adathoz.

**K: Milyen adatokat kell gyűjteni?**

**A**: Az alkalmazás forgatókönyv legközelebb adatok gyűjtésére, és a használat célját, a lehető. Az adatgyűjtés meg kell egyeznie a cél alkalmazás és a felhasználók eszköz vagy eszközök, a környezetek és előadói típusú tekintetében. Általában érdemes összegyűjtötte az adatokat a legszélesebb beszélők minél számos. 

**K: Hogyan kell gyűjteni akusztikai adathoz?**

**A**: Adatok gyűjtése önálló alkalmazás létrehozása, vagy off-the-shelf rendszerhang rögzítésének szoftvert használ. Az alkalmazás, amely naplózza a hang adatokat, majd az adatok egy verziója is létrehozhat. 

**K: Kell átvezeti betanítás magam?**

**A**: Igen! Lefényképezze saját maga, vagy szakmai beszédátírási szolgáltatást használ. Néhány felhasználó inkább szakmai transcribers és mások közösségi használja, vagy hajtsa végre a beszédátírás magukat.

## <a name="accuracy-testing"></a>Pontosságtesztelés

**K: Végrehajthatok offline tesztelés saját egyéni akusztikai modell egy testreszabott nyelvi modell használatával?**

**A**: Igen, csak adja meg a testreszabott nyelvi modell a legördülő menüben az offline vizsgálat beállításakor.

**K: Végrehajthatok offline tesztelés saját egyéni nyelvi modell, egy egyéni akusztikai modell használatával?**

**A**: Igen, csak adja meg az importálni akusztikai modell a legördülő menüben az offline vizsgálat beállításakor.

**K: Mi a word Hibaarány (WER), és hogyan, számított?**

**A**: A WER a kiértékelés metrika beszédfelismerési. A WER akkor számít, a hibák teljes száma, amely tartalmazza a beszúrások, törléseket és helyettesítések továbbá, a hivatkozás beszédátírási szavak száma osztva. További információkért lásd: [word Hibaarány](https://en.wikipedia.org/wiki/Word_error_rate).

**K: Hogyan állapítható meg, hogy jó-e egy pontossága teszt eredményeit?**

**A**: Az eredmények megjelenítése a modell és a testreszabott modell összehasonlítását. A referenciakonfiguráció modellt, hogy a testreszabási adnak a munkánknak beat kell megfelelnek.

**K: Hogyan állapítható a WER egy alapszintű modell, így látható, hogy történt-e egy fokozása?** 

**A**: A kapcsolat nélküli teszt eredményei a egyéni modell és a javítása alapkonfiguráció pontosságát alapkonfiguráció mutatja.

## <a name="creating-a-language-model"></a>Nyelvi modell létrehozása

**K: Szöveges adatok mennyiségét van szükségem a feltöltendő?**

**A**: Attól függ, a különféle beszédstílusok, és az alkalmazásban használt kifejezések a kiindulási nyelvi modellek származnak. Minden új szavak célszerű azokat a szavakat használatát a lehető legtöbb példákat biztosítanak. Az alkalmazásában használt gyakori kifejezések többek között a mondatok a nyelvi adatok akkor is hasznos, mert azt jelzi, hogy a rendszer ezeket a feltételeket is figyelhet. Gyakori, hogy legalább 100, és általában több száz vagy több utterances nyelvi adatkészlet. Is ha bizonyos típusú lekérdezések gyakoribb a többinél várhatóan, beszúrhat több másolatot a gyakori lekérdezések az adatkészletben.

**K: Csak feltölthetek olyan szólistát?**

**A**: Olyan szólistát feltöltése a szavakat felveszi a szöveg szóhasználati, de azt nem tanórákon a rendszer a szavakat általában használata. Azáltal, hogy a teljes vagy részleges utterances (mondatokból vagy kifejezésekből vagy az, hogy a felhasználók valószínűleg tegyük fel, hogy miket), a nyelvi modell tudhat meg az új szavakat és a használatuk. Az egyéni nyelvi modell jó, nemcsak a rendszer új szavak, hanem az alkalmazás ismert szavak valószínűségét hangolását. Ismerje meg jobban a rendszer teljes utterances nyújtó segítségével. 

## <a name="next-steps"></a>További lépések

* [hibaelhárítással](troubleshooting.md)
* [Kibocsátási megjegyzések](releasenotes.md)
