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
ms.date: 11/07/2019
ms.author: erhopf
ms.openlocfilehash: f3f28cdd8c3c77a5cde2ead86c49b41d54bab5d7
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961175"
---
Ebben a rövid útmutatóban a [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) használatával interaktívan felismerheti a mikrofonból rögzített hangadatokból származó beszédet. Néhány előfeltétel kielégítése után a mikrofonból való beszéd felismerése csak négy lépést vesz igénybe:

> [!div class="checklist"]
> * Hozzon létre egy `SpeechConfig` objektumot az előfizetési kulcsból és régióból.
> * Hozzon létre egy `SpeechRecognizer` objektumot a fenti `SpeechConfig` objektum használatával.
> * A `SpeechRecognizer` objektum használatával a felismerési folyamat elindítható egyetlen kilépéshez.
> * Vizsgálja meg a visszaadott `SpeechRecognitionResult`.
