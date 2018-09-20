---
title: A Bing Speech Recognition mód kiválasztása |} A Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Hogyan választható ki a legjobb mód a Bing Speech.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: 6ea11054e3062c0a265701f6d7fdd136cb692213
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46366403"
---
# <a name="bing-speech-recognition-modes"></a>Bing Speech recognition módok

A Bing hang-szöveg API-k a beszédfelismerés több módot támogatja. Válassza ki azt a módot, amely a legjobb felismerési eredményeket az alkalmazás.

| Mód | Leírás |
|---|---|
| *Interaktív* | Interaktív felhasználó alkalmazás-forgatókönyvek "Parancs és vezérlés" felismerése. Felhasználók beszélnek permanens alkalmazás parancsok rövid kifejezés. |
| *Diktálás* | Folyamatos felismerés Diktálás forgatókönyvek esetén. Felhasználók beszélnek hosszabb mondatokat, amelyek szövegként jelenik meg. Felhasználók elfogadják a formális beszédmódjához. |
| *beszélgetés* | Folyamatos tudathatja átírás beszélgetések emberek között. A felhasználók egy kisebb formális beszédmódjához fogad el, és előfordulhat, hogy az eredetitől eltérő hosszabb mondatokat és rövidebb mondatok közötti.

> [!NOTE]
> Módokban alkalmazandók, ha használja a [REST API-k](../GetStarted/GetStartedREST.md). A [klienskódtárak](../GetStarted/GetStartedClientLibraries.md) különböző paraméterek használatával adja meg a mód. További információkért tekintse meg az ügyféloldali kódtár a választott.

További információkért lásd: a [felismerés módok](../concepts.md#recognition-modes) lapot.
