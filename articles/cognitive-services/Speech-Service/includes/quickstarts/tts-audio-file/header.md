---
title: 'Gyors útmutató: beszédfelismerés a hangfájlba – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 9b13d8fc3b77426a59dea5399223b79c4bb4b1a1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467219"
---
Ebben a rövid útmutatóban a [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) használatával konvertálja a szöveget egy hangfájlban a szintetizált beszédbe. Néhány előfeltétel kielégítése után a beszédfelismerés egy fájlba csak öt lépést vesz igénybe:
> [!div class="checklist"]
> * Hozzon létre egy ````SpeechConfig```` objektumot az előfizetési kulcsból és régióból.
> * Hozzon létre egy hangkonfigurációs objektumot, amely meghatározza a t. WAV-fájlnév.
> * Hozzon létre egy ````SpeechSynthesizer```` objektumot a fenti konfigurációs objektumok használatával.
> * A ````SpeechSynthesizer```` objektum használatával alakítsa át a szöveget a szintetizált beszédbe, és mentse a megadott hangfájlba.
> * Ellenőrizze, hogy a ````SpeechSynthesizer```` hibákat adott-e vissza.
