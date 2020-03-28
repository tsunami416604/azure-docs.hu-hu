---
title: 'Rövid útmutató: Egyéni hangasszisztens létrehozása – beszédszolgáltatás'
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80241142"
---
Ebben a rövid útmutatóban a [beszédfelismerési SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) használatával hozzon létre egy egyéni hangasszisztens-alkalmazást, amely már létrehozott és konfigurált robothoz csatlakozik. Ha létre kell hoznia egy robotot, tekintse meg [a kapcsolódó oktatóanyag](~/articles/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk.md) egy átfogóbb útmutatót.

Néhány előfeltétel teljesítése után az egyéni hangasszisztens csatlakoztatása csak néhány lépést tesz lehet:
> [!div class="checklist"]
> * Hozzon `BotFrameworkConfig` létre egy objektumot az előfizetési kulcsból és a régióból.
> * Objektum `DialogServiceConnector` létrehozása felülről az `BotFrameworkConfig` objektum használatával.
> * Az `DialogServiceConnector` objektum használatával indítsa el a figyelési folyamat egyetlen utterance (kifejezés).
> * Vizsgálja `ActivityReceivedEventArgs` meg a visszaküldötteket.
