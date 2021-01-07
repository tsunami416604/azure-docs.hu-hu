---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: a752b723980c45e1488f1471ee8139190853b54c
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2021
ms.locfileid: "97966590"
---
Ebben a rövid útmutatóban a [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) és a Language UNDERSTANDING (Luis) szolgáltatást fogja használni a mikrofonból rögzített hangadatokból való felismeréshez. Pontosabban a Speech SDK használatával rögzítheti a beszédet és a LUIS egy előre elkészített tartományát, hogy azonosítsa az otthoni automatizálás célját, például a fény bekapcsolását és kikapcsolását. 

Néhány előfeltétel kielégítése után a beszédfelismerés felismerése és a szándékok egy mikrofonból való felismerése csak néhány lépést vesz igénybe:

> [!div class="checklist"]
>
> * Hozzon létre egy `SpeechConfig` objektumot az előfizetési kulcs és a régió alapján.
> * Hozzon létre egy `IntentRecognizer` objektumot a `SpeechConfig` fenti objektum használatával.
> * Az `IntentRecognizer` objektum használatával egyetlen lemondás esetén indíthatja el az elismerési folyamatot.
> * Vizsgálja meg a `IntentRecognitionResult` visszaadott értéket.

> [!NOTE]
> Létrehozhat egy LanguageUnderstandingModel a végpont URL-címének a FromEndpoint metódusba való átadásával.
> A Speech SDK csak a LUIS 2.0-s végpontokat támogatja, és a LUIS v 2.0-végpontok mindig az alábbi két minta egyikét követik:
> * `https://{AzureResourceName}.cognitiveservices.azure.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`
> * `https://{Region}.api.cognitive.microsoft.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`
