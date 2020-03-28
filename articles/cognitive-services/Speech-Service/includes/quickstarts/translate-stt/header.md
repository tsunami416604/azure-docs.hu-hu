---
title: 'Rövid útmutató: Beszéd-szöveg fordítása – Beszédszolgáltatás'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: d4781808ce8e80f62e86ce1d0c6db9c38b2636d0
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980941"
---
Ebben a rövid útmutatóban a [Beszéd SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) segítségével interaktívan lefordítja a beszédeket az egyik nyelvről a másik nyelvű szövegre. Néhány előfeltétel teljesítése után a beszéd-szöveg fordítás a következő öt lépést teszi:
> [!div class="checklist"]
> * Hozzon ````SpeechConfig```` létre egy objektumot az előfizetési kulcsból és a régióból.
> * Frissítse ````SpeechConfig```` az objektumot a forrás- és célnyelvek megadásához.
> * Objektum ````TranslationRecognizer```` létrehozása felülről az ````SpeechConfig```` objektum használatával.
> * Az ````TranslationRecognizer```` objektum használatával indítsa el a felismerési folyamat egyetlen utterance (kifejezés).
> * Vizsgálja ````TranslationRecognitionResult```` meg a visszaküldötteket.
