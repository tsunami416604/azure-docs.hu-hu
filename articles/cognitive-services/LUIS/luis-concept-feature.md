---
title: Jellemzők - LUIS
titleSuffix: Azure Cognitive Services
description: Adjon hozzá funkciókat egy nyelvi modellhez, és adjon tippeket a címkézni vagy osztályozni kívánt bemenetek felismerésével kapcsolatban.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: diberry
ms.openlocfilehash: b151043babd6c67a17f704bb671a415fcc3ba7b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220995"
---
# <a name="machine-learned-features"></a>Gépmegtanult funkciók 

A gépi tanulásban a _funkció_ a rendszer által megfigyelt adatok megkülönböztető tulajdonsága vagy attribútuma, & tanul. A nyelvi megértés (LUIS) egy szolgáltatás ismerteti, és elmagyarázza, mi a fontos a szándékok és entitások.

Az [előzetes LUIS-portálon](https://preview.luis.ai)a szolgáltatások _leírók,_ mert a szándék vagy az entitás _leírására_ szolgálnak.  

## <a name="features-_descriptors_-in-language-understanding"></a>Jellemzők (_leírók_) a nyelvi megértés

Jellemzők, más néven leírók, írja le a nyomokat, amelyek segítenek a nyelvi megértés azonosítani a példa utterances. Funkciók: 

* Kifejezéslista a szándékok vagy entitások szolgáltatásaként
* Entitások, mint szándékok vagy entitások jellemzői

A jellemzőket a modell felbontásához szükséges séma szükséges részének kell tekinteni. 

## <a name="what-is-a-phrase-list"></a>Mi az a kifejezéslista?

A kifejezéslista olyan szavak, kifejezések, számok vagy más karakterek listája, amelyek segítenek azonosítani az azonosításra kívánt fogalmat. A lista nem imitot tetszés szerint. 

## <a name="when-to-use-a-phrase-list"></a>Mikor kell kifejezéslistát használni?

Egy kifejezéslista, LUIS környezetben, és általánosítja az elemek azonosításához, amelyek hasonlóak, de nem pontos szöveg egyezés. Ha a LUIS-alkalmazásnak általánosítania és azonosítania kell az új elemeket, használjon kifejezéslistát. 

Ha fel szeretné ismerni az új példányokat, például egy értekezlet-ütemezőt, amelynek fel kell ismernie az új kapcsolattartók nevét, vagy egy olyan készletalkalmazást, amelynek fel kell ismernie az új termékeket, kezdje egy gép által megtanult entitással. Ezután hozzon létre egy kifejezéslistát, amely segít a LUIS-nak a hasonló jelentésű szavak megtalálásában. Ez a kifejezéslista a LUIS-t a példák felismeréséhez irányítja azáltal, hogy további jelentőséget ad ezeknek a szavaknak az értékéhez. 

A kifejezéslisták olyanok, mint a tartományspecifikus szókincs, amelyek segítenek mind a szándékok, mind az entitások megértésének minőségének javításában. 

## <a name="considerations-when-using-a-phrase-list"></a>Szempontok kifejezéslista használatakor

A kifejezéslista alapértelmezés szerint az alkalmazás összes modelljére vonatkozik. Ez olyan kifejezéslisták esetén fog működni, amelyek minden szándékot és entitást keresztezhetnek. A lebomláshatóság érdekében csak azokra a modellekre kell alkalmaznia a kifejezéslistát, amelyekre vonatkozik. 

Ha létrehoz egy kifejezéslistát (alapértelmezés szerint globálisan létrehozva), majd később egy adott modellre leíróként (jellemzőként) alkalmazza azt, és eltávolítja a többi modellből. Ez az eltávolítás növeli a modell kifejezéslistájának relevanciáját, és javítja a modellben biztosított pontosságot. 

A `enabledForAllModels` jelző szabályozza ezt a modell hatókörét az API-ban. 

<a name="how-to-use-phrase-lists"></a>

### <a name="how-to-use-a-phrase-list"></a>Kifejezéslista használata

[Hozzon létre egy kifejezéslistát,](luis-how-to-add-features.md) ha a szándék vagy az entitás olyan szavakkal vagy kifejezésekkel rendelkezik, amelyek fontosak, például:

* iparági feltételek
* Szleng
* Rövidítések
* vállalatspecifikus nyelv
* más nyelven, de az alkalmazásban gyakran használt nyelv
* kulcsszavak és kifejezések a példakimondott szövegben

**Ne** adjon hozzá minden lehetséges szót vagy kifejezést. Ehelyett adjon hozzá egyszerre néhány szót vagy kifejezést, majd képezze be újra és tegye közzé. Ahogy a lista idővel növekszik, előfordulhat, hogy egyes kifejezések számos formát (szinonimákat) találnak. Törd ki ezeket egy másik listába. 

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="when-to-use-an-entity-as-a-feature"></a>Mikor kell entitást használni szolgáltatásként? 

Egy entitás a szándék vagy az entitás szintjén is hozzáadható szolgáltatásként. 

### <a name="entity-as-a-feature-to-an-intent"></a>Entitás mint egy szándék szolgáltatása

Adjon hozzá egy entitást leíróként (jellemzőként) egy szándékhoz, ha az entitás észlelése jelentős a szándék szempontjából.

Ha például a szándék egy járat foglalása, és az entitás jegyinformációk (például az ülések száma, a származási és a cél), majd a jegyinformációs entitás megkeresésének súlyt kell adnia a könyv repülési szándékának előrejelzéséhez. 

### <a name="entity-as-a-feature-to-another-entity"></a>Entitás szolgáltatásként egy másik entitásszámára

Egy entitást (A) hozzá kell adni egy másik entitáshoz (B), ha az entitás (A) észlelése jelentős az entitás (B) előrejelzéséhez.

Ha például az utcacím entitást (A) észleli, akkor az utcacím (A) megtalálása súlyt ad a szállítási cím entitás (B) előrejelzéséhez. 

## <a name="best-practices"></a>Ajánlott eljárások
Ismerje meg [az ajánlott eljárásokat](luis-concept-best-practices.md).

## <a name="next-steps"></a>További lépések

További információ a [funkciók](luis-how-to-add-features.md) LUIS-alkalmazáshoz való hozzáadásáról.