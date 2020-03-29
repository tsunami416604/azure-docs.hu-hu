---
title: Gyakorlati tanácsok a LUIS-alkalmazás létrehozásához
titleSuffix: Azure Cognitive Services
description: Ismerje meg az ajánlott eljárásokat a LUIS-alkalmazás modelljéből származó legjobb eredmények leértékeléséhez.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74280927"
---
# <a name="best-practices-for-building-a-language-understanding-luis-app"></a>Gyakorlati tanácsok a nyelvi megértési (LUIS) alkalmazások létrehozásához
Az alkalmazásszerzői folyamat segítségével hozhatja létre a LUIS-alkalmazást: 

* Nyelvi modellek (szándékok és entitások) létrehozása
* Adjon hozzá néhány betanítási példa utterances (15-30 leképezésenként)
* Közzététel a végpontra
* Tesztelés a végpontról 

Az alkalmazás [közzététele](luis-how-to-publish-app.md)után a fejlesztési életciklus segítségével funkciókat adhat hozzá, közzétehet és tesztelhet a végpontról. Ne kezdje el a következő szerzői ciklust további példakimondott szöveghozzáadásával, mert ez nem teszi lehetővé a LUIS számára, hogy valós felhasználói kimondott szövegekkel ismerje meg a modellt. 

Ne bontsa ki az utterances, amíg az aktuális készlet e példa és a végpont utterances advissza magabiztos, magas előrejelzési pontszámok. Az aktív tanulás segítségével javíthatja a [pontszámokat.](luis-concept-review-endpoint-utterances.md) 




## <a name="do-and-dont"></a>Ne és ne tegye
Az alábbi lista a LUIS-alkalmazásokra vonatkozó gyakorlati tanácsokat tartalmazza:

