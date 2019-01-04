---
title: Az entitások szerepkörök
titleSuffix: Azure Cognitive Services
description: Ezek a megnevezett, környezetfüggő altípus egy entitás csak a mintákat használják. Ha például az utterance (kifejezés) a `buy a ticket from New York to London`, New York-i és a londoni városok, de mindegyik eltérő jelentéssel rendelkezik a mondat helyett szerepel. New York-i a forrás város, London pedig a cél város.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: diberry
ms.openlocfilehash: cb155486365ffa1beb4657e2d9cc56fcf143b624
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53547745"
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


[!INCLUDE [H2 Roles versus hierarchical entities](../../../includes/cognitive-services-luis-hier-roles.md)] 

## <a name="example-role-for-entities"></a>Az entitások például szerepkör

A szerepkör az imént kontextusban megismert elhelyezését egy entitás az utterance (kifejezés) belül. Akkor a leghatékonyabb, ha az utterance (kifejezés) rendelkezik az adott entitás egynél több. A legegyszerűbb például bármely entitástípus, megkülönböztetni a, és a helyről. A hely sok különböző entitástípusok jelölhető ki. 

Egy példa használati esetekhez átviteléhez egy alkalmazott egy részlegből egy másikba minden részleg esetén a lista egy elemének. Példa: 

`Move [PersonName] from [Department:from] to [Department:to]`. 

A visszaadott előrejelzésbe mindkét részleg entitások a JSON-válasz lesznek visszaadva, és a szerepkör nevének valamennyi tartalmazza. 

## <a name="roles-with-prebuilt-entities"></a>Szerepkörök az előre összeállított entitások

Az előre összeállított entitások szerepkörök használatával jelentése adhat különböző példányai az előre létrehozott entitás az utterance (kifejezés) belül. 

### <a name="roles-with-datetimev2"></a>DatetimeV2 szerepkörök

Az előre összeállított entitások datetimeV2, hajtja végre egy remek, számos különböző ismertetése a dátumok és időpontok a kimondott szöveg. Adja meg a dátum- és más, mint az előre összeállított entitások alapértelmezett ismertetése dátumtartományokat érdemes. 

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan adhat hozzá [szerepkörök](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
