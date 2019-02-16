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
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 69a4c136e9c210dd40e004b8d5e1c1a2a8fceaa7
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312533"
---
# <a name="detect-adult-and-racy-content"></a>Észleli a felnőtt és szexuális tartalom

Computer Vision felnőtteknek szóló anyagot észlelését képeken az, hogy a fejlesztők korlátozhatja a szoftvereket a képeket a megjelenítését. Tartalom jelzőket, hogy a fejlesztők az eredmények a saját beállítások szerint tudja értelmezni a nulla és a egy közötti pontszámot érvényesek. 

> [!NOTE]
> Ez a funkció is kínál a [Azure Content Moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview) szolgáltatás. Tekintse meg az alternatív megoldások szigorúbb tartalom-jóváhagyás forgatókönyveket, például szöveg moderálása és emberi munkafolyamatokat.

## <a name="content-flag-definitions"></a>Tartalom jelző definíciók

**Felnőtt** azokra, amelyek pornográf jellegűek, és gyakran meztelenség és a szexuális tevékenység ábrázoló képek lettek definiálva. 

**Pikáns** képeket a meghatározott jellegűek, és gyakran nyíltan kétértelmű rendszerképek kevesebb nyíltan szexuális tartalmú képek megjelölve mint **felnőtt**. 

## <a name="identify-adult-and-racy-content"></a>Felnőtt és szexuális tartalom azonosítása

A [elemzése](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API-t.

A kép elemzése metódus adja vissza a két logikai tulajdonságokat, `isAdultContent` és `isRacyContent`, az a JSON-válasz jelzi a felnőtt és szexuális tartalom rendre metody. A módszer is adja vissza a két tulajdonság `adultScore` és `racyScore`, amely a felnőtt és szexuális tartalom azonosítja törléseknek megfelelő megbízhatósági értékeket jelölik.

## <a name="next-steps"></a>További lépések

Tudnivalók a fogalmak [tartomány-specifikus tartalom észlelése](concept-detecting-domain-content.md) és [arcok észlelése](concept-detecting-faces.md).
