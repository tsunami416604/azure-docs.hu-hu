---
title: Kognitív szolgáltatások beszéd SDK hibaelhárítása |} Microsoft Docs
description: Hiba történt a hívjuk kognitív szolgáltatások beszéd SDK
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: wolfma
ms.openlocfilehash: 16eaebcf9494ab57521068a9418ccf2ac7f5a8fe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348983"
---
# <a name="troubleshooting-speech-services-sdk"></a>Beszéd szolgáltatások SDK hibaelhárítása

Ez a cikk a beszédfelismerés SDK használatakor esetleg előforduló problémák megoldásához információkat biztosít.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>Hiba történt `WebSocket Upgrade failed with an authentication error (403).`

Előfordulhat, hogy a megfelelő végpont a régió vagy a szolgáltatás. Ellenőrizze az URI-t győződjön meg arról, hogy helyes-e. A következő szakaszban is láthatja, ez is lehet probléma van az Előfizetés kulcs vagy engedélyezési jogkivonat.

## <a name="error-http-403-forbidden-or-error-http-401-unauthorized"></a>Hiba `HTTP 403 Forbidden` vagy hiba `HTTP 401 Unauthorized`

Ez a hiba gyakran okozza a hitelesítéssel kapcsolatos problémáiról. Csatlakozási kérések nélkül egy érvényes `Ocp-Apim-Subscription-Key` vagy `Authorization` fejléc 401-es vagy a 403-as állapotú utasítja el.

* Ha egy előfizetés kulcsot használ a hitelesítéshez, a okozhatják:

    - az Előfizetés kulcs nem található vagy érvénytelen
    - az előfizetés memóriahasználati kvóta túllépve

* Ha egy engedélyezési jogkivonatot használ a hitelesítéshez, a okozhatják:

    - az engedélyezési jogkivonat érvénytelen.
    - az engedélyezési jogkivonat érvényessége lejárt

### <a name="validate-your-subscription-key"></a>Az Előfizetés kulcs ellenőrzése

Ellenőrizheti, hogy ellenőrizze, hogy egy érvényes előfizetés-kulcs az alábbi parancsok futtatásával.

> [!NOTE]
> Cserélje le `YOUR_SUBSCRIPTION_KEY` és `YOUR_REGION` saját előfizetés kulcs és a kapcsolódó régióban, illetve.

* PowerShell

    ```Powershell
    $FetchTokenHeader = @{
      'Content-type'='application/x-www-form-urlencoded'
      'Content-Length'= '0'
      'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
    }
    $OAuthToken = Invoke-RestMethod -Method POST -Uri https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader
    $OAuthToken
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0"
    ```

### <a name="validate-an-authorization-token"></a>Egy engedélyezési jogkivonatot ellenőrzése

Egy engedélyezési jogkivonatot használnak a hitelesítéshez, ha annak ellenőrzéséhez, hogy az engedélyezési jogkivonat még érvényes a következő parancsok egyikét futtatja. 10 percig jogkivonatok érvényesek.

> [!NOTE]
> Cserélje le `YOUR_AUDIO_FILE` az elérési útját a korábban rögzített hangfájl `YOUR_ACCESS_TOKEN` az az engedélyezési jogkivonatot adott vissza az előző lépésben és `YOUR_REGION` és a megfelelő régió.

* PowerShell

    ```Powershell
    $SpeechServiceURI =
    'https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US'
    
    # $OAuthToken is the authorization token returned by the token service.
    $RecoRequestHeader = @{
      'Authorization' = 'Bearer '+ $OAuthToken
      'Transfer-Encoding' = 'chunked'
      'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
    }
    
    # Read audio into byte array
    $audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")
    
    $RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes
    
    # Show the result
    $RecoResponse
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Transfer-Encoding: chunked" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
    ```

---

## <a name="error-http-400-bad-request"></a>Hiba történt `HTTP 400 Bad Request`

Ez a hiba általában akkor fordul elő, ha a kérés törzsében hang érvénytelen adatokat tartalmaz. Csak `WAV` formátum támogatott. Győződjön meg arról, hogy meg egy megfelelő a kérelmet a fejlécek ellenőrizze `Content-Type` és `Content-Length`.

## <a name="error-http-408-request-timeout"></a>Hiba történt `HTTP 408 Request Timeout`

A hiba valószínűleg azért, mert a szolgáltatás hang adatküldést alatt. Ezt a hibát emellett okozhatja hálózati problémák.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>A `RecognitionStatus` a válasz: `InitialSilenceTimeout`

Hangadatok oka általában az okozza a problémát. Példa:

* A hang elején csend hosszú stretch van. A szolgáltatás leállítása a felismerési néhány másodpercen belül, és térjen vissza `InitialSilenceTimeout`.
* A hang egy nem támogatott kodek formátumot, aminek következtében a hangadatok kell tekinteni a csend használja.

## <a name="next-steps"></a>További lépések

* [Kiadási megjegyzések](releasenotes.md)

