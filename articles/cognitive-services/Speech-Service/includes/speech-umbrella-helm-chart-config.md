---
title: Beszédfelismerési tárolók telepítése
titleSuffix: Azure Cognitive Services
description: Részletek a Speech Umbrella Helm diagram konfigurációs lehetőségeiről.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: ed64412ccf9d192506fafe546b1ccee7941aa43a
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523069"
---
### <a name="speech-umbrella-chart"></a>Beszéd (esernyő diagram)

A legfelső szintű "esernyő" diagram értékei felülbírálják a megfelelő Aldiagram-értékeket. Ezért itt fel kell venni az összes helyszíni testreszabott értéket.

|Paraméter|Leírás|Alapértelmezett|
| -- | -- | -- | -- |
| `speechToText.enabled` | Azt határozza meg, hogy a **beszédfelismerési** szolgáltatás engedélyezve van-e. | `true` |
| `speechToText.verification.enabled` | Azt jelzi, hogy engedélyezve van-e a **beszédfelismerési** szolgáltatás `helm test` képessége. | `true` |
| `speechToText.verification.image.registry` | A `helm test` által a **beszédfelismerési** szolgáltatás teszteléséhez használt Docker-rendszerkép tárháza. A Helm külön hüvelyt hoz létre a fürtön belül a teszteléshez, és lekéri a *tesztelési célú* rendszerképet ebből a beállításjegyzékből. | `docker.io` |
| `speechToText.verification.image.repository` | A `helm test` által a **beszédfelismerési** szolgáltatás teszteléséhez használt Docker-rendszerkép tárháza. A Helm test Pod ezt a tárházat használja a *tesztelési célú* rendszerképek lekéréséhez. | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | A `helm test` által a **beszéd-szöveges** szolgáltatáshoz használt Docker-rendszerkép címkéje. A Helm test Pod ezt a címkét használja a *tesztelési célú* rendszerképek lekéréséhez. | `latest` |
| `speechToText.verification.image.pullByHash` | Azt határozza meg, hogy a rendszer *használja-e a tesztet* a Docker-rendszerképpel. Ha `true`, `speechToText.verification.image.hash`t kell hozzáadnia, érvényes képkivonat-értékkel. | `false` |
| `speechToText.verification.image.arguments` | A *tesztelési célú* Docker-rendszerkép végrehajtásához használt argumentumok. A Helm test Pod a `helm test`futtatásakor átadja ezeket az argumentumokat a tárolónak. | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | Azt jelzi, hogy engedélyezve van-e a **szöveg-beszéd** szolgáltatás. | `true` |
| `textToSpeech.verification.enabled` | Azt jelzi, hogy engedélyezve van-e a **beszédfelismerési** szolgáltatás `helm test` képessége. | `true` |
| `textToSpeech.verification.image.registry` | A `helm test` által a **beszédfelismerési** szolgáltatás teszteléséhez használt Docker-rendszerkép tárháza. A Helm külön hüvelyt hoz létre a fürtön belül a teszteléshez, és lekéri a *tesztelési célú* rendszerképet ebből a beállításjegyzékből. | `docker.io` |
| `textToSpeech.verification.image.repository` | A `helm test` által a **beszédfelismerési** szolgáltatás teszteléséhez használt Docker-rendszerkép tárháza. A Helm test Pod ezt a tárházat használja a *tesztelési célú* rendszerképek lekéréséhez. | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | A `helm test` által a **beszéd-szöveges** szolgáltatáshoz használt Docker-rendszerkép címkéje. A Helm test Pod ezt a címkét használja a *tesztelési célú* rendszerképek lekéréséhez. | `latest` |
| `textToSpeech.verification.image.pullByHash` | Azt határozza meg, hogy a rendszer *használja-e a tesztet* a Docker-rendszerképpel. Ha `true`, `textToSpeech.verification.image.hash`t kell hozzáadnia, érvényes képkivonat-értékkel. | `false` |
| `textToSpeech.verification.image.arguments` | A *tesztelési célú* Docker-képpel végrehajtandó argumentumok. A Helm test Pod a `helm test`futtatásakor átadja ezeket az argumentumokat a tárolónak. | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |