---
title: 'Gyors útmutató: beszéd felismerése egy mikrofon-beszédfelismerési szolgáltatásból'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 11/20/2019
ms.author: erhopf
ms.openlocfilehash: a72597163a8c11cd8b515d052dc69992784e61e0
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818751"
---
Ebben a rövid útmutatóban a [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) használatával interaktív módon felismerheti a mikrofon bemenetét, és lekérheti a rögzített hanggal való átírást. Egyszerűen integrálhatja ezt a funkciót az alkalmazásaiba vagy eszközeibe az általános felismerési feladatokhoz, például a beszélgetések átírásához. Emellett összetettebb integrációk is használhatók, például a bot Framework és a Speech SDK együttes használata a hangsegédek létrehozásához.

Néhány előfeltétel kielégítése után a mikrofonból való beszéd felismerése csak négy lépést vesz igénybe:

> [!div class="checklist"]
> * Hozzon létre egy `SpeechConfig` objektumot az előfizetési kulcsból és régióból.
> * Hozzon létre egy `SpeechRecognizer` objektumot a fenti `SpeechConfig` objektum használatával.
> * A `SpeechRecognizer` objektum használatával a felismerési folyamat elindítható egyetlen kilépéshez.
> * Vizsgálja meg a visszaadott `SpeechRecognitionResult`.
