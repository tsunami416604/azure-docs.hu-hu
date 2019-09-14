---
title: Bing Speech felismerési mód kiválasztása | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: A Bing Speech legjobb felismerési módjának kiválasztása.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 1860acb571c837a5eb4c75be69a96d1b22682118
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965676"
---
# <a name="bing-speech-recognition-modes"></a>Bing Speech felismerési módok

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

A Bing Speech to Text API-k többféle beszédfelismerési módot támogatnak. Válassza ki azt a módot, amely az alkalmazás legjobb felismerési eredményeit állítja elő.

| Mód | Leírás |
|---|---|
| *interactive* | A "parancs és vezérlés" felismerése az interaktív felhasználói alkalmazási forgatókönyvek esetében. A felhasználók rövid kifejezéseket beszélnek egy alkalmazás parancsaiként. |
| *Diktálás* | A diktálási forgatókönyvek folyamatos felismerése. A felhasználók továbbra is szövegként megjelenített mondatokat beszélnek. A felhasználók egy formálisan beszélő stílust fogadnak. |
| *beszélgetés* | Folyamatos felismerés az emberek közötti beszélgetések átírásához. A felhasználók kevésbé formálisan beszélő stílust hoznak, és a hosszabb mondatok és a rövidebb kifejezések között váltakoznak.

> [!NOTE]
> Ezek a módok a [REST API](../GetStarted/GetStartedREST.md)-k használata esetén alkalmazhatók. Az [ügyféloldali kódtárak](../GetStarted/GetStartedClientLibraries.md) eltérő paramétereket használnak az elismerési mód megadásához. További információkért tekintse meg az Ön által választott ügyféloldali könyvtárat.

További információ: [felismerési módok](../concepts.md#recognition-modes) oldal.
