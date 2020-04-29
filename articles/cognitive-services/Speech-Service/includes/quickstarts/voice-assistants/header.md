---
title: 'Gyors útmutató: egyéni hangsegéd létrehozása – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/20/2020
ms.author: travisw
ms.openlocfilehash: bb88ef7f048b2454f05ef33286e59277070c3ea0
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241142"
---
Ebben a rövid útmutatóban a [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) használatával hozzon létre egy egyéni hangsegéd-alkalmazást, amely egy már létrehozott és konfigurált robothoz csatlakozik. Ha robotot kell létrehoznia, tekintse meg [a kapcsolódó oktatóanyagot](~/articles/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk.md) az átfogóbb útmutatóhoz.

Néhány előfeltétel kielégítése után az egyéni hangsegéd csatlakoztatása csak néhány lépést vesz igénybe:
> [!div class="checklist"]
> * Hozzon `BotFrameworkConfig` létre egy objektumot az előfizetési kulcs és a régió alapján.
> * Hozzon `DialogServiceConnector` létre egy objektumot `BotFrameworkConfig` a fenti objektum használatával.
> * Az `DialogServiceConnector` objektum használatával megkezdheti a figyelési folyamatot egyetlen Kimondás esetén.
> * Vizsgálja meg `ActivityReceivedEventArgs` a visszaadott értéket.
