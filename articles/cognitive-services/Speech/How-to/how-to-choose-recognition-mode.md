---
title: Mód kiválasztása |} Microsoft Docs
description: Válassza ki a legjobb mód módjáról.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 4f02b683dde16b537ae5554e6435c068f0fcb808
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347399"
---
# <a name="speech-recognition-modes"></a>Beszéd felismerés módok

A Microsoft *szöveg beszédfelismerés* API-k a beszédfelismerés több módot támogat. Válassza ki a módot, amely a legjobb felismerési eredmények az alkalmazás létrehozza.

| Mód | Leírás |
|---|---|
| *Interaktív* | Interaktív felhasználó alkalmazás-forgatókönyveket "Parancs és vezérlő" használatát. Felhasználók beszéd szándék szerint parancsok egy alkalmazás rövid kifejezés. |
| *Diktálás* | Folyamatos felismerés Diktálás forgatókönyvek esetén. Felhasználók beszéd hosszabb mondatokat, amelyek szövegként jelenik meg. Felhasználók elfogadják a formális nyelvű stílusát. |
| *Téma* | Folyamatos felismerés katódsugárcsövön beszélgetések emberek között. Felhasználók egy kisebb formális nyelvű stílus használatára, és előfordulhat, hogy hosszabb mondatokat és rövidebb kifejezések felváltva.

> [!NOTE]
> Az üzemmódokról vonatkoznak, ha használja a [REST API-k](../GetStarted/GetStartedREST.md). A [klienskódtárak](../GetStarted/GetStartedClientLibraries.md) mód adja meg a különböző paraméterek használatával. További információkért tekintse meg az ügyféloldali kódtár az Ön által választott.

További információkért lásd: a [felismerés módok](../concepts.md#recognition-modes) lap.
