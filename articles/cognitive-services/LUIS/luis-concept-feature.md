---
title: A LUIS-alkalmazások az Azure-ban szolgáltatások megismerése |} A Microsoft Docs
description: Ismerje meg a szolgáltatásokat, amelyek a LUIS-alkalmazás teljesítményének javítása érdekében. Többek között a kifejezés listák és a minták a reguláris kifejezések felismerve.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 04/18/2018
ms.author: v-geberr
ms.openlocfilehash: 597948947303b7fdf16f24576620d6f39d7c51f4
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37887446"
---
# <a name="phrase-list-features-in-luis"></a>A LUIS kifejezés szolgáltatásai

A machine learning szolgáltatás egy *funkció* egy megkülönböztető is leálljon vagy attribútum, amelyek a rendszer figyelembe veszi. 

Funkciók hozzáadása tegyen ismeri fel a bemeneti címke vagy besorolni azokat a kívánt kapcsolatos, nyelvi modell. Szolgáltatások révén is szándékok és entitások felismerésére LUIS, de a funkciók nem állnak a leképezések vagy entitások magukat. Ehelyett szolgáltatásokat biztosíthatnak példák a vonatkozó feltételek.  

## <a name="what-is-a-phrase-list-feature"></a>Mi az a kifejezés lista szolgáltatás?
Kifejezés listája tartalmaz egy csoportot az értékek (szavak vagy kifejezések), amely ugyanahhoz az osztályhoz tartozik, és hasonló módon (például az városok vagy a termék nevét) kell kezelni. Mi a LUIS megismerkedik az egyik automatikusan alkalmazza a mások számára is. Ez nem egy zárt [entitás listában](luis-concept-entity-types.md#types-of-entities) (pontos egyezés szövege) egyező szó.

Kifejezések listáját, LUIS egy második jelzés ezeket szavakkal kapcsolatos hozzáadja az jobban illeszkedhet az alkalmazás tartomány.

## <a name="how-to-use-phrase-lists"></a>A kifejezés listák használata
Egy ügynök utazási alkalmazás hozzon létre egy kifejezést "Város" nevű, London, Párizs és Cairo értékeket tartalmazó listát. Ha az egyszerű egységként címke egyik ezeket az értékeket egy [példa utterance (kifejezés)](luis-how-to-add-example-utterances.md#add-simple-entity-label) egy szándékkal, a LUIS megtanulja felismerni a többi. 

A kifejezéslista felcserélhetők vagy nem cserélhető lehet. Egy *felcserélhetők* kifejezéslista van, amelyek szinonimák, értékek és a egy *nem cserélhető* kifejezéslista érték nem a szinonimák, de hasonló módon, egy másik szól. 

## <a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>Kifejezés tartalmazza a Súgó egyszerű exchangeable entitások azonosítása
Formát exchangeable kifejezés listák helyes módon a LUIS-alkalmazás teljesítményének finomhangolása. Ha az alkalmazás hiba történt a megfelelő leképezés kimondott szöveg előrejelzésére vagy entitások FELISMERVE, gondolja át e a kimondott szöveg tartalmazza-e a szokatlan szavakat vagy szavak, amelyek lehet, hogy a jelentése nem egyértelmű. Ezeknek a szavaknak esetén használható jól az egy kifejezést listára.

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>Kifejezés tartalmazza a Súgó azonosíthatja a leképezések jobb megértése környezet
Használja a kifejezés a ritkán használt, a szellemi tulajdont képező és a külső szavakat sorolja fel. Előfordulhat, hogy a LUIS nem ismeri fel a ritka és a szellemi tulajdont képező szavak, valamint a külső szavakat (kívül az alkalmazás kulturális környezet), és ezért azokat kell adni a kifejezés listáját. Ez a kifejezés a lista nem cserélhető, annak jelzésére, hogy a ritka szavak készletét képezi egy osztályt, amely a LUIS kell további ismeri fel, de azok nem szinonimák vagy cserélhető, egymással kell megjelölni.

Egy kifejezés lista tehát nem a LUIS szigorú az egyeztetés elvégzéséhez, vagy a címke mindig a kifejezés lista összes használati azok, ugyanúgy utasítást. Érdemes egyszerűen csak egy érhető el. Például rendelkezhet, amely azt jelzi, hogy "Patti" és "Selma" nevek kifejezések listáját, de a LUIS továbbra is használhatja környezeti információkat felismerje, hogy azok jelentését mutatják a más "2 foglalás tenni a Patti Diner ma a" és "-fiókom vezetési utasításokat Selma, Grúzia". 

A kifejezéslista hozzáadása nem megjelölésű további példa beszédmódok hozzáadása helyett. 

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>A kifejezés listák és a lista entitások használata
Kifejezések listáját és a lista entitások hatással lehet a kimondott szöveg összes leképezések között, bár egyes azért teszi ezt más módon. Egy kifejezés listával szándék előrejelzési pontszám hatással. Egy lista entitás használatával hatással vannak a szöveg pontos egyezéssel entitások kinyeréséhez. 

### <a name="use-a-phrase-list"></a>Egy kifejezés helyett szerepel a listában
Kifejezés listáját a LUIS továbbra is figyelembe kell venni a környezet, és általánosítsa a géphez való azonosításához az elemek, amelyek hasonló, de nem pontos egyezést, egy listán szereplő elemeket. Ha a LUIS-alkalmazás lehessen generalize és az új elemeket egy kategória van szüksége, használja a kifejezés listáját. 

Ha képesek felismerni az entitások, például egy értekezlet scheduler, amely felismeri az új ügyfelekhez, vagy egy szoftverleltár-alkalmazást, amely felismeri az új termékek nevét új példányokat szeretne használni a gép megtanult entitás, például egy egyszerű más típusú vagy hierarchikus entitás. Ezután hozzon létre egy kifejezést szavak és kifejezések, amellyel a LUIS hasonló az entitás keresése más szavakat. Ez a lista végigvezeti a LUIS példák az entitás felismerje a további többszörösére hozzáadja azokat a szavakat értékét. 

Kifejezés listák hasonlóak a tartomány-specifikus szöveg szóhasználati, amely a szándékok és entitások ismertetése minőségének javítása érdekében. Egy kifejezés listáját közös használata tulajdonnevek, például a város nevét. Egy város nevét több szóból kötőjeleket vagy aposztrófot többek között lehet.
 
### <a name="dont-use-a-phrase-list"></a>Ne használjon egy kifejezéslista 
Egy lista entitás egyértelműen meghatározza egy entitás is igénybe vehet, és csak azonosítja az értékeket, amelyek pontosan meg minden egyes értékhez. Egy lista entitásra, amelyben egy entitás összes példánya ismertek és hasonló az élelmiszer-elemek egy étterem menüben, amely csak ritkán változnak gyakran, ne módosítsa az alkalmazás lehet. Ha egy entitás egy pontos egyezés egyeztetése, ne használja a kifejezések listáját. 

## <a name="best-practices"></a>Ajánlott eljárások
Ismerje meg, [ajánlott eljárások](luis-concept-best-practices.md).

## <a name="next-steps"></a>További lépések

Lásd: [szolgáltatások hozzáadása](luis-how-to-add-features.md) további információt a szolgáltatások hozzáadása a LUIS-alkalmazás.
