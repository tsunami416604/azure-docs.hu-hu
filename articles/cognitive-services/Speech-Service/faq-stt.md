---
title: Gyakran ismételt kérdések az Azure-beli szöveges szolgáltatásról
titleSuffix: Azure Cognitive Services
description: Választ kaphat a beszédfelismeréssel kapcsolatos legnépszerűbb kérdésekre a szöveges szolgáltatással kapcsolatban.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: bde68a70ac047433e86b7e06bc5f4a56bdd28595
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028513"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Beszéd szöveggel kapcsolatos gyakori kérdések

Ha nem talál választ a kérdéseire ebben a GYIK-ban, tekintse meg az [egyéb támogatási lehetőségeket](support.md).

## <a name="general"></a>Általános

**K: Mi a különbség az alapmodell és a szöveges modell egyéni beszéde között?**

**A**: Az alapmodell a Microsoft tulajdonában lévő adataival lett kiképezve, és már telepítve van a felhőben.  Egyéni modell használatával úgy alakíthatja ki a modelleket, hogy jobban illeszkedjen egy adott környezethez, amely adott környezeti zajt vagy nyelvet tartalmaz. A gyári szintekhez, autókhoz vagy zajos utcákhoz alkalmazkodó akusztikai modellre van szükség. A biológia, a fizika, a radiológia, a terméknév és az egyéni betűszók által használt témákhoz alkalmazkodó nyelvi modellre van szükség.

**K: Hol kezdjem, ha alapmodellt szeretnék használni?**

**A**: Először szerezze be az [előfizetési kulcsot](get-started.md). Ha szeretne REST-hívásokat kezdeményezni az előtelepített alapmodellekhez, tekintse meg a [REST API-kat](rest-apis.md). Ha WebSockets-t szeretne használni, [töltse le az SDK](speech-sdk.md)-t.

**K: Mindig létre kell hozni egy egyéni beszédfelismerési modellt?**

**A**: Nem. Ha az alkalmazás általános, napról napra kiterjedő nyelvet használ, nem kell testreszabnia a modellt. Ha az alkalmazás olyan környezetben van használatban, ahol kevés vagy nincs háttérzaj, nem kell testreszabnia a modellt.

Üzembe helyezhet alapkonfigurációt és testreszabott modelleket a portálon, majd elvégezheti a pontossági tesztek futtatását. Ezzel a funkcióval mérhető az alapmodell pontossága, és egy egyéni modell is megadható.

**K: Honnan tudom, hogy elkészült az adatkészlet vagy a modell feldolgozása?**

**A**: Jelenleg csak a modell vagy az adatkészlet állapota látható a táblában. Ha a feldolgozás befejeződött, az állapot **sikeres**lesz.

**K: Létrehozhatok több modellt is?**

**A**: A gyűjteményben megadható modellek száma nincs korlátozva.

**K: Észrevettem, hogy hiba történt. Hogyan megszakítja az adatimportálást, vagy folyamatban van a modell létrehozása? @no__t – 0

**A**: Jelenleg nem állítható vissza akusztikus vagy nyelvi átalakítási folyamat. Az importált adattípusokat és modelleket törölheti, ha azok egy terminál-állapotban vannak.

**K: Mi a különbség a keresési és a diktálási modell és a társalgási modell között?**

**A**: A Speech Service-ben több alapmodell közül választhat. A társalgási modell hasznos lehet a beszélgetési stílusban beszélt beszéd felismeréséhez. Ez a modell ideális a telefonhívások átírásához. A keresési és a diktálási modell ideális a hangvezérelt alkalmazások számára. Az univerzális modell egy új modell, amelynek célja mindkét forgatókönyv kezelése. Az univerzális modell jelenleg a legtöbb területi szinten a társalgási modell minőségi szintjén van vagy magasabb.

**K: Frissíthetem a meglévő modellt (modell-halmozás)?**

**A**: Meglévő modell nem frissíthető. Megoldásként egyesítse a régi adatkészletet az új adatkészlettel, és módosítsa újra.

A régi adatkészletet és az új adatkészletet egyetlen. zip fájlban (akusztikai adatokat) vagy egy. txt fájlban (nyelvi adatokat) kell egyesíteni. Az adaptáció befejezése után az új, frissített modellt újra kell telepíteni új végpont beszerzéséhez.

