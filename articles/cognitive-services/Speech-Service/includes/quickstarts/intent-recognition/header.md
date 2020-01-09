---
title: 'Gyors útmutató: beszédek, szándékok és entitások felismerése – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 1/02/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a833d39ab91cd803f066d707306a6ff648d37e8f
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660503"
---
Ebben a rövid útmutatóban a [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) és a Language UNDERSTANDING (Luis) szolgáltatást fogja használni a mikrofonból rögzített hangadatokból való felismeréshez. Pontosabban a Speech SDK használatával rögzítheti a beszédet és a LUIS egy előre elkészített tartományát, hogy azonosítsa az otthoni automatizálás célját, például a fény bekapcsolását és kikapcsolását. 

Néhány előfeltétel kielégítése után a beszédfelismerés felismerése és a szándékok egy mikrofonból való felismerése csak néhány lépést vesz igénybe:

> [!div class="checklist"]
>
> * Hozzon létre egy ````SpeechConfig```` objektumot az előfizetési kulcsból és régióból.
> * Hozzon létre egy ````IntentRecognizer```` objektumot a fenti ````SpeechConfig```` objektum használatával.
> * A ````IntentRecognizer```` objektum használatával a felismerési folyamat elindítható egyetlen kilépéshez.
> * Vizsgálja meg a visszaadott ````IntentRecognitionResult````.
