---
title: Funkciók – LUIS
titleSuffix: Azure Cognitive Services
description: Adjon hozzá funkciókat a nyelvi modellhez, hogy javaslatokat nyújtson a címkével vagy osztályozással ellátott bemenetek felismeréséhez.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: diberry
ms.openlocfilehash: 5b8257e24cf52d01be8065d97db17fd685aa316d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81531898"
---
# <a name="machine-learned-features"></a>Gépi megtanult funkciók

A gépi tanulásban a _funkció_ egy olyan megkülönböztető tulajdonság vagy adatattribútum, amelyet a rendszer a & megismerésével megfigyel. A Language Understanding (LUIS) egy funkció leírja és ismerteti a szándékait és entitásait illető jelentős jelentőséget.

A [Luis-portálon](https://preview.luis.ai)a funkciók _leírók_ , mert a szándék vagy az entitás _leírására_ szolgálnak.

## <a name="features-_descriptors_-in-language-understanding"></a>Szolgáltatások (_leírók_) Language Understanding

A funkciók, más néven leírók, leírják a Language Understanding azonosítására szolgáló hosszúságú kimondott szöveg. Funkciók:

* Kifejezések listája a szándékok vagy entitások szolgáltatásként
* Entitások szolgáltatásként a szándékok vagy az entitások számára

A funkciókat a séma szükséges részeként kell figyelembe venni a modell kibontásához.

## <a name="what-is-a-phrase-list"></a>A kifejezések listája

A kifejezés lista a szavak, kifejezések, számok vagy más karakterek listája, amelyek segítenek azonosítani az azonosítani kívánt koncepciót. A lista megkülönbözteti a kis-és nagybetűket.

## <a name="when-to-use-a-phrase-list"></a>Mikor kell használni a kifejezések listáját

A kifejezést tartalmazó listával LUIS a kontextust és az általánosításokat úgy tekinti meg, hogy azonosítsa a hasonló elemeket, de nem pontos szöveges egyezést. Ha a LUIS-alkalmazásnak képesnek kell lennie az új elemek általánosítására és azonosítására, használjon kifejezéseket tartalmazó listát.

Ha szeretné tudni, hogy az új példányok felismerhetők legyenek, például egy olyan értekezlet-ütemező, amelynek fel kell ismernie az új névjegyek nevét, vagy egy olyan leltározási alkalmazást, amely új termékeket ismer fel, kezdjen egy géppel megtanult entitással. Ezután hozzon létre egy olyan kifejezést, amely segít a LUIS hasonló jelentéssel rendelkező szavak megkeresésében. Ez a kifejezési lista a LUIS-t a példák felismerésére szolgáló további jelentőséggel bír a szavak értékének megadásával.

A kifejezések listája hasonló a tartományhoz tartozó szókincshez, amely segít a szándékok és az entitások megismerésének javításában.

## <a name="considerations-when-using-a-phrase-list"></a>Szempontok kifejezések listájának használatakor

Alapértelmezés szerint a rendszer az alkalmazás összes modelljére alkalmazza a kifejezések listáját. Ez a kifejezés felsorolja az összes szándékot és entitást. A dekomponálás esetében a kifejezések listáját csak azokra a modellekre kell alkalmazni, amelyekre a szükséges.

Ha létrehoz egy kifejezés listát (alapértelmezés szerint globálisan létrehozva), akkor később egy adott modellre alkalmazza azt leíróként (funkcióként), akkor azt a rendszer eltávolítja a többi modellből. Ez az Eltávolítás a modellhez tartozó kifejezési listához ad hozzá fontosságot, amely segít a modellben elérhető pontosság javításában.

A `enabledForAllModels` jelző a modell hatókörét SZABÁLYOZZA az API-ban.

<a name="how-to-use-phrase-lists"></a>

### <a name="how-to-use-a-phrase-list"></a>Kifejezések listájának használata

[Hozzon létre egy mondatot](luis-how-to-add-features.md) , ha a szándék vagy az entitás olyan szavakat vagy kifejezéseket tartalmaz, amelyek fontosak például a következők:

* iparági feltételek
* szleng
* rövidítéseket
* vállalatra jellemző nyelv
* más nyelvről származó, de gyakran az alkalmazásban használt nyelv
* a példában szereplő hosszúságú kimondott szöveg és kifejezések

Ne **adjon hozzá** minden lehetséges szót vagy kifejezést. Ehelyett adjon hozzá néhány szót vagy kifejezést egyszerre, majd végezze el az újratanítást és a közzétételt. Ahogy a lista az idő múlásával növekszik, előfordulhat, hogy néhány kifejezésnek számos formája van (szinonimák). Ezeket egy másik listára bontja.

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="when-to-use-an-entity-as-a-feature"></a>Ha egy entitást szolgáltatásként kíván használni

Az entitások a szándék vagy az entitás szintjén vehetők fel szolgáltatásként.

### <a name="entity-as-a-feature-to-an-intent"></a>Entitás szolgáltatásként egy szándék

Adja hozzá az entitást leíróként (szolgáltatásként), ha az adott entitás észlelése jelentős a szándék szempontjából.

Ha például a cél egy járat foglalása, és az entitás a jegyekkel kapcsolatos információk (például a munkaállomások száma, a forrás és a célhely), akkor a Ticket Information (jegy) információval rendelkező entitásnak hozzá kell adnia súlyozást a könyv repülési szándékának előrejelzéséhez.

### <a name="entity-as-a-feature-to-another-entity"></a>Entitás szolgáltatásként egy másik entitáshoz

Egy entitást (A) a szolgáltatásként egy másik entitáshoz (B) kell hozzáadni, ha az adott entitás (A) észlelése jelentős az entitás (B) előrejelzéséhez.

Ha például a rendszer az utca címe entitást (A) észleli, akkor az utca címe (A) a szállítási címek entitásának (B) kiszámításához hozzáadja a súlyozást az előrejelzéshez.

## <a name="best-practices"></a>Ajánlott eljárások
Ismerje meg az [ajánlott eljárásokat](luis-concept-best-practices.md).

## <a name="next-steps"></a>További lépések

* Az alkalmazás modelljeinek [kiterjesztése](schema-change-prediction-runtime.md) előrejelzési futtatókörnyezetben
* További információ a szolgáltatások hozzáadása a LUIS-alkalmazáshoz [című témakörben](luis-how-to-add-features.md) talál további információt.
