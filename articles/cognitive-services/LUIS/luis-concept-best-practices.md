---
title: Ajánlott eljárások
titleSuffix: Language Understanding - Azure Cognitive Services
description: Ismerje meg, a LUIS gyakorlati tanácsok a legjobb eredményt a LUIS-alkalmazás modellből.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: diberry
ms.openlocfilehash: 9a6f9d54c52f36b8f709eacaf25d3fea31dbe516
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58895815"
---
# <a name="best-practices-for-building-a-language-understanding-app-with-cognitive-services"></a>Ajánlott eljárások a Cognitive Services language understanding alkalmazás létrehozásához
Az alkalmazások használatával hozhat létre a LUIS-alkalmazás. 

* Nyelvi modell létrehozása
* Néhány képzési példa (10 – 15 / leképezés) beszédmódok hozzáadása
* Közzététel 
* A végpont tesztelése 
* Funkciók hozzáadása

Miután az alkalmazás [közzétett](luis-how-to-publish-app.md), használja az Authoring Tool ciklust, a szolgáltatás hozzáadása, közzététel és végpontról teszteléséhez. Ne kezdje el a következő szerzői ciklus további példa utterances hozzáadásával. Amely nem teszi lehetővé a LUIS ismerje meg, a modell a való életből vett felhasználói kimondott szöveg. 

Ahhoz, hogy a feladat a tanulás, hatékony, LUIS nem bontsa ki a kimondott szöveg mindaddig, amíg az aktuális készletét példa és a végpont utterances vannak visszaadó biztosabb, magas előrejelzési pontszámokat. Használatával pontszámukat [aktív tanulás](luis-concept-review-endpoint-utterances.md), [minták](luis-concept-patterns.md), és [listák kifejezés](luis-concept-feature.md). 

## <a name="do-and-dont"></a>Hajtsa végre, és nem
Az alábbi lista tartalmazza az ajánlott eljárások a LUIS-alkalmazások:

