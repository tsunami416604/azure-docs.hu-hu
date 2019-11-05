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
ms.openlocfilehash: f69c00f9cf787a192c62f12a707927c5c51a1d31
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502946"
---
Ebben a rövid útmutatóban a [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) használatával konvertálja a szöveget egy hangfájlban a szintetizált beszédbe. Néhány előfeltétel kielégítése után a beszédfelismerés egy fájlba csak öt lépést vesz igénybe:
> [!div class="checklist"]
> * Hozzon létre egy ````SpeechConfig```` objektumot az előfizetési kulcsból és régióból.
> * Hozzon létre egy hangkonfigurációs objektumot, amely meghatározza a t. WAV-fájlnév.
> * Hozzon létre egy ````SpeechSynthesizer```` objektumot a fenti konfigurációs objektumok használatával.
> * A ````SpeechSynthesizer```` objektum használatával alakítsa át a szöveget a szintetizált beszédbe, és mentse a megadott hangfájlba.
> * Ellenőrizze, hogy a ````SpeechSynthesizer```` hibákat adott-e vissza.
