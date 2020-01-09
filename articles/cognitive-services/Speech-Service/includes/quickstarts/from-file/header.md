---
title: 'Rövid útmutató: beszéd felismerése hangfájlból – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: e333b156eaf9c4b6e09e631513ea099018f0691b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468386"
---
Ebben a rövid útmutatóban a [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) használatával ismeri fel a hangfájlok beszédét. Néhány előfeltétel kielégítése után a beszéd egy fájlból való felismerése csak öt lépést vesz igénybe:
> [!div class="checklist"]
> * Hozzon létre egy ````SpeechConfig```` objektumot az előfizetési kulcsból és régióból.
> * Hozzon létre egy ````AudioConfig```` objektumot, amely megadja a t. WAV-fájlnév.
> * Hozzon létre egy ````SpeechRecognizer```` objektumot a fenti ````SpeechConfig```` és ````AudioConfig```` objektumok használatával.
> * A ````SpeechRecognizer```` objektum használatával a felismerési folyamat elindítható egyetlen kilépéshez.
> * Vizsgálja meg a visszaadott ````SpeechRecognitionResult````.