|Ajánlott|Nem ajánlott|
|--|--|
|[Különböző leképezések definiálása](#do-define-distinct-intents)<br>[Leírók hozzáadása a leképezésekhez](#do-add-descriptors-to-intents) |[Számos példa kimondott szöveg hozzáadása a leképezésekhez](#dont-add-many-example-utterances-to-intents)<br>[Kevés vagy egyszerű entitás használata](#dont-use-few-or-simple-entities) |
|[Keressen egy édes folt között túl általános és túl specifikus minden szándék](#do-find-sweet-spot-for-intents)|[A LUIS használata képzési platformként](#dont-use-luis-as-a-training-platform)|
|[Az alkalmazás iteratív módon való létrehozása verziókkal](#do-build-your-app-iteratively-with-versions)<br>[Entitások létrehozása a modell felbontásához](#do-build-for-model-decomposition)|[Adjon hozzá sok azonos formátumú példakimondott szöveget, figyelmen kívül hagyva más formátumokat](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Mintázatok hozzáadása későbbi ismétlésekben](#do-add-patterns-in-later-iterations)|[A szándékok és entitások meghatározásának összekeverése](#dont-mix-the-definition-of-intents-and-entities)|
|[Az utterances az összes szándék között,](#balance-your-utterances-across-all-intents) kivéve a Nincs szándék ot.<br>[Példakimondott szöveg hozzáadása a Nincs szándékhoz](#do-add-example-utterances-to-none-intent)|[Leírók létrehozása az összes lehetséges értékkel](#dont-create-descriptors-with-all-the-possible-values)|
|[Használja ki a javasolt funkciót az aktív tanuláshoz](#do-leverage-the-suggest-feature-for-active-learning)|[Túl sok minta hozzáadása](#dont-add-many-patterns)|
|[Az alkalmazás teljesítményének figyelése kötegelt teszteléssel](#do-monitor-the-performance-of-your-app)|[Betanítása és közzététele minden egyes példa utterance hozzáadott](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>Különböző leképezések definiálása
Győződjön meg arról, hogy az egyes szándékok szókincse csak erre a szándékra van, és nem fedi át egymást egy másik szándékkal. Például ha azt szeretné, hogy egy alkalmazás, amely kezeli az utazási megállapodások, például a légitársaságok járatai és szállodái, kiválaszthatja, hogy ezeket a tárgyterületeket külön szándékként vagy azonos szándékkal entitások adott adatok az utterance (kifejezés) belül.

Ha a szókincs két leképezések azonos, kombinálja a szándékot, és használja az entitásokat. 

Vegye figyelembe a következő példa utterances:

|Példák kimondott szövegekre|
|--|
|Foglaljon repülőjegyet|
|Foglaljon szállodát|

`Book a flight`és `Book a hotel` használja ugyanazt `book a `a szókincset. Ez a formátum ugyanaz, így meg kell ugyanazt `flight` `hotel` a szándékot a különböző szavakat, és a kinyert entitások. 

## <a name="do-add-descriptors-to-intents"></a>Leírók hozzáadása a leképezésekhez

A leírók segítenek a szándék kalkulátorai leírásában. A leíró lehet az adott szándék szempontjából jelentős szavak vagy az adott szándék szempontjából jelentős szavak kifejezéslistája. 

## <a name="do-find-sweet-spot-for-intents"></a>Ne találja édes helyet szándékok
A LUIS előrejelzési adataisegítségével határozza meg, hogy a szándékok átfedésben vannak-e. Egymást átfedő szándékok megzavarja a LUIS. Az eredmény az, hogy a legfelső pontozási szándék túl közel van egy másik szándékhoz. Mivel a LUIS nem használja pontosan ugyanazt az elérési utat az adatokon keresztül a betanítás minden alkalommal, egy egymást átfedő szándék van esélye, hogy az első vagy a második a képzésben. Azt szeretné, hogy az utterance (kifejezés) pontszáma minden szándékesetében távolabb legyen egymástól, így ez a flip/flop nem történik meg. A leképezések jó megkülönböztetésének minden alkalommal a várt felső szándékot kell eredményeznie. 
 
<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>Az alkalmazás iteratív módon való összeállítása verziókkal

Minden szerzői ciklusnak egy új [verzión](luis-concept-version.md)belül kell lennie, amelyet egy meglévő verzióról kell klónozni. 

## <a name="do-build-for-model-decomposition"></a>Ne épít a modell felbontása

A modell bomlásának tipikus folyamata van:

* **szándék** létrehozása az ügyfélalkalmazás felhasználói szándékai alapján
* 15–30 példa utterances hozzáadása valós felhasználói bevitel alapján
* címke legfelső szintű adatkoncepció példa utterance
* adatkoncepció felosztása alösszetevőkre
* leírók (szolgáltatások) hozzáadása alösszetevőkhöz
* leírók (funkciók) hozzáadása a szándékhoz 

Miután létrehozta a szándékot, és hozzáadott példa utterances, a következő példa ismerteti az entitás felbontását. 

Első ként azonosítsa a teljes adatfogalmakat szeretne kinyerni egy utterance (kifejezés). Ez a gép-tanult entitás. Ezután bomlik le a kifejezést a részeire. Ez magában foglalja az alösszetevők azonosítását (entitásként), valamint a leírókat és a megkötéseket. 

Ha például ki szeretne nyerni egy címet, a legfelső `Address`gép által megtanult entitás neve is megadható. A cím létrehozása közben azonosítsa néhány alösszetevőjét, például az utcacímet, a várost, az államot és az irányítószámot. 

Az irányítószám reguláris kifejezésre **való korlátozásával** folytassa az elemek törlését. Az utcanév lefektése egy utcaszám részeire (előre összeállított számmal), egy utcanévre és egy utcatípusra. Az utcatípus leírható egy **leírólistával,** például sugárral, körrel, úttal és sávval.

A V3 szerzői API lehetővé teszi a modell felbontását. 

## <a name="do-add-patterns-in-later-iterations"></a>Mintázatok hozzáadása későbbi ismétlésekben

Meg kell értenie, hogyan viselkedik az alkalmazás a minták hozzáadása [előtt,](luis-concept-patterns.md) mert a minták súlyozása nagyobb, mint a példa kimondott szöveg, és ferde megbízhatóságot. 

Ha tisztában van azzal, hogyan viselkedik az alkalmazása, adjon hozzá mintákat az alkalmazásra vonatkozó mintákhoz. Nem kell hozzáadni őket minden [iteráció](luis-concept-app-iteration.md). 

Nincs kár hozzátéve őket az elején a modell terv, de könnyebb látni, hogy az egyes minták hogyan módosítja a modell után a modell által tesztelt utterances. 
 
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

## <a name="do-balance-your-utterances-across-all-intents"></a>Az utterances kiegyensúlyozása az összes szándék között

Annak érdekében, hogy a LUIS-előrejelzések pontosak legyenek, az egyes szándékokban lévő példa kimondott szövegmennyiségnek (kivéve a Nincs szándékot) viszonylag egyenlőnek kell lennie. 

Ha van egy szándék 100 példa utterances és egy szándék 20 példa utterances, a 100-utterance (kifejezés) szándék nagyobb lesz az előrejelzési.  

## <a name="do-add-example-utterances-to-none-intent"></a>Példakimondott szöveg hozzáadása a Nincs szándékhoz

Ez a szándék a tartalék szándék, jelezve mindent az alkalmazáson kívül. Adjon hozzá egy példa utterance (kifejezés) a Nincs szándék minden 10 példa utterances a LUIS-alkalmazás többi részében.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Használja ki a javasolt funkciót az aktív tanuláshoz

Használja [az aktív tanulási](luis-how-to-review-endpoint-utterances.md)'s tekintse végpont **utterances** rendszeresen, ahelyett, hogy további példa utterances a szándékok. Mivel az alkalmazás folyamatosan megkapja a végpontkimondott szövegeket, ez a lista növekszik és változik.

## <a name="do-monitor-the-performance-of-your-app"></a>Az alkalmazás teljesítményének figyelése

Az előrejelzés pontosságának figyelése [kötegelt](luis-concept-batch-test.md) tesztkészlettel. 

Tartsa meg a kimondott szöveg, amely nem példa [utterances](luis-concept-utterance.md) vagy végpont kimondott szövegként nem használt külön. Tovább fejlesztheti az alkalmazást a tesztkészlethez. Igazítsa a tesztkészletet a valós felhasználói kimondott szövegnek megfelelően. Ezzel a tesztkészlettel kiértékelheti az alkalmazás egyes iterációit vagy verzióit. 

## <a name="dont-add-many-example-utterances-to-intents"></a>Ne adjon hozzá sok példa kimondott szöveget a szándékokhoz

Az alkalmazás közzététele után csak adja hozzá az aktív tanulás utterances a fejlesztési életciklus-folyamat. Ha az utterances túl hasonló, adjon hozzá egy mintát. 

## <a name="dont-use-few-or-simple-entities"></a>Ne használjon kevés vagy egyszerű entitást

Az entitások az adatok kinyerésére és előrejelzésére épülnek. Fontos, hogy minden szándék gép által megtanult entitások, amelyek leírják az adatokat a szándékban. Ez segít a LUIS-nak megjósolni a szándékot, még akkor is, ha az ügyfélalkalmazásnak nem kell használnia a kinyert entitást. 

## <a name="dont-use-luis-as-a-training-platform"></a>Ne használja a LUIS-t képzési platformként

A LUIS egy nyelvi modell tartományára jellemző. Ez nem azt jelentette, hogy a munka, mint egy általános természetes nyelvi képzési platform. 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Ne adjon hozzá sok azonos formátumú példakimondott szöveget, figyelmen kívül hagyva más formátumokat

A LUIS a szándék kimondott szövegének eltéréseket vár. A kimondott szöveg eltérő lehet, miközben ugyanazt az általános jelentését. A változatok közé tartozhat az utterance (kifejezés) hossza, a szóválasztás és a szóelhelyezés. 

|Ne használjon ugyanazt a formátumot|Használjon különböző formátumú|
|--|--|
|Vegyél egy jegyet Seattle-be<br>Vegyél egy jegyet Párizsba<br>Vegyél egy jegyet Orlando|Vásároljon 1 jegyet Seattle-be<br>Foglaljon két helyet a vörös szem, hogy Párizs jövő hétfőn<br>Szeretnék 3 jegyet foglalni Orlandóba a tavaszi szünetre|

A második oszlop különböző igéket (vétel, foglalás, könyv), különböző mennyiségeket (1, kettő, 3) és különböző szóelrendezéseket használ, de mindegyiknek ugyanaz a szándéka, hogy repülőjegyeket vásároljon az utazáshoz. 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Ne keverje a szándékok és entitások definícióját

Hozzon létre egy szándékot a robot által végrehajtott műveletekhez. Entitások használata olyan paraméterekként, amelyek lehetővé teszik ezt a műveletet. 

Egy olyan robot esetén, amely **BookFlight** repülőjegyet foglal, hozzon létre egy BookFlight-szándékot. Ne hozzon létre szándékot minden légitársaság vagy minden rendeltetési helyre. Használja ezeket az adatokat [entitásként,](luis-concept-entity-types.md) és jelölje meg őket a példa utterances. 

## <a name="dont-create-descriptors-with-all-the-possible-values"></a>Ne hozzon létre összes lehetséges értéket

Adjon meg néhány példát a leíró [kifejezéslistáiban,](luis-concept-feature.md) de nem minden szót. A LUIS általánosítja és figyelembe veszi a kontextust. 

## <a name="dont-add-many-patterns"></a>Ne adjon hozzá sok mintát

Ne adjon hozzá túl sok [mintát.](luis-concept-patterns.md) A LUIS célja, hogy gyorsan tanuljon kevesebb példával. Ne terhelje túl feleslegesen a rendszert.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Ne képezze be és tegye közzé minden egyes példautterance

Adjon hozzá 10 vagy 15 utterances betanítása és közzététele előtt. Ez lehetővé teszi, hogy az előrejelzés pontosságára gyakorolt hatás. Egyetlen utterance (kifejezés) hozzáadása nem feltétlenül befolyásolja a pontszám. 

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [tervezheti](luis-how-plan-your-app.md) meg az alkalmazást a LUIS alkalmazásban.
