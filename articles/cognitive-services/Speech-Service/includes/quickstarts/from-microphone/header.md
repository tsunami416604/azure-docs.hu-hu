---
title: 'Rövid útmutató: Beszédfelismerés mikrofonból – Beszédszolgáltatás'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/20/2019
ms.author: erhopf
ms.openlocfilehash: ab51fe0323ea23c16cb52aa7a0570f8d51d40b2f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75468994"
---
Ebben a rövid útmutatóban a [Beszéd sdk](~/articles/cognitive-services/speech-service/speech-sdk.md) segítségével interaktívan felismerheti a mikrofonbemenetből érkező beszédfelismerést, és leszeretné szerezni a rögzített hang szövegátírását. Ezt a funkciót egyszerűen integrálhatja alkalmazásaiba vagy eszközeibe a gyakori felismerési feladatokhoz, például a beszélgetések átírásához. Összetettebb integrációkhoz is használható, például a Robot keretrendszer és a beszédsdk használatával hangasszisztensek létrehozásához.

Néhány előfeltétel teljesítése után a mikrofonból érkező beszéd felismerése csak négy lépést tesz:

> [!div class="checklist"]
> * Hozzon `SpeechConfig` létre egy objektumot az előfizetési kulcsból és a régióból.
> * Objektum `SpeechRecognizer` létrehozása felülről az `SpeechConfig` objektum használatával.
> * Az `SpeechRecognizer` objektum használatával indítsa el a felismerési folyamat egyetlen utterance (kifejezés).
> * Vizsgálja `SpeechRecognitionResult` meg a visszaküldötteket.
