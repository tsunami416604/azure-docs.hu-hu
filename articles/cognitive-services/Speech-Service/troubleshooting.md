---
title: Cognitive Services beszéd SDK hibáinak elhárítása
description: Problémajegyek hibaelhárítási Cognitive Services beszédfelismerő SDK
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: wolfma
ms.openlocfilehash: ff8aba562cfd2d6d54c708ee7fdc4c6ca7185f29
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284122"
---
# <a name="troubleshooting-speech-services-sdk"></a>Hibaelhárítási Speech Services SDK-val

Ez a cikk előforduló beszéd SDK-val kapcsolatos problémák megoldásához információkat nyújt.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>Hiba történt `WebSocket Upgrade failed with an authentication error (403).`

Előfordulhat, hogy a nem megfelelő végpont az régió vagy szolgáltatás. Ellenőrizze, hogy helyes-e az URI-t. A következő szakaszban is láthatja, ahogy ez az előfizetési kulcs vagy engedélyezési hiba is lehet tokent.

## <a name="error-http-403-forbidden-or-error-http-401-unauthorized"></a>Hiba `HTTP 403 Forbidden` vagy hiba `HTTP 401 Unauthorized`

Ez a hiba gyakran okozza a hitelesítési problémákat. Csatlakozási kérelmek nem egy érvényes `Ocp-Apim-Subscription-Key` vagy `Authorization` fejléc visszautasítja 401-es vagy a 403-as állapotú.

* Ha egy előfizetési kulcsot használ a hitelesítéshez, a ok lehet:

    - az előfizetési kulcs nem található vagy érvénytelen
    - túllépte az előfizetés használati kvóta

* Ha egy engedélyezési jogkivonatot használ a hitelesítéshez, a ok lehet:

    - a hitelesítési jogkivonat érvénytelen
    - a hitelesítési jogkivonat lejárt

### <a name="validate-your-subscription-key"></a>Az előfizetési kulcs ellenőrzése

Ellenőrizze, hogy egy érvényes előfizetési kulcsot az alábbi parancsok futtatásával ellenőrizheti.

> [!NOTE]
> Cserélje le `YOUR_SUBSCRIPTION_KEY` és `YOUR_REGION` saját előfizetési kulcs és a társított régió jelölik.

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

### <a name="validate-an-authorization-token"></a>Egy engedélyezési jogkivonat érvényesítése

Egy engedélyezési jogkivonatot használnak a hitelesítéshez, ha annak ellenőrzéséhez, hogy az engedélyezési jogkivonatot továbbra is érvényes a következő parancsok egyikét futtatja. 10 percig jogkivonatok érvényesek.

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

Ez a hiba általában akkor fordul elő, ha a kérés törzsében érvénytelen hang adatokat tartalmaz. Csak `WAV` formátum támogatott. Ellenőrizze a kérelemfejlécek, hogy adja meg egy megfelelő `Content-Type` és `Content-Length`.

## <a name="error-http-408-request-timeout"></a>Hiba történt `HTTP 408 Request Timeout`

A hiba oka az, valószínűleg nincs hang adatot küld a szolgáltatásnak. Ezt a hibát emellett okozhatja hálózati probléma.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>A `RecognitionStatus` a válaszban `InitialSilenceTimeout`

Hang adatokat általában a a probléma okát. Példa:

* Nincs hosszú többhelyes csend a hanganyag elején. A szolgáltatás leáll a felismerés néhány másodperc múlva, és vissza `InitialSilenceTimeout`.
* A hanganyag egy nem támogatott kodek formátumot, azaz a hang kell tekinteni a csend használja.

## <a name="next-steps"></a>További lépések

* [Kiadási megjegyzések](releasenotes.md)

