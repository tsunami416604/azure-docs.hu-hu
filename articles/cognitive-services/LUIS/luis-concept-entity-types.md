---
title: Entity types – LUIS
titleSuffix: Azure Cognitive Services
description: 'Az entitások kinyerik az adatok kinyerését. Az entitások típusai kiszámítható kitermelést biztosítanak. Az entitások két típusa létezik: a gépi megtanult és a nem gépi megtanult. Fontos tudni, hogy milyen típusú entitással dolgozik a hosszúságú kimondott szöveg-ben.'
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 9dc26e50e1c0f43e816e422f0fee91a246ea04a9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487606"
---
# <a name="entities-and-their-purpose-in-luis"></a>Entitások és céljuk a LUIS-ben

Az entitások elsődleges célja, hogy az ügyfélalkalmazások kiszámítható módon kinyerje az adatmennyiséget. Egy _opcionális_, másodlagos cél az, hogy növelje a leírókkal való szándék előrejelzését. 

Két típusú entitás létezik: 

* gépi megtanult – környezetből
* nem gépi megtanult – a szöveg pontos egyezése

Mindig kezdjen egy géppel megtanult entitással, mert ez az kinyerési lehetőségek széles skáláját biztosítja.

## <a name="entity-compared-to-intent"></a>Entitás a szándékhoz képest

Az entitás a kibontani kívánt kifejezésen belüli adatkoncepciót jelöli. 

Vegye figyelembe a következő 3 hosszúságú kimondott szöveg:

|Kimondott szöveg|Kinyert adathalmaz|Magyarázat|
|--|--|--|
|`Help`|-|Nincs kibontva.|
|`Send Bob a present`|Bob, jelen|Bob határozottan fontos a feladat elvégzéséhez. A jelen lehet elég információ, vagy előfordulhat, hogy a robotnak tisztáznia kell, hogy mi a jelen egy követő kérdésben.|
|`Send Bob a box of chocolates.`|A felhasználó kérésének elvégzéséhez fontos a két fontos adat, a Bob és a csokoládés Box.|

A Kimondás számos entitást tartalmazhat, vagy egyetlen sem. Az ügyfélalkalmazás feladatának elvégzéséhez szükség _lehet_ az entitásra. 

Összehasonlítva a Kimondás céljának előrejelzése _szükséges_ , és a teljes teljességet jelenti. A LUIS használatához példa hosszúságú kimondott szöveg van szükség. Ha a Kimondás elsődleges szándéka nem fontos az ügyfélalkalmazás számára, adja hozzá az összes hosszúságú kimondott szöveg a none szándékhoz. 

Ha úgy találja, hogy az alkalmazás életciklusának későbbi részében megtalálhatja a hosszúságú kimondott szöveg, egyszerűen megteheti. Ez lehet a hosszúságú kimondott szöveg rendszerezése a szerzői műveletek során, vagy az is lehet, hogy az ügyfél-alkalmazásban az előre jelzett szándékot használja. 

Nincs szükség az ügyfélalkalmazás előre jelzett szándékának használatára, de a rendszer az előrejelzési végpont válaszának részeként visszaadja.

## <a name="entities-represent-data"></a>Az entitások az adathalmazokat jelölik

Az entitások olyan adatok, amelyekről le szeretné kérni a kiértékelést. Ez lehet a név, a dátum, a terméknév vagy a szavak bármely csoportja. 

|Kimondott szöveg|Entitás|Adatok|
|--|--|--|
|3 jegy vásárlása a New Yorkba|Előre elkészített szám<br>Hely. cél|3<br>New York|
|Jegyet vásárolhat a New York-i és Londonba március 5-én|Hely. forrás<br>Hely. cél<br>Előre elkészített datetimeV2|New York<br>London<br>2018. március 5.|

## <a name="entities-are-optional-but-highly-recommended"></a>Az entitások nem kötelező, de kifejezetten ajánlott

A szándékok megadásakor az entitások megadása nem kötelező. Nem kell entitásokat létrehoznia az alkalmazás minden fogalmához, de csak azok számára, akiknek szükségük van az ügyfélalkalmazás beavatkozására. 

Ha a hosszúságú kimondott szöveg nem rendelkezik a robot folytatásához szükséges adatokkal, nem kell őket felvennie. Az alkalmazás futása során később is hozzáadhatók. 

Ha nem tudja, hogyan használja fel az adatokat, adjon hozzá néhány gyakori előre elkészített entitást, például a [datetimeV2](luis-reference-prebuilt-datetimev2.md), a [sorszámot](luis-reference-prebuilt-ordinal.md), az [e-mailt](luis-reference-prebuilt-email.md)és a [telefonszámot](luis-reference-prebuilt-phonenumber.md).

## <a name="design-entities-for-decomposition"></a>Entitások megtervezése a dekompozícióhoz

Megkezdheti az entitás kialakítását egy géppel megtanult entitással. Ez lehetővé teszi, hogy az entitások az idő múlásával egyszerűen megtervezzék a növekedést és a változásokat. Az entitások kialakításának befejezéséhez adja hozzá az **alösszetevőket** (gyermek entitásokat) a **korlátozásokkal** és a **leírókkal** . 

