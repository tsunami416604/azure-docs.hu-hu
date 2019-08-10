---
title: Hibaelhárítási Bing Speech | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: A Bing Speech használatakor felmerülő problémák megoldása.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: f222d9d4cf6c56dea0832938dcb132cf711491bc
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934061"
---
# <a name="troubleshooting-bing-speech"></a>Hibaelhárítási Bing Speech

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

## <a name="error-http-403-forbidden"></a>Hiba`HTTP 403 Forbidden`

A Speech Recognition API használatakor `HTTP 403 Forbidden` hibaüzenetet ad vissza.

### <a name="cause"></a>Ok

Ezt a hibát gyakran a hitelesítési problémák okozzák. A szolgáltatás érvényes `Ocp-Apim-Subscription-Key` vagy `Authorization` fejléc nélküli kapcsolatokat fogad `HTTP 403 Forbidden` el a válaszsal.

Ha előfizetés-kulcsot használ a hitelesítéshez, az oka lehet

- az előfizetési kulcs hiányzik vagy érvénytelen
- túllépte az előfizetési kulcs használati kvótáját
- a `Ocp-Apim-Subscription-Key` mező nincs beállítva a kérelem fejlécében, ha a REST API neve

Ha hitelesítési tokent használ a hitelesítéshez, a következő okok okozhatják a hibát.

- a `Authorization` Rest használatakor a fejléc hiányzik a kérelemből
- az engedélyezési fejlécben megadott engedélyezési jogkivonat érvénytelen.
- az engedélyezési jogkivonat lejárt. A hozzáférési token érvényessége 10 percet vesz igénybe

A hitelesítéssel kapcsolatos további információkért tekintse meg a [hitelesítés](How-to/how-to-authentication.md) lapot.

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

#### <a name="verify-that-your-subscription-key-is-valid"></a>Ellenőrizze, hogy érvényes-e az előfizetési kulcs

A következő parancs futtatásával ellenőrizhető. Vegye figyelembe, hogy a *YOUR_SUBSCRIPTION_KEY* a saját előfizetési kulcsával cseréli le. Ha az előfizetési kulcs érvényes, akkor az engedélyezési jogkivonat válasza JSON Web Tokenként (JWT) jelenik meg. Ellenkező esetben hibaüzenetet kap.

> [!NOTE]
> Cserélje `YOUR_SUBSCRIPTION_KEY` le a t a saját előfizetési kulcsára.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

# <a name="curltabcurl"></a>[Curl](#tab/curl)

A példa a curlot használja a Linuxon a bash használatával. Ha nem érhető el a platformon, lehet, hogy telepítenie kell a curlot. A példának a Cygwin on Windows, a git bash, a zsh és más rendszerhéjokon is működnie kell.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```
---

Győződjön meg arról, hogy ugyanazt az előfizetési kulcsot használja az alkalmazásban vagy a REST-kérelemben, amelyet a fentiekben használ.

#### <a name="verify-the-authorization-token"></a>Az engedélyezési jogkivonat ellenőrzése

Erre a lépésre csak akkor van szükség, ha a hitelesítéshez hitelesítési tokent használ. Futtassa a következő parancsot annak ellenőrzéséhez, hogy az engedélyezési jogkivonat továbbra is érvényes. A parancs POST-kérést küld a szolgáltatásnak, és válaszüzenetet vár a szolgáltatástól. Ha továbbra is http `403 Forbidden` -hibaüzenetet kap, ellenőrizze, hogy a hozzáférési token helyesen van-e beállítva, és nem járt-e le.

> [!IMPORTANT]
> A token érvényessége 10 percet vesz igénybe.
> [!NOTE]
> Cserélje `YOUR_AUDIO_FILE` le az elemet az előre rögzített hangfájl elérési útjára, `YOUR_ACCESS_TOKEN` és az előző lépésben visszaadott engedélyezési jogkivonattal.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authrization token returned by the token service.
$RecoRequestHeader = @{
  'Authorization' = 'Bearer '+ $OAuthToken;
  'Transfer-Encoding' = 'chunked'
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[Curl](#tab/curl)

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

---

## <a name="error-http-400-bad-request"></a>Hiba`HTTP 400 Bad Request`

Ennek az az oka, hogy a kérelem törzse érvénytelen hangadatokat tartalmaz. Jelenleg csak a WAV-fájlok támogatottak.

## <a name="error-http-408-request-timeout"></a>Hiba`HTTP 408 Request Timeout`

A hiba valószínűleg azért van, mert nem küld hangadatokat a szolgáltatásnak, és a szolgáltatás időtúllépés után visszaadja ezt a hibát. REST API esetén a hangadatokat a kérelem törzsében kell elhelyezni.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>A `RecognitionStatus` válaszban a`InitialSilenceTimeout`

A hangadatok általában a probléma okának okai. Például:

- a hang hosszú csendben tart az elején. A szolgáltatás néhány másodperc elteltével leállítja az elismerést, és visszaadja `InitialSilenceTimeout`azt.
- a hang nem támogatott codec formátumot használ, így a hangadatok csendként lesznek kezelve.
