---
title: A Bing Speech Recognition mód kiválasztása |} A Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Hogyan választható ki a legjobb mód a Bing Speech.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 99e69691d9904285ecff356523813b426c98fca2
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669720"
---
# <a name="bing-speech-recognition-modes"></a>Bing Speech recognition módok

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

A Bing hang-szöveg API-k a beszédfelismerés több módot támogatja. Válassza ki azt a módot, amely a legjobb felismerési eredményeket az alkalmazás.

| Mód | Leírás |
|---|---|
| *interactive* | Interaktív felhasználó alkalmazás-forgatókönyvek "Parancs és vezérlés" felismerése. Felhasználók beszélnek permanens alkalmazás parancsok rövid kifejezés. |
| *Diktálás* | Folyamatos felismerés Diktálás forgatókönyvek esetén. Felhasználók beszélnek hosszabb mondatokat, amelyek szövegként jelenik meg. Felhasználók elfogadják a formális beszédmódjához. |
| *beszélgetés* | Folyamatos tudathatja átírás beszélgetések emberek között. A felhasználók egy kisebb formális beszédmódjához fogad el, és előfordulhat, hogy az eredetitől eltérő hosszabb mondatokat és rövidebb mondatok közötti.

> [!NOTE]
> Módokban alkalmazandók, ha használja a [REST API-k](../GetStarted/GetStartedREST.md). A [klienskódtárak](../GetStarted/GetStartedClientLibraries.md) különböző paraméterek használatával adja meg a mód. További információkért tekintse meg az ügyféloldali kódtár a választott.

További információkért lásd: a [felismerés módok](../concepts.md#recognition-modes) lapot.