A kiépítésének megtervezése lehetővé teszi a LUIS számára, hogy a nagymértékű entitás-feloldást adja vissza az ügyfélalkalmazás számára. Ez lehetővé teszi, hogy az ügyfél-alkalmazás az üzleti szabályokra koncentráljon, és az adatfelbontást a LUIS-re bízza.

### <a name="machine-learned-entities-are-primary-data-collections"></a>A géppel megtanult entitások elsődleges adatgyűjtemények

A géppel megtanult entitások a legfelső szintű adategységek. Az alösszetevők a géppel megtanult entitások alárendelt entitásai. 

A **megkötések** pontos szöveges egyeztetési entitások, amelyek szabályokat alkalmaznak az adatok azonosítására és kinyerésére. A **leírók** olyan szolgáltatások, amelyek az előrejelzéshez tartozó szavak vagy kifejezések relevanciájának növelésére vonatkoznak.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Entitások típusai

Válassza ki az entitást az Adatkivonatok és a kinyerés utáni megjelenítésük alapján.

|Entitás típusa|Cél|
|--|--|
|[**Gépi megtanult**](#composite-entity)|Entitások szülőjének csoportosítása, az entitás típusától függetlenül. A géppel megtanult entitások a teljes kontextusban tanulnak. Így az elhelyezés variációja jelentős hosszúságú kimondott szöveg jelent. |
|[**Listáját**](#list-entity)|A **pontos szöveges egyezéssel**kinyert elemek és szinonimáik listája.|
|[**Minta. any**](#patternany-entity)|Az entitás, amelyben nehéz meghatározni az entitás végét. |
|[**Prebuilt**](#prebuilt-entity)|Már betanítva bizonyos típusú adatok, például URL-cím vagy e-mailek kinyerésére. Ezen előre összeépített entitások némelyike a nyílt forráskódú [felismerők – Text](https://github.com/Microsoft/Recognizers-Text) projektben van meghatározva. Ha az adott kulturális környezet vagy entitás jelenleg nem támogatott, akkor hozzájárul a projekthez.|
|[**Reguláris kifejezés**](#regular-expression-entity)|Reguláris kifejezést használ a **pontos szöveges egyeztetéshez**.|

### <a name="entity-role-defines-context"></a>Az entitás szerepköre definiálja a kontextust

Az entitások szerepköre a megnevezett alias, amely a teljes tartományon belüli kontextuson alapul. Egy példa egy olyan járat foglalásának kimondása, amelynek két helye van, a forrás és a cél.

`Book a flight from Seattle to Cairo`

`location` entitás két példáját ki kell vonni. Az ügyfél-alkalmazásnak ismernie kell a hely típusát, hogy el lehessen végezni a jegyek megvásárlását. A `location` entitásnak két `origin` és `destination` szerepkörrel kell rendelkeznie, és mindkettőt meg kell adni a példában szereplő hosszúságú kimondott szöveg. 

Ha a LUIS megkeresi a `location`, de nem tudja meghatározni a szerepkört, a hely entitás továbbra is visszaadja. Az ügyfélalkalmazás nyomon kell követnie egy kérdést, hogy meghatározza, hogy a felhasználó milyen típusú helyet igényel. 

A rendszer több entitást is használhat, és szerepkörök használata nélkül is kinyerhető. Ha a mondat kontextusa az entitás értékét jelöli, akkor egy szerepkört kell használni.

Ha a Kimondás tartalmazza a helyszínek listáját, `I want to travel to Seattle, Cairo, and London.`, ez egy lista, ahol az egyes elemek nem rendelkeznek további jelentéssel. 

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Ha az entitások maximális számánál többre van szüksége 

Ha a korlátnál nagyobbra van szüksége, forduljon az ügyfélszolgálathoz. Ehhez gyűjtsön részletes információkat a rendszeréről, lépjen a [Luis](luis-reference-regions.md#luis-website) webhelyére, és válassza a **támogatás**lehetőséget. Ha az Azure-előfizetése támogatási szolgáltatásokat tartalmaz, forduljon az [Azure technikai támogatási](https://azure.microsoft.com/support/options/)szolgálatához. 

## <a name="entity-prediction-status"></a>Entitás-előrejelzés állapota

A LUIS-portál azt mutatja be, hogy az entitás egy példa szerinti Kimondás esetén a kiválasztott entitástól eltérő egyed-előrejelzéssel rendelkezik. Ez a különböző pontszám az aktuálisan betanított modellen alapul. 

## <a name="next-steps"></a>További lépések

Ismerje meg a jó [hosszúságú kimondott szöveg](luis-concept-utterance.md)kapcsolatos fogalmakat. 

Az entitások LUIS-alkalmazáshoz való hozzáadásával kapcsolatos további tudnivalókért tekintse meg az [entitások hozzáadása](luis-how-to-add-entities.md) című témakört.
