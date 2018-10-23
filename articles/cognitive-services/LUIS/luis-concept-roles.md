---
title: A minta-alapú entitások használata szerepkörök ismertetése
titleSuffix: Azure Cognitive Services
description: Ezek a megnevezett, környezetfüggő altípus egy entitás csak a mintákat használják. Például az utterance (kifejezés) vételi egy jegyet, New York-i, London, New York-i és a londoni városok de mindegyik eltérő jelentéssel rendelkezik a mondat helyett szerepel. New York-i a forrás város, London pedig a cél város.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 9bbbb797cd7e7d1cea52f1d5b1b491998b595db7
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638083"
---
# <a name="entity-roles-in-patterns-are-contextual-subtypes"></a>A minták entitás szerepkör a környezeti altípus
Ezek az entitások csak a felhasznált nevesített, környezetfüggő altípusainak [minták](luis-concept-patterns.md).

Ha például az utterance (kifejezés) a `buy a ticket from New York to London`, New York-i és a londoni városok, de mindegyik eltérő jelentéssel rendelkezik a mondat helyett szerepel. New York-i a forrás város, London pedig a cél város. 

Szerepkörök e különbségek adjon meg egy nevet:

|Entitás|Szerepkör|Cél|
|--|--|--|
|Hely|forrás|Ha az adatsík elhagyja a|
|Hely|cél|Ha az adatsík hajtanak végre|
|Előre összeállított datetimeV2|erre:|Záró dátum|
|Előre összeállított datetimeV2|forrás:|Kezdő dátum|

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

## <a name="roles-with-prebuilt-entities"></a>Szerepkörök az előre összeállított entitások

Az előre összeállított entitások szerepkörök használatával jelentése adhat különböző példányai az előre létrehozott entitás az utterance (kifejezés) belül. 

### <a name="roles-with-datetimev2"></a>DatetimeV2 szerepkörök

Az előre összeállított entitások datetimeV2, hajtja végre egy remek, számos különböző ismertetése a dátumok és időpontok a kimondott szöveg. Adja meg a dátum- és más, mint az előre összeállított entitások alapértelmezett ismertetése dátumtartományokat érdemes. 

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan adhat hozzá [szerepkörök](luis-how-to-add-entities.md#add-role-to-pattern-based-entity)
