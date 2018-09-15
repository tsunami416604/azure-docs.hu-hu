---
title: Ajánlott eljárások az alkalmazásfejlesztés az intelligens hangfelismerési szolgáltatással – beszédfelismerés
titleSuffix: Azure Cognitive Services
description: A legjobb eredmények eléréséhez a LUIS ajánlott eljárások megismerése.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: a7791c468bebf2fd683f163f6b1ed532f7f027d6
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2018
ms.locfileid: "45632075"
---
# <a name="best-practices-for-building-a-language-understanding-app-with-cognitive-services"></a>Ajánlott eljárások a Cognitive Services language understanding alkalmazás létrehozásához
Az alkalmazások használatával hozhat létre a LUIS-alkalmazás. 

* Nyelvi modell létrehozása
* Néhány képzési példa (10 – 15 / leképezés) beszédmódok hozzáadása
* Közzététel 
* A végpont tesztelése 
* Funkciók hozzáadása

Miután az alkalmazás [közzétett](luis-how-to-publish-app.md), használja az Authoring Tool ciklust, a szolgáltatás hozzáadása, közzététel és végpontról teszteléséhez. Ne kezdje el a következő szerzői ciklus további példa utterances hozzáadásával. Amely nem teszi lehetővé a LUIS ismerje meg, a modell a való életből vett felhasználói kimondott szöveg. 

Ahhoz, hogy a feladat a tanulás, hatékony, LUIS nem bontsa ki a kimondott szöveg mindaddig, amíg az aktuális készletét példa és a végpont utterances vannak visszaadó biztosabb, magas előrejelzési pontszámokat. Aktív tanulás használata pontszámukat [minták](luis-concept-patterns.md), és [listák kifejezés](luis-concept-feature.md). 

## <a name="do-and-dont"></a>Hajtsa végre, és nem
Az alábbi lista tartalmazza az ajánlott eljárások a LUIS-alkalmazások:

