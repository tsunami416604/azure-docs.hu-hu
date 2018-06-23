---
title: Microsoft beszéd szolgáltatásához |} Microsoft Docs
description: A Microsoft-hang transzformációs API használandó hitelesítési kérelem
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: e36168cf3ff938af44f1028c2d26fd475d60b148
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347423"
---
# <a name="authenticate-to-the-speech-api"></a>Hitelesítés a hang transzformációs API

Beszéd szolgáltatás támogatja a hitelesítés:

- Egy előfizetés kulcs.
- Egy engedélyezési jogkivonatot.

## <a name="use-a-subscription-key"></a>Előfizetés van szükség

Beszéd szolgáltatás használatához a hang transzformációs API kognitív szolgáltatások (korábban projekt Oxford) részét képező először előfizetett kell. Ingyenes próba-előfizetés kulcsokat beszerezheti a [kognitív szolgáltatások előfizetés](https://azure.microsoft.com/try/cognitive-services/) lap. Miután kiválasztotta a hang transzformációs API-t, jelölje ki a **API-kulcs beolvasása** a kulcs beszerzése. Egy elsődleges és másodlagos kulcsot adja vissza. Mindkét kulcsot, vagy kulccsal a azonos kvóta vannak társítva.

Hosszú távú használatra vagy egy nagyobb kvótát, regisztráljon egy [Azure-fiók](https://azure.microsoft.com/free/).

A beszédfelismerés REST API használatával kell az Előfizetés kulcs a következő átadni a `Ocp-Apim-Subscription-Key` mezőt a kérelem fejlécében.

Name (Név)| Formátum| Leírás
----|-------|------------
Az OCP-Apim-előfizetés-kulcs | ASCII | YOUR_SUBSCRIPTION_KEY

A következő egy példa egy kérelem fejléce:

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
> Ha [klienskódtárak](../GetStarted/GetStartedClientLibraries.md) az alkalmazásban, győződjön meg arról, hogy az engedélyezési jogkivonat beszerezheti az előfizetés-kulcsban, a következő szakaszban leírt módon. A klienskódtárak segítségével egy engedélyezési jogkivonatot, és ezután használja a jogkivonat hitelesítési előfizetés kulcs használata.

## <a name="use-an-authorization-token"></a>Egy engedélyezési jogkivonatot használja

Másik lehetőségként használhatja egy engedélyezési jogkivonatot hitelesítéshez azonosítóként, hitelesítési/engedélyezési. Ahhoz, hogy a jogkivonat, be kell szereznie egy előfizetés kulcs a hang transzformációs API leírtak szerint a [szakasz megelőző](#use-a-subscription-key).

### <a name="get-an-authorization-token"></a>Az engedélyezési jogkivonat beolvasása

Miután egy érvényes előfizetés kulcs, küldése egy POST kérést kognitív szolgáltatások jogkivonat-szolgáltatás. A válaszban kapni az engedélyezési jogkivonat, egy JSON webes jogkivonat (JWT).

> [!NOTE]
> A token rendelkezik egy lejárati 10 perc. A token megújításához, a következő részben.

A jogkivonat-szolgáltatás URI a következő helyen található:

```
https://api.cognitive.microsoft.com/sts/v1.0/issueToken
```

A következő példakód bemutatja, hogyan szereznie egy hozzáférési jogkivonatot. Cserélje le `YOUR_SUBSCRIPTION_KEY` saját előfizetés kulccsal:

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

A példa curl Linux bash együtt. Ha nem érhető el a platformon, szükség lehet a curl telepítéséhez. A példa is működik a Cygwin Windows, a Git bash eszközt, a zsh és az egyéb ismertetése.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

# <a name="ctabcsharp"></a>[C#](#tab/CSharp)

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

A következő egy minta POST kérelem:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
Connection: Keep-Alive
```

Ha nem kap egy engedélyezési jogkivonat a jogkivonat-szolgáltatás, ellenőrizze, hogy a az Előfizetés kulcs még mindig érvényes. Ha egy ingyenes próba kulcsot használ, lépjen a [kognitív szolgáltatások előfizetés](https://azure.microsoft.com/try/cognitive-services/) lapján kattintson a "Bejelentkezés" bejelentkezni a fiókkal, amely az ingyenes próbaverzió kulcs alkalmazása használja, és ellenőrizze, hogy az Előfizetés kulcs lejárt, illetve meghaladja a kvóta.

### <a name="use-an-authorization-token-in-a-request"></a>A kérelemben egy engedélyezési jogkivonatot használja

Minden alkalommal, amikor a hang transzformációs API hívása, kell átadni a engedélyezési lexikális elem szerepel a `Authorization` fejléc. A `Authorization` fejléc tartalmaznia kell egy JWT jogkivonat.

A következő példa bemutatja, hogyan egy engedélyezési jogkivonatot használja, ha a beszédfelismerés REST API hívása.

> [!NOTE]
> Cserélje le `YOUR_AUDIO_FILE` az elérési útját a korábban rögzített hangfájl. Cserélje le `YOUR_ACCESS_TOKEN` az az engedélyezési jogkivonatot kapott az előző lépésben [egy engedélyezési jogkivonat beolvasása](#get-an-authorization-token).

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

# <a name="ctabcsharp"></a>[C#](#tab/CSharp)

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

### <a name="renew-an-authorization-token"></a>Egy engedélyezési jogkivonatot megújítása

Az engedélyezési jogkivonatot egy meghatározott időszak (jelenleg 10 perc) után lejár. Szeretné megújítani a engedélyezési jogkivonat lejárata előtt.

A következő kód egy megvalósítási példát szemléltet, az engedélyezési jogkivonat megújítása a C# nyelven:

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
