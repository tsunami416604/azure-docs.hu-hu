---
title: Hibaelhárítás |} Microsoft Docs
description: Hogyan Microsoft beszéd szolgáltatással kapcsolatos problémák megoldásához.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 04f3da19939d523d201d357b2b0293db1508431d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347407"
---
# <a name="troubleshooting"></a>Hibaelhárítás

## <a name="error-http-403-forbidden"></a>Hiba történt `HTTP 403 Forbidden`

Beszédfelismerés API használata esetén adja vissza egy `HTTP 403 Forbidden` hiba.

### <a name="cause"></a>Ok

Ez a hiba gyakran okozza a hitelesítéssel kapcsolatos problémáiról. Kapcsolat nélküli kérelmek érvényes `Ocp-Apim-Subscription-Key` vagy `Authorization` fejléc visszautasítja a szolgáltatást, amely egy `HTTP 403 Forbidden` választ.

Előfizetés kulcs használ a hitelesítéshez, ha az OK lehet-e.

- az Előfizetés kulcs nem található vagy érvénytelen
- az Előfizetés kulcs a memóriahasználati kvóta túllépése
- a `Ocp-Apim-Subscription-Key` mező értéke nem a kérelem fejlécében REST API-t kell meghívni

Ha engedélyezési jogkivonat használ a hitelesítéshez, a következő oka is lehet a hiba.

- a `Authorization` fejléc hiányzik a kérelemben szereplő többi használatakor
- az engedélyezési fejlécben megadott engedélyezési jogkivonat érvénytelen.
- az engedélyezési jogkivonat érvényessége lejárt. A hozzáférési token rendelkezik egy 10 perc elteltével

Hitelesítéssel kapcsolatos további információkért lásd: a [hitelesítési](How-to/how-to-authentication.md) lap.

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

#### <a name="verify-that-your-subscription-key-is-valid"></a>Győződjön meg arról, hogy az Előfizetés kulcs érvényes

Az ellenőrzéshez a következő parancsot futtathatja. Cserélje le a Megjegyzés *YOUR_SUBSCRIPTION_KEY* saját előfizetés kulccsal. Ha az Előfizetés kulcs érvényes, megjelenik a válasz az engedélyezési jogkivonat, egy JSON webes jogkivonat (JWT). Ellenkező esetben hibaüzenet válaszként.

> [!NOTE]
> Cserélje le `YOUR_SUBSCRIPTION_KEY` saját előfizetés kulccsal.

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

# <a name="curltabcurl"></a>[curl](#tab/curl)

A példa curl Linux bash együtt. Ha nem érhető el a platformon, szükség lehet a curl telepítéséhez. A példa kell működnie is Cygwin a Windows, a Git bash eszközt, a zsh és az egyéb ismertetése.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```
---

Győződjön meg arról, hogy az előfizetés ugyanazzal a kulccsal használja a az alkalmazás vagy a REST-kérelem a, a fenti használt.

#### <a name="verify-the-authorization-token"></a>Az engedélyezési jogkivonatának ellenőrzése

Ez a lépés csak akkor szükséges, ha engedélyezési jogkivonat hitelesítéshez használandó. A következő parancsot annak ellenőrzéséhez, hogy az engedélyezési jogkivonat továbbra is érvényes. A parancs egy POST kérést küld a szolgáltatás, és a szolgáltatás válaszüzenetet vár. Ha továbbra is HTTP `403 Forbidden` hiba, ellenőrizze a hozzáférési jogkivonat helyesen beállítva, és nem járt le.

> [!IMPORTANT]
> A token egy 10 perc elteltével rendelkezik.
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

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

---

## <a name="error-http-400-bad-request"></a>Hiba történt `HTTP 400 Bad Request`

Ezért általában az, hogy a kérés törzsében érvénytelen hang adatokat tartalmazza. Jelenleg csak támogatjuk a WAV-fájl.

## <a name="error-http-408-request-timeout"></a>Hiba történt `HTTP 408 Request Timeout`

A hiba oka az a legvalószínűbb ok az, hogy a hang adatküldést a szolgáltatáshoz, és a szolgáltatás időtúllépési után ezt a hibát adja vissza. A REST API-t a hangadatok kell rendezni, a kérés törzsében.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>A `RecognitionStatus` a válasz: `InitialSilenceTimeout`

Hangadatok oka általában az okozza a problémát. Például:

- a hang egy hosszú csend idő tartozik elején. A szolgáltatás leáll, a felismerési után néhány másodperc, és visszaadja a `InitialSilenceTimeout`.
- a hang nem támogatott kodek formátum, ami lehetővé teszi a csend kezelni hangadatok használja.
