---
title: Beszédtárolók telepítése
titleSuffix: Azure Cognitive Services
description: Részletek a beszéd esernyő kormányrúd konfigurációs lehetőségeket.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 0257f3af44cc85d0a3656472db224ae5a7e19161
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874345"
---
### <a name="speech-umbrella-chart"></a>Beszéd (esernyő diagram)

A legfelső szintű "esernyő" diagram értékei felülbírálják a megfelelő aldiagramértékeket. Ezért itt kell hozzáadni az összes helyszíni testreszabott értéket.

|Paraméter|Leírás|Alapértelmezett|
| -- | -- | -- | -- |
| `speechToText.enabled` | Azt **jelzi, hogy a beszéd-szöveg** szolgáltatás engedélyezve van-e. | `true` |
| `speechToText.verification.enabled` | Azt `helm test` jelzi, hogy engedélyezve **van-e a beszéd-szöveg** szolgáltatás. | `true` |
| `speechToText.verification.image.registry` | A docker-rendszertár, amely `helm test` a **beszéd-szöveg** szolgáltatás tesztelésére szolgál. Helm külön podot hoz létre a fürtön belül tesztelésre, és lekéri a *teszthasználatú* lemezképet ebből a beállításjegyzékből. | `docker.io` |
| `speechToText.verification.image.repository` | A docker-rendszertár, amely `helm test` a **beszéd-szöveg** szolgáltatás tesztelésére szolgál. Helm tesztpod használja ezt a tárházat a *teszthasználat-rendszerkép* lekérése. | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | A **beszéd-szöveg** szolgáltatáshoz `helm test` használt docker-képcímke. Helm tesztpod használja ezt a címkét, hogy lekéri *teszt-használat* kép. | `latest` |
| `speechToText.verification.image.pullByHash` | Azt jelzi, hogy a *teszthasználatú docker-lemezképet* kivonat oltja-e le. Ha `true` `speechToText.verification.image.hash` hozzá kell adni, érvényes képkivonat-értékkel. | `false` |
| `speechToText.verification.image.arguments` | A *teszthasználatú docker-rendszerkép* végrehajtásához használt argumentumok. Helm tesztpod átadja ezeket az érveket a tárolóba futás `helm test`közben. | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | Azt jelzi, hogy engedélyezve **van-e a szövegfelolvasó** szolgáltatás. | `true` |
| `textToSpeech.verification.enabled` | Azt `helm test` jelzi, hogy engedélyezve **van-e a beszéd-szöveg** szolgáltatás. | `true` |
| `textToSpeech.verification.image.registry` | A docker-rendszertár, amely `helm test` a **beszéd-szöveg** szolgáltatás tesztelésére szolgál. Helm külön podot hoz létre a fürtön belül tesztelésre, és lekéri a *teszthasználatú* lemezképet ebből a beállításjegyzékből. | `docker.io` |
| `textToSpeech.verification.image.repository` | A docker-rendszertár, amely `helm test` a **beszéd-szöveg** szolgáltatás tesztelésére szolgál. Helm tesztpod használja ezt a tárházat a *teszthasználat-rendszerkép* lekérése. | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | A **beszéd-szöveg** szolgáltatáshoz `helm test` használt docker-képcímke. Helm tesztpod használja ezt a címkét, hogy lekéri *teszt-használat* kép. | `latest` |
| `textToSpeech.verification.image.pullByHash` | Azt jelzi, hogy a *teszthasználatú docker-lemezképet* kivonat oltja-e le. Ha `true` `textToSpeech.verification.image.hash` hozzá kell adni, érvényes képkivonat-értékkel. | `false` |
| `textToSpeech.verification.image.arguments` | A *teszthasználatú* docker-lemezképpel végrehajtandó argumentumok. A helm tesztpod ezeket az argumentumokat a tárolóba továbbítja futás `helm test`közben. | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |