---
title: Hibaelhárítás – személyre szabás
titleSuffix: Azure Cognitive Services
description: A személyre szabással kapcsolatos hibaelhárítási kérdések ebben a cikkben találhatók.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: article
ms.date: 06/15/2019
ms.author: diberry
ms.openlocfilehash: 7f7a6a08b86d21287c644f6a851d465d97f32e74
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663686"
---
# <a name="personalizer-troubleshooting"></a>Személyre szabás – hibaelhárítás

Ez a cikk a személyre szabással kapcsolatos gyakran ismételt hibaelhárítási kérdésekre adott válaszokat tartalmazza.

## <a name="learning-loop"></a>Tanulási hurok

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>Úgy tűnik, hogy a tanulási hurok nem tanul. Hogyan javíthatom ki?

A tanulási huroknak néhány ezer jutalmazási hívásra van szüksége, mielőtt rangsorolja a rangsorolási hívásokat. 

Ha nem biztos abban, hogy hogyan viselkedik a tanulási hurok jelenleg, futtasson egy [Offline értékelést](concepts-offline-evaluation.md), és alkalmazza a javított tanulási szabályzatot. 

## <a name="next-steps"></a>További lépések

[A modell frissítési gyakoriságának konfigurálása](how-to-settings.md#model-update-frequency)