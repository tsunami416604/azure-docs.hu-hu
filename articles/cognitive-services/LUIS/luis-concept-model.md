---
title: Tervezés modellekből – LUIS
titleSuffix: Azure Cognitive Services
description: A Language Understanding számos típusú modellt biztosít. Egyes modellek több módon is használhatók.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219994"
---
# <a name="design-with-intent-and-entity-models"></a>Tervezés a szándék és az entitás modelljeivel 

A Language Understanding számos típusú modellt biztosít. Egyes modellek több módon is használhatók. 

## <a name="v3-authoring-uses-machine-teaching"></a>A v3 authoring gépi tanítást használ

LUIS lehetővé teszi, hogy a felhasználók könnyedén tanítsák a fogalmakat a gépekre. A gép ezután olyan modelleket építhet ki (olyan fogalmakat, mint az osztályozók és a kivonók), amelyek az intelligens alkalmazások működéséhez használhatók. Míg a LUIS-t gépi tanulás működteti, a gépi tanulás megismerése nem szükséges a használatához. A gépi tanárok Ehelyett a koncepció pozitív és negatív példáit, valamint a koncepciók más kapcsolódó fogalmakkal való modellezését mutatják be a LUIS számára. A tanárok interaktív módon is fejleszthetik a LUIS modelljét az előrejelzési hibák azonosításával és javításával. 

## <a name="v3-authoring-model-decomposition"></a>V3 authoring Model dekompozíciója

LUIS támogatja a _Model dekompozíciót_ a v3 authoring API-kkal, és lebontja a modellt kisebb részekre. Ez lehetővé teszi, hogy a modelleket a különböző részek kiépítésének és előrejelzésének módjával bízza.

A modell elbomlása a következő részekből áll:

