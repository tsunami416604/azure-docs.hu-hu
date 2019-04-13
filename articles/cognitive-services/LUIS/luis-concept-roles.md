---
title: Az entitások szerepkörök
titleSuffix: Azure Cognitive Services
description: Ezek a megnevezett, környezetfüggő altípus egy entitás csak a mintákat használják. Ha például az utterance (kifejezés) a `buy a ticket from New York to London`, New York-i és a londoni városok, de mindegyik eltérő jelentéssel rendelkezik a mondat helyett szerepel. New York-i a forrás város, London pedig a cél város.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 5fa922cb91d34483256faf4dcf70569aa2f17b97
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59522486"
---
# <a name="entity-roles-for-contextual-subtypes"></a>Környezetfüggő altípus entitás szerepkörei

Szerepkörök altípus elnevezett entitások lehetővé teszik. A szerepkör minden olyan előre összeállított vagy egyéni entitás típushoz használt, és használni például kimondott szöveg és a minták. 

<a name="example-role-for-entities"></a>
<a name="roles-with-prebuilt-entities"></a>

## <a name="machine-learned-entity-example-of-roles"></a>Szerepkörök gép megismert entitások – példa

Az utterance (kifejezés) a "vásárol egy jegyet az **New York-i** való **London**, New York-i és a londoni városok, de mindegyik eltérő jelentéssel rendelkezik a mondat helyett szerepel. New York-i a forrás város, London pedig a cél város. 

```
buy a ticket from New York to London
```

Szerepkörök e különbségek adjon meg egy nevet:

|Entitástípus|Entitás neve|Szerepkör|Cél|
|--|--|--|--|
|Egyszerű|Földrajzi egység|forrás|Ha az adatsík elhagyja a|
|Egyszerű|Földrajzi egység|cél|Ha az adatsík hajtanak végre|

## <a name="non-machine-learned-entity-example-of-roles"></a>Szerepkörök nem gép megismert entitások – példa

Az utterance (kifejezés) az "Ütemezés az értekezlet 8-9", mind a számok jelölik egyszerre, de minden alkalommal eltérő jelentéssel rendelkezik az utterance (kifejezés). Szerepkörök a különbségek nevet kell adnia. 

```
Schedule the meeting from 8 to 9
```

|Entitástípus|Szerepkörnév|Érték|
|--|--|--|
|Előre összeállított datetimeV2|Kezdés időpontja|8|
|Előre összeállított datetimeV2|Befejezés időpontja:|9|

## <a name="are-multiple-entities-in-an-utterance-the-same-thing-as-roles"></a>Azok az utterance (kifejezés) több entitások ugyanaz, mint a szerepkörök? 

Több entitás is szerepelnek az utterance (kifejezés), és kinyerhetők a szerepkörök használata nélkül. A mondat kontextusában az azt jelzi, hogy az entitás verziójának értéke, akkor a szerepkör kell használni. 

### <a name="dont-use-roles-for-duplicates-without-meaning"></a>Ne használja a szerepkörök az ismétlődések nélkül jelentése

Ha az utterance (kifejezés) tartalmaz, helyek listáját `I want to travel to Seattle, Cairo, and London.`, ez a lista ahol egyes elemek nincs-e egy további jelentése. 

### <a name="use-roles-if-duplicates-indicate-meaning"></a>Használjon szerepköröket, ha ismétlődő jelentése alapján

Ha az utterance (kifejezés) azt jelenti, hogy a helyek listáját tartalmazza `I want to travel from Seattle, with a layover in Londen, landing in Cairo.`, ezt vezérlőtől forrás layover és cél szerepköröket kell rögzíteni.

### <a name="roles-can-indicate-order"></a>Szerepkörök sorrendben adhatja meg.

Szeretne kinyerni, rendelés jelzi az utterance (kifejezés) megváltozásakor `I want to first start with Seattle, second London, then third Cairo`, a többféleképpen is kibonthat egy. Megjelölheti a jogkivonatokat, amelyek jelzik a szerepkör `first start with`, `second`, `third`. Is használhatja az előre összeállított entitások **sorszámát** és a **GeographyV2** előre összeállított entitás rögzítheti az elgondolásra épül, sorrend és a hely egy összetett entitásban. 

## <a name="how-are-roles-used-in-example-utterances"></a>Hogyan használja a példa utterances szerepkörök?

Ha egy entitás rendelkezik egy szerepkört, és az entitás egy példa utterance (kifejezés) meg van jelölve, választhat, hogy csak az entitás kiválasztásával, vagy az entitás- és szerepkör kiválasztása. 

A következő példa utterances entitásokat és szerepköröket használja:

|Jogkivonat megjelenítése|Entitás megtekintése|
|--|--|
|Tanulási további információ az érdekes vagyok **Seattle**|A(z) {Location} helyre vonatkozó többet vagyok|
|Egy a jegy vásárolnak, New York, Seattle|{Helye: Destination} forrásból {helye:} egy jegyet vásárolni|

## <a name="how-are-roles-related-to-hierarchical-entities"></a>Hogyan kapcsolódnak a szerepkörök hierarchikus entitásokhoz?

Szerepkörök számára érhető el összes entitásának példa utterances, valamint a korábbi minták használatát. Mert elérhetők mindenhol, azok szükségtelenné az hierarchikus entitásokat. Új entitások szerepkörök helyett hierarchikus entitások használatával kell létrehozni. 

Hierarchikus entitások idővel elavulttá válik.

## <a name="how-are-roles-used-in-patterns"></a>Hogyan használhatók a szerepkörök a minták?
A minta sablon utterance (kifejezés), a szerepkörök az utterance (kifejezés) használunk: 

|Az entitás szerepkörök minta|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>Szerepkör-szintaxis a minták
Az entitás- és szerepkör csúcsos zárójelek között, között `{}`. Az entitás- és a szerepkör egymástól kettősponttal elválasztva. 

## <a name="entity-roles-versus-collaborator-roles"></a>Entitás szerepkörök és a közreműködő szerepkört

Az adatmodell a LUIS alkalmazás entitás szerepkörök vonatkoznak. [Közreműködő](luis-concept-collaborator.md) szerepkörök authoring hozzáférés szintjei érvényesek. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>További lépések

* Használja a [gyakorlati oktatóanyagban](tutorial-entity-roles.md) entitás szerepkörök használata nem gép megismert entitások
* Ismerje meg, hogyan adhat hozzá [szerepkörök](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
