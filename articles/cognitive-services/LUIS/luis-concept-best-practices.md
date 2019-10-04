---
title: Ajánlott eljárások – LUIS
titleSuffix: Azure Cognitive Services
description: Ismerje meg, a LUIS gyakorlati tanácsok a legjobb eredményt a LUIS-alkalmazás modellből.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: 91ff99f674439580d369aad1490ded85d39d377c
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382878"
---
# <a name="best-practices-for-building-a-language-understanding-app-with-cognitive-services"></a>Ajánlott eljárások a Cognitive Services language understanding alkalmazás létrehozásához
A LUIS-alkalmazás létrehozásához használja az alkalmazás-létrehozási folyamatot: 

* Nyelvi modell létrehozása
* Néhány képzési példa (10 – 15 / leképezés) beszédmódok hozzáadása
* Közzététel 
* A végpont tesztelése 
* Funkciók hozzáadása

Miután [közzétette](luis-how-to-publish-app.md)az alkalmazást, használja a szerzői ciklust a szolgáltatások hozzáadásához, közzétételéhez és teszteléséhez a végpontról. Ne kezdje el a következő szerzői ciklus további példa utterances hozzáadásával. Amely nem teszi lehetővé a LUIS ismerje meg, a modell a való életből vett felhasználói kimondott szöveg. 

Ahhoz, hogy a feladat a tanulás, hatékony, LUIS nem bontsa ki a kimondott szöveg mindaddig, amíg az aktuális készletét példa és a végpont utterances vannak visszaadó biztosabb, magas előrejelzési pontszámokat. A pontszámok fejlesztése az [aktív tanulás](luis-concept-review-endpoint-utterances.md), a [minták](luis-concept-patterns.md)és a [kifejezések listáját](luis-concept-feature.md)használva. 

## <a name="do-and-dont"></a>Hajtsa végre, és nem
Az alábbi lista tartalmazza az ajánlott eljárások a LUIS-alkalmazások:

