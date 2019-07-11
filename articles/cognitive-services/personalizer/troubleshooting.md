---
title: Hibaelhárítás – Personalizer
titleSuffix: Azure Cognitive Services
description: Hibaelhárítási Personalizer kapcsolatos kérdéseket is található ebben a cikkben.
author: edjez
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: article
ms.date: 06/15/2019
ms.author: edjez
ms.openlocfilehash: be6119d96b89622f45db1099a47e858a5893c2cb
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722245"
---
# <a name="personalizer-troubleshooting"></a>Personalizer hibáinak elhárítása

Ez a cikk Personalizer kapcsolatos gyakori hibaelhárítási kérdésekre adott válaszokat tartalmazza.

## <a name="learning-loop"></a>Tanulási hurok

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>A learning hurok ismerje meg, nem Microsoft. Hogyan javíthatom ezt ki?

A learning hurok néhány ezer ellenszolgáltatás hívások van szüksége, mielőtt rangsorolják hívások rangsorolhatja hatékonyan. 

Ha biztos abban, hogy hogyan a tanulási hurok jelenleg viselkedik, futtassa az [offline értékelési](concepts-offline-evaluation.md), és a alkalmazni a javított learning szabályzatot. 

## <a name="next-steps"></a>További lépések

[A modell frissítési gyakoriság beállítása](how-to-settings.md#model-update-frequency)