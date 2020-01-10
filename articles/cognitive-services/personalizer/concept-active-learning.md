---
title: Aktív és inaktív események – személyre szabás
titleSuffix: Azure Cognitive Services
description: Ez a cikk az aktív és inaktív események, a tanulási beállítások és a testreszabott szolgáltatáson belüli tanulási szabályzatok használatát ismerteti.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: 90658e030c907a9fd99dd8fb9a6e90698d72b1f0
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834463"
---
# <a name="active-and-inactive-events"></a>Aktív és inaktív események

Ha az alkalmazás meghívja a Rank API-t, akkor megkapja azt a műveletet, amelyet az alkalmazásnak a **rewardActionId** mezőben kell megjelenítenie.  Ettől kezdve a személyre szabott jutalom olyan hívást vár, amely ugyanazzal a Napszállta rendelkezik. A jutalmazási pontszám a modell jövőbeli rangsorolási hívásokhoz való betanítására szolgál. Ha a Napszállta nem érkezik jutalmazási hívás, a rendszer az alapértelmezett jutalmat alkalmazza. Az alapértelmezett jutalmak a Azure Portal vannak beállítva.

Bizonyos esetekben előfordulhat, hogy az alkalmazásnak meg kell hívnia a rangsort, mielőtt azt is tudja, hogy az eredményt fogja-e használni vagy megjeleníteni a felhasználó számára. Ez olyan helyzetekben fordulhat elő, amikor például a előléptetett tartalom lapjait a marketing-kampány felülírja. Ha a rangsor hívásának eredménye soha nem volt használatban, és a felhasználó soha nem látta, ne küldjön Önnek megfelelő jutalom-hívást.

Ezek a forgatókönyvek jellemzően a következő esetekben történnek:

* Olyan felhasználói felületet jelenít meg, amelyet a felhasználó esetleg nem lát.
* Az alkalmazása prediktív megszemélyesítést végez, amelyben a rangsorolási hívások kis valós idejű kontextusban történnek, és előfordulhat, hogy az alkalmazás nem használja a kimenetet.

Ezekben az esetekben a személyre szabással hívhatja meg a rangot, és az esemény _inaktívvá_tételét kéri. A személyre szabott szolgáltatás nem vár jutalmat erre az eseményre, és nem alkalmaz alapértelmezett jutalmat.
Az üzleti logikában később, ha az alkalmazás a rangsor hívásában található információkat használja, csak _aktiválja_ az eseményt. Amint az esemény aktív, a személyre szabási esemény jutalmat vár. Ha a jutalmazási API-ra nem készül explicit hívás, a személyre szabott alapértelmezett jutalom érvényes.

## <a name="inactive-events"></a>Inaktív események

Egy esemény betanításának letiltásához hívja meg a rangsort `learningEnabled = False`használatával. Inaktív esemény esetén a tanulás implicit módon aktiválódik, ha jutalmat küld a Napszállta, vagy meghívja az adott Napszállta tartozó `activate` API-t.

## <a name="learning-settings"></a>Tanulási beállítások

A képzési beállítások határozzák meg a *hiperparaméterek beállításaát* . A különböző tanulási beállításokba betanított két modell eltérő lesz.

### <a name="import-and-export-learning-policies"></a>Képzési szabályzatok importálása és exportálása

A Azure Portalból importálhat és exportálhat tanulási-házirend fájlokat. Ezzel a módszerrel mentheti a meglévő szabályzatokat, tesztelheti és lecserélheti őket, és archiválhatja őket a forráskód vezérlőelemben a későbbi referenciák és a naplózás érdekében.

Útmutató [a](how-to-learning-policy.md) tanulási szabályzatok importálásához és exportálásához.

### <a name="understand-learning-policy-settings"></a>A tanulási szabályzat beállításainak megismerése

A tanulási szabályzat beállításai nem változnak. Csak akkor módosítsa a beállításokat, ha tisztában van azzal, hogyan befolyásolják a személyre szabást. Ezen ismeret nélkül problémákat okozhat, beleértve a személyre szabott modellek érvénytelenítését is.

A személyre szabott [vowpalwabbit](https://github.com/VowpalWabbit) -t használ az események betanítására és kiértékelésére. Tekintse meg a [vowpalwabbit dokumentációját](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments) , amely bemutatja, hogyan szerkesztheti a tanulási beállításokat a vowpalwabbit használatával. Ha a parancssori argumentumok megfelelőek, mentse a parancsot egy fájlba a következő formátumban (az argumentumok tulajdonság értékét cserélje le a kívánt parancsra), majd töltse fel a fájlt a személyre szabott erőforráshoz tartozó Azure Portal **modell és tanulási beállítások** paneljén található tanulási beállítások importálásához.

Az alábbi `.json` egy tanulási szabályzatra mutat példát.

```json
{
  "name": "new learning settings",
  "arguments": " --cb_explore_adf --epsilon 0.2 --power_t 0 -l 0.001 --cb_type mtr -q ::"
}
```

### <a name="compare-learning-policies"></a>Tanulási szabályzatok összehasonlítása

Összehasonlíthatja, hogy a különböző képzési szabályzatok hogyan végezhetők el a személyre szabott naplók korábbi adatain az [Offline értékelések](concepts-offline-evaluation.md)használatával.

[Töltse fel saját képzési szabályzatait](how-to-learning-policy.md) , hogy összehasonlítsa őket az aktuális tanulási házirenddel.

### <a name="optimize-learning-policies"></a>Tanulási szabályzatok optimalizálása

A személyre szabott képzési szabályzatok egy [Offline próbaverzióban](how-to-offline-evaluation.md)is létrehozhatók. Egy optimalizált tanulási szabályzat, amely jobb jutalmakat tartalmaz az offline értékelésekben, jobb eredményeket fog eredményezni, amikor online használatban van a személyre Szabásban.

A tanulási szabályzatok optimalizálása után közvetlenül is alkalmazhatja a személyre szabást, hogy azonnal felváltsa az aktuális házirendet. Vagy mentheti az optimalizált szabályzatot a további értékeléshez, és később eldöntheti, hogy elveti, menti vagy alkalmazza.