**K: Ha az alapkonfiguráció új verziója érhető el, az üzembe helyezésem automatikusan frissül?**

**A**: A központi telepítések nem frissülnek automatikusan.

Ha kiigazította és telepítette az alapkonfiguráció 1.0-s verziójával rendelkező modellt, akkor az üzemelő példány a következőképpen marad. Az ügyfelek leszerelik az üzembe helyezett modellt, újra alkalmazkodnak az alapkonfiguráció újabb verziójához, és újból üzembe helyezik azokat.

**K: Mi a teendő, ha a portálon elérhetőnél magasabb szintű egyidejűségre van szükségem a saját üzembe helyezett modellhez?**

**A**: A modellt akár 20 egyidejű kérelemben is felskálázással bővítheti.

Ha nagyobb méretűre van szüksége, forduljon a [beszédfelismerési támogatási szolgálathoz](mailto:speechsupport@microsoft.com?subject=Request%20for%20higher%20concurrency%20for%20Speech-to-text) .

**K: Le tudom tölteni a modellt, és helyileg futtatom?**

**A**: A modellek nem tölthetők le és nem hajthatók végre helyileg.

**K: A rendszer naplózza a kérelmeket?**

**A**: A nyomkövetés kikapcsolásához egy központi telepítés létrehozásakor választhat. Ekkor a rendszer nem naplózza a hanganyagot vagy az átírást. Ellenkező esetben a rendszer általában a biztonságos tárolóban naplózza a kérelmeket az Azure-ban.

**K: A kérések szabályozva vannak?**

**A**: Az REST API 5 másodpercenként 25 másodpercenként korlátozza a kérelmeket. A részletek megtalálhatók az oldalain a [szövegre való beszédhez](speech-to-text.md).

**K: Hogyan számítunk fel díjat a kettős csatornás hangért?**

**A**: Ha az egyes csatornákat külön küldi el (mindegyik csatornát a saját fájljában), az egyes fájlok időtartama alapján kell fizetnie. Ha egyetlen fájlt küld el minden egyes csatornával együtt, akkor az adott fájl időtartamára kell fizetnie.

> [!IMPORTANT]
> Ha további adatvédelmi kérdései vannak, amelyek tiltják a Custom Speech Service használatát, lépjen kapcsolatba az egyik támogatási csatornával.

## <a name="importing-data"></a>Adatok importálása

**K: Mekkora a korlátja egy adatkészlet méretének, és miért van ez a korlát?**

**A**: Az adatkészletek jelenlegi korlátja 2 GB. A korlátot a HTTP-feltöltéshez használt fájl méretének korlátozása okozza. 

**K: Használhatom a szövegfájlom zip-fájljait, hogy feltölthető legyen egy nagyobb szövegfájl?** 

**A**: Nem. Jelenleg csak a nem tömörített szövegfájlok engedélyezettek.

**K: Az Adatjelentés szerint a hosszúságú kimondott szöveg sikertelen volt. Mi a probléma?**

**A**: Nem sikerült feltölteni a hosszúságú kimondott szöveg 100 százalékát a fájlban. Ha a hosszúságú kimondott szöveg túlnyomó többsége akusztikai vagy nyelvi adatkészletekben (például több mint 95 százalék) sikeresen importálva lett, az adatkészlet használható. Azt azonban javasoljuk, hogy próbálja meg megérteni, miért nem sikerült a hosszúságú kimondott szöveg és elhárítani a problémákat. A leggyakoribb problémák (például a formázási hibák) könnyen orvosolhatók. 

## <a name="creating-an-acoustic-model"></a>Akusztikai modell létrehozása

**K: Mennyi akusztikai adatmennyiségre van szükségem?**

**A**: Javasoljuk, hogy 30 perc és egy órányi akusztikai érték között kezdjen.

**K: Milyen adatokat kell gyűjteni?**

**A**: Gyűjtsön adatokat az alkalmazási forgatókönyvnek megfelelően, és használja az esetet a lehető leghamarabb. Az adatgyűjtésnek meg kell egyeznie a célalkalmazás és a felhasználók számára az eszközök, a környezetek és a hangszórók típusai alapján. Általánosságban elmondható, hogy a lehető legszélesebb körben gyűjtsön adatokat a különböző hangszórókból. 

