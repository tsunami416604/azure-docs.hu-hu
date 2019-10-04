---
title: Hitelesítés Bing Speech | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Hitelesítés kérése a Bing Speech API használatához
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d1e708ff29293b87935d0d191ba44ad4a11917a0
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965659"
---
# <a name="authenticate-to-the-speech-api"></a>Hitelesítés a Speech API-val

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Bing Speech támogatja a hitelesítést a használatával:

- Egy előfizetői kulcs.
- Egy engedélyezési jogkivonat.

## <a name="use-a-subscription-key"></a>Előfizetés-kulcs használata

A Speech Service használatához először elő kell fizetnünk a Cognitive Services részét képező Speech API-ra (korábban Project Oxford). A [Cognitive Services előfizetés](https://azure.microsoft.com/try/cognitive-services/) oldaláról ingyenes próbaverziós előfizetési kulcsokat szerezhet be. Miután kiválasztotta a Speech API-t, válassza az **API-kulcs beolvasása** elemet a kulcs lekéréséhez. Egy elsődleges és egy másodlagos kulcsot ad vissza. Mindkét kulcs ugyanahhoz a kvótához van kötve, így bármelyik kulcsot használhatja.

A hosszú távú használat vagy a megnövekedett kvóta esetén regisztráljon egy [Azure-fiókra](https://azure.microsoft.com/free/).

A Speech REST API használatához át kell adni az előfizetési kulcsot `Ocp-Apim-Subscription-Key` a kérelem fejlécében lévő mezőben.

Name (Név)| Formátum| Leírás
----|-------|------------
OCP-Apim-Subscription-Key | ASCII | YOUR_SUBSCRIPTION_KEY

A következő példa egy kérelem fejlécére mutat:

```HTTP
POST https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: speech.platform.bing.com
Transfer-Encoding: chunked
Expect: 100-continue
```

> [!IMPORTANT]
> Ha az alkalmazásban [ügyféloldali kódtárakat](../GetStarted/GetStartedClientLibraries.md) használ, ellenőrizze, hogy a következő szakaszban leírtak szerint beszerezheti-e az engedélyezési jogkivonatot az előfizetési kulccsal. Az ügyféloldali kódtárak az előfizetési kulccsal kapják meg az engedélyezési jogkivonatot, majd a tokent használják a hitelesítéshez.

## <a name="use-an-authorization-token"></a>Engedélyezési jogkivonat használata

Azt is megteheti, hogy hitelesítési jogkivonatot használ hitelesítés/engedélyezés igazolására. A jogkivonat beszerzéséhez először be kell szereznie egy előfizetési kulcsot a Speech API-ból az [előző szakaszban](#use-a-subscription-key)leírtak szerint.

### <a name="get-an-authorization-token"></a>Engedélyezési jogkivonat beszerzése

Ha érvényes előfizetési kulccsal rendelkezik, küldjön egy POST-kérést a Cognitive Services jogkivonat-szolgáltatásának. A válaszban megkapja az engedélyezési jogkivonatot JSON Web Tokenként (JWT).

> [!NOTE]
> A jogkivonat 10 percet vesz igénybe. A jogkivonat megújításához tekintse meg a következő szakaszt.

A jogkivonat-szolgáltatás URI-ja itt található:

```
https://api.cognitive.microsoft.com/sts/v1.0/issueToken
```

A következő mintakód bemutatja, hogyan kérhet le hozzáférési tokent. Cserélje `YOUR_SUBSCRIPTION_KEY` le a t a saját előfizetési kulcsára:

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

A példa a curlot használja a Linuxon a bash használatával. Ha nem érhető el a platformon, lehet, hogy telepítenie kell a curlot. A példa a Cygwin on Windows, a git bash, a zsh és más rendszerhéjok esetében is működik.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
    /*
     * This class demonstrates how to get a valid access token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri = "https://api.cognitive.microsoft.com/sts/v1.0";
        private string subscriptionKey;
        private string token;

        public Authentication(string subscriptionKey)
        {
            this.subscriptionKey = subscriptionKey;
            this.token = FetchTokenAsync(FetchTokenUri, subscriptionKey).Result;
        }

        public string GetAccessToken()
        {
            return this.token;
        }

        private async Task<string> FetchTokenAsync(string fetchUri, string subscriptionKey)
        {
            using (var client = new HttpClient())
            {
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                UriBuilder uriBuilder = new UriBuilder(fetchUri);
                uriBuilder.Path += "/issueToken";

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```

---

A következő egy példa utáni kérelem:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
Connection: Keep-Alive
```

Ha nem kaphat engedélyezési jogkivonatot a jogkivonat-szolgáltatásból, ellenőrizze, hogy az előfizetési kulcs továbbra is érvényes-e. Ha ingyenes próbaverziós kulcsot használ, lépjen a [Cognitive Services előfizetés](https://azure.microsoft.com/try/cognitive-services/) oldalra, és kattintson a Bejelentkezés gombra a bejelentkezéshez az ingyenes próbaverziós kulcs alkalmazásához használt fiók használatával, és ellenőrizze, hogy az előfizetés-kulcs lejárt-e, vagy meghaladja-e a kvótát.

### <a name="use-an-authorization-token-in-a-request"></a>Engedélyezési jogkivonat használata kérelemben

Minden alkalommal, amikor meghívja a Speech API-t, át kell adnia az engedélyezési `Authorization` jogkivonatot a fejlécben. A `Authorization` fejlécnek tartalmaznia kell egy JWT hozzáférési tokent.

Az alábbi példa bemutatja, hogyan használható az engedélyezési token a Speech REST API hívásakor.

> [!NOTE]
> Cserélje le `YOUR_AUDIO_FILE` a korábban rögzített hangfájl az elérési útját. Cserélje `YOUR_ACCESS_TOKEN` le az elemet az előző lépésben kapott engedélyezési jogkivonatra, és [kérjen meg egy engedélyezési jogkivonatot](#get-an-authorization-token).

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

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = @"speech.platform.bing.com";
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Authorization"] = "Bearer " + token;

// Send an audio file by 1024 byte chunks
using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{

    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```

---

### <a name="renew-an-authorization-token"></a>Engedélyezési jogkivonat megújítása

Az engedélyezési jogkivonat egy bizonyos időtartam (jelenleg 10 perc) után lejár. A lejárta előtt meg kell újítania az engedélyezési jogkivonatot.

Az alábbi kód az engedélyezési jogkivonat megújításának megvalósítását C# szemlélteti:

```cs
    /*
     * This class demonstrates how to get a valid O-auth token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri = "https://api.cognitive.microsoft.com/sts/v1.0";
        private string subscriptionKey;
        private string token;
        private Timer accessTokenRenewer;

        //Access token expires every 10 minutes. Renew it every 9 minutes.
        private const int RefreshTokenDuration = 9;

        public Authentication(string subscriptionKey)
        {
            this.subscriptionKey = subscriptionKey;
            this.token = FetchToken(FetchTokenUri, subscriptionKey).Result;

            // renew the token on set duration.
            accessTokenRenewer = new Timer(new TimerCallback(OnTokenExpiredCallback),
                                           this,
                                           TimeSpan.FromMinutes(RefreshTokenDuration),
                                           TimeSpan.FromMilliseconds(-1));
        }

        public string GetAccessToken()
        {
            return this.token;
        }

        private void RenewAccessToken()
        {
            this.token = FetchToken(FetchTokenUri, this.subscriptionKey).Result;
            Console.WriteLine("Renewed token.");
        }

        private void OnTokenExpiredCallback(object stateInfo)
        {
            try
            {
                RenewAccessToken();
            }
            catch (Exception ex)
            {
                Console.WriteLine(string.Format("Failed renewing access token. Details: {0}", ex.Message));
            }
            finally
            {
                try
                {
                    accessTokenRenewer.Change(TimeSpan.FromMinutes(RefreshTokenDuration), TimeSpan.FromMilliseconds(-1));
                }
                catch (Exception ex)
                {
                    Console.WriteLine(string.Format("Failed to reschedule the timer to renew access token. Details: {0}", ex.Message));
                }
            }
        }

        private async Task<string> FetchToken(string fetchUri, string subscriptionKey)
        {
            using (var client = new HttpClient())
            {
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                UriBuilder uriBuilder = new UriBuilder(fetchUri);
                uriBuilder.Path += "/issueToken";

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```
