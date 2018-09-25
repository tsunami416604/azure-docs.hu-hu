---
title: Bing Speech hitelesítésre |} A Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Hitelesítési kérelem a Bing Speech API használata
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX
ms.openlocfilehash: 4d4b9f43b6b3a4127d70989414d467f724f6ec28
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972793"
---
# <a name="authenticate-to-the-speech-api"></a>A beszédfelismerő API-t hitelesítésre

Bing Speech támogatja a hitelesítés használatával:

- Egy előfizetési kulcsot.
- Egy engedélyezési jogkivonatot.

## <a name="use-a-subscription-key"></a>Előfizetési kulcs használata

Beszédszolgáltatás használatához, elő kell fizetniük a Speech API, amely része a Cognitive Services-(korábban Project Oxford). Ingyenes próba-előfizetését helyenk beszerezheti a [Cognitive Services-előfizetés](https://azure.microsoft.com/try/cognitive-services/) lapot. Miután kiválasztotta a beszédfelismerő API, válassza ki a **API-kulcs lekérése** a kulcs beszerzése. Egy elsődleges és másodlagos kulcsát adja vissza. Mindkét kulcsot kvóta, így használhatja az egyiket sem vannak társítva.

Hosszú távú használathoz vagy megemelt kvótához regisztráljon egy [Azure-fiók](https://azure.microsoft.com/free/).

A beszédfelismerés REST API használata, teljesítenie kell a az előfizetési kulcsot, az a `Ocp-Apim-Subscription-Key` mezőt a kérelem fejlécében.

Name (Név)| Formátum| Leírás
----|-------|------------
OCP-Apim-Subscription-Key | ASCII | YOUR_SUBSCRIPTION_KEY

Az alábbiakban látható egy példa fejléc:

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
> Ha [klienskódtárak](../GetStarted/GetStartedClientLibraries.md) az alkalmazásban, győződjön meg arról, hogy az engedélyezési jogkivonatot az előfizetési kulccsal végzett megtekintheti a következő szakaszban leírtak szerint. A klienskódtárak segítségével az előfizetési kulcs engedélyezési jogkivonatot kapjon, majd a jogkivonatot a hitelesítéshez.

## <a name="use-an-authorization-token"></a>Egy engedélyezési jogkivonatot használata

Azt is megteheti használhatja egy engedélyezési jogkivonatot hitelesítéshez hitelesítés/engedélyezés bizonyítékaként. Beszerezni a jogkivonatot, be kell szereznie egy előfizetési kulcsot a beszédfelismerő API leírtak szerint a [előző fejezet](#use-a-subscription-key).

### <a name="get-an-authorization-token"></a>Egy engedélyezési jogkivonat beolvasása

Miután egy érvényes előfizetési kulcsot, küldjön egy POST kérelmet a jogkivonat a Cognitive Services-szolgáltatás. A válaszban kap az engedélyezési jogkivonatra, a JSON webes jogkivonat (JWT).

> [!NOTE]
> A jogkivonat-lejárati 10 perc rendelkezik. A token megújításának, tekintse meg a következő szakaszt.

A jogkivonat-szolgáltatás URI-t Itt megtalálható:

```
https://api.cognitive.microsoft.com/sts/v1.0/issueToken
```

Az alábbi példakód bemutatja a hozzáférési jogkivonatot kapjon. Cserélje le `YOUR_SUBSCRIPTION_KEY` a saját előfizetés-kulccsal:

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

A példában a curl Linux bash-környezet. Ha nem érhető el a platformon, szüksége lehet a curl telepítéséhez. A példában is működik a Cygwin a Windows, a Git Bash, a zsh és más ismertetése.

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

A következő egy minta POST-kérelem:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
Connection: Keep-Alive
```

Ha nem kap egy engedélyezési jogkivonatot a jogkivonat-szolgáltatás, ellenőrizze, hogy a az előfizetési kulcs továbbra is érvényes. Ha egy ingyenes próba-kulcsot használ, lépjen a [Cognitive Services-előfizetés](https://azure.microsoft.com/try/cognitive-services/) lap, kattintson a "Bejelentkezés" bejelentkezést a fiókkal, amely az ingyenes próbaverziós kulcs alkalmazása használ, és ellenőrizze, hogy az előfizetési kulcs lejárt, vagy meghaladja a kvóta.

### <a name="use-an-authorization-token-in-a-request"></a>A kérelem egy engedélyezési jogkivonatot használata

Minden alkalommal, amikor a beszédfelismerő API hívása, teljesítenie kell az engedélyezési jogkivonatot a a `Authorization` fejléc. A `Authorization` fejlécet kell tartalmaznia a JWT-jogkivonatokkal.

Az alábbi példa bemutatja, hogyan használhatja egy engedélyezési jogkivonatot a Speech REST API hívásakor.

> [!NOTE]
> Cserélje le `YOUR_AUDIO_FILE` a korábban rögzített hangfájl az elérési útját. Cserélje le `YOUR_ACCESS_TOKEN` az előző lépésben kapott engedélyezési jogkivonat [engedélyezési jogkivonatot kapjon](#get-an-authorization-token).

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

### <a name="renew-an-authorization-token"></a>Egy engedélyezési jogkivonat megújításához.

Az engedélyezési jogkivonatot egy adott időszakra (jelenleg 10 perc) után lejár. Újítsa meg az engedélyezési jogkivonat lejárata előtt kell.

Az alábbi kód egy megvalósítási példát szemléltet, a C#, a hitelesítési token megújítása:

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
