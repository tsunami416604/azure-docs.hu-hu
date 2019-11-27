---
title: Ajánlott eljárások a LUIS-alkalmazás létrehozásához
titleSuffix: Azure Cognitive Services
description: Ismerje meg az ajánlott eljárásokat a LUIS-alkalmazás modelljéből származó legjobb eredmények eléréséhez.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: b4be79338db71ad83204fae971da0b77885a8070
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280927"
---
# <a name="best-practices-for-building-a-language-understanding-luis-app"></a>Ajánlott eljárások a Language Understanding (LUIS) alkalmazások létrehozásához
A LUIS-alkalmazás létrehozásához használja az alkalmazás-létrehozási folyamatot: 

* Nyelvi modellek (szándékok és entitások) létrehozása
* Vegyen fel néhány betanítási példát a hosszúságú kimondott szöveg (15-30/szándék)
* Közzététel a végponton
* A végpont tesztelése 

Miután [közzétette](luis-how-to-publish-app.md)az alkalmazást, a fejlesztési életciklus használatával hozzáadhat szolgáltatásokat, közzétehet és tesztelheti a végpontról. Ne kezdje el a következő szerzői ciklust további példa hosszúságú kimondott szöveg hozzáadásával, mivel ez nem teszi lehetővé, hogy a LUIS a valós felhasználói hosszúságú kimondott szöveg segítségével Ismerje meg a modellt. 

Ne bontsa ki a hosszúságú kimondott szöveg, amíg a példa és a végpontok hosszúságú kimondott szöveg aktuális készlete nem ad vissza magabiztosan, nagy előrejelzési pontszámokat. Az [aktív tanulással](luis-concept-review-endpoint-utterances.md)javíthatja a pontszámokat. 




## <a name="do-and-dont"></a>Hajtsa végre, és nem
Az alábbi lista tartalmazza az ajánlott eljárások a LUIS-alkalmazások:

