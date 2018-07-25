---
title: Ismertetése szerepkörök használata a minta-alapú entitások – Azure |} A Microsoft Docs
description: Ismerje meg, hogyan szerepkör használható egy minta-alapú entitásban adjon egy nevet a környezetfüggő entitás altípus.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: diberry
ms.openlocfilehash: d2692cdce9da7428bd7b30c4feaf7347792618f5
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222703"
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

```
buy a ticket from {Location:origin} to {Location:destination}
```

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
