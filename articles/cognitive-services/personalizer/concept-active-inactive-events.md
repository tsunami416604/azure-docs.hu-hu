---
title: Aktív és inaktív események – személyre szabás
description: Ez a cikk a személyre szabott szolgáltatáson belüli aktív és inaktív események használatát ismerteti.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: a8f27542208965e2b820b9fc45cfcc5353a7f193
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624252"
---
# <a name="active-and-inactive-events"></a>Aktív és inaktív események

Az **aktív** események bármelyike a rangsorban, ahol tudni fogja, hogy az eredményt az ügyfélnek kell megjelennie, és meg kell határoznia a jutalom pontszámát. Ez az az alapértelmezett viselkedést.

Az **inaktív** esemény olyan rangú hívás, amelyben nem biztos abban, hogy a felhasználó az üzleti logika miatt mindig látni fogja-e a javasolt műveletet. Ez lehetővé teszi az esemény elvetését, hogy a személyre szabás ne legyen betanítva az alapértelmezett jutalommal. Az inaktív események nem hívhatják meg a jutalmazási API-t.

Fontos, hogy a tanulási hurok tudja a tényleges esemény típusát. Az inaktív események nem kapnak jutalmat. Az aktív eseménynek jutalmazási hívással kell rendelkeznie, de ha az API-hívás soha nem történt meg, a rendszer az alapértelmezett jutalom pontszámot alkalmazza. Egy esemény állapotának módosítása inaktívról aktívra, amint tudja, hogy az hatással lesz a felhasználói élményre.

## <a name="typical-active-events-scenario"></a>Tipikus aktív események forgatókönyve

Amikor az alkalmazás meghívja a Rank API-t, megkapja a műveletet, amelyet az alkalmazásnak a **rewardActionId** mezőben kell megjelenítenie.  Ettől az időponttól kezdve a személyre szabott jutalom-hívás egy jutalom pontszámmal, amely ugyanazzal a Napszállta rendelkezik. A jutalmazási pontszám a modell jövőbeli rangsorolási hívásokhoz való betanítására szolgál. Ha a Napszállta nem érkezik jutalmazási hívás, a rendszer az alapértelmezett jutalmat alkalmazza. Az [alapértelmezett jutalmak](how-to-settings.md#configure-rewards-for-the-feedback-loop) a Azure Portal személyre szabott erőforrására vannak beállítva.

## <a name="other-event-type-scenarios"></a>Egyéb eseménytípus-forgatókönyvek

Bizonyos esetekben előfordulhat, hogy az alkalmazásnak meg kell hívnia a rangsort, mielőtt azt is tudja, hogy az eredményt fogja-e használni vagy megjeleníteni a felhasználó számára. Ez olyan helyzetekben fordulhat elő, amikor például a előléptetett tartalom lapjait a marketing-kampány felülírja. Ha a rangsor hívásának eredménye soha nem volt használatban, és a felhasználó soha nem látta, ne küldjön Önnek megfelelő jutalom-hívást.

Ezek a forgatókönyvek jellemzően a következő esetekben történnek:

* Olyan felhasználói felületet jelenít meg, amelyet a felhasználó esetleg nem lát.
* Az alkalmazása prediktív megszemélyesítést végez, amelyben a rangsorolási hívások kis valós idejű kontextusban történnek, és előfordulhat, hogy az alkalmazás nem használja a kimenetet.

Ezekben az esetekben a személyre szabással hívhatja meg a rangot, és az esemény _inaktívvá_tételét kéri. A személyre szabott szolgáltatás nem vár jutalmat erre az eseményre, és nem alkalmaz alapértelmezett jutalmat.

Az üzleti logikában később, ha az alkalmazás a rangsor hívásában található információkat használja, csak _aktiválja_ az eseményt. Amint az esemény aktív, a személyre szabási esemény jutalmat vár. Ha a jutalmazási API-ra nem készül explicit hívás, a személyre szabott alapértelmezett jutalom érvényes.

## <a name="inactive-events"></a>Inaktív események

Egy esemény betanításának letiltásához hívja meg a rangsort `learningEnabled = False`használatával.

Inaktív esemény esetén a tanulás implicit módon aktiválódik, ha jutalmat küld a Napszállta, vagy meghívja az adott Napszállta tartozó `activate` API-t.

## <a name="next-steps"></a>Következő lépések

* Megtudhatja [, hogyan határozhatja meg a jutalom pontszámát és a megfontolandó információkat](concept-rewards.md).
