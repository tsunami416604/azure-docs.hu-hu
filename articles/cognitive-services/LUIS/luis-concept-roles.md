---
title: Entitások szerepkörei – LUIS
titleSuffix: Azure Cognitive Services
description: Ezek a megnevezett, környezetfüggő altípus egy entitás csak a mintákat használják. Ha például az utterance (kifejezés) a `buy a ticket from New York to London`, New York-i és a londoni városok, de mindegyik eltérő jelentéssel rendelkezik a mondat helyett szerepel. New York-i a forrás város, London pedig a cél város.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: eead9e0fec8ac4322bc7816de4a4774f8be8129c
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70257956"
---
# <a name="entity-roles-for-contextual-subtypes"></a>Környezetfüggő altípusok entitás-szerepkörei

A szerepkörök lehetővé teszik az entitások számára a névvel ellátott altípusok használatát. A szerepkörök bármely előre elkészített vagy egyéni entitás típussal használhatók, és a példaként használt hosszúságú kimondott szöveg és mintákban is használhatók. 

<a name="example-role-for-entities"></a>
<a name="roles-with-prebuilt-entities"></a>

## <a name="machine-learned-entity-example-of-roles"></a>Gépi megtanult entitás – példa szerepkörökre

A teljes "Buy a **New York** -ról **Londonba**-be, a New York-i és London városa is, de a mondatnak más jelentése van. New York-i a forrás város, London pedig a cél város. 

```
buy a ticket from New York to London
```

Szerepkörök e különbségek adjon meg egy nevet:

|Entitástípus|Entitás neve|Role|Cél|
|--|--|--|--|
|Egyszerű|Location|forrás|Ha az adatsík elhagyja a|
|Egyszerű|Location|cél|Ha az adatsík hajtanak végre|

## <a name="non-machine-learned-entity-example-of-roles"></a>Nem gépi megtanult entitások – példa a szerepkörökre

A kiírásban "a találkozó 8 és 9 között történő megjelölése" esetén a számok egyszerre jelzik az időpontot, de minden alkalommal eltérő jelentéssel rendelkeznek a teljes leírásban. A szerepkörök nevet adnak a különbségeknek. 

```
Schedule the meeting from 8 to 9
```

|Entitástípus|Szerepkörnév|Value|
|--|--|--|
|Előre összeállított datetimeV2|StartTime|8|
|Előre összeállított datetimeV2|EndTime|9|

## <a name="are-multiple-entities-in-an-utterance-the-same-thing-as-roles"></a>Több entitás szerepel a teljes műveletben, mint a szerepkörök? 

A rendszer több entitást is használhat, és szerepkörök használata nélkül is kinyerhető. Ha a mondat kontextusa azt jelzi, hogy az entitás melyik verzióját tartalmazza egy érték, akkor a szerepkört kell használni. 

### <a name="dont-use-roles-for-duplicates-without-meaning"></a>Ne használja a szerepköröket az ismétlődésekhez jelentés nélkül

Ha a Kimondás tartalmazza a helyszínek `I want to travel to Seattle, Cairo, and London.`listáját, akkor ez egy lista, amelyben az egyes elemek nem rendelkeznek további jelentéssel. 

### <a name="use-roles-if-duplicates-indicate-meaning"></a>Szerepkörök használata, ha a duplikált kifejezés azt jelzi

Ha a Kimondás tartalmazza a jelentéssel `I want to travel from Seattle, with a layover in Londen, landing in Cairo.`rendelkező helyekről szóló listát, a forrás, a elnapolás és a cél fogalmát szerepkörökkel kell rögzíteni.

### <a name="roles-can-indicate-order"></a>A szerepkörök jelezhetik a sorrendet

Ha a Kimondás úgy módosult, hogy a kinyerni `I want to first start with Seattle, second London, then third Cairo`kívánt sorrendet jelzi, több módon is kinyerheti azt. Megcímkézheti a jogkivonatokat, amelyek jelzik `first start with`a `second`szerepkört `third`,,,. Az előre elkészített entitások **sorszámát** és az **GeographyV2** előre összeépített entitását is használhatja egy összetett entitásban a megrendelés és a hely gondolatának rögzítéséhez. 

## <a name="how-are-roles-used-in-example-utterances"></a>Hogyan használják például a hosszúságú kimondott szöveg a szerepköröket?

Ha egy entitás rendelkezik szerepkörrel, és az entitás egy Kimondás szerint van megjelölve, választhat, hogy csak az entitást jelöli ki, vagy kiválasztja az entitást és a szerepkört. 

Az alábbi példa hosszúságú kimondott szöveg használ entitásokat és szerepköröket:

|Jogkivonat-nézet|Entitás nézet|
|--|--|
|Érdekes vagyok a **Seattle** -vel kapcsolatos további információk megismeréséhez|Többet szeretne megtudni a (hely) {Location} szolgáltatásról|
|Jegy vásárlása Seattle-ből New Yorkba|Jegy vásárlása a (z) {Location: Origin} helyről {Location: Destination}|

## <a name="how-are-roles-used-in-patterns"></a>Hogyan használhatók a szerepkörök a minták?
A minta sablon utterance (kifejezés), a szerepkörök az utterance (kifejezés) használunk: 

|Az entitás szerepkörök minta|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>Szerepkör-szintaxis a minták
Az entitás- és szerepkör csúcsos zárójelek között, között `{}`. Az entitás- és a szerepkör egymástól kettősponttal elválasztva. 

## <a name="entity-roles-versus-collaborator-roles"></a>Entitás-szerepkörök és közreműködő szerepkörök

Az entitás szerepkörei a LUIS alkalmazás adatmodelljére vonatkoznak. A közreműködő [vagy közreműködő](luis-concept-keys.md#contributions-from-other-authors) szerepkörök a szerzői hozzáférés szintjére vonatkoznak. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>További lépések

* [Gyakorlati oktatóanyag](tutorial-entity-roles.md) használata nem gépi megtanult entitásokkal rendelkező entitás-szerepkörök használatával
* Ismerje meg, hogyan adhat hozzá [szerepkörök](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
