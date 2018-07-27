---
title: Beszédszolgáltatás REST API-k
description: A beszédfelismerési szolgáltatás REST API-hivatkozás.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/09/2018
ms.author: v-jerkin
ms.openlocfilehash: 9991e0a1fc54e6aa4a99b8bfbd93abdfe974b01b
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39283069"
---
# <a name="speech-service-rest-apis"></a>Beszédszolgáltatás REST API-k

A REST API-k, az egyesített beszédszolgáltatás hasonlóak az API-k által biztosított a [Speech API](https://docs.microsoft.com/azure/cognitive-services/Speech) (korábbi nevén a Bing Speech Service). A végpontok különböznek az előző beszédfelismerési szolgáltatás által használt a végpontok.

## <a name="speech-to-text"></a>Diktálás

A Speech to Text API, a használt végpontokat különböznek az előző beszédszolgáltatás Speech Recognition API. Az új végpontok az alábbi táblázatban láthatók. Használja az egyik, amely megfelel az előfizetés régiót.

[!include[](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

A Speech to Text API hasonlít ellenkező esetben a [REST API-val](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest) az előző Speech API-hoz.

A Speech to Text REST API csak rövid beszédet támogat. Kérelmek legfeljebb 10 másodpercet, hang és tartalmazhat az elmúlt 14 másodperc teljes legfeljebb. A REST API-t csak a végső eredmények, nem átmeneti vagy részleges eredményt adja vissza.

> [!NOTE]
> Ha testre szabta az akusztikai modell vagy a nyelvi modell, vagy a írásmódja, használja az egyéni végpontra.

## <a name="text-to-speech"></a>Szövegfelolvasás

Az új Text to Speech API 24-KHz hangkimeneti támogatja. A `X-Microsoft-OutputFormat` fejléc most már előfordulhat, hogy a következő értékeket tartalmaznak.

|||
|-|-|
`raw-16khz-16bit-mono-pcm`         | `audio-16khz-16kbps-mono-siren`
`riff-16khz-16kbps-mono-siren`     | `riff-16khz-16bit-mono-pcm`
`audio-16khz-128kbitrate-mono-mp3` | `audio-16khz-64kbitrate-mono-mp3`
`audio-16khz-32kbitrate-mono-mp3`  | `raw-24khz-16bit-mono-pcm`
`riff-24khz-16bit-mono-pcm`        | `audio-24khz-160kbitrate-mono-mp3`
`audio-24khz-96kbitrate-mono-mp3`  | `audio-24khz-48kbitrate-mono-mp3`

A beszédfelismerési szolgáltatás most már két 24-KHz beszédhangot biztosítja:

Területi beállítás | Nyelv   | Nem | A felhasználónév-leképezés
-------|------------|--------|------------
hu-HU  | Amerikai angol | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-US, Jessa24kRUS)" 
hu-HU  | Amerikai angol | Férfi   | "A Microsoft Server beszéd szöveg Speech Voice (en-US, Guy24kRUS)"

Az alábbiakban az egyesített Speech service Text to Speech API a REST-végpontokat. A végpont, amely megfelel az előfizetés régiót használni.

[!include[](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

Tartsa szem előtt a különbségeket, mivel, tekintse meg a [REST API-dokumentáció](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/bingvoiceoutput) az előző Speech API-hoz.

## <a name="authentication"></a>Hitelesítés

Hozzáférési jogkivonat egy kérést küld a beszédfelismerési szolgáltatás REST API-t igényel. Azáltal, hogy az előfizetési kulcs egy regionális Speech Service jogkivonat beszerzése, `issueToken` végponton, az alábbi táblázatban látható. A végpont, amely megfelel az előfizetés régiót használni.

[!include[](../../../includes/cognitive-services-speech-service-endpoints-token-service.md)]

Minden hozzáférési jogkivonatot a 10 percig érvényes. Egy új jogkivonatot bármikor szerezheti be – Ha kívánja, többek között a következők csak előtt minden Speech REST API-kérelem. A hálózati forgalom és a késés minimalizálása érdekében azonban javasoljuk hogy ugyanezt a tokent kilenc perc.

A következő szakaszok bemutatják egy token beszerzése és hogyan használható a kérelemben.

### <a name="getting-a-token-http"></a>Jogkivonatok: HTTP

Alább egy mintát egy token beszerzése a HTTP-kérelem van. Cserélje le `YOUR_SUBSCRIPTION_KEY` az Speech service előfizetési kulccsal végzett. Ha az előfizetés nem szerepel az USA nyugati régiója, cserélje le a `Host` saját régiójában gazdanevű fejléc.

```
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

A kérés adott válasz törzse a hozzáférési jogkivonat Java webes jogkivonat (JWT) formátumú.

### <a name="getting-a-token-powershell"></a>Jogkivonatok: PowerShell

Az alábbi Windows PowerShell-parancsprogram mutatja be a hozzáférési jogkivonat beszerzése. Cserélje le `YOUR_SUBSCRIPTION_KEY` az Speech service előfizetési kulccsal végzett. Az előfizetés nem szerepel az USA nyugati régiója, az adott URI állomásnevét megfelelően módosítja.

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken
 -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

### <a name="getting-a-token-curl"></a>Jogkivonatok: cURL

a cURL egy Linux (és a Linux Windows alrendszere) elérhető parancssori eszköz. Az alábbi cURL-parancs bemutatja, hogyan hozzáférési jogkivonat beszerzése. Cserélje le `YOUR_SUBSCRIPTION_KEY` az Speech service előfizetési kulccsal végzett. Az előfizetés nem szerepel az USA nyugati régiója, az adott URI állomásnevét megfelelően módosítja.

> [!NOTE]
> A parancs az olvashatóság érdekében több sorban jelenik meg, de meg kell egy shell parancssornál ugyanabban a sorban.

```
curl -v -X POST 
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" 
 -H "Content-type: application/x-www-form-urlencoded" 
 -H "Content-Length: 0" 
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

### <a name="getting-a-token-c"></a>Jogkivonatok: C#

A C# osztály az alábbi mutatja be a hozzáférési jogkivonat beszerzése. Adja át a Speech service előfizetési kulcs, az osztály hárítható el. Ha az előfizetés nem szerepel az USA nyugati régiója, módosítsa a állomásnevét `FetchTokenUri` megfelelően.

```cs
    /*
     * This class demonstrates how to get a valid access token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri =
            "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
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

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```

### <a name="using-a-token"></a>Egy jogkivonat használatával

Szeretne használni egy tokent a REST API-kérelem, adja meg azt a a `Authorization` fejléc a következő szót `Bearer`. Például Íme egy példa szöveget a Speech REST-kérelmet, amely tartalmazza a jogkivonatot. Helyettesítse be a tényleges token `YOUR_ACCESS_TOKEN` és a helyes állomásnevet használja a `Host` fejléc.

```xml
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.tts.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    Hello, world!
</voice></speak>
```

### <a name="renewing-authorization"></a>Engedély megújítása

Az engedélyezési jogkivonatot 10 perc múlva lejár. Az engedély megújítása szerint egy új token beszerzése után járjon le – például a következő kilenc perc múlva. 

Az alábbi C#-kódot az osztály korábban bemutatott protokollkompatibilitását. A `Authentication` osztály automatikusan kap egy új hozzáférési jogkivonat egy időzítő segítségével kilenc percenként. Ez a megközelítés biztosítja, hogy érvényes token mindig elérhető legyen a program futása közben.

> [!NOTE]
> Időzítő helyett tárolhatja egy időbélyeg, ha a jelenlegi token lett lekérve, majd egy új kérelem csak akkor, ha a jelenlegi token hamarosan lejár. Ezt a módszert elkerülhető, hogy új jogkivonatok feleslegesen kér, és a programok, amelyek ritkán beszédalapú kéréseket a megfelelőbb lehet.

Ahogy korábban is, győződjön meg arról, hogy a `FetchTokenUri` értéke megegyezik az előfizetés régiót. Az osztály hárítható el, adja át az előfizetési kulcs.

```cs
    /*
     * This class demonstrates how to maintain a valid access token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri = 
            "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
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

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```

## <a name="next-steps"></a>További lépések

- [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [Akusztikai modell testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modell testreszabása](how-to-customize-language-model.md)