|Ajánlott|Nem ajánlott|
|--|--|
|[Adja meg a különböző leképezések](#do-define-distinct-intents) |[Számos példa utterances leképezések hozzáadása](#dont-add-many-example-utterances-to-intents) |
|[Keres édes helyet túl általános, és túl adott között a minden egyes leképezés](#do-find-sweet-spot-for-intents)|[Használja a LUIS képzési platform](#dont-use-luis-as-a-training-platform)|
|[Iteratív az alkalmazás létrehozása](#do-build-the-app-iteratively)|[Ugyanazt a formátumot, figyelmen kívül hagyja a más formátumú, sok példa beszédmódok hozzáadása](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Újabb iterációban kifejezés listák és a minták hozzáadása](#do-add-phrase-lists-and-patterns-in-later-iterations)|[Vegyes szándékok és entitások definíciója](#dont-mix-the-definition-of-intents-and-entities)|
|[Minden leképezések között a kimondott szöveg egyenleg](#balance-your-utterances-across-all-intents) , kivéve a nincs szándék.<br>[Nincs leképezés példa beszédmódok hozzáadása](#do-add-example-utterances-to-none-intent)|[Az összes lehetséges értékét kifejezés listák létrehozása](#dont-create-phrase-lists-with-all-the-possible-values)|
|[A javaslat szolgáltatás aktív tanulás](#do-leverage-the-suggest-feature-for-active-learning)|[Túl sok minták hozzáadása](#dont-add-many-patterns)|
|[Az alkalmazás teljesítményének figyelése](#do-monitor-the-performance-of-your-app)|[Betanítás, közzététel és minden hozzá egyetlen példa utterance](#dont-train-and-publish-with-every-single-example-utterance)|
|[Alkalmazás törzsének verziók használata](#do-use-versions-for-each-app-iteration)||

## <a name="do-define-distinct-intents"></a>Adja meg a különböző leképezések
Ellenőrizze, hogy a szöveg szóhasználati, az egyes leképezés csak az adott szándékot és a különböző leképezés nem átfedő. Például ha azt szeretné, hogy leírók utazási szabályok légitársaság repülőjáratok és a "Hotels" például egy alkalmazás, is választja, hogy ezek a területek tulajdonos külön leképezések vagy azonos célja az entitások az utterance (kifejezés) belül meghatározott adatok.

A két szándék közötti szószedet megegyezik, ha a leképezés egyesítése, és használjon az entitást. 

Vegye figyelembe a következő példa kimondott szöveg:

|Példák kimondott szövegekre|
|--|
|Könyvet a repülőút|
|Egy szállodai Vendég könyv|

"Például book repülőjegyet" és a "egy szállodai Vendég például Book" használata a azonos szóhasználatát "könyv egy". Ez a formátum akkor ugyanaz, így azonos célja a flight és Szálloda különböző szóból álló kinyert entitásokként kell lennie. 

További információk:
* Fogalom: [A LUIS-alkalmazás a leképezések kapcsolatos fogalmak](luis-concept-intent.md)
* Oktatóanyag: [Felhasználói céljaira meghatározni a LUIS-alkalmazás létrehozása](luis-quickstart-intents-only.md)
* Útmutató: [Leképezések meghatározni a felhasználó szándékának beszédmódok hozzáadása](luis-how-to-add-intents.md)


## <a name="do-find-sweet-spot-for-intents"></a>Édes helyszíni keresése leképezések
Előrejelzési adatokat a LUIS segítségével meghatározhatja, amennyiben a szándék átfedésben. Átfedésben lévő leképezések LUIS tévessze össze. Eredménye, hogy a leképezés pontozási felső túl van egy másik szándékot. LUIS oktatási minden alkalommal, amikor nem használja a pontos ugyanazt az elérési utat az adatok között, mert átfedő megjelölésű rendelkezik, hogy az első és második képzési lehetőséget. Azt szeretné, hogy minden egyes szándékát, hogy lehet távolabb egymástól, így nem fordulhat elő, a Tükrözés vagy oldalirányú fordítás pontszámának az utterance (kifejezés). A leképezések jó különbséget kell eredményeznie a várt felső célt minden alkalommal. 
 
## <a name="do-build-the-app-iteratively"></a>Az alkalmazás ismételt létrehozása
Kimondott szöveg, amely nem egy külön készlete tartsa [példa utterances](luis-concept-utterance.md) vagy a végpont kimondott szöveg. Az alkalmazás a tesztelési készlethez tartozó fejlettebbek. Alkalmazkodjon a vizsgálatot, hogy az megfeleljen a valós felhasználói kimondott szöveg. Ez a vizsgálat beállítása kiértékelése minden egyes ismétlés vagy az alkalmazás verzióját használja. 

Fejlesztők számára az adatok három különböző kell rendelkeznie. Az első példa megcímkézzen a modell létrehozásához az. A második pedig a modellt, a végpont tesztelése. A harmadik pedig a vakok Tesztadatok használt [batch tesztelés](luis-how-to-batch-test.md). Az utolsó beállítása nem szerepel az alkalmazás képzési sem küldött a végponton.  

További információk:
* Fogalom: [A LUIS-alkalmazás az Authoring Tool ciklus](luis-concept-app-iteration.md)

## <a name="do-add-phrase-lists-and-patterns-in-later-iterations"></a>Újabb iterációban kifejezés listák és a minták hozzáadása

Ajánlott eljárás, hogy ezek az eljárások nem alkalmaznia az alkalmazás tesztelve lett. Ismerje meg az alkalmazás viselkedésének hozzáadása kifejezés listák és a minták, mivel ezek a funkciók részletesebben, mint például utterances súlyozott vannak, és magabiztosan fog döntés előtt. 

Ha már megismerte, hogyan viselkedik az alkalmazás ezek hiányában, adja hozzá a ezeket a funkciókat alkalmazni kell az alkalmazást. Nem kell hozzáadnia ezeket a funkciókat minden egyes [iteráció](luis-concept-app-iteration.md) vagy módosítsa a funkciók egyes verzióival. 

Nem árt hozzáadják őket a modell felépítésének elején, de egyszerűbb meg, hogyan alapdokumentációjában változik-e eredmények után a szolgáltatás a modell tesztelése kimondott szöveg. 

Ajánlott eljárás, hogy keresztül tesztelése a [végpont](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance) annak érdekében, hogy a további előnnyel [aktív tanulás](luis-concept-review-endpoint-utterances.md). A [interaktív vizsgálati ablaktáblán](luis-interactive-test.md) érvényes tesztelési módszer is van. 
 

### <a name="phrase-lists"></a>Kifejezéslisták

[Listák kifejezés](luis-concept-feature.md) alkalmazástartománya kapcsolódó szó szótárak meghatározhatja. A kifejezés néhány szó a listában, majd használja a javaslat funkciót, így az alkalmazás további szavak meghatározott szókészletet ismer LUIS kezdőérték. Kifejezések listáját szándék észleléséhez és az entitás osztályozás növeli a szavakat vagy kifejezéseket, jelentős az alkalmazáshoz társított jel kiemelése. 

Minden szó nem fel, a szöveg szóhasználati, mert a kifejezéslista nem pontos egyezést. 

További információk:
* Fogalom: [A LUIS-alkalmazás a kifejezés szolgáltatásai](luis-concept-feature.md)
* Útmutató: [Használja a kifejezés boost jelre a word lista sorolja fel](luis-how-to-add-features.md)

### <a name="patterns"></a>Minták

Valós felhasználói utterances a végpontról, nagyon hasonlít egymáshoz, felfedheti az word választási lehetőséget és elhelyezési mintáinak. A [minta](luis-concept-patterns.md) funkció leveszi a word választási lehetőség és a reguláris kifejezések együtt elhelyezési az előrejelzési pontosság növeléséhez. A mintában egy reguláris kifejezést lehetővé teszi a szavak és írásjelek, miközben továbbra is a mintának megfelelő figyelmen kívül kívánja. 

Használja a minta [választható szintaxis](luis-concept-patterns.md) az absztrakt, ezért írásjelek figyelmen kívül hagyható. Használja a [explicit lista](luis-concept-patterns.md#explicit-lists) pattern.any szintaktikai problémák kiegyenlítése érdekében. 

További információk:
* Fogalom: [Minták előrejelzési pontosság növeléséhez.](luis-concept-patterns.md)
* Útmutató: [Az előrejelzési pontosság növeléséhez minták hozzáadása](luis-how-to-model-intent-pattern.md)

## <a name="balance-your-utterances-across-all-intents"></a>Minden leképezések között a kimondott szöveg terheléselosztása

Ahhoz, hogy pontosak, LUIS-előrejelzéseket az egyes szándékot (kivéve a nincs szándék), például utterances mennyisége viszonylag egyenlőnek kell lennie. 

Ha rendelkezik a 100 példa utterances megjelölésű és a 20 példa utterances megjelölésű, 100-utterance (kifejezés) célja előrejelzési nagyobb mértékű kell.  

## <a name="do-add-example-utterances-to-none-intent"></a>Nincs leképezés példa beszédmódok hozzáadása

A célja a tartalék szándékkal, jelzett mindent az alkalmazáson kívül. Egy példa utterance (kifejezés) hozzáadása a nincs szándék, a többi része a LUIS-alkalmazás minden 10 példa kimondott szöveg.

További információk:
* Fogalom: [Jó utterances Mik a LUIS alkalmazás ismertetése](luis-concept-utterance.md)

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Kihasználhatja a javaslat funkciót aktív tanulás

Használat [aktív tanulás](luis-how-to-review-endpoint-utterances.md)a **tekintse át a végpont utterances** rendszeres időközönként, szándék további példa beszédmódok hozzáadása helyett. Az alkalmazás végponti utterances folyamatosan fogad, mivel a lista egyre növekvő és módosítása.

További információk:
* Fogalom: [Aktív tanulás engedélyezésének végpont utterances áttekintésével kapcsolatos fogalmak](luis-concept-review-endpoint-utterances.md)
* Oktatóanyag: [Oktatóanyag: Javítsa ki a nem tudja, hogy előrejelzéseket végpont utterances áttekintésével](luis-tutorial-review-endpoint-utterances.md)
* Útmutató: [A LUIS-portál végpontja utterances felülvizsgálata](luis-how-to-review-endpoint-utterances.md)

## <a name="do-monitor-the-performance-of-your-app"></a>Az alkalmazás teljesítményének figyelése

Az előrejelzési pontosság használatával figyelheti a [batch teszt](luis-concept-batch-test.md) beállítása. 

## <a name="dont-add-many-example-utterances-to-intents"></a>Nem sok példa utterances leképezések hozzáadása

Az alkalmazás közzététele után csak beszédmódok hozzáadása a aktív tanulás a iteratív folyamat. Ha kimondott szöveg túl hasonló, adjon hozzá egy minta. 

## <a name="dont-use-luis-as-a-training-platform"></a>Ne használja a LUIS képzési platform

A LUIS csak a nyelvi modell tartományhoz. Ez nem arra készült, általános természetes nyelvi képzési platformként működik. 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Nem sok példa beszédmódok hozzáadása, ugyanazt a formátumot, figyelmen kívül hagyja a többi formátumok

A LUIS változata létezik egy leképezés kimondott szöveg vár. A kimondott szöveg azonos teljes jelentésű során változhatnak. Változatok utterance (kifejezés) hossza, a word választási lehetőség és a word elhelyezési is tartalmazhat. 

|Ne használja ugyanazt a formátumot|Különböző formátumot használja.|
|--|--|
|Seattle jegyet vásárolni<br>Vásároljon egy jegyet, Párizs<br>A jegy Orlandóban vásárlása|Seattle 1 jegyet vásárolni<br>Két hely foglalására két Párizsba piros szemmel a következő hétfőn<br>Szeretném, ha lefoglalja 3 jegyek Orlandóban, spring break|

A második oszlop használja különböző műveletek (vételi, a tartalék, a könyvet.), a másik mennyiségek (1, két, 3), és különböző elrendezésben szavak, de az összes van utazási légitársaság jegyek vásárolhat azonos szándékkal. 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Ne keverje a szándékok és entitások definíciója

Hozzon létre minden művelethez megjelölésű robotjait vesz igénybe. Entitások használják, amelyek lehetővé teszik, hogy a műveleti paraméterek. 

Egy csevegőrobot, amely fog repülőjáratra légitársaság repülőjáratok, hozzon létre egy **BookFlight** szándékot. Ne hozzon létre minden egyes légitársaság vagy minden cél megjelölésű. Használja az adatok az egyes elemek [entitások](luis-concept-entity-types.md) és a példa megcímkézzen jelölje meg őket. 

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>Ne hozzon létre kifejezés listák az összes lehetséges értékét

Adja meg a néhány példa a [listák kifejezés](luis-concept-feature.md) , de nem minden szó. A LUIS általánosítja, és figyelembe veszi a környezetben. 

## <a name="dont-add-many-patterns"></a>Ne adja hozzá a sok minták

Nem túl sok [minták](luis-concept-patterns.md). A LUIS ismerje meg gyorsan, kevesebb példákkal szolgál. A rendszer feleslegesen túlterhelni nem.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Nem betanításához használatával létrehozott és közzétett minden egyetlen példa utterance (kifejezés)

10 vagy 15 beszédmódok hozzáadása a képzés és a közzététel előtt. Lehetővé teszi a előrejelzési pontosság-azonosítókra gyakorolt hatást. Egy egyetlen utterance (kifejezés) hozzáadása nincs hatással lehet egy látható a pontszámot. 

## <a name="do-use-versions-for-each-app-iteration"></a>Alkalmazás törzsének verziók használata

Minden szerzői ciklust kell lennie egy új [verzió](luis-concept-version.md), klónozott egy meglévő verzióból. A LUIS-verziók korlátlan rendelkezik. A verziónév az API-útvonal részeként történik, ezért fontos, hogy válasszon ki egy URL-címet az engedélyezett, valamint a verziók a 10 karakterszám belül tartása karakter. Alakítson ki vannak rendszerezve a verzió megtartása verzió neve stratégiát. 

További információk:
* Fogalom: [Megismerheti, hogyan és mikor a LUIS-verzió használatához](luis-concept-version.md)
* Útmutató: [Verziók segítségével módosíthatja, és átmeneti és éles alkalmazások befolyásolása nélkül tesztelése](luis-how-to-manage-versions.md)


## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [az alkalmazás megtervezése](luis-how-plan-your-app.md) a LUIS-alkalmazás található.