|Ajánlott|Nem ajánlott|
|--|--|
|[Különböző leképezések definiálása](#do-define-distinct-intents)<br>[Descripters hozzáadása a szándékokhoz](#do-add-descriptors-to-intents) |[Számos példa hosszúságú kimondott szöveg hozzáadása a leképezésekhez](#dont-add-many-example-utterances-to-intents)<br>[Néhány vagy egyszerű entitás használata](#dont-use-few-or-simple-entities) |
|[Keressen egy édes helyet a túl általános és az egyes szándékok között](#do-find-sweet-spot-for-intents)|[A LUIS használata képzési platformként](#dont-use-luis-as-a-training-platform)|
|[Az alkalmazás iteratív létrehozása verziókkal](#do-build-your-app-iteratively-with-versions)<br>[Entitások létrehozása a modell elbomlásához](#do-build-for-model-decomposition)|[Több, azonos formátumú hosszúságú kimondott szöveg hozzáadása, figyelmen kívül hagyva más formátumok](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Mintázatok hozzáadása a későbbi ismétlésekben](#do-add-patterns-in-later-iterations)|[A leképezések és entitások definíciójának összekeverése](#dont-mix-the-definition-of-intents-and-entities)|
|A [hosszúságú kimondott szöveg egyensúlyt kell kiosztania az összes](#balance-your-utterances-across-all-intents) szándékon, kivéve a none szándékot.<br>[Példa hosszúságú kimondott szöveg hozzáadása nincs szándékhoz](#do-add-example-utterances-to-none-intent)|[Leírók létrehozása az összes lehetséges értékkel](#dont-create-descriptors-with-all-the-possible-values)|
|[Az aktív tanulásra vonatkozó javaslati funkció kihasználása](#do-leverage-the-suggest-feature-for-active-learning)|[Túl sok mintázat hozzáadása](#dont-add-many-patterns)|
|[Az alkalmazás teljesítményének figyelése batch-teszteléssel](#do-monitor-the-performance-of-your-app)|[Betanítás és közzététel minden egyes példa Kimondás esetén](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>Adja meg a különböző leképezések
Ellenőrizze, hogy a szöveg szóhasználati, az egyes leképezés csak az adott szándékot és a különböző leképezés nem átfedő. Ha például olyan alkalmazást szeretne használni, amely az utazási szabályokat, például a légitársasági repülőjáratokat és a szállodákat kezeli, dönthet úgy, hogy ezeket a témaköröket külön szándékként vagy azonos szándékkal látja el a részletekben lévő konkrét adategységekhez.

A két szándék közötti szószedet megegyezik, ha a leképezés egyesítése, és használjon az entitást. 

Vegye figyelembe a következő példa kimondott szöveg:

|Példák kimondott szövegekre|
|--|
|Könyvet a repülőút|
|Egy szállodai Vendég könyv|

`Book a flight` és `Book a hotel` ugyanazt a szókincset használja `book a `. Ez a formátum ugyanaz, mint a `flight` különböző szavaival, és `hotel` kinyert entitásként. 

## <a name="do-add-descriptors-to-intents"></a>Adja hozzá a leírókat a szándékokhoz

A descripters súgója egy szándék szolgáltatásait ismerteti. A leíró lehet olyan szavak felsorolása, amelyek jelentősek az adott szándék szempontjából, vagy olyan entitások, amelyek jelentősek erre a célra. 

## <a name="do-find-sweet-spot-for-intents"></a>Édes helyszíni keresése leképezések
Előrejelzési adatokat a LUIS segítségével meghatározhatja, amennyiben a szándék átfedésben. Az átfedésben lévő leképezések megzavarják a LUIS-t. Eredménye, hogy a leképezés pontozási felső túl van egy másik szándékot. LUIS oktatási minden alkalommal, amikor nem használja a pontos ugyanazt az elérési utat az adatok között, mert átfedő megjelölésű rendelkezik, hogy az első és második képzési lehetőséget. Azt szeretné, hogy a teljes pontszám az egyes céloknál távolabb legyen, így ez a flip/flop nem fog történni. A leképezések jó különbséget kell eredményeznie a várt felső célt minden alkalommal. 
 
<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>Készítse el az alkalmazás iteratív a verziókkal

Minden authoring ciklusnak egy meglévő verzióról klónozott új [verzióba](luis-concept-version.md)kell esnie. 

## <a name="do-build-for-model-decomposition"></a>Build a Model dekompozícióhoz

A modell kibontása a következőket jellemző folyamattal rendelkezik:

* **szándék** létrehozása ügyfél-alkalmazás felhasználói szándékai alapján
* 15-30-es példa hosszúságú kimondott szöveg hozzáadása a valós felhasználói bevitel alapján
* a legfelső szintű adatelemzési koncepció megjelölése példaként
* az adatkoncepció felosztása alösszetevőkbe
* descripters (szolgáltatások) hozzáadása alösszetevőkhöz
* leíró (szolgáltatások) hozzáadása a szándékhoz 

Miután létrehozta a szándékot, és hozzáadta például a hosszúságú kimondott szöveg-t, az alábbi példa az entitások lebomlását ismerteti. 

Első lépésként azonosítsa a kinyerni kívánt teljes adatfogalmakat. Ez a gép által megtanult entitás. Ezután bontsa ki a kifejezést a részébe. Ide tartozik az alösszetevők (entitások) azonosítása, valamint a leírók és a megkötések. 

Ha például egy címeket szeretne kinyerni, a legfelső szintű géppel megismert entitást `Address`lehet meghívni. A cím létrehozásakor azonosítsa a hozzá tartozó alösszetevők némelyikét, például a házszámot, a várost, az állapotot és az irányítószámot. 

Folytassa ezen elemek kibontását azáltal, hogy **korlátozza** a postai kódot egy reguláris kifejezésre. Az utcanév kibontása az utca egy részébe (egy előre elkészített szám használatával), egy utcanév és egy utca típusa. Az utca típusát **leíró** listával (például Avenue, Circle, Road és Lane) lehet leírni.

A v3 authoring API lehetővé teszi a modellek lebomlását. 

## <a name="do-add-patterns-in-later-iterations"></a>Minták hozzáadása a későbbi iterációkban

Érdemes tisztában lennie azzal, hogy az alkalmazás hogyan viselkedik a [mintázatok](luis-concept-patterns.md) hozzáadása előtt, mivel a mintákat sokkal nagyobb mértékben súlyozzák, mint például a hosszúságú kimondott szöveg, és a megbízhatóságot is rontja. 

Miután megértette, hogyan viselkedik az alkalmazás, vegyen fel mintákat, ahogyan azok az alkalmazásra vonatkoznak. Az egyes [iterációk](luis-concept-app-iteration.md)nem szükségesek hozzájuk. 

A modell kialakításának elején nem árt felvenni a problémát, de könnyebben láthatja, hogy az egyes minták Hogyan változnak a modell hosszúságú kimondott szöveg való tesztelése után. 
 
<!--

### Phrase lists

[Phrase lists](luis-concept-feature.md) allow you to define dictionaries of words related to your app domain. Seed your phrase list with a few words then use the suggest feature so LUIS knows about more words in the vocabulary specific to your app. A Phrase List improves intent detection and entity classification by boosting the signal associated with words or phrases that are significant to your app. 

Don't add every word to the vocabulary since the phrase list isn't an exact match. 

For more information:
* Concept: [Phrase list features in your LUIS app](luis-concept-feature.md)
* How-to: [Use phrase lists to boost signal of word list](luis-how-to-add-features.md)



### Patterns

Real user utterances from the endpoint, very similar to each other, may reveal patterns of word choice and placement. The [pattern](luis-concept-patterns.md) feature takes this word choice and placement along with regular expressions to improve your prediction accuracy. A regular expression in the pattern allows for words and punctuation you intend to ignore while still matching the pattern. 

Use pattern's [optional syntax](luis-concept-patterns.md) for punctuation so punctuation can be ignored. Use the [explicit list](luis-concept-patterns.md#explicit-lists) to compensate for pattern.any syntax issues. 

For more information:
* Concept: [Patterns improve prediction accuracy](luis-concept-patterns.md)
* How-to: [How to add Patterns to improve prediction accuracy](luis-how-to-model-intent-pattern.md)
-->

<a name="balance-your-utterances-across-all-intents"></a>

## <a name="do-balance-your-utterances-across-all-intents"></a>A hosszúságú kimondott szöveg egyensúlyba vétele az összes cél között

Ahhoz, hogy a LUIS-előrejelzések pontosak legyenek, az egyes szándékok (kivéve a none szándékot) hosszúságú kimondott szöveg mennyiségének viszonylag egyenlőnek kell lennie. 

Ha az 100-es hosszúságú kimondott szöveg és egy 20 példás hosszúságú kimondott szöveg, akkor a 100-Kimondás szándéka magasabb előrejelzési aránnyal fog rendelkezni.  

## <a name="do-add-example-utterances-to-none-intent"></a>Nincs leképezés példa beszédmódok hozzáadása

Ez a szándék a tartalék szándék, amely az alkalmazáson kívül mindent jelez. Egy példa utterance (kifejezés) hozzáadása a nincs szándék, a többi része a LUIS-alkalmazás minden 10 példa kimondott szöveg.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Kihasználhatja a javaslat funkciót aktív tanulás

Az [aktív tanulás](luis-how-to-review-endpoint-utterances.md) **felülvizsgálati végpontjának hosszúságú kimondott szöveg** rendszeresen használhatja, ahelyett, hogy további példákat hosszúságú kimondott szöveg hozzá. Az alkalmazás végponti utterances folyamatosan fogad, mivel a lista egyre növekvő és módosítása.

## <a name="do-monitor-the-performance-of-your-app"></a>Az alkalmazás teljesítményének figyelése

Figyelje meg az előrejelzés pontosságát egy [Batch-tesztkörnyezet](luis-concept-batch-test.md) használatával. 

Tartsa meg a [hosszúságú kimondott szöveg](luis-concept-utterance.md) vagy a végpont hosszúságú kimondott szöveg nem használt különálló hosszúságú kimondott szöveg. Az alkalmazás a tesztelési készlethez tartozó fejlettebbek. Alkalmazkodjon a vizsgálatot, hogy az megfeleljen a valós felhasználói kimondott szöveg. Ezzel a tesztelési készlettel értékelheti ki az alkalmazás minden egyes iterációját vagy verzióját. 

## <a name="dont-add-many-example-utterances-to-intents"></a>Nem sok példa utterances leképezések hozzáadása

Az alkalmazás közzététele után csak a fejlesztési életciklus folyamatában hosszúságú kimondott szöveg adhat hozzá az aktív tanulásból. Ha kimondott szöveg túl hasonló, adjon hozzá egy minta. 

## <a name="dont-use-few-or-simple-entities"></a>Ne használjon néhány vagy egyszerű entitást

Az entitások az kinyeréshez és előrejelzéshez készültek. Fontos, hogy az egyes szándékok géppel megtanult entitásokat adjanak meg, amelyek leírják a szándékban lévő adatkészleteket. Ez segíti a LUIS előrejelzését abban az esetben is, ha az ügyfélalkalmazás nem kell a kibontott entitást használnia. 

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

Egy olyan robot esetében, amely légitársasági repülőjáratokat fog foglalni, hozzon létre egy **BookFlight** szándékot. Ne hozzon létre minden egyes légitársaság vagy minden cél megjelölésű. Ezeket az [adategységeket entitásként](luis-concept-entity-types.md) használhatja, és megjelölheti őket a példában szereplő hosszúságú kimondott szöveg. 

## <a name="dont-create-descriptors-with-all-the-possible-values"></a>Ne hozzon létre leírókat az összes lehetséges értékkel

Adjon meg néhány példát a leíró [kifejezések listájában](luis-concept-feature.md) , de ne minden szót. A LUIS általánosítja, és figyelembe veszi a környezetben. 

## <a name="dont-add-many-patterns"></a>Ne adja hozzá a sok minták

Ne adjon hozzá túl sok [mintázatot](luis-concept-patterns.md). A LUIS ismerje meg gyorsan, kevesebb példákkal szolgál. A rendszer feleslegesen túlterhelni nem.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Nem betanításához használatával létrehozott és közzétett minden egyetlen példa utterance (kifejezés)

10 vagy 15 beszédmódok hozzáadása a képzés és a közzététel előtt. Lehetővé teszi a előrejelzési pontosság-azonosítókra gyakorolt hatást. Egy egyetlen utterance (kifejezés) hozzáadása nincs hatással lehet egy látható a pontszámot. 

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan [tervezze meg alkalmazását](luis-how-plan-your-app.md) a Luis-alkalmazásban.