|Ajánlott|Nem ajánlott|
|--|--|
|[Adja meg a különböző leképezések](#do-define-distinct-intents) |[Számos példa utterances leképezések hozzáadása](#dont-add-many-example-utterances-to-intents) |
|[Keres édes helyet túl általános, és túl adott között a minden egyes leképezés](#do-find-sweet-spot-for-intents)|[Használja a LUIS képzési platform](#dont-use-luis-as-a-training-platform)|
|[Iteratív az alkalmazás létrehozása](#do-build-the-app-iteratively)|[Ugyanazt a formátumot, figyelmen kívül hagyja a más formátumú, sok példa beszédmódok hozzáadása](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Újabb iterációban kifejezés listák és a minták hozzáadása](#do-add-phrase-lists-and-patterns-in-later-iterations)|[Vegyes szándékok és entitások definíciója](#dont-mix-the-definition-of-intents-and-entities)|
|A [hosszúságú kimondott szöveg egyensúlyt kell kiosztania az összes](#balance-your-utterances-across-all-intents) szándékon, kivéve a none szándékot.<br>[Nincs leképezés példa beszédmódok hozzáadása](#do-add-example-utterances-to-none-intent)|[Az összes lehetséges értékét kifejezés listák létrehozása](#dont-create-phrase-lists-with-all-the-possible-values)|
|[A javaslat szolgáltatás aktív tanulás](#do-leverage-the-suggest-feature-for-active-learning)|[Túl sok mintázat hozzáadása](#dont-add-many-patterns)|
|[Az alkalmazás teljesítményének figyelése](#do-monitor-the-performance-of-your-app)|[Betanítás, közzététel és minden hozzá egyetlen példa utterance](#dont-train-and-publish-with-every-single-example-utterance)|
|[Verziók használata az egyes alkalmazások iterációhoz](#do-use-versions-for-each-app-iteration)||

## <a name="do-define-distinct-intents"></a>Adja meg a különböző leképezések
Ellenőrizze, hogy a szöveg szóhasználati, az egyes leképezés csak az adott szándékot és a különböző leképezés nem átfedő. Ha például olyan alkalmazást szeretne használni, amely az utazási szabályokat, például a légitársasági repülőjáratokat és a szállodákat kezeli, dönthet úgy, hogy ezeket a témaköröket külön szándékként vagy azonos szándékkal látja el a részletekben lévő konkrét adategységekhez.

A két szándék közötti szószedet megegyezik, ha a leképezés egyesítése, és használjon az entitást. 

Vegye figyelembe a következő példa kimondott szöveg:

|Példák kimondott szövegekre|
|--|
|Könyvet a repülőút|
|Egy szállodai Vendég könyv|

"Például book repülőjegyet" és a "egy szállodai Vendég például Book" használata a azonos szóhasználatát "könyv egy". Ez a formátum ugyanaz, mint a Flight és a Hotel különböző szavaival kibontott entitásoknak. 

További információk:
* Koncepció [A LUIS-alkalmazásban található szándékokkal kapcsolatos fogalmak](luis-concept-intent.md)
* Oktatóanyag: [LUIS-alkalmazás létrehozása a felhasználói szándékok meghatározásához](luis-quickstart-intents-only.md)
* Útmutató: [Leképezések hozzáadása a hosszúságú kimondott szöveg felhasználói szándékának meghatározásához](luis-how-to-add-intents.md)


## <a name="do-find-sweet-spot-for-intents"></a>Édes helyszíni keresése leképezések
Előrejelzési adatokat a LUIS segítségével meghatározhatja, amennyiben a szándék átfedésben. Az átfedésben lévő leképezések megzavarják a LUIS-t. Eredménye, hogy a leképezés pontozási felső túl van egy másik szándékot. LUIS oktatási minden alkalommal, amikor nem használja a pontos ugyanazt az elérési utat az adatok között, mert átfedő megjelölésű rendelkezik, hogy az első és második képzési lehetőséget. Azt szeretné, hogy a teljes pontszám az egyes céloknál távolabb legyen, így ez a flip/flop nem fog történni. A leképezések jó különbséget kell eredményeznie a várt felső célt minden alkalommal. 
 
## <a name="do-build-the-app-iteratively"></a>Az alkalmazás ismételt létrehozása
Tartsa meg a hosszúságú kimondott szöveg különálló készletét, amely nem használható [példaként hosszúságú kimondott szöveg](luis-concept-utterance.md) vagy Endpoint hosszúságú kimondott szöveg. Az alkalmazás a tesztelési készlethez tartozó fejlettebbek. Alkalmazkodjon a vizsgálatot, hogy az megfeleljen a valós felhasználói kimondott szöveg. Ezzel a tesztelési készlettel értékelheti ki az alkalmazás minden egyes iterációját vagy verzióját. 

Fejlesztők számára az adatok három különböző kell rendelkeznie. Az első példa megcímkézzen a modell létrehozásához az. A második pedig a modellt, a végpont tesztelése. A harmadik pedig a vakok Tesztadatok használt [batch tesztelés](luis-how-to-batch-test.md). Ez az utolsó készlet nem használatos az alkalmazás betanításakor és a végponton való elküldésekor.  

További információk:
* Koncepció [A LUIS-alkalmazás szerzői ciklusa](luis-concept-app-iteration.md)

## <a name="do-add-phrase-lists-and-patterns-in-later-iterations"></a>Újabb iterációban kifejezés listák és a minták hozzáadása

Az ajánlott eljárás az, hogy az alkalmazás tesztelése előtt ne alkalmazza ezeket a gyakorlatot. Meg kell értenie, hogy az alkalmazás hogyan viselkedik a [kifejezések listáját](luis-concept-feature.md) és [mintázatait](luis-concept-patterns.md) felhasználva, mivel ezek a funkciók sokkal nagyobb mértékben vannak kiértékelve, mint például a hosszúságú kimondott szöveg, és a megbízhatóságot. 

Ha megértette, hogy az alkalmazás ezek nélkül viselkedik, adja hozzá ezeket a funkciókat az alkalmazásához. Ezeket a funkciókat nem kell hozzáadnia minden egyes [iterációhoz](luis-concept-app-iteration.md) , vagy módosítania kell a szolgáltatásokat az egyes verziókkal. 

A modell kialakításának elején nem árt felvenni a problémát, de könnyebben láthatja, hogy az egyes szolgáltatások hogyan változnak az eredmények a modell hosszúságú kimondott szöveg való tesztelése után. 

Az ajánlott eljárás az, hogy a [végponton](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) keresztül tesztelje az [aktív tanulás](luis-concept-review-endpoint-utterances.md)előnyeit. Az [interaktív tesztelési panel](luis-interactive-test.md) is érvényes tesztelési módszer. 
 

### <a name="phrase-lists"></a>Kifejezéslisták

[Listák kifejezés](luis-concept-feature.md) alkalmazástartománya kapcsolódó szó szótárak meghatározhatja. Sorolja fel a kifejezések listáját néhány Szóval, majd használja a javaslat funkciót, hogy LUIS tudja, hogy az alkalmazásra jellemző szókincsben több szó is van. A kifejezések listája javítja a leképezések észlelését és az entitások besorolását azáltal, hogy növeli az alkalmazás számára jelentős szavakhoz vagy kifejezésekhez kapcsolódó jeleket. 

Minden szó nem fel, a szöveg szóhasználati, mert a kifejezéslista nem pontos egyezést. 

További információk:
* Koncepció [A LUIS-alkalmazás kifejezés-listájának funkciói](luis-concept-feature.md)
* Útmutató: [A szólisták használata a szavak listájának növelésére](luis-how-to-add-features.md)

### <a name="patterns"></a>Minták

Valós felhasználói utterances a végpontról, nagyon hasonlít egymáshoz, felfedheti az word választási lehetőséget és elhelyezési mintáinak. A [minta](luis-concept-patterns.md) funkció leveszi a word választási lehetőség és a reguláris kifejezések együtt elhelyezési az előrejelzési pontosság növeléséhez. A mintában egy reguláris kifejezést lehetővé teszi a szavak és írásjelek, miközben továbbra is a mintának megfelelő figyelmen kívül kívánja. 

A minta nem [kötelező szintaxisa](luis-concept-patterns.md) a központozás, így a központozás figyelmen kívül hagyható. A [explicit listával](luis-concept-patterns.md#explicit-lists) kompenzálhatja a mintázatot. bármilyen szintaktikai probléma. 

További információk:
* Koncepció [A minták az előrejelzési pontosságot javítják](luis-concept-patterns.md)
* Útmutató: [Mintázatok hozzáadása az előrejelzési pontosság növeléséhez](luis-how-to-model-intent-pattern.md)

## <a name="balance-your-utterances-across-all-intents"></a>A hosszúságú kimondott szöveg egyensúlyba vétele az összes cél között

Ahhoz, hogy a LUIS-előrejelzések pontosak legyenek, az egyes szándékok (kivéve a none szándékot) hosszúságú kimondott szöveg mennyiségének viszonylag egyenlőnek kell lennie. 

Ha az 100-es hosszúságú kimondott szöveg és egy 20 példás hosszúságú kimondott szöveg, akkor a 100-Kimondás szándéka magasabb előrejelzési aránnyal fog rendelkezni.  

## <a name="do-add-example-utterances-to-none-intent"></a>Nincs leképezés példa beszédmódok hozzáadása

Ez a szándék a tartalék szándék, amely az alkalmazáson kívül mindent jelez. Egy példa utterance (kifejezés) hozzáadása a nincs szándék, a többi része a LUIS-alkalmazás minden 10 példa kimondott szöveg.

További információk:
* Koncepció [Ismerje meg, hogy a LUIS-alkalmazás milyen jó hosszúságú kimondott szöveg](luis-concept-utterance.md)

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Kihasználhatja a javaslat funkciót aktív tanulás

Használat [aktív tanulás](luis-how-to-review-endpoint-utterances.md)a **tekintse át a végpont utterances** rendszeres időközönként, szándék további példa beszédmódok hozzáadása helyett. Az alkalmazás végponti utterances folyamatosan fogad, mivel a lista egyre növekvő és módosítása.

További információk:
* Koncepció [Az aktív tanulás engedélyezésével kapcsolatos fogalmak a végpontok hosszúságú kimondott szöveg áttekintésével](luis-concept-review-endpoint-utterances.md)
* Oktatóanyag: [Oktatóanyag: A nem biztos előrejelzések kijavítása a végpontok hosszúságú kimondott szöveg áttekintésével](luis-tutorial-review-endpoint-utterances.md)
* Útmutató: [Az Endpoint hosszúságú kimondott szöveg áttekintése a LUIS portálon](luis-how-to-review-endpoint-utterances.md)

## <a name="do-monitor-the-performance-of-your-app"></a>Az alkalmazás teljesítményének figyelése

Figyelje meg az előrejelzés pontosságát egy [Batch-tesztkörnyezet](luis-concept-batch-test.md) használatával. 

## <a name="dont-add-many-example-utterances-to-intents"></a>Nem sok példa utterances leképezések hozzáadása

Az alkalmazás közzététele után csak beszédmódok hozzáadása a aktív tanulás a iteratív folyamat. Ha kimondott szöveg túl hasonló, adjon hozzá egy minta. 

## <a name="dont-use-luis-as-a-training-platform"></a>Ne használja a LUIS képzési platform

A LUIS csak a nyelvi modell tartományhoz. Nem az általános természetes nyelvi képzési platformként működik. 

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

## <a name="do-use-versions-for-each-app-iteration"></a>Verziók használata az egyes alkalmazások iterációhoz

Minden authoring ciklusnak egy meglévő verzióról klónozott új [verzióba](luis-concept-version.md)kell esnie. A LUIS nem rendelkezik korláttal a verziókhoz. A verzió neve az API-útvonal részeként van használatban, ezért fontos, hogy az URL-címekben engedélyezett karakterek legyenek kiválasztva, valamint a verziók 10 karakteres számának megtartása. A verziók rendszerezésének megtartásához fejlesszen ki egy Version Name stratégiát. 

További információk:
* Koncepció [A LUIS-verzió használatának ismertetése](luis-concept-version.md)
* Útmutató: [Verziók használata az átmeneti vagy éles alkalmazások befolyásolása nélkül](luis-how-to-manage-versions.md)


## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [az alkalmazás megtervezése](luis-how-plan-your-app.md) a LUIS-alkalmazás található.
