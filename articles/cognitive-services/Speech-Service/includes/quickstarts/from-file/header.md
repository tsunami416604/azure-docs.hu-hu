---
title: 'Rövid útmutató: beszéd felismerése hangfájlból – beszédfelismerési szolgáltatás'
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
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400545"
---
Ebben a rövid útmutatóban a [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) használatával ismeri fel a hangfájlok beszédét. Néhány előfeltétel kielégítése után a beszédek felismerése csak néhány lépést vesz igénybe:
> [!div class="checklist"]
> * Hozzon létre egy `SpeechConfig` objektumot az előfizetési kulcs és a régió alapján.
> * Hozzon létre egy `AudioConfig` objektumot, amely megadja a t. WAV-fájlnév.
> * Hozzon létre egy `SpeechRecognizer` objektumot `SpeechConfig` a `AudioConfig` fenti és objektumok használatával.
> * Az `SpeechRecognizer` objektum használatával egyetlen lemondás esetén indíthatja el az elismerési folyamatot.
> * Vizsgálja meg a `SpeechRecognitionResult` visszaadott értéket.