|Ajánlott|Nem ajánlott|
|--|--|
|[Adja meg a különböző leképezések](#do-define-distinct-intents) |[Számos példa utterances leképezések hozzáadása](#dont-add-many-example-utterances-to-intents) |
|[Keres édes helyet túl általános, és túl adott között a minden egyes leképezés](#do-find-sweet-spot-for-intents)|[Használja a LUIS képzési platform](#dont-use-luis-as-a-training-platform)|
|[Iteratív az alkalmazás létrehozása](#do-build-the-app-iteratively)|[Ugyanazt a formátumot, figyelmen kívül hagyja a más formátumú, sok példa beszédmódok hozzáadása](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Újabb iterációban kifejezés listák és a minták hozzáadása](#do-add-phrase-lists-and-patterns-in-later-iterations)|[Vegyes szándékok és entitások definíciója](#dont-mix-the-definition-of-intents-and-entities)|
|[Nincs leképezés példa beszédmódok hozzáadása](#do-add-example-utterances-to-none-intent)|[Az összes lehetséges értékét kifejezés listák létrehozása](#dont-create-phrase-lists-with-all-the-possible-values)|
|[A javaslat szolgáltatás aktív tanulás](#do-leverage-the-suggest-feature-for-active-learning)|[Sok minták hozzáadása](#dont-add-many-patterns)|
|[Az alkalmazás teljesítményének figyelése](#do-monitor-the-performance-of-your-app)|[Betanítás, közzététel és minden hozzá egyetlen példa utterance](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>Adja meg a különböző leképezések
Ellenőrizze, hogy a szöveg szóhasználati, az egyes leképezés csak az adott szándékot és a különböző leképezés nem átfedő. Például egy alkalmazást, amely az utazási szabályok például légitársaság repülőjáratok és a "Hotels" kezelni szeretne használni, ha akkor is választja, hogy ezeket az adatokat külön leképezések vagy azonos célja az entitások az utterance (kifejezés) belül meghatározott adatok.

A két szándék közötti szószedet megegyezik, ha a leképezés egyesítése, és használjon az entitást. 

Vegye figyelembe a következő példa kimondott szöveg:

```
Book a flight
Book a hotel
```

"Például book repülőjegyet" és a "egy szállodai Vendég például Book" használata a azonos szóhasználatát "könyv egy". Ez átfedésben van így azonosnak kell lennie a flight és Szálloda különböző szóból álló szándékot kinyert entitásokat. 

## <a name="do-find-sweet-spot-for-intents"></a>Édes helyszíni keresése leképezések
Előrejelzési adatokat a LUIS segítségével meghatározhatja, amennyiben a szándék átfedésben. A LUIS egymással átfedésben lévő leképezések confuses. Eredménye, hogy a leképezés pontozási felső túl van egy másik szándékot. LUIS oktatási minden alkalommal, amikor nem használja a pontos ugyanazt az elérési utat az adatok között, mert átfedő megjelölésű rendelkezik, hogy az első és második képzési lehetőséget. Azt szeretné, hogy az utterance (kifejezés) pontszámának egyes szándékát, hogy lehet távolabb egymástól, ezért ez nem fordulhat elő. A leképezések jó különbséget kell eredményeznie a várt felső célt minden alkalommal. 
 
## <a name="do-build-the-app-iteratively"></a>Az alkalmazás ismételt létrehozása
Tartsa egy külön *vak* tesztelése, amely nem [példa utterances](luis-concept-utterance.md) vagy a végpont kimondott szöveg. Az alkalmazás a tesztelési készlethez tartozó fejlettebbek. Alkalmazkodjon a vizsgálatot, hogy az megfeleljen a valós felhasználói kimondott szöveg. Minden egyes ismétléskor kiértékelheti, hogy állítsa be a vakok teszt használja. 

Fejlesztők számára az adatok három különböző kell rendelkeznie. Az első példa megcímkézzen a modell létrehozásához az. A második pedig a modellt, a végpont tesztelése. A harmadik pedig a vakok Tesztadatok használt [batch tesztelés](luis-how-to-batch-test.md). Az utolsó beállítása nem szerepel az alkalmazás képzési és küldött a végponton.  

## <a name="do-add-phrase-lists-and-patterns-in-later-iterations"></a>Újabb iterációban kifejezés listák és a minták hozzáadása
[Listák kifejezés](luis-concept-feature.md) alkalmazástartománya kapcsolódó szó szótárak meghatározhatja. A kifejezés néhány szó a listában, majd használja a javaslat funkciót, így további szavak a szöveg szóhasználati ismer LUIS kezdőérték. Minden szó nem fel, a szöveg szóhasználati, mert a kifejezéslista nem pontos egyezést. 

Valós felhasználói utterances a végpontról, nagyon hasonlít egymáshoz, felfedheti az word választási lehetőséget és elhelyezési mintáinak. A [minta](luis-concept-patterns.md) funkció leveszi a word választási lehetőség és a reguláris kifejezések együtt elhelyezési az előrejelzési pontosság növeléséhez. A mintában egy reguláris kifejezést lehetővé teszi a szavak és írásjelek, miközben továbbra is a mintának megfelelő figyelmen kívül kívánja. 

A minta a nem kötelező szintaxist használják írásjelek így írásjelek figyelmen kívül hagyható.

Ezek az eljárások nem érvényesek, mielőtt az alkalmazás kapott végpont kérelmek, mert, amely megdönti magabiztosan.  

## <a name="do-add-example-utterances-to-none-intent"></a>Nincs leképezés példa beszédmódok hozzáadása
Ez a tartalék a szándékkal, jelzett mindent az alkalmazáson kívül. Egy példa utterance (kifejezés) hozzáadása a nincs szándék, a többi része a LUIS-alkalmazás minden 10 példa kimondott szöveg.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Kihasználhatja a javaslat funkciót aktív tanulás
Használat [aktív tanulás](luis-how-to-review-endoint-utt.md)a **tekintse át a végpont utterances** rendszeres időközönként, szándék további példa beszédmódok hozzáadása helyett. Az alkalmazás végponti utterances folyamatosan fogad, mivel a lista egyre növekvő és módosítása.

## <a name="do-monitor-the-performance-of-your-app"></a>Az alkalmazás teljesítményének figyelése
Az előrejelzési pontosság teszt használatával figyelheti. 

## <a name="dont-add-many-example-utterances-to-intents"></a>Nem sok példa utterances leképezések hozzáadása
Az alkalmazás közzététele után csak beszédmódok hozzáadása a aktív tanulás a iteratív folyamat. Ha kimondott szöveg túl hasonló, adjon hozzá egy minta. 

## <a name="dont-use-luis-as-a-training-platform"></a>Ne használja a LUIS képzési platform
A LUIS csak a nyelvi modell tartományhoz. Nem célja általános képzés platformként működik. 

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

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [az alkalmazás megtervezése](luis-how-plan-your-app.md) a LUIS-alkalmazás található.