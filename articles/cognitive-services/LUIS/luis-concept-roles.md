---
title: A minta-alapú entitások használata szerepkörök ismertetése
titleSuffix: Azure Cognitive Services
description: Ezek a megnevezett, környezetfüggő altípus egy entitás csak a mintákat használják. Például az utterance (kifejezés) vételi egy jegyet, New York-i, London, New York-i és a londoni városok de mindegyik eltérő jelentéssel rendelkezik a mondat helyett szerepel. New York-i a forrás város, London pedig a cél város.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 033e5e5e054b0a29961ad60e72b1466b51d1df0c
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47035198"
---
# <a name="entity-roles-in-patterns-are-contextual-subtypes"></a>A minták entitás szerepkör a környezeti altípus
Ezek az entitások csak a felhasznált nevesített, környezetfüggő altípusainak [minták](luis-concept-patterns.md).

Ha például az utterance (kifejezés) a `buy a ticket from New York to London`, New York-i és a londoni városok, de mindegyik eltérő jelentéssel rendelkezik a mondat helyett szerepel. New York-i a forrás város, London pedig a cél város. 

Szerepkörök e különbségek adjon meg egy nevet:

|Entitás|Szerepkör|Cél|
|--|--|--|
|Hely|forrás|Ha az adatsík elhagyja a|
|Hely|cél|Ha az adatsík hajtanak végre|

## <a name="how-are-roles-used-in-patterns"></a>Hogyan használhatók a szerepkörök a minták?
A minta sablon utterance (kifejezés), a szerepkörök az utterance (kifejezés) használunk: 

|Az entitás szerepkörök minta|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>Szerepkör-szintaxis a minták
Az entitás- és szerepkör csúcsos zárójelek között, között `{}`. Az entitás- és a szerepkör egymástól kettősponttal elválasztva. 

## <a name="roles-versus-hierarchical-entities"></a>Szerepkörök és a hierarchikus entitások
Hierarchikus entitások, adja meg az azonos környezetfüggő adatokat szerepkörökként, hanem csak a kimondott szöveg **leképezések**. Hasonlóképpen adja meg a szerepkörök hierarchikus entitásokként, de csak az azonos környezeti információkat **minták**.

|Környezetfüggő tanulás|Használatban|
|--|--|
|hierarchikus entitások|Leképezések|
|roles|Minták|

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan adhat hozzá [szerepkörök](luis-how-to-add-entities.md#add-role-to-pattern-based-entity)
