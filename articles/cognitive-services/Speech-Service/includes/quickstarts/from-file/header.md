---
title: 'Rövid útmutató: beszéd felismerése hangfájlból – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: bbd3880ea679dc5fc86c0fc1ece101ca3fca74d9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504136"
---
Ebben a rövid útmutatóban a [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) használatával ismeri fel a hangfájlok beszédét. Néhány előfeltétel kielégítése után a beszéd egy fájlból való felismerése csak öt lépést vesz igénybe:
> [!div class="checklist"]
> * Hozzon létre egy ````SpeechConfig```` objektumot az előfizetési kulcsból és régióból.
> * Hozzon létre egy ````AudioConfig```` objektumot, amely megadja a t. WAV-fájlnév.
> * Hozzon létre egy ````SpeechRecognizer```` objektumot a fenti ````SpeechConfig```` és ````AudioConfig```` objektumok használatával.
> * A ````SpeechRecognizer```` objektum használatával a felismerési folyamat elindítható egyetlen kilépéshez.
> * Vizsgálja meg a visszaadott ````SpeechRecognitionResult````.
