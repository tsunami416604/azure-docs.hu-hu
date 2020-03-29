---
title: Tervezés modellekkel - LUIS
titleSuffix: Azure Cognitive Services
description: A nyelvi megértés többféle modellt kínál. Egyes modellek több módon is használhatók.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: d721ceb25b3ce2408563a0bed16457d05affe7b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219994"
---
# <a name="design-with-intent-and-entity-models"></a>Tervezés szándékkal és entitásmodellekkel 

A nyelvi megértés többféle modellt kínál. Egyes modellek több módon is használhatók. 

## <a name="v3-authoring-uses-machine-teaching"></a>V3 Authoring használ géptanítás

A LUIS lehetővé teszi az emberek számára, hogy könnyen tanítsanak fogalmakat egy gépre. A gép ezután olyan modelleket (funkcionális közelítéseket, például osztályozók és elszívók) hozhat létre, amelyek intelligens alkalmazások működtetésére használhatók. Míg a LUIS gépi tanulás, a gépi tanulás megértése nem szükséges használni. Ehelyett a géptanárok a koncepciókat a LUIS-nak kommunikálják a koncepció pozitív és negatív példáinak bemutatásával, és elmagyarázzák, hogyan kell egy fogalmat más kapcsolódó fogalmak használatával modellezni. A tanárok is javíthatja a LUIS-modell interaktívan azonosításával és kijavításával az előrejelzési hibákat. 

## <a name="v3-authoring-model-decomposition"></a>V3 Szerzői modell bomlása

A LUIS támogatja a _modell felbontását_ a V3-as szerzői API-kkal, a modell kisebb részekre bontásával. Ez lehetővé teszi, hogy a modelleket bizalommal építse fel a különböző alkatrészek felépítésében és előrejelzésében.

A modell bomlása a következő részeket rendelkezik:

