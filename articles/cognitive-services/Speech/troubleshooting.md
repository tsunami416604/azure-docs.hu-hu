---
title: Bing Speech elhárítása |} A Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Kapcsolatos problémák megoldását, a Bing Speech használatakor.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX
ms.openlocfilehash: 532916106f62e0236b8dd53cf7988a648355aef4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991861"
---
# <a name="troubleshooting-bing-speech"></a>Bing Speech elhárítása

## <a name="error-http-403-forbidden"></a>Hiba történt `HTTP 403 Forbidden`

Beszédfelismerő API használatakor adja vissza egy `HTTP 403 Forbidden` hiba.

### <a name="cause"></a>Ok

Ez a hiba gyakran okozza a hitelesítési problémákat. Kapcsolat nélküli kérelmek érvényes `Ocp-Apim-Subscription-Key` vagy `Authorization` fejléc utasítja el a szolgáltatást egy `HTTP 403 Forbidden` választ.

Ha az előfizetési kulcsot használ a hitelesítéshez, az oka az lehet

- az előfizetési kulcs nem található vagy érvénytelen
- az előfizetési kulcsot, a használati kvóta túllépésekor
- a `Ocp-Apim-Subscription-Key` mező nincs beállítva a kérelem fejlécét, amikor REST API hívása

Ha engedélyezési jogkivonatot használ a hitelesítéshez, a következő okok miatt sikerült okozza a problémát.

- a `Authorization` szabályozó fejléc nincs megadva a kérelemben, amikor REST használatával
- az engedélyezési jogkivonatot az engedélyeztetési fejléc megadott értéke érvénytelen
- a hitelesítési jogkivonat lejárt. A hozzáférési jogkivonat rendelkezik egy 10 perc elteltével

A hitelesítéssel kapcsolatos további információkért tekintse meg a [hitelesítési](How-to/how-to-authentication.md) lap.

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

#### <a name="verify-that-your-subscription-key-is-valid"></a>Ellenőrizze, hogy érvényes-e az előfizetési kulcs

Az ellenőrzéshez a következő parancsot futtathatja. Cserélje le a Megjegyzés *YOUR_SUBSCRIPTION_KEY* a saját előfizetés-kulccsal. Ha az előfizetési kulcs érvényes, kap a válaszban az engedélyezési jogkivonatra, a JSON webes jogkivonat (JWT). Egyébként hibaüzenetet kap választ.

> [!NOTE]
> Cserélje le `YOUR_SUBSCRIPTION_KEY` a saját előfizetés-kulccsal.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/Powershell)

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

# <a name="curltabcurl"></a>[A curl](#tab/curl)

A példában a curl Linux bash-környezet. Ha nem érhető el a platformon, szükség lehet a curl telepítéséhez. A példa kell működik is a Cygwin Windows, a Git Bash, a zsh és más ismertetése.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```
---

Ellenőrizze, hogy az azonos előfizetés kulcs használata az alkalmazásban vagy a REST-kérés ahogy fent használt.

#### <a name="verify-the-authorization-token"></a>A hitelesítési token ellenőrzése

Ez a lépés csak akkor szükséges, ha engedélyezési jogkivonat használ a hitelesítéshez. Futtassa a következő parancsot annak ellenőrzéséhez, hogy az engedélyezési jogkivonatot továbbra is érvényes. A parancs POST kérést küld a szolgáltatásnak, és vár egy válaszüzenetet a szolgáltatásból. Ha továbbra is HTTP `403 Forbidden` hiba, ellenőrizze a hozzáférési jogkivonat megfelelően beállítva, és nem járt le.

> [!IMPORTANT]
> A jogkivonat-10 perc elteltével rendelkezik.
> [!NOTE]
> Cserélje le `YOUR_AUDIO_FILE` az elérési útját a korábban rögzített hangfájl és `YOUR_ACCESS_TOKEN` az az engedélyezési jogkivonatot adott vissza az előző lépésben.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/Powershell)

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

# <a name="curltabcurl"></a>[A curl](#tab/curl)

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

---

## <a name="error-http-400-bad-request"></a>Hiba történt `HTTP 400 Bad Request`

Ezért általában a, hogy a kérés törzsében érvénytelen hang adatokat tartalmaz. Jelenleg csak támogatjuk a WAV-fájl.

## <a name="error-http-408-request-timeout"></a>Hiba történt `HTTP 408 Request Timeout`

A hiba oka valószínűleg hang adatok küldésére nem a szolgáltatáshoz, és a szolgáltatás időtúllépési után ezt a hibát adja vissza. REST API-hoz a kérelem törzsében a hívásaiból kell rendezni.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>A `RecognitionStatus` a válaszban `InitialSilenceTimeout`

Hang adatokat általában a a probléma okát. Például:

- a hanganyag van egy hosszú csend idő elején. A szolgáltatás leáll a felismerés elteltével néhány másodpercet, és adja vissza `InitialSilenceTimeout`.
- a hanganyag használ nem támogatott kodek formátum, ami lehetővé teszi a hívásaiból csend kell kezelni.