* [leképezések](#intents-classify-utterances)
    * a funkciók által biztosított [leírók](#descriptors-are-features)
* [gépi megtanult entitások](#machine-learned-entities)
    * [alösszetevők](#entity-subcomponents-help-extract-data) (a géppel megtanult entitások is)
        * a funkciók által biztosított [leírók](#descriptors-are-features) 
        * nem géppel megtanult entitások, például reguláris kifejezések és felsorolások által biztosított [korlátozások](#constraints-are-text-rules)

## <a name="v2-authoring-models"></a>V2 authoring modellek

A LUIS támogatja az összetett entitásokat a v2 authoring API-kkal. Ez hasonló modellt tartalmaz, de nem ugyanaz, mint a v3 modell lebomlása. Az ajánlott modell architektúra a v3 authoring API-k modellbeli elbomlására való áttérés. 

## <a name="intents-classify-utterances"></a>Szándékok osztályozása hosszúságú kimondott szöveg

A szándék egy példaként osztályozza a hosszúságú kimondott szöveg, hogy megtanítsa LUISt a szándékról. Egy szándékon belüli hosszúságú kimondott szöveg például pozitív példaként szolgál a kiértékeléshez. Ugyanezek a hosszúságú kimondott szöveg az összes többi szándékban negatív példákként használatosak.

Vegyünk egy olyan alkalmazást, amelynek meg kell határoznia a felhasználó azon szándékát, hogy egy könyvet és egy olyan alkalmazást rendeljen, amelynek szüksége van az ügyfél szállítási címeként. Az alkalmazásnak két célja van: `OrderBook` és `ShippingLocation`.

A következő Kimondás **pozitív példa** a `OrderBook` szándékra és **negatív példára** a `ShippingLocation` és a `None`: 

`Buy the top-rated book on bot architecture.`

A jól megtervezett leképezések eredménye, például a hosszúságú kimondott szöveg, a nagy szándékú előrejelzés. 

## <a name="entities-extract-data"></a>Az entitások kinyerik az adatok

Az entitás a kinyert adatok adategységét jelöli. 

### <a name="machine-learned-entities"></a>Gépi megtanult entitások

A géppel megtanult entitások olyan legfelső szintű entitások, amelyek alösszetevőket tartalmaznak, amelyek a géppel megtanult entitások is. 

**Gépi megtanult entitás használata**:

* Ha az ügyfélalkalmazás szükség van az alösszetevőkre
* az entitások lebontásának elősegítése a gépi tanulási algoritmusban

Minden alösszetevő rendelkezhet:

* alösszetevők
* megkötések (reguláris kifejezés entitás vagy lista entitás)
* descripters (funkciók, például kifejezések listája) 

Egy gép által megtanult entitás például egy repülőjegy-jegy rendelése. Ez egy tranzakció, amely sok kisebb adategységgel rendelkezik, mint például a dátum, az idő, az ülőhelyek száma, az ülés típusa, például az első osztály vagy az edző, a forrás helye, a célhely és az étkezési lehetőség.


### <a name="entity-subcomponents-help-extract-data"></a>Az entitás alösszetevők segítenek az adatok kinyerésében

Az alösszetevő egy géppel megtanult alárendelt entitás egy géppel megtanult szülő entitáson belül. 

**Az alösszetevő használata**a következőhöz:

* a gép által megtanult entitás (szülő entitás) részeinek deösszeállítása.

A következő egy géppel megtanult entitást jelent, amely az alábbi különálló adatokkal rendelkezik:

* TravelOrder (gépi megtanult entitás)
    * DateTime (előre elkészített datetimeV2)
    * Hely (gépi megtanult entitás)
        * Forrás (a szerepkör olyan kontextusban található, mint a `from`)
        * Cél (a szerepkör olyan kontextusban található, mint a `to`)
    * Ülőhelyek (gépi megtanult entitás)
        * Mennyiség (előre elkészített szám)
        * Minőség (gépi megtanult entitás a kifejezések listájának leírója)
    * Étkezés (gépi megtanult entitás, amely a listával rendelkező entitást élelmiszer-döntések megkötésével együtt)

Ezen adatok némelyikét – például a forrás helyét és a célhelyet – meg kell tanulni a teljesség kontextusában, például `from` és `to`. Az adategységek egyéb részei kinyerhető pontos karakterlánc-egyezéssel (`Vegan`) vagy előre elkészített entitásokkal (`Seattle` és `Cairo`geographyV2). 

Megtervezheti, hogy az adattípusok hogyan legyenek összehasonlítva és kinyerve a kiválasztott modellekkel és azok konfigurálásával.

### <a name="constraints-are-text-rules"></a>A megkötések szöveges szabályok

A megkötés egy szöveg-egyeztetési szabály, amelyet egy nem gépi megtanult entitás, például a reguláris kifejezés entitás vagy egy lista entitás biztosít. A rendszer előrejelzési időpontban alkalmazza a megkötést, hogy korlátozza a jóslatot, és megadja az ügyfélalkalmazás számára szükséges entitás-feloldást. Ezeket a szabályokat az alösszetevő létrehozásakor kell meghatároznia. 

**Megkötés használata**:
* Ha ismeri a kinyerni kívánt szöveg pontos részleteit.

A megkötések a következők:

* [reguláris kifejezések](reference-entity-regular-expression.md) entitásai
* entitások [listázása](reference-entity-list.md) 
* [előre elkészített](luis-reference-prebuilt-entities.md) entitások

A repülőgép-jegy példáját követve a repülőtéri kódok egy lista entitásban lehetnek a pontos szöveges egyezésekhez. 

A repülőtér listájában a Seattle-re vonatkozó List bejegyzés a város neve, `Seattle` és a Seattle szinonimái közé tartozik a Seattle-i repülőtér kódja, valamint a környező városok és városok:

|`Seattle` az entitás szinonimáinak listázása|
|--|
|`Sea`|
|`seatac`|
|`Bellevue`|

Ha csak a repülőtéri kódok 3 betűs kódját szeretné felismerni, használjon egy reguláris kifejezést a korlátozásként. 

`/^[A-Z]{3}$/`

## <a name="intents-versus-entities"></a>Leképezések versus entitások

A teljes kiértékelés kívánt eredménye az, hogy az entitások a _teljességből_ kinyert adatok részleteit jelentik. Általában a leképezések olyan műveletekhez vannak kötve, amelyeket az ügyfélalkalmazás elvégez, és az entitások a művelet végrehajtásához szükséges információk. Programozási szempontból a szándék egy metódus hívását indítja el, és az entitások paraméterként lesznek használva a metódus hívásakor.

Ennek a Kimondás szándékának _meg kell felelnie_ , és _rendelkezhet_ entitásokkal:

`Buy an airline ticket from Seattle to Cairo`

Ez a Kimondás egyetlen szándékkal rendelkezik:

* Repülőjegy vásárlása

A Kimondás több entitással _is_ rendelkezhet:

* Seattle (Origin) és Kairó (rendeltetés) helyei
* Egyetlen jegy mennyisége

## <a name="descriptors-are-features"></a>A descripters szolgáltatások

A leíró egy olyan szolgáltatás, amely a modellre vonatkozik a betanítási idő, beleértve a kifejezések listáját és az entitásokat. 

**Használjon leírót, ha a**következőket kívánja használni:

* fokozza a leíró által azonosított szavak és kifejezések jelentőségét
* a-nek a leíróhoz javasolt új szöveget vagy kifejezéseket ajánljuk a LUIS számára
* a betanítási adatgyűjtési hiba elhárítása

## <a name="next-steps"></a>Következő lépések

* A [szándékok](luis-concept-intent.md) és az [entitások](luis-concept-entity-types.md)ismertetése. 