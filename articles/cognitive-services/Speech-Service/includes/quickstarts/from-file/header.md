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
ms.openlocfilehash: 2f7ec24667514fb131af29321a53f97210e58fc9
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74819338"
---
Ebben a rövid útmutatóban a [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) használatával ismeri fel a hangfájlok beszédét. Néhány előfeltétel kielégítése után a beszéd egy fájlból való felismerése csak öt lépést vesz igénybe:
> [!div class="checklist"]
> * Hozzon létre egy ````SpeechConfig```` objektumot az előfizetési kulcsból és régióból.
> * Hozzon létre egy ````AudioConfig```` objektumot, amely megadja a t. WAV-fájlnév.
> * Hozzon létre egy ````SpeechRecognizer```` objektumot a fenti ````SpeechConfig```` és ````AudioConfig```` objektumok használatával.
> * A ````SpeechRecognizer```` objektum használatával a felismerési folyamat elindítható egyetlen kilépéshez.
> * Vizsgálja meg a visszaadott ````SpeechRecognitionResult````.
