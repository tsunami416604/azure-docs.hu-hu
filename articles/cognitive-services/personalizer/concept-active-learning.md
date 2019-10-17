---
title: Aktív és inaktív események – személyre szabás
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: diberry
ms.openlocfilehash: aa6f53901f21dcb0726454d641a4a2a66007f9e0
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72429042"
---
# <a name="active-and-inactive-events"></a>Aktív és inaktív események

Ha az alkalmazás meghívja a Rank API-t, akkor az alkalmazásnak az rewardActionId mezőben megjelenítendő műveletnek kell megjelennie.  Ettől kezdve a személyre szabott jutalom meghívása ugyanazzal a Napszállta történik. A jutalom pontszám a jövőbeli rangsorolási hívásokhoz használt modell betanítására szolgál majd. Ha a Napszállta nem érkezik jutalmazási hívás, a rendszer a defaul jutalmat alkalmazza. Az alapértelmezett jutalmak az Azure Portalon vannak kialakítva.

Bizonyos esetekben előfordulhat, hogy az alkalmazásnak meg kell hívnia a Rank Range-t, de tudja, hogy az eredményt fogja használni vagy displayedn a felhasználó számára. Ez olyan helyzetekben fordulhat elő, amikor például a előléptetett tartalom lapja megjelenik egy marketing-kampányban. Ha a rangsorolási hívás eredménye soha nem volt használatban, és a felhasználónak soha nem kellett volna megnéznie, akkor helytelen lenne a betanítása bármilyen jutalommal, nulla vagy egyéb módon.
Ez általában akkor fordul elő, ha:

* Előfordulhat, hogy a felhasználó előre megjelenít néhány felhasználói felületet, amelyet a felhasználónak esetleg nem fog látni. 
* Előfordulhat, hogy az alkalmazás prediktív megszemélyesítést végez, amelyben a rangsorolási hívások kevésbé valós idejű környezettel történnek, és az alkalmazás nem használja fel a kimenetet. 

Ezekben az esetekben a személyre szabást úgy használhatja, hogy az eseményt az esemény _inaktívvá_tételét kérő rangsorban hívja meg. A személyre szabott eszköz nem vár jutalmat erre az eseményre, és nem alkalmaz alapértelmezett jutalmat sem. Letr az üzleti logikában, ha az alkalmazás a rangsor hívásában található információkat használja, mindössze annyit kell tennie, hogy _aktiválja_ az eseményt. Az esemény aktív állapotában a személyre szabott jutalom várható az eseményhez, vagy ha a jutalom API-ra nem érkezik kifejezett hívás, akkor az alapértelmezett jutalmat kell alkalmaznia.

## <a name="get-inactive-events"></a>Inaktív események beolvasása

Egy esemény betanításának letiltásához hívja a Rank értéket `learningEnabled = False` értékkel.

Az inaktív események betanítása implicit módon aktiválódik, ha jutalmat küld a Napszállta, vagy meghívja a `activate` API-t az adott Napszállta.

## <a name="learning-settings"></a>Tanulási beállítások

A tanulási beállítások határozzák meg a modell képzésének adott *hiperparaméterek beállítása* . A különböző tanulási beállításokra kitanított két modell ugyanazokat az adattípusokat veszi át.

### <a name="import-and-export-learning-policies"></a>Képzési szabályzatok importálása és exportálása

A Azure Portalból importálhat és exportálhat tanulási házirend-fájlokat. Ez lehetővé teszi a meglévő szabályzatok mentését, tesztelését, cseréjét és archiválását a forráskód vezérlőelemben a későbbi referenciák és auditálások összetevőiként.

### <a name="learning-policy-settings"></a>Tanulási szabályzat beállításai

A **tanulási szabályzat** beállításai nem módosíthatók. Csak akkor módosítsa a beállításokat, ha megérti, hogyan befolyásolják a személyre szabást. Ha az ismeret nélkül módosítja a beállításokat, a rendszer mellékhatásokat okoz, beleértve a személyre szabott modellek érvénytelenítését is.

### <a name="comparing-effectiveness-of-learning-policies"></a>A tanulási szabályzatok hatékonyságának összehasonlítása

Az [Offline értékelések](concepts-offline-evaluation.md)segítségével összehasonlíthatja, hogy a különböző képzési szabályzatok hogyan lettek elvégezve a személyre szabott naplók korábbi adatainak használatával.

[Töltse fel a saját képzési szabályzatait](how-to-offline-evaluation.md) , és hasonlítsa össze az aktuális képzési szabályzatot.

### <a name="discovery-of-optimized-learning-policies"></a>Optimalizált tanulási szabályzatok felderítése

Ha [Offline értékelést](how-to-offline-evaluation.md)végez, a személyre szabott képzési szabályzatot hozhat létre. Egy optimalizált tanulási szabályzat, amely azt mutatja, hogy jobb jutalmak vannak az offline értékelésekben, jobb eredményeket fog eredményezni, amikor online használatban van a személyre Szabásban.

Az optimalizált tanulási szabályzat létrehozása után közvetlenül a személyre szabható, így azonnal lecseréli az aktuális szabályzatot, vagy további értékelés céljából mentheti, és a jövőben dönthet arról, hogy később elkerülheti, mentheti vagy alkalmazhatja azt.
