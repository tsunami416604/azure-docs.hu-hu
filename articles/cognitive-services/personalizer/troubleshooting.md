---
title: Hibaelhárítás – személyre szabás
titleSuffix: Azure Cognitive Services
description: A személyre szabással kapcsolatos hibaelhárítási kérdések ebben a cikkben találhatók.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/15/2019
ms.author: diberry
ms.openlocfilehash: 9f4c4129217923f7fb32996f7447ed09a034f888
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955225"
---
# <a name="personalizer-troubleshooting"></a>Személyre szabás – hibaelhárítás

Ez a cikk a személyre szabással kapcsolatos gyakran ismételt hibaelhárítási kérdésekre adott válaszokat tartalmazza.

## <a name="learning-loop"></a>Tanulási hurok

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>Úgy tűnik, hogy a tanulási hurok nem tanul. Hogyan javíthatom ki?

A tanulási huroknak néhány ezer jutalmazási hívásra van szüksége, mielőtt rangsorolja a rangsorolási hívásokat. 

Ha nem biztos abban, hogy hogyan viselkedik a tanulási hurok jelenleg, futtasson egy [Offline értékelést](concepts-offline-evaluation.md), és alkalmazza a javított tanulási szabályzatot. 

## <a name="next-steps"></a>További lépések

[A modell frissítési gyakoriságának konfigurálása](how-to-settings.md#model-update-frequency)