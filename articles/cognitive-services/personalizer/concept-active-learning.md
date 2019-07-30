---
title: Aktív tanulás – személyre szabás
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
ms.openlocfilehash: 8c1579be3d11ae14ca45ee861de2d4f705e5d62c
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663711"
---
# <a name="active-learning-and-learning-policies"></a>Aktív tanulási és képzési szabályzatok 

Ha az alkalmazás meghívja a Rank API-t, akkor a tartalom rangját kapja meg. Az üzleti logika ezt a rangsort használva állapíthatja meg, hogy a tartalom megjelenjen-e a felhasználó számára. Ha megjeleníti a rangsorolt tartalmat, az egy _aktív_ Range esemény. Ha az alkalmazás nem jeleníti meg a rangsorolt tartalmat, ez egy inaktív Range esemény. 

A rendszer az aktív Range eseményre vonatkozó információkat adja vissza a személyre. Ezek az információk a modell tanításának folytatására szolgálnak a jelenlegi tanulási szabályzaton keresztül.

## <a name="active-events"></a>Aktív események

Az aktív eseményeket mindig a felhasználónak kell megjelennie, és a jutalmazási hívást vissza kell adni a tanulási hurok bezárásához. 

### <a name="inactive-events"></a>Inaktív események 

Az inaktív események nem változtathatják meg az alapul szolgáló modellt, mert a felhasználó nem adott lehetőséget a rangsorolt tartalom kiválasztására.

## <a name="dont-train-with-inactive-rank-events"></a>Ne legyen betanítva az inaktív Range eseményeivel 

Egyes alkalmazások esetében előfordulhat, hogy a rangsor API-t kell meghívnia anélkül, hogy még tudnia kellene, hogy az alkalmazás az eredményeket a felhasználónak fogja megjeleníteni. 

Ez a következő esetekben fordul elő:

* Előfordulhat, hogy a felhasználó előre megjelenít néhány felhasználói felületet, amelyet a felhasználónak esetleg nem fog látni. 
* Előfordulhat, hogy az alkalmazás prediktív megszemélyesítést végez, amelyben a rangsorolási hívások kevésbé valós idejű környezettel történnek, és az alkalmazás nem használja fel a kimenetet. 

### <a name="disable-active-learning-for-inactive-rank-events-during-rank-call"></a>Inaktív rangsorolási események aktív tanulásának letiltása a rangsor hívása során

Az automatikus tanulás letiltásához hívja a `learningEnabled = False`rangsort a következővel:.

Az inaktív események megismerése implicit módon aktiválódik, ha a Rangsorért jutalmat küld.

## <a name="learning-policies"></a>Képzési szabályzatok

A képzési szabályzat meghatározza a modell betanításának konkrét *hiperparaméterek beállítása* . A különböző tanulási szabályzatokra kitanított két modell eltérő módon viselkedik.

### <a name="importing-and-exporting-learning-policies"></a>Tanulási szabályzatok importálása és exportálása

A Azure Portalból importálhat és exportálhat tanulási házirend-fájlokat. Ez lehetővé teszi a meglévő szabályzatok mentését, tesztelését, cseréjét és archiválását a forráskód vezérlőelemben a későbbi referenciák és auditálások összetevőiként.

### <a name="learning-policy-settings"></a>Tanulási szabályzat beállításai

A **tanulási szabályzat** beállításai nem módosíthatók. Csak akkor módosítsa a beállításokat, ha megérti, hogyan befolyásolják a személyre szabást. Ha az ismeret nélkül módosítja a beállításokat, a rendszer mellékhatásokat okoz, beleértve a személyre szabott modellek érvénytelenítését is.

### <a name="comparing-effectiveness-of-learning-policies"></a>A tanulási szabályzatok hatékonyságának összehasonlítása

Az [Offline értékelések](concepts-offline-evaluation.md)segítségével összehasonlíthatja, hogy a különböző képzési szabályzatok hogyan lettek elvégezve a személyre szabott naplók korábbi adatainak használatával.

[Töltse fel a saját képzési szabályzatait](how-to-offline-evaluation.md) , és hasonlítsa össze az aktuális képzési szabályzatot.

### <a name="discovery-of-optimized-learning-policies"></a>Optimalizált tanulási szabályzatok felderítése

Ha [Offline értékelést](how-to-offline-evaluation.md)végez, a személyre szabott képzési szabályzatot hozhat létre. Egy optimalizált tanulási szabályzat, amely azt mutatja, hogy jobb jutalmak vannak az offline értékelésekben, jobb eredményeket fog eredményezni, amikor online használatban van a személyre Szabásban.

Az optimalizált tanulási szabályzat létrehozása után közvetlenül a személyre szabható, így azonnal lecseréli az aktuális szabályzatot, vagy további értékelés céljából mentheti, és a jövőben dönthet arról, hogy később elkerülheti, mentheti vagy alkalmazhatja azt.