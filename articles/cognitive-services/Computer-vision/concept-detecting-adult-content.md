---
title: Írja le a felnőtt és szexuális tartalom – a Computer Vision
titleSuffix: Azure Cognitive Services
description: Észleli a felnőtt és szexuális tartalom képeket a Computer Vision APi használatával kapcsolatos fogalmakat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 64db05e5e40b76d219ea0e3214c20297f32da4b5
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55861272"
---
# <a name="detecting-adult-and-racy-content"></a>Felnőtteknek szóló és kényes tartalom észlelése

A kategóriák között a felnőtt és kényes tartalom is szerepel, ezáltal észlelhető a felnőtt tartalom, és korlátozható a szexuális tartalmú képek megjelenítése. A felnőtteknek szóló vagy kényes tartalom észlelésére vonatkozó szűrő érzékenységét a felhasználó egy csúszka segítségével állíthatja be igényei szerint.

## <a name="defining-adult-and-racy-content"></a>Felnőtt és szexuális tartalom meghatározása

Többek között a különböző vizuális jellemzőket hatálya alá a [kép elemzése metódus](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa), a felnőtt vizuális szolgáltatás lehetővé teszi a felnőtteknek szóló és rasszista képeinek felismerése. "Felnőtt" lemezképeket, amelyek pornográf jellegűek, és gyakran jelzik a meztelenség és a szexuális tevékenység vannak meghatározva. "Pikáns" képek vannak meghatározva, amely nyíltan kétértelmű jellegűek, és gyakran kisebb nyíltan szexuális tartalmú képek "Felnőtt." címkével, mint képek A felnőtt visual szolgáltatástípus gyakran segítségével nyíltan kétértelmű tartalmazó lemezképek és explicit módon szexuális tartalom megjelenítésének korlátozása.

## <a name="identifying-adult-and-racy-content"></a>Felnőtt és szexuális tartalom azonosítása

A kép elemzése metódus adja vissza a két tulajdonság `isAdultContent` és `isRacyContent`, az a JSON-válasz jelzi, felnőtt és szexuális tartalom metody. Mindkét tulajdonság egy logikai értéket ad vissza, IGAZ vagy hamis. A módszer is adja vissza a két tulajdonság `adultScore` és `racyScore`, amelyek képviselik, osztályban, a felnőtt és szexuális tartalom azonosítására megbízhatósági pontszámokat. Megbízhatósági szűrheti a felnőtt és szexuális tartalom észlelési értékét is beállíthat a beállítások alapján az adott befogadásához.

## <a name="next-steps"></a>További lépések

Tudnivalók a fogalmak [tartomány-specifikus tartalom észlelése](concept-detecting-domain-content.md) és [arcok észlelése](concept-detecting-faces.md).
