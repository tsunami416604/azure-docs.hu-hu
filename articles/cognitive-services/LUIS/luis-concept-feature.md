---
title: Szolgáltatások
titleSuffix: Language Understanding - Azure Cognitive Services
description: Funkciók hozzáadása tegyen ismeri fel a bemeneti címke vagy besorolni azokat a kívánt kapcsolatos, nyelvi modell.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 7889f223b607912fd88c798b31ec028f97dfbbd6
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59522853"
---
# <a name="phrase-list-features-in-your-luis-app"></a>A LUIS-alkalmazás a kifejezés szolgáltatásai

A machine learning szolgáltatás egy *funkció* egy megkülönböztető is leálljon vagy attribútum, amelyek a rendszer figyelembe veszi. 

Funkciók hozzáadása tegyen ismeri fel a bemeneti címke vagy besorolni azokat a kívánt kapcsolatos, nyelvi modell. Szolgáltatások révén is szándékok és entitások felismerésére LUIS, de a funkciók nem állnak a leképezések vagy entitások magukat. Ehelyett szolgáltatásokat biztosíthatnak példák a vonatkozó feltételek.  

## <a name="what-is-a-phrase-list-feature"></a>Mi az a kifejezés lista szolgáltatás?
Egy kifejezés Tranzakciólista szavakat vagy kifejezéseket, az alkalmazáshoz, szükség van egy listája több úgy, mint más szavakat a kimondott szöveg. Kifejezések listáját, egy további jel LUIS-e szavakkal kapcsolatos hozzáadja az jobban illeszkedhet az alkalmazás tartományhoz. Mi a LUIS megismerkedik az egyik automatikusan alkalmazza a mások számára is. A lista tehát nem egy zárt [entitás listában](luis-concept-entity-types.md#types-of-entities) pontos szöveg megfelel.

A kifejezés listák segít az így bármely jelentős szókincsből eredőket szavak és kifejezések, amely szerint széles használó utterance (kifejezés) Példák hozzá kell adnia, amely szerint.

## <a name="phrase-lists-help-all-models"></a>A kifejezés listák segítségével minden modell

A kifejezés listák nem kapcsolódnak egy adott szándékot vagy egy entitás, de jelentős boost adja hozzá a szándékok és entitások. Feladata, szándék észlelési és entitás besorolás javításához.

## <a name="how-to-use-phrase-lists"></a>A kifejezés listák használata

Kifejezés lista létrehozása, ha az alkalmazás szavak vagy kifejezések, amelyek is fontos, hogy az alkalmazás például:

* iparági kifejezések
* szleng
* rövidítések
* a vállalatra jellemző nyelv
* egy másik nyelven történő, de az alkalmazás gyakran használt nyelv
* kulcs szavak és kifejezések a példa kimondott szöveg

Miután megadta az néhány szavakat vagy kifejezéseket, a **javasoljuk** kapcsolódó értékek keresése a szolgáltatás. A kifejezés listaértékek való felvétele előtt tekintse át a kapcsolódó értékeket.

|Listatípus|Cél|
|--|--|
|Cserélhető|A szinonimák vagy szavak, amikor a listában egy másik szóban változik, rendelkezik az azonos szándékot, és az entitások kinyeréséhez.|
|Nem cserélhető|Az alkalmazás több, mint általában más szavakat azon a nyelven jellemző alkalmazás szakszavakat.|

### <a name="interchangeable-lists"></a>Cserélhető listák

Egy *felcserélhetők* kifejezéslista értékek, amelyek szinonimák van. Ha azt szeretné, például víz összes szervek található, és rendelkezik példa utterances például: 

* Milyen városok megközelítette a nagy Lake? 
* Milyen út mentén Lake Havasu fut?
* Ha nem a nílusi kezdő és záró? 

Minden kimondásakor szándékot és entitások víz törzse függetlenül kell meghatározni: 

* Milyen városok [bodyOfWater] közel vannak?
* Milyen út mentén [bodyOfWater] fut?
* Ha nem a [bodyOfWater] kezdő és záró? 

Mivel a szavakat vagy kifejezéseket víz törzse megfelel, és a megcímkézzen felcserélhetők, használja a **cserélhető** a kifejezéslista beállítást. 

### <a name="non-interchangeable-lists"></a>Nem cserélhető listák

Nem cserélhető kifejezések listáját, amelyek serkenti az észlelési módszert a LUIS jel. A kifejezéslista azt jelzi, hogy szavak vagy kifejezések, amelyek nagyobb jelentőségű tényező, hogy más szavakat. Ez segít az mindkét meghatározó szándékot és egyéb entitások szolgáltatását. Például tegyük fel például, hogy a globális (azaz a kulturális környezetek között, de továbbra is egyetlen nyelvet) van utazási tulajdonos tartomány rendelkezik. Nincsenek szavak és kifejezések, amelyek fontosak az alkalmazáshoz, de nem egyenértékű. 

Egy másik példa használja a kifejezés nem cserélhető listáját a ritkán használt, a szellemi tulajdont képező és a külső szavakat. Előfordulhat, hogy a LUIS nem ismeri fel a ritka és a szellemi tulajdont képező szavak, valamint a külső szavakat (kívül az alkalmazás kulturális környezet). A nem cserélhető beállítás azt jelzi, hogy a ritka szavak készletét képezi egy osztályt, amely a LUIS kell további ismeri fel, de azok nem szinonimák vagy cserélhető, egymással.

Nem minden lehetséges szó vagy kifejezés hozzáadása egy kifejezést listához, adjon hozzá néhány szavakat vagy kifejezéseket egyszerre, majd újratanítás és közzététele. 

A kifejezés lista növekedésével idővel tapasztalhatja bizonyos feltételek több formában (szinonimák). Bontja ezeket egy másik kifejezés listája, amelyek felcserélhető. 

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="phrase-lists-help-identify-simple-interchangeable-entities"></a>Kifejezés tartalmazza a Súgó egyszerű felcserélhetők entitások azonosítása
Cserélhető kifejezés listák helyes módon a LUIS-alkalmazás teljesítményének finomhangolása. Ha az alkalmazás hiba történt a megfelelő leképezés kimondott szöveg előrejelzésére vagy entitások FELISMERVE, gondolja át e a kimondott szöveg tartalmazza-e a szokatlan szavakat vagy szavak, amelyek lehet, hogy a jelentése nem egyértelmű. Ezeknek a szavaknak esetén használható jól az egy kifejezést listára.

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>Kifejezés tartalmazza a Súgó azonosíthatja a leképezések jobb megértése környezet
Egy kifejezés lista tehát nem a LUIS szigorú az egyeztetés elvégzéséhez, vagy a címke mindig a kifejezés lista összes használati azok, ugyanúgy utasítást. Érdemes egyszerűen csak egy érhető el. Például rendelkezhet, amely azt jelzi, hogy "Patti" és "Selma" nevek kifejezések listáját, de a LUIS továbbra is használhatja környezeti információkat felismerje, hogy azok jelentését mutatják a más "2 foglalás tenni a Patti Diner ma a" és "-fiókom vezetési utasításokat Selma, Grúzia". 

A kifejezéslista hozzáadása nem megjelölésű további példa beszédmódok hozzáadása helyett. 

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>A kifejezés listák és a lista entitások használata
Kifejezések listáját és a lista entitások hatással lehet a kimondott szöveg összes leképezések között, bár egyes azért teszi ezt más módon. Egy kifejezés listával szándék előrejelzési pontszám hatással. Egy lista entitás használatával hatással vannak a szöveg pontos egyezéssel entitások kinyeréséhez. 

### <a name="use-a-phrase-list"></a>Egy kifejezés helyett szerepel a listában
Kifejezés listáját a LUIS továbbra is figyelembe kell venni a környezet, és általánosítsa a géphez való azonosításához az elemek, amelyek hasonló, de nem pontos egyezést, egy listán szereplő elemeket. Ha a LUIS-alkalmazás lehessen generalize és az új elemeket egy kategória van szüksége, használja a kifejezés listáját. 

Ha képesek felismerni az entitások, például egy értekezlet scheduler, amely felismeri az új ügyfelekhez, vagy egy szoftverleltár-alkalmazást, amely felismeri az új termékek nevét új példányokat szeretne használni a gép megtanult entitás, például egy egyszerű entitás más típusú. Ezután hozzon létre egy kifejezést szavak és kifejezések, amellyel a LUIS hasonló az entitás keresése más szavakat. Ez a lista végigvezeti a LUIS példák az entitás felismerje a további többszörösére hozzáadja azokat a szavakat értékét. 

Kifejezés listák hasonlóak a tartomány-specifikus szöveg szóhasználati, amely a szándékok és entitások ismertetése minőségének javítása érdekében. Egy kifejezés listáját közös használata tulajdonnevek, például a város nevét. Egy város nevét több szóból kötőjeleket vagy aposztrófot többek között lehet.
 
### <a name="dont-use-a-phrase-list"></a>Ne használjon egy kifejezéslista 
Egy lista entitás egyértelműen meghatározza egy entitás is igénybe vehet, és csak azonosítja az értékeket, amelyek pontosan meg minden egyes értékhez. Egy lista entitásra, amelyben egy entitás összes példánya ismert, és nem módosítja a gyakran egy alkalmazás lehet. Példák találhatók food elemek egy étterem menüben, amely csak ritkán változnak. Ha egy entitás egy pontos egyezés egyeztetése, ne használja a kifejezések listáját. 

## <a name="best-practices"></a>Ajánlott eljárások
Ismerje meg, [ajánlott eljárások](luis-concept-best-practices.md).

## <a name="next-steps"></a>További lépések

Lásd: [szolgáltatások hozzáadása](luis-how-to-add-features.md) további információt a szolgáltatások hozzáadása a LUIS-alkalmazás.
