---
title: Funkciók – LUIS
titleSuffix: Azure Cognitive Services
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
ms.openlocfilehash: 1aed6f9a0ceec18ca800e5030ec09bbb8d98cb76
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560728"
---
# <a name="phrase-list-features-in-your-luis-app"></a>A LUIS-alkalmazás kifejezés-listájának funkciói

A machine learning szolgáltatás egy *funkció* egy megkülönböztető is leálljon vagy attribútum, amelyek a rendszer figyelembe veszi. 

Funkciók hozzáadása tegyen ismeri fel a bemeneti címke vagy besorolni azokat a kívánt kapcsolatos, nyelvi modell. Szolgáltatások révén is szándékok és entitások felismerésére LUIS, de a funkciók nem állnak a leképezések vagy entitások magukat. Ehelyett szolgáltatásokat biztosíthatnak példák a vonatkozó feltételek.  

## <a name="what-is-a-phrase-list-feature"></a>Mi az a kifejezés lista szolgáltatás?
A kifejezések listája az alkalmazás szempontjából jelentős szavakat vagy kifejezéseket sorolja fel, így több, mint a hosszúságú kimondott szöveg többi szava. A kifejezések listája az alkalmazás tartományának szókincséhez további jelet helyez el a LUIS-vel kapcsolatban. Mi a LUIS megismerkedik az egyik automatikusan alkalmazza a mások számára is. Ez a lista nem a pontos szöveges egyezések lezárt [lista entitása](luis-concept-entity-types.md#types-of-entities) .

A kifejezések listája nem segít a létrehozásban, ezért olyan teljes példákat kell hozzáadnia, amelyek számos jelentős szókincset és kifejezést használnak.

## <a name="phrase-lists-help-all-models"></a>A kifejezések listája az összes modell súgóját tartalmazza

A kifejezések listája nem kapcsolódik egy adott szándékhoz vagy entitáshoz, de jelentős lökésként hozzá lett adva az összes szándékhoz és entitáshoz. Ennek célja, hogy javítsa a szándék észlelését és az entitások besorolását.

## <a name="how-to-use-phrase-lists"></a>A kifejezés listák használata

Hozzon létre egy kifejezést tartalmazó listát, ha az alkalmazás olyan szavakat vagy kifejezéseket tartalmaz, amelyek fontosak az alkalmazáshoz, például:

* iparági feltételek
* szleng
* rövidítéseket
* vállalatra jellemző nyelv
* más nyelvről származó, de gyakran az alkalmazásban használt nyelv
* a példában szereplő hosszúságú kimondott szöveg és kifejezések

Ha néhány szót vagy kifejezést adott meg, a **javasolt** funkció használatával megkeresheti a kapcsolódó értékeket. Tekintse át a kapcsolódó értékeket, mielőtt hozzáadja a kifejezés-lista értékeihez.

|Lista típusa|Cél|
|--|--|
|Felcserélhetők|Szinonimák vagy szavak, amelyek, ha a lista egy másik szavára módosulnak, ugyanazzal a szándékkal és az entitások kinyerésével rendelkeznek.|
|Nem felcserélhető|Az alkalmazáshoz tartozó szókincs, amely az adott nyelvhez képest több, mint általában más szavakat tartalmaz.|

### <a name="interchangeable-lists"></a>Felcserélhető listája

A  megváltoztathatatlan kifejezések listája olyan értékekre mutat, amelyek szinonimák. Ha például azt szeretné, hogy az összes víztest található, és van például hosszúságú kimondott szöveg, például: 

* Milyen városokhoz közelítenek a Nagy tavak? 
* Milyen úton fut a Lake Havasu?
* Hol kezdődik és végződik a Nílus? 

Minden Kimondás esetén a kiértékelést és az entitásokat a víztesttől függetlenül kell meghatározni: 

* Milyen városokhoz közeledik a [bodyOfWater]?
* Milyen úton fut a [bodyOfWater]?
* Hol tart a [bodyOfWater] kezdete és vége? 

Mivel a víz törzsének szavai vagy kifejezései szinonimák, és a hosszúságú kimondott szöveg szinonimaként használhatók, használja a kifejezések listájának **felcserélhető** beállítását. 

### <a name="non-interchangeable-lists"></a>Nem felcserélhető felsorolások

A nem felcserélhető kifejezések listája egy olyan jel, amely növeli az észlelést a LUIS számára. A kifejezés lista olyan szavakat vagy kifejezéseket jelöl, amelyek nagyobb jelentőséggel rendelkeznek más szóval. Ez segít a szándékok és az entitások észlelésének meghatározásában. Tegyük fel például, hogy van egy olyan tulajdonosi tartománya, mint a globális utazás (azaz a kultúrák között, de még mindig egyetlen nyelven). Vannak olyan szavak és kifejezések, amelyek fontosak az alkalmazáshoz, de nem szinonimák. 

Egy másik példa a ritka, tulajdonosi és idegen szavakat nem felcserélhető kifejezések listáját használja. Előfordulhat, hogy a LUIS nem ismeri fel a ritka és a szellemi tulajdont képező szavak, valamint a külső szavakat (kívül az alkalmazás kulturális környezet). A nem cserélhető beállítás azt jelzi, hogy a ritka szavak készletét képezi egy osztályt, amely a LUIS kell további ismeri fel, de azok nem szinonimák vagy cserélhető, egymással.

Ne adjon hozzá minden lehetséges szót vagy kifejezést egy kifejezés listához, adjon hozzá néhány szót vagy kifejezést egyszerre, majd végezze el az újratanítást és a közzétételt. 

Ahogy a kifejezések listája az idő múlásával növekszik, előfordulhat, hogy néhány kifejezésnek számos formája van (szinonimák). Ezt kibonthatja egy másik, egymással megváltoztatható kifejezési listára. 

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

Ha egy entitás új példányait szeretné felismerni, például egy olyan értekezlet-ütemező, amelynek fel kell ismernie az új névjegyek nevét, vagy egy olyan leltározási alkalmazást, amely fel kell ismernie az új termékeket, használjon más típusú gépi megtanult entitást, például egy egyszerű entitást. Ezután hozzon létre egy kifejezést szavak és kifejezések, amellyel a LUIS hasonló az entitás keresése más szavakat. Ez a lista végigvezeti a LUIS példák az entitás felismerje a további többszörösére hozzáadja azokat a szavakat értékét. 

Kifejezés listák hasonlóak a tartomány-specifikus szöveg szóhasználati, amely a szándékok és entitások ismertetése minőségének javítása érdekében. Egy kifejezés listáját közös használata tulajdonnevek, például a város nevét. Egy város nevét több szóból kötőjeleket vagy aposztrófot többek között lehet.
 
### <a name="dont-use-a-phrase-list"></a>Ne használjon egy kifejezéslista 
Egy lista entitás egyértelműen meghatározza egy entitás is igénybe vehet, és csak azonosítja az értékeket, amelyek pontosan meg minden egyes értékhez. Egy lista entitásra, amelyben egy entitás összes példánya ismert, és nem módosítja a gyakran egy alkalmazás lehet. Példák találhatók food elemek egy étterem menüben, amely csak ritkán változnak. Ha egy entitás egy pontos egyezés egyeztetése, ne használja a kifejezések listáját. 

## <a name="best-practices"></a>Ajánlott eljárások
Ismerje meg, [ajánlott eljárások](luis-concept-best-practices.md).

## <a name="next-steps"></a>További lépések

Lásd: [szolgáltatások hozzáadása](luis-how-to-add-features.md) további információt a szolgáltatások hozzáadása a LUIS-alkalmazás.
