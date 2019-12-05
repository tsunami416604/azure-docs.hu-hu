---
title: 'Gyors útmutató: beszédfelismerés a hangfájlba – beszédfelismerési szolgáltatás'
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
ms.openlocfilehash: 7d9a03f5a57473ce651560b261a4cf84b4ca4824
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74817862"
---
Ebben a rövid útmutatóban a [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) használatával konvertálja a szöveget egy hangfájlban a szintetizált beszédbe. Néhány előfeltétel kielégítése után a beszédfelismerés egy fájlba csak öt lépést vesz igénybe:
> [!div class="checklist"]
> * Hozzon létre egy ````SpeechConfig```` objektumot az előfizetési kulcsból és régióból.
> * Hozzon létre egy hangkonfigurációs objektumot, amely meghatározza a t. WAV-fájlnév.
> * Hozzon létre egy ````SpeechSynthesizer```` objektumot a fenti konfigurációs objektumok használatával.
> * A ````SpeechSynthesizer```` objektum használatával alakítsa át a szöveget a szintetizált beszédbe, és mentse a megadott hangfájlba.
> * Ellenőrizze, hogy a ````SpeechSynthesizer```` hibákat adott-e vissza.
