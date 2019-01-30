---
title: Mi az az Emotion API?
titlesuffix: Azure Cognitive Services
description: A felhőalapú érzelemfelismeréssel személyre szabottabb alkalmazások készíthetők.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: overview
ms.date: 02/06/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 555b03be679b0d1ea61371d22ec9865e0e72b558
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55215792"
---
# <a name="what-is-the-emotion-api"></a>Mi az az Emotion API?

> [!IMPORTANT]
> Az Emotion API 2019. február 15-ével elavulttá válik. Az érzelemfelismerési képesség mostantól a [Face API](https://docs.microsoft.com/azure/cognitive-services/face/) részeként általánosan elérhető. 

Üdvözli a Microsoft Emotion API, amellyel személyre szabottabb alkalmazásokat készíthet a Microsoft felhőalapú érzelemfelismerési algoritmusa alapján.

### <a name="emotion-recognition"></a>Érzelemfelismerés

Az Emotion API bétaverziója bemenetként egy képet fogad, eredményként pedig megadja, hogy a képen szereplő egyes arcokra milyen megbízhatósággal illenek különböző érzelmek, valamint a Face API segítségével határoló keretbe is foglalja az arcokat. Az észlelt érzelmek a boldogság, a szomorúság, a meglepetés, a düh, a félelem, a megvetés, az undor és a semlegesség. Ezeket az érzelmeket az emberek a különböző kultúrákban világszerte azonos arckifejezésekkel kommunikálják, az Emotion API pedig képes a felismerésükre.

**Az eredmények értelmezése:**

Az Emotion API eredményeinek értelmezése során az észlelt érzelmet a legmagasabb pontszámmal rendelkező érzelemként kell értelmezni, ha a normalizált pontszámok összértéke 1. A felhasználók igény szerint magasabb megbízhatósági határértéket is megadhatnak az alkalmazásban.

Az érzelemfelismeréssel kapcsolatos további információkért lásd az API-referenciát:
  * Alapszintű: Ha egy felhasználó már meghívta a Face API, küldje el a face téglalap bemenetként, és az alapszintű csomagot használja. [API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f23eb019845524ec61c4d7)
  * Standard: Ha a felhasználó nem küldenek egy négyszög meghatározása, azok szabványos mód használata ajánlott.  [API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)

A streamelt videók Emotion API-val való értelmezésére példát a [videók valós idejű elemzését](https://docs.microsoft.com/azure/cognitive-services/emotion/emotion-api-how-to-topics/howtoanalyzevideo_emotion) ismertető útmutatóban talál.