**K: Hogyan gyűjthetek akusztikus adatokat?**

**A**: Létrehozhat egy önálló adatgyűjtési alkalmazást, vagy használhatja a polcon kívüli hangrögzítési szoftvert is. Létrehozhatja az alkalmazás egy olyan verzióját is, amely naplózza a hangadatokat, majd az adatok használatával. 

**K: Magamra is szükség van az alkalmazkodási adattovábbításra?**

**A**: Igen! Saját maga is lemásolhatja, vagy használhat Professional transzkripciós szolgáltatást. Egyes felhasználók előnyben részesítettek a professzionális átiratokkal, mások pedig a közösségi használják, vagy magukra az átírásokra.

## <a name="accuracy-testing"></a>Pontosság tesztelése

**K: Használhatom az egyéni akusztikai modellem offline tesztelését egyéni nyelvi modell használatával?**

**A**: Igen, egyszerűen válassza ki az egyéni nyelvi modellt a legördülő menüben az offline teszt beállításakor.

**K: Használhatom az egyéni nyelvi modell offline tesztelését egyéni akusztikus modell használatával?**

**A**: Igen, egyszerűen válassza ki az egyéni akusztikai modellt a legördülő menüben az offline teszt beállításakor.

**K: Mi a Word Error Rate (WER) és hogyan számítható ki?**

**A**: A WER a beszédfelismerés kiértékelési mérőszáma. A WER a hibák teljes száma, amely tartalmazza a beszúrásokat, a törléseket és a behelyettesítéseket, a hivatkozás átírásakor a szavak teljes száma szerint elosztva. További információ: [Word Error Rate](https://en.wikipedia.org/wiki/Word_error_rate).

**K: Hogyan megállapítani, hogy a pontossági teszt eredményei jók-e? @no__t – 0

**A**: Az eredmények az alapmodell és a testre szabott modell összehasonlítását mutatják be. Érdemes megverni az alapmodellt, hogy a testreszabási lehetőségek is hasznosak legyenek.

**K: Hogyan meghatározni egy alapmodell WERjét, hogy megtudjam, van-e javulás? @no__t – 0 

**A**: Az offline teszt eredményei az egyéni modell alapkonfigurációjának pontosságát és az alapterv tökéletesítését mutatják be.

## <a name="creating-a-language-model"></a>Nyelvi modell létrehozása

**K: Mennyi szöveges adatokra van szükség a feltöltéshez?**

**A**: Ez attól függ, hogy az alkalmazásban használt szókincs és kifejezések milyen eltérőek a kezdő nyelvi modelltől. Minden új szó esetében hasznos lehet a szavak használatának lehető legtöbb példáját megadnia. Az alkalmazásban használt általános kifejezések, beleértve a nyelvi adatkifejezéseket is, hasznosak lehetnek, mivel azt jelzi, hogy a rendszer a jelen feltételeket is figyeli. Gyakori, hogy legalább 100, és általában több száz vagy több hosszúságú kimondott szöveg van a nyelvi adatkészletben. Továbbá, ha a lekérdezések bizonyos típusai gyakoribbak, mint mások, a közös lekérdezések több példányát is beillesztheti az adatkészletbe.

**K: Csak egy szólistát tölthetek fel?**

**A**: A szavak listájának feltöltése felveszi a szavakat a szókincsbe, de nem fogja megtanítani, hogy a rendszer hogyan használja a szavakat általában. Ha teljes vagy részleges hosszúságú kimondott szöveg (mondatokat vagy kifejezéseket) biztosít a felhasználók számára, akkor a nyelvi modell megismerheti az új szavakat és azok használatát. Az egyéni nyelvi modell nem csupán az új szavak a rendszerhez való hozzáadására, hanem az alkalmazáshoz tartozó ismert szavak valószínűségének módosítására is jó. A teljes hosszúságú kimondott szöveg biztosítása segít a rendszeren a jobb megismerésben. 

## <a name="next-steps"></a>További lépések

* [Hibaelhárítás](troubleshooting.md)
* [Kibocsátási megjegyzések](releasenotes.md)
