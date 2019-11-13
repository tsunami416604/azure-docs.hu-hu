---
title: Hibaelhárítás – személyre szabás
titleSuffix: Azure Cognitive Services
description: Ez a cikk a személyre szabással kapcsolatos gyakran ismételt hibaelhárítási kérdésekre adott válaszokat tartalmazza.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/15/2019
ms.author: diberry
ms.openlocfilehash: 5911cba54d7dd0eb1a5621112b41e2fc40fa68eb
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953165"
---
# <a name="personalizer-troubleshooting"></a>Személyre szabás – hibaelhárítás

Ez a cikk a személyre szabással kapcsolatos gyakran ismételt hibaelhárítási kérdésekre adott válaszokat tartalmazza.

## <a name="learning-loop"></a>Tanulási hurok

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>Úgy tűnik, hogy a tanulási hurok nem tanul. Hogyan javítsa ezt?

A tanulási huroknak néhány ezer jutalmazási hívásra van szüksége, mielőtt rangsorolja a rangsorolási hívásokat. 

Ha nem biztos abban, hogy hogyan viselkedik a tanulási hurok jelenleg, futtasson egy [Offline értékelést](concepts-offline-evaluation.md), és alkalmazza a javított tanulási szabályzatot. 

## <a name="next-steps"></a>Következő lépések

[A modell frissítési gyakoriságának konfigurálása](how-to-settings.md#model-update-frequency)