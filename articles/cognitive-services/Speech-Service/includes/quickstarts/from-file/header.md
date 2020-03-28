---
title: 'Rövid útmutató: Beszédfelismerés hangfájlból – Beszédszolgáltatás'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: dapine
ms.openlocfilehash: 2b6270535c0cf69549a7412bd38d9207454e5500
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76037780"
---
Ebben a rövid útmutatóban a [Beszéd SDK-t](~/articles/cognitive-services/speech-service/speech-sdk.md) fogja használni a hangfájlból érkező beszéd felismerésére. Néhány előfeltétel teljesítése után a fájlból származó beszéd felismerése csak néhány lépést tesz leve:
> [!div class="checklist"]
> * Hozzon `SpeechConfig` létre egy objektumot az előfizetési kulcsból és a régióból.
> * Hozzon `AudioConfig` létre egy objektumot, amely megadja a . WAV fájlnév.
> * Hozzon `SpeechRecognizer` létre `SpeechConfig` egy `AudioConfig` objektumot a felülről és az objektumoksegítségével.
> * Az `SpeechRecognizer` objektum használatával indítsa el a felismerési folyamat egyetlen utterance (kifejezés).
> * Vizsgálja `SpeechRecognitionResult` meg a visszaküldötteket.
