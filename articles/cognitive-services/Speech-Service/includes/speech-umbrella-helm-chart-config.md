---
title: Telepítse a beszédfelismerési tárolókhoz
titleSuffix: Azure Cognitive Services
description: A beszédfelismerés összevonó helm diagram konfigurációs beállításokat ismerteti.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: ed64412ccf9d192506fafe546b1ccee7941aa43a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711812"
---
### <a name="speech-umbrella-chart"></a>Beszéd (összevonó diagram)

A legfelső szintű "összevonó" diagram értékeit a megfelelő alárendelt értékeinek felülírása. Ezért az összes helyszíni egyéni itt fel kell venni.

|Paraméter|Leírás|Alapértelmezett|
| -- | -- | -- | -- |
| `speechToText.enabled` | -E a **hang-szöveg transzformációs** szolgáltatás engedélyezve van. | `true` |
| `speechToText.verification.enabled` | -E a `helm test` funkció **hang-szöveg transzformációs** szolgáltatás engedélyezve van. | `true` |
| `speechToText.verification.image.registry` | A docker-rendszerkép tárház, amely `helm test` teszteléséhez használja **hang-szöveg transzformációs** szolgáltatás. Helm külön pod tesztelési a fürtben hoz létre, és lekéri a *teszt használható* rendszerképet a beállításjegyzékből. | `docker.io` |
| `speechToText.verification.image.repository` | A docker-rendszerkép tárház, amely `helm test` teszteléséhez használja **hang-szöveg transzformációs** szolgáltatás. Helm teszt pod használja a tárház lekéréses *teszt használható* kép. | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | A docker rendszerkép címke együttes `helm test` a **hang-szöveg transzformációs** szolgáltatás. Helm teszt pod használja ezt a címkét való lekérésére *teszt használható* kép. | `latest` |
| `speechToText.verification.image.pullByHash` | -E a *teszt használható* kivonata alapján kéri le a docker-rendszerkép. Ha `true`, `speechToText.verification.image.hash` vehet fel, az érvényes lemezkép-kivonat értéke. | `false` |
| `speechToText.verification.image.arguments` | Az argumentumok végrehajtásához használt a *teszt használható* docker-rendszerképet. Helm teszt pod ezek az argumentumok a tároló továbbítja futtatáskor `helm test`. | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | -E a **szöveg-hang transzformációs** szolgáltatás engedélyezve van. | `true` |
| `textToSpeech.verification.enabled` | -E a `helm test` funkció **hang-szöveg transzformációs** szolgáltatás engedélyezve van. | `true` |
| `textToSpeech.verification.image.registry` | A docker-rendszerkép tárház, amely `helm test` teszteléséhez használja **hang-szöveg transzformációs** szolgáltatás. Helm külön pod tesztelési a fürtben hoz létre, és lekéri a *teszt használható* rendszerképet a beállításjegyzékből. | `docker.io` |
| `textToSpeech.verification.image.repository` | A docker-rendszerkép tárház, amely `helm test` teszteléséhez használja **hang-szöveg transzformációs** szolgáltatás. Helm teszt pod használja a tárház lekéréses *teszt használható* kép. | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | A docker rendszerkép címke együttes `helm test` a **hang-szöveg transzformációs** szolgáltatás. Helm teszt pod használja ezt a címkét való lekérésére *teszt használható* kép. | `latest` |
| `textToSpeech.verification.image.pullByHash` | -E a *teszt használható* kivonata alapján kéri le a docker-rendszerkép. Ha `true`, `textToSpeech.verification.image.hash` vehet fel, az érvényes lemezkép-kivonat értéke. | `false` |
| `textToSpeech.verification.image.arguments` | Hajtsa végre az argumentumok a *teszt használható* docker-rendszerképet. A helm teszt pod ezek az argumentumok tároló továbbítja futtatáskor `helm test`. | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |