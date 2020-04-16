---
title: 'Rövid útmutató: Beszédfelismerés hangfájlból – Beszédszolgáltatás'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: trbye
ms.openlocfilehash: a7c91775411f100a92dfcb0a7199dd4b25f6eca4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400545"
---
Ebben a rövid útmutatóban a [Beszéd SDK-t](~/articles/cognitive-services/speech-service/speech-sdk.md) fogja használni a hangfájlból érkező beszéd felismerésére. Néhány előfeltétel teljesítése után a fájlból származó beszéd felismerése csak néhány lépést tesz leve:
> [!div class="checklist"]
> * Hozzon `SpeechConfig` létre egy objektumot az előfizetési kulcsból és a régióból.
> * Hozzon `AudioConfig` létre egy objektumot, amely megadja a . WAV fájlnév.
> * Hozzon `SpeechRecognizer` létre `SpeechConfig` egy `AudioConfig` objektumot a felülről és az objektumoksegítségével.
> * Az `SpeechRecognizer` objektum használatával indítsa el a felismerési folyamat egyetlen utterance (kifejezés).
> * Vizsgálja `SpeechRecognitionResult` meg a visszaküldötteket.
