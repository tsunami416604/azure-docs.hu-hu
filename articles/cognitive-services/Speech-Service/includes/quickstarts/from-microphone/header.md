---
title: 'Gyors útmutató: beszéd felismerése egy mikrofon-beszédfelismerési szolgáltatásból'
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
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468994"
---
Ebben a rövid útmutatóban a [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) használatával interaktív módon felismerheti a mikrofon bemenetét, és lekérheti a rögzített hanggal való átírást. Egyszerűen integrálhatja ezt a funkciót az alkalmazásaiba vagy eszközeibe az általános felismerési feladatokhoz, például a beszélgetések átírásához. Emellett összetettebb integrációk is használhatók, például a bot Framework és a Speech SDK együttes használata a hangsegédek létrehozásához.

Néhány előfeltétel kielégítése után a mikrofonból való beszéd felismerése csak négy lépést vesz igénybe:

> [!div class="checklist"]
> * Hozzon létre egy `SpeechConfig` objektumot az előfizetési kulcsból és régióból.
> * Hozzon létre egy `SpeechRecognizer` objektumot a fenti `SpeechConfig` objektum használatával.
> * A `SpeechRecognizer` objektum használatával a felismerési folyamat elindítható egyetlen kilépéshez.
> * Vizsgálja meg a visszaadott `SpeechRecognitionResult`.
