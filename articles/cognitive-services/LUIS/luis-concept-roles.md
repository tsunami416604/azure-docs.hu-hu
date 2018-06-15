---
title: Szerepkörök használata a minta-alapú entitások - Azure ismertetése |} Microsoft Docs
description: Ismerje meg, hogyan szerepkör használható minta-alapú entitás adjon nevet a környezetfüggő entitás altípusa.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: ab6100e33fb767528b87c6afde4c5ef275fc7c81
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35350051"
---
# <a name="entity-roles-in-patterns-are-contextual-subtypes"></a>A minták entitás szerepkörök környezetfüggő altípusainál
Szerepkörök egy entitás használt csak a megnevezett, megfelelő altípusainak [minták](luis-concept-patterns.md).

Például a utterance a `buy a ticket from New York to London`, Budapest és a londoni város tartozik, de eltérő jelentéssel rendelkezhetnek mindegyik rendelkezik a mondatok helyett szerepel. Győr a származási város, London pedig a cél város. 

Szerepkörök nevezze el e különbségek:

|Entitás|Szerepkör|Cél|
|--|--|--|
|Hely|forrás|Ha a vezérlősík hagyja el a|
|Hely|Cél|Ha a vezérlősík fájljai|

## <a name="how-are-roles-used-in-patterns"></a>Hogyan használja a minták szerepkörök?
A minta sablon utterance szerepkörök szerepelnek a utterance belül: 

```
buy a ticket from {Location:origin} to {Location:destination}
```

## <a name="role-syntax-in-patterns"></a>A minták szerepkör szintaxis
Az entitás és a szerepkör csúcsos zárójelek között, között `{}`. Az entitás- és a szerepkör kettősponttal elválasztva. 

## <a name="roles-versus-hierarchical-entities"></a>Szerepkörök és a hierarchikus entitások
Hierarchikus entitások adja meg az azonos környezetfüggő adatokat szerepkörök, de csak a utterances **leképezések**. Hasonlóképpen, szerepkörök információkkal a azonos környezetfüggő hierarchikus entitásként, de csak a **minták**.

|Környezetfüggő tanulás|A használt|
|--|--|
|hierarchikus entitások|Leképezések|
|roles|Minták|

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan adhat [szerepkörök](luis-how-to-add-entities.md#add-role-to-pattern-based-entity)
