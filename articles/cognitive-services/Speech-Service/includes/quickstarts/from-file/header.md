---
title: 'Rövid útmutató: beszéd felismerése hangfájlból – beszédfelismerési szolgáltatás'
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
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "76037780"
---
Ebben a rövid útmutatóban a [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) használatával ismeri fel a hangfájlok beszédét. Néhány előfeltétel kielégítése után a beszédek felismerése csak néhány lépést vesz igénybe:
> [!div class="checklist"]
> * Hozzon létre egy `SpeechConfig` objektumot az előfizetési kulcsból és régióból.
> * Hozzon létre egy `AudioConfig` objektumot, amely megadja a t. WAV-fájlnév.
> * Hozzon létre egy `SpeechRecognizer` objektumot a fenti `SpeechConfig` és `AudioConfig` objektumok használatával.
> * A `SpeechRecognizer` objektum használatával a felismerési folyamat elindítható egyetlen kilépéshez.
> * Vizsgálja meg a visszaadott `SpeechRecognitionResult`.
