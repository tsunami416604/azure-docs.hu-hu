---
title: A beszédfelismerési SDK – Beszédfelismerési szolgáltatás hibaelhárítása
titleSuffix: Azure Cognitive Services
description: Ez a cikk a beszédfelismerési SDK használata során felmerülő problémák megoldásához nyújt segítséget.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: jhakulin
ms.openlocfilehash: 421b9adf4ae5d2c641484e646bea096716d46cca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74815409"
---
# <a name="troubleshoot-the-speech-sdk"></a>A Speech SDK hibaelhárítása

Ez a cikk a beszédfelismerési SDK használata során felmerülő problémák megoldásához nyújt segítséget.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>Hiba: A WebSocket-frissítés hitelesítési hibával (403) nem sikerült

Lehet, hogy a régió vagy a szolgáltatás nem megfelelő végpontot. Ellenőrizze az URI-t, hogy helyes-e.

Emellett lehet, hogy probléma van az előfizetési kulccsal vagy az engedélyezési jogkivonattal. További információt a következő szakaszban talál.

## <a name="error-http-403-forbidden-or-http-401-unauthorized"></a>Hiba: HTTP 403 Tiltott vagy HTTP 401 jogosulatlan

Ezt a hibát gyakran hitelesítési problémák okozzák. Az érvényes `Ocp-Apim-Subscription-Key` vagy `Authorization` fejléc nélküli csatlakozási kérelmeket a rendszer 403-as vagy 401-es állapotúan utasítja el.

* Ha előfizetési kulcsot használ a hitelesítéshez, a hiba a következők miatt jelenhet meg:

    - Az előfizetési kulcs hiányzik vagy érvénytelen
    - Túllépte az előfizetés használati kvótáját

* Ha hitelesítéshez engedélyezési jogkivonatot használ, a hiba a következők miatt jelenhet meg:

    - Az engedélyezési jogkivonat érvénytelen
    - Az engedélyezési jogkivonat lejárt

### <a name="validate-your-subscription-key"></a>Az előfizetési kulcs ellenőrzése

Az alábbi parancsok futtatásával ellenőrizheti, hogy rendelkezik-e érvényes előfizetési kulccsal.

> [!NOTE]
> Cserélje `YOUR_SUBSCRIPTION_KEY` `YOUR_REGION` le, és a saját előfizetési kulcs és a kapcsolódó régió.

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

Ha érvényes előfizetési kulcsot adott meg, a parancs egy engedélyezési jogkivonatot ad vissza, ellenkező esetben a rendszer hibát ad vissza.

### <a name="validate-an-authorization-token"></a>Engedélyezési jogkivonat ellenőrzése

Ha hitelesítéshez engedélyezési jogkivonatot használ, futtassa az alábbi parancsok egyikét annak ellenőrzéséhez, hogy az engedélyezési jogkivonat még mindig érvényes-e. A tokenek 10 percig érvényesek.

> [!NOTE]
> Cserélje `YOUR_AUDIO_FILE` le az előre felvett hangfájl elérési útját. Cserélje `YOUR_ACCESS_TOKEN` le az előző lépésben visszaadott engedélyezési jogkivonatra. Cserélje `YOUR_REGION` le a megfelelő régióra.

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

    # Read audio into byte array.
    $audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

    $RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

    # Show the result.
    $RecoResponse
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Transfer-Encoding: chunked" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
    ```

Ha érvényes engedélyezési tokent adott meg, a parancs visszaadja a hangfájl átírását, ellenkező esetben a rendszer hibát ad vissza.

---

## <a name="error-http-400-bad-request"></a>Hiba: HTTP 400 rossz kérés

Ez a hiba általában akkor fordul elő, ha a kérelemtörzs érvénytelen hangadatokat tartalmaz. Csak wav formátum támogatott. Ellenőrizze a kérelem fejléceit is, hogy megadja-e `Content-Length`a megfelelő értékeket a és a számára. `Content-Type`

## <a name="error-http-408-request-timeout"></a>Hiba: HTTP 408 kérelem időmeghosszabbítása

A hiba valószínűleg azért fordul elő, mert nem küld hangadatokat a szolgáltatásnak. Ezt a hibát hálózati problémák is okozhatják.

## <a name="recognitionstatus-in-the-response-is-initialsilencetimeout"></a>"RecognitionStatus" a válasz "InitialSilenceTimeout"

Ezt a problémát általában a hangadatok okozzák. Ez a hiba a következők miatt jelenhet meg:

* Van egy hosszú szakaszon a csend elején a hang. Ebben az esetben a szolgáltatás néhány másodperc múlva leállítja az elismerést, és visszatér `InitialSilenceTimeout`.

* A hang nem támogatott kodek formátumot használ, ami azt eredményezi, hogy a hangadatokat csendként kell kezelni.

## <a name="next-steps"></a>További lépések

* [A kibocsátási megjegyzések áttekintése](releasenotes.md)