* [Cél](#intents-classify-utterances)
    * funkciók által biztosított [leírók](#descriptors-are-features)
* [gépileg megtanult entitások](#machine-learned-entities)
    * [alösszetevők](#entity-subcomponents-help-extract-data) (gépáltal megtanult entitások is)
        * funkciók által biztosított [leírók](#descriptors-are-features) 
        * nem gépi legeltetésű entitások, például reguláris kifejezések és listák által [biztosított](#constraints-are-text-rules)

## <a name="v2-authoring-models"></a>V2 Szerzői modellek

A LUIS támogatja az összetett entitásokat a V2 szerzői API-kkal. Ez biztosítja a hasonló modell bomlás, de nem ugyanaz, mint a V3 modell bomlás. Az ajánlott modell architektúra a v3-as szerzői API-k modellbomlási ra való áttérés. 

## <a name="intents-classify-utterances"></a>Szándékok osztályoznak utterances

A szándék osztályozni példa utterances a szándékkal kapcsolatos tanítása LUIS. Példa utterances belül egy szándék pozitív példaként az utterance (kifejezés) pozitív példaként. Ezek az utterances használják negatív példaként minden más szándékok.

Vegyünk egy alkalmazást, amelynek meg kell határoznia a felhasználó azon szándékát, hogy megrendeljen egy könyvet, és egy olyan alkalmazást, amelynek szüksége van az ügyfél szállítási címére. Ez az alkalmazás két `OrderBook` `ShippingLocation`szándékkal rendelkezik: és .

A következő utterance (kifejezés) **egy pozitív példa** `ShippingLocation` a `None` `OrderBook` szándék és a leképezések **negatív példa:** 

`Buy the top-rated book on bot architecture.`

Az eredmény a jól megtervezett szándékok, azok példa utterances, egy nagy szándékú előrejelzés. 

## <a name="entities-extract-data"></a>Entitások adatok kinyerése

Az entitás az utterance (kifejezés) kinyerni kívánt adatok egyegységét jelöli. 

### <a name="machine-learned-entities"></a>Gépmegtanult entitások

A gép megtanult entitás egy legfelső szintű entitás, amely alösszetevőket tartalmaz, amelyek szintén gép által megtanult entitások. 

**Gépmegtanult entitás használata:**

* amikor az ügyfélalkalmazás nak szüksége van az alösszetevőkre
* a gépi tanulási algoritmus segítése az entitások lebomlásában

Minden alösszetevő rendelkezhet:

* Alösszetevői
* megkötések (reguláris kifejezés entitás vagy listaentitás)
* leírók (olyan jellemzők, mint például kifejezéslista) 

Egy gép megtanult entitás például egy repülőjegy rendelése. Fogalmilag ez egy tranzakció, amely számos kisebb adategységet tartalmaz, mint például a dátum, az idő, az ülések mennyisége, az ülőhely típusa, mint például az első osztályú vagy az autóbusz, a származási hely, a rendeltetési hely és az étkezés választása.


### <a name="entity-subcomponents-help-extract-data"></a>Entitás-alösszetevők segítenek az adatok kinyeréséhez

Az alösszetevő egy gép által megtanult gyermekentitás egy gép által megtanult szülőentitáson belül. 

**Az alösszetevő vel:**

* a gép által megtanult entitás (fölérendelt entitás) részeinek lebomlása.

A következőkben egy gép által megtanult entitást jelölnek mindezen különálló adatokkal:

* TravelOrder (gépáltal megtanult entitás)
    * DateTime (előre összeállított datetimeV2)
    * Hely (gép által megtanult entitás)
        * Origin (a szerepkör a `from`környezetben található, például )
        * Cél (a szerepkör a `to`környezetben található, például )
    * Ülőhelyek (gépileg tanult entitás)
        * Mennyiség (előre összeállított szám)
        * Minőség (gépileg megtanult entitás a kifejezéslista leírójával)
    * Étkezés (gépi legeltetésű entitás, amely korlátozza a listaentitást, mint élelmiszer-választást)

Ezen adatok egy részét, például a származási helyet és a célhelyet, az utterance `from` (kifejezés) környezetéből kell megtanulni, például a következő szöveggel: és `to`a. Az adatok más részei pontos karakterlánc-egyezésekkel ( )`Vegan`vagy előre `Seattle` összeállított `Cairo`entitásokkal (a és a földrajzV2) nyerhetők ki. 

Ön határozza meg, hogyan egyezteti és nyerje ki az adatokat, hogy mely modelleket választja, és hogyan konfigurálja őket.

### <a name="constraints-are-text-rules"></a>A kényszerek szöveges szabályok

A megkötés egy szövegegyeztetési szabály, amelyet egy nem gép által megtanult entitás, például a reguláris kifejezés entitás vagy egy listaentitás biztosít. A megkötés t alkalmazva előrejelzési időben, hogy korlátozza az előrejelzést, és az entitás megoldása szükséges az ügyfélalkalmazás. Ezeket a szabályokat az alösszetevő szerkesztése közben határozhatja meg. 

**Megkötés használata**:
* ha tudod, hogy a pontos szöveget kivonat.

A korlátozások a következők:

* [reguláris kifejezés](reference-entity-regular-expression.md) entitások
* [lista](reference-entity-list.md) entitások 
* [előre összeállított](luis-reference-prebuilt-entities.md) entitások

Folytatva a példa a repülőjegy, a repülőtér-kódok lehet nek egy lista entitás pontos szöveges egyezések. 

A repülőtéri lista, a lista bejegyzés Seattle `Seattle` a város neve, és a szinonimák Seattle tartalmazza a repülőtér-kódot Seattle együtt a környező városokban:

|`Seattle`Entitás szinonimáinak listázása|
|--|
|`Sea`|
|`seatac`|
|`Bellevue`|

Ha csak 3 betűs kódot szeretne felismerni a repülőtéri kódokhoz, használjon reguláris kifejezést megkötésként. 

`/^[A-Z]{3}$/`

## <a name="intents-versus-entities"></a>Szándékok és entitások

A szándék a kívánt kimenetelét a _teljes_ utterance (kifejezés), míg az entitások az utterance (kifejezés) kinyert adatok. Általában leképezések vannak kötve a műveleteket az ügyfélalkalmazás kell tennie, és az entitások a művelet végrehajtásához szükséges információkat. Programozási szempontból a szándék metódushívást indít, és az entitások az adott metódushívás paramétereiként lesznek használva.

Ennek az utterance _(kifejezés)_ szándékkal kell rendelkeznie, és _lehetnek_ entitások:

`Buy an airline ticket from Seattle to Cairo`

Ez az utterance (kifejezés) egyetlen szándék:

* Repülőjegy vásárlása

Ez az utterance (kifejezés) több entitást _is_ tartalmazhat:

* Seattle (származási) és Kairó (úti cél) helyei
* Egyetlen jegy mennyisége

## <a name="descriptors-are-features"></a>A leírók olyan jellemzők,

A leíró egy olyan funkció, amely et alkalmaznak a modellre a betanítási időben, beleértve a kifejezéslistákat és az entitásokat. 

**Akkor használjon leírót, ha:**

* a leíró által azonosított szavak és kifejezések jelentőségének növelése
* a LUIS-nak új szöveget vagy kifejezéseket kell javasolnia a leíróhoz
* hiba kijavítása a betanítási adatokon

## <a name="next-steps"></a>További lépések

* A [szándékok](luis-concept-intent.md) és [entitások megismerése.](luis-concept-entity-types.md) 