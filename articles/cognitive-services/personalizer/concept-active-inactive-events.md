---
title: Aktív és inaktív események - Személyre szabás
description: Ez a cikk a Personalizer szolgáltatáson belüli aktív és inaktív események használatát ismerteti.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: a8f27542208965e2b820b9fc45cfcc5353a7f193
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624252"
---
# <a name="active-and-inactive-events"></a>Aktív és inaktív események

Az **aktív** esemény minden olyan ranghívás, ahol tudod, hogy meg fogod mutatni az eredményt az ügyfélnek, és meghatározod a jutalom pontszámot. Ez az alapértelmezett viselkedés.

**Az inaktív** esemény a Rang hívása, ahol nem biztos abban, hogy a felhasználó az üzleti logika miatt valaha is látni fogja-e az ajánlott műveletet. Ez lehetővé teszi, hogy elvesse az eseményt, így personalizer nem képzett az alapértelmezett jutalom. Az inaktív események nem hívhatják meg a Jutalom API-t.

Fontos, hogy a tanulási hurok tudja a tényleges esemény típusát. Egy inaktív eseménynem rendelkezik Jutalom hívással. Egy aktív eseménynek jutalomhívással kell rendelkeznie, de ha az API-hívás soha nem történik meg, a rendszer az alapértelmezett jutalompontszámot alkalmazza. Módosítsa az esemény állapotát inaktívról aktívra, amint tudja, hogy az befolyásolja a felhasználói élményt.

## <a name="typical-active-events-scenario"></a>Tipikus aktív események forgatókönyve

Amikor az alkalmazás meghívja a Rank API-t, megkapja a műveletet, amelyet az alkalmazásnak meg kell jelennie a **rewardActionId** mezőben.  Ettől a pillanattól kezdve a Personalizer jutalomhívást vár jutalompontszámmal, amely ugyanazt az eventId azonosítót. A jutalom pontszám a modell betanítására szolgál a jövőbeli Ranghívásokhoz. Ha az eseményazonosítóhoz nem érkezik jutalomhívás, a program alapértelmezett jutalmat alkalmaz. [Az alapértelmezett jutalmak](how-to-settings.md#configure-rewards-for-the-feedback-loop) az Azure Portalon a Personalizer erőforráson vannak beállítva.

## <a name="other-event-type-scenarios"></a>Egyéb eseménytípus-forgatókönyvek

Bizonyos esetekben előfordulhat, hogy az alkalmazásnak meg kell hívnia a Rangsort, mielőtt még tudnia kellene, hogy az eredmény használatos lesz-e, vagy megjelenik-e a felhasználó számára. Ez olyan helyzetekben fordulhat elő, amikor például az előléptetett tartalom oldalmegjelenítését egy marketingkampány felülírja. Ha a Rang hívás eredményét soha nem használták, és a felhasználó soha nem látta, ne küldjön megfelelő Jutalom hívást.

Ezek a forgatókönyvek általában a következő esetekben következnek be:

* Előrendereli a felhasználói felületet, amelyet a felhasználó esetleg nem láthat, vagy nem.
* Az alkalmazás prediktív személyre szabást végez, amelyben a Rank hívások kevés valós idejű környezetben történnek, és az alkalmazás lehet, hogy nem használja a kimenetet.

Ezekben az esetekben a Personalizer segítségével hívja rank, kérve az esemény _inaktív_. A Personalizer nem számít jutalomra ezért az eseményért, és nem számít fel alapértelmezett jutalomra.

Később az üzleti logika, ha az alkalmazás használja az információkat a Rang hívás, csak _aktiválja_ az eseményt. Amint az esemény aktívvá vált, a Personalizer eseményjutalmat vár. Ha nem történik kifejezett hívás a Jutalom API-hoz, a Personalizer alapértelmezett jutalmat alkalmaz.

## <a name="inactive-events"></a>Inaktív események

Egy esemény betanításának letiltásához `learningEnabled = False`hívja meg a Rank metódust a használatával.

Inaktív esemény esetén a tanulás implicit módon aktiválódik, ha jutalmat küld `activate` az eventId-ért, vagy felhívja az adott eventId API-t.

## <a name="next-steps"></a>További lépések

* További információ [a jutalompontszám meghatározásáról és a figyelembe vejedtső adatokról.](concept-rewards.md)
