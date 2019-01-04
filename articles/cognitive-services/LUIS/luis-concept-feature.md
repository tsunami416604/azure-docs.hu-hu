---
title: Szolgáltatások
titleSuffix: Language Understanding - Azure Cognitive Services
description: Funkciók hozzáadása tegyen ismeri fel a bemeneti címke vagy besorolni azokat a kívánt kapcsolatos, nyelvi modell.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: 2d6f7e2fd332e1687db1564befeb6f531045c5dd
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2019
ms.locfileid: "53993063"
---
# <a name="phrase-list-features-in-your-luis-app"></a>A LUIS-alkalmazás a kifejezés szolgáltatásai

A machine learning szolgáltatás egy *funkció* egy megkülönböztető is leálljon vagy attribútum, amelyek a rendszer figyelembe veszi. 

Funkciók hozzáadása tegyen ismeri fel a bemeneti címke vagy besorolni azokat a kívánt kapcsolatos, nyelvi modell. Szolgáltatások révén is szándékok és entitások felismerésére LUIS, de a funkciók nem állnak a leképezések vagy entitások magukat. Ehelyett szolgáltatásokat biztosíthatnak példák a vonatkozó feltételek.  

## <a name="what-is-a-phrase-list-feature"></a>Mi az a kifejezés lista szolgáltatás?
Kifejezés listája tartalmaz egy csoportot az értékek (szavak vagy kifejezések), amely ugyanahhoz az osztályhoz tartozik, és hasonló módon (például az városok vagy a termék nevét) kell kezelni. Mi a LUIS megismerkedik az egyik automatikusan alkalmazza a mások számára is. A lista tehát nem egy zárt [entitás listában](luis-concept-entity-types.md#types-of-entities) (pontos egyezés szövege) egyező szó.

Kifejezések listáját, LUIS egy második jelzés ezeket szavakkal kapcsolatos hozzáadja az jobban illeszkedhet az alkalmazás tartomány.

## <a name="phrase-lists-help-all-models"></a>A kifejezés listák segítségével minden modell

Kifejezés listák nem kapcsolódnak egy adott szándékot vagy egy entitás, de egy boost adja hozzá az összes modellekhez. Feladata, szándék észlelési és entitás besorolás javításához.

## <a name="how-to-use-phrase-lists"></a>A kifejezés listák használata
A HR alkalmazás [egyszerű entitás oktatóanyag](luis-quickstart-primary-and-secondary-data.md), az alkalmazás által használt egy **feladat** programozói, roofer és miniszter feladat típusú kifejezés listája. Ha az alábbi értékek egyikére gép megtanult egységként címke, LUIS megtanulja felismerni a többi. 

A kifejezéslista felcserélhetők vagy nem cserélhető lehet. Egy *felcserélhetők* kifejezéslista van, amelyek szinonimák, értékek és a egy *nem cserélhető* kifejezéslista célja az, egy meghatározott szókészletet alkalmazáslistájában. A szöveg szóhasználati kifejezés listáját növekedésével tapasztalhatja bizonyos feltételek több formában (szinonimák). Bontja ezeket egy másik kifejezés listája, amelyek felcserélhető. 

|Listatípus|Cél|
|--|--|
|Cserélhető|A szinonimák vagy szavak, amikor a listában egy másik szóban változik, rendelkezik az azonos szándékot, és az entitások kinyeréséhez.|
|Nem cserélhető|Az alkalmazás több, mint általában más szavakat azon a nyelven jellemző alkalmazás szakszavakat.|

A kifejezés nem csak az entitás észlelési kapcsolatban sorolja fel, de is szándék besorolást, ha nem cserélhető logikus például a szöveg szóhasználati szavak nem ismert angol nyelven kívül hozzáadása.


<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="phrase-lists-help-identify-simple-interchangeable-entities"></a>Kifejezés tartalmazza a Súgó egyszerű felcserélhetők entitások azonosítása
Cserélhető kifejezés listák helyes módon a LUIS-alkalmazás teljesítményének finomhangolása. Ha az alkalmazás hiba történt a megfelelő leképezés kimondott szöveg előrejelzésére vagy entitások FELISMERVE, gondolja át e a kimondott szöveg tartalmazza-e a szokatlan szavakat vagy szavak, amelyek lehet, hogy a jelentése nem egyértelmű. Ezeknek a szavaknak esetén használható jól az egy kifejezést listára.

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>Kifejezés tartalmazza a Súgó azonosíthatja a leképezések jobb megértése környezet
Egy kifejezés lista tehát nem a LUIS szigorú az egyeztetés elvégzéséhez, vagy a címke mindig a kifejezés lista összes használati azok, ugyanúgy utasítást. Érdemes egyszerűen csak egy érhető el. Például rendelkezhet, amely azt jelzi, hogy "Patti" és "Selma" nevek kifejezések listáját, de a LUIS továbbra is használhatja környezeti információkat felismerje, hogy azok jelentését mutatják a más "2 foglalás tenni a Patti Diner ma a" és "-fiókom vezetési utasításokat Selma, Grúzia". 

A kifejezéslista hozzáadása nem megjelölésű további példa beszédmódok hozzáadása helyett. 

## <a name="an-interchangeable-phrase-list"></a>Egy cserélhető kifejezéslista
Egy cserélhető kifejezéslista akkor használja, ha a szavak vagy kifejezések listája a osztályának vagy csoportjának hoz létre. Ilyen például, egy hónapig, például a "January", "February", "Március"; listája vagy nevét, például "János", "Mary", "Frank".  Ezek a listák felcserélhető, hogy az utterance (kifejezés) lenne feliratú ugyanezzel a leképezés vagy entitás, ha egy másik szót, kifejezést listájában használtak. Például ha "a naptár megjelenítése a januári" ugyanennyi a szándék szerint "a naptár megjelenítése a február", majd a szavakat az felcserélhetők listájának kell lennie. 

## <a name="a-non-interchangeable-phrase-list"></a>Nem cserélhető kifejezéslista
A kifejezés nem cserélhető listáját használata nem azonos szavakat vagy kifejezéseket, a tartományban lévő csoportosíthatók. 

Egy példát használja a kifejezés nem cserélhető listáját a ritkán használt, a szellemi tulajdont képező és a külső szavakat. Előfordulhat, hogy a LUIS nem ismeri fel a ritka és a szellemi tulajdont képező szavak, valamint a külső szavakat (kívül az alkalmazás kulturális környezet). A nem cserélhető beállítás azt jelzi, hogy a ritka szavak készletét képezi egy osztályt, amely a LUIS kell további ismeri fel, de azok nem szinonimák vagy cserélhető, egymással.

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>A kifejezés listák és a lista entitások használata
Kifejezések listáját és a lista entitások hatással lehet a kimondott szöveg összes leképezések között, bár egyes azért teszi ezt más módon. Egy kifejezés listával szándék előrejelzési pontszám hatással. Egy lista entitás használatával hatással vannak a szöveg pontos egyezéssel entitások kinyeréséhez. 

### <a name="use-a-phrase-list"></a>Egy kifejezés helyett szerepel a listában
Kifejezés listáját a LUIS továbbra is figyelembe kell venni a környezet, és általánosítsa a géphez való azonosításához az elemek, amelyek hasonló, de nem pontos egyezést, egy listán szereplő elemeket. Ha a LUIS-alkalmazás lehessen generalize és az új elemeket egy kategória van szüksége, használja a kifejezés listáját. 

Ha képesek felismerni az entitások, például egy értekezlet scheduler, amely felismeri az új ügyfelekhez, vagy egy szoftverleltár-alkalmazást, amely felismeri az új termékek nevét új példányokat szeretne használni a gép megtanult entitás, például egy egyszerű más típusú vagy hierarchikus entitás. Ezután hozzon létre egy kifejezést szavak és kifejezések, amellyel a LUIS hasonló az entitás keresése más szavakat. Ez a lista végigvezeti a LUIS példák az entitás felismerje a további többszörösére hozzáadja azokat a szavakat értékét. 

Kifejezés listák hasonlóak a tartomány-specifikus szöveg szóhasználati, amely a szándékok és entitások ismertetése minőségének javítása érdekében. Egy kifejezés listáját közös használata tulajdonnevek, például a város nevét. Egy város nevét több szóból kötőjeleket vagy aposztrófot többek között lehet.
 
### <a name="dont-use-a-phrase-list"></a>Ne használjon egy kifejezéslista 
Egy lista entitás egyértelműen meghatározza egy entitás is igénybe vehet, és csak azonosítja az értékeket, amelyek pontosan meg minden egyes értékhez. Egy lista entitásra, amelyben egy entitás összes példánya ismert, és nem módosítja a gyakran egy alkalmazás lehet. Példák találhatók food elemek egy étterem menüben, amely csak ritkán változnak. Ha egy entitás egy pontos egyezés egyeztetése, ne használja a kifejezések listáját. 

## <a name="best-practices"></a>Ajánlott eljárások
Ismerje meg, [ajánlott eljárások](luis-concept-best-practices.md).

## <a name="next-steps"></a>További lépések

Lásd: [szolgáltatások hozzáadása](luis-how-to-add-features.md) további információt a szolgáltatások hozzáadása a LUIS-alkalmazás.
