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
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 64f084f58ae4b12d92d6dee343a59dd56b1c351e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503506"
---
Ebben a rövid útmutatóban a [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) használatával interaktívan felismerheti a mikrofonból rögzített hangadatokból származó beszédet. Néhány előfeltétel kielégítése után a mikrofonból való beszéd felismerése csak négy lépést vesz igénybe:
> [!div class="checklist"]
> * Hozzon létre egy ````SpeechConfig```` objektumot az előfizetési kulcsból és régióból.
> * Hozzon létre egy ````SpeechRecognizer```` objektumot a fenti ````SpeechConfig```` objektum használatával.
> * A ````SpeechRecognizer```` objektum használatával a felismerési folyamat elindítható egyetlen kilépéshez.
> * Vizsgálja meg a visszaadott ````SpeechRecognitionResult````.
