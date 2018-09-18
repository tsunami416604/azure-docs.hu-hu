---
title: Ismertető a felnőtt és szexuális tartalom – a Computer Vision
titleSuffix: Azure Cognitive Services
description: Észleli a felnőtt és szexuális tartalom képeket a Computer Vision APi használatával kapcsolatos fogalmakat.
services: cognitive-services
author: deken
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: b1ba8e7556b6ba134624548142bf73e84d875c6a
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984522"
---
# <a name="detecting-adult-and-racy-content"></a>Felnőtteknek szóló és kényes tartalom észlelése

A különféle visual osztályok között van a felnőtt és szexuális csoport, amely lehetővé teszi, hogy felnőtt anyagok észlelését, és a szexuális tartalmú képek megjelenítéséhez korlátozza. A felnőtt és szexuális tartalom észlelési szűrő értékét is beállíthat a felhasználói beállítás szerint befogadásához.

## <a name="defining-adult-and-racy-content"></a>Felnőtt és szexuális tartalom meghatározása

Többek között a különböző vizuális jellemzőket hatálya alá a [kép elemzése metódus](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa), a felnőtt vizuális szolgáltatás lehetővé teszi a felnőtteknek szóló és rasszista képeinek felismerése. "Felnőtt" lemezképeket, amelyek pornográf jellegűek, és gyakran jelzik a meztelenség és a szexuális tevékenység vannak meghatározva. "Pikáns" képek vannak meghatározva, amely nyíltan kétértelmű jellegűek, és gyakran kisebb nyíltan szexuális tartalmú képek "Felnőtt." címkével, mint képek A felnőtt visual szolgáltatástípus gyakran segítségével nyíltan kétértelmű tartalmazó lemezképek és explicit módon szexuális tartalom megjelenítésének korlátozása.

## <a name="identifying-adult-and-racy-content"></a>Felnőtt és szexuális tartalom azonosítása

A kép elemzése metódus adja vissza a két tulajdonság `isAdultContent` és `isRacyContent`, az a JSON-válasz jelzi, felnőtt és szexuális tartalom metody. Mindkét tulajdonság egy logikai értéket ad vissza, IGAZ vagy hamis. A módszer is adja vissza a két tulajdonság `adultScore` és `racyScore`, amelyek képviselik, osztályban, a felnőtt és szexuális tartalom azonosítására megbízhatósági pontszámokat. Megbízhatósági szűrheti a felnőtt és szexuális tartalom észlelési értékét is beállíthat a beállítások alapján az adott befogadásához.

## <a name="next-steps"></a>További lépések

Tudnivalók a fogalmak [tartomány-specifikus tartalom észlelése](concept-detecting-domain-content.md) és [arcok észlelése](concept-detecting-faces.md).