---
title: Beszéd szolgáltatás REST API-k |} Microsoft Docs
description: A beszédfelismerés szolgáltatás REST API-k hivatkozását.
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/09/2018
ms.author: v-jerkin
ms.openlocfilehash: 53560fd4f8240c4446898f58992a9319e5177435
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37085371"
---
# <a name="speech-service-rest-apis"></a>Beszéd szolgáltatás REST API-k

Az egyesített beszéd szolgáltatás REST API-k által nyújtott API-kat hasonlóak a [Diktálásfelismerési API](https://docs.microsoft.com/azure/cognitive-services/Speech) (korábbi nevén a Bing Diktálásfelismerési szolgáltatás). A végpontok különböznek az előző beszéd szolgáltatás által használt végpontok.

## <a name="speech-to-text"></a>Diktálás

A szöveg API beszéd átalakítás csak a használt végpontok térnek el egymástól, az előző beszéd szolgáltatásból Beszéd felismerés API. Az új végpontok az alábbi táblázatban láthatók. Használja az egyik, amely megfelel az előfizetése régiójában.

[!include[](includes/endpoints-speech-to-text.md)]

A beszédfelismerés szöveg API hasonlít ellenkező esetben a [REST API](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest) az előző Diktálásfelismerési API-hoz.

A beszédfelismerés szöveg REST API támogatja a csak rövid utterances. Kérelmek legfeljebb 10 másodpercnyi és tartalmazhat utolsó legfeljebb általános 14 másodpercig. A REST API-t csak végső eredményeket, nem részleges vagy köztes eredményeket ad vissza.

> [!NOTE]
> Ha testreszabta a akusztikus modell vagy a nyelvi modell, vagy a kiejtés, használja az egyéni végpontot.

## <a name="text-to-speech"></a>Szövegfelolvasás

Az új szöveg-hang transzformációs API 24-KHz hangkimeneti támogatja. A `X-Microsoft-OutputFormat` most fejlécében a következő értékeket.

|||
|-|-|
`raw-16khz-16bit-mono-pcm`         | `audio-16khz-16kbps-mono-siren`
`riff-16khz-16kbps-mono-siren`     | `riff-16khz-16bit-mono-pcm`
`audio-16khz-128kbitrate-mono-mp3` | `audio-16khz-64kbitrate-mono-mp3`
`audio-16khz-32kbitrate-mono-mp3`  | `raw-24khz-16bit-mono-pcm`
`riff-24khz-16bit-mono-pcm`        | `audio-24khz-160kbitrate-mono-mp3`
`audio-24khz-96kbitrate-mono-mp3`  | `audio-24khz-48kbitrate-mono-mp3`

A beszédfelismerés szolgáltatás most két 24-KHz hangok biztosítja:

Területi beállítás | Nyelv   | Nem | Szolgáltatás felhasználónevek hozzárendelése
-------|------------|--------|------------
hu-HU  | Amerikai angol | Nő | "Microsoft Server beszéd szöveg beszéd hang (en-US, Jessa24kRUS)" 
hu-HU  | Amerikai angol | Férfi   | "Microsoft Server beszéd szöveg beszéd hang (en-US, Guy24kRUS)"

Az alábbiakban a REST-végpontok az egyesített beszéd szolgáltatás szöveg-hang transzformációs API számára. A végpontot, amely megfelel az előfizetése régiójában használni.

[!include[](includes/endpoints-text-to-speech.md)]

Ezek a különbségek szem előtt tartani, akkor tekintse meg a [REST API-dokumentáció](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/bingvoiceoutput) az előző Diktálásfelismerési API-hoz.

## <a name="authentication"></a>Hitelesítés

Olyan hozzáférési jogkivonatot egy kérést küld a beszédfelismerés szolgáltatás REST API szükséges. Ön jogkivonat beszerzése az Előfizetés kulcs regionális beszéd szolgáltatás megadásával `issueToken` végpont, az alábbi táblázatban látható. A végpontot, amely megfelel az előfizetése régiójában használni.

[!include[](includes/endpoints-token-service.md)]

A hozzáférési tokenek 10 percig érvényes. Előfordulhat, hogy megszerezzen egy olyan új jogkivonatot, bármikor – Ha kívánja, beleértve, közvetlenül az előtt minden beszéd REST API-kérelem. Hálózati forgalom és a Késleltetés minimalizálása érdekében azonban javasoljuk ugyanezt a tokent kilenc percig.

A következő szakaszok bemutatják a szolgáltatáshitelesítést egy token és történő használatát egy kérelmet.

### <a name="getting-a-token-http"></a>A token első: HTTP

Alatt van egy minta HTTP-kérelem jogkivonat megszerzéséhez. Cserélje le `YOUR_SUBSCRIPTION_KEY` a beszédfelismerés szolgáltatás előfizetés kulccsal. Ha az előfizetés nincs USA nyugati régiója régióban, cserélje le a `Host` a régió állomásnév fejléc.

```
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

A a irányuló kérelemre adott válasz törzse a hozzáférési jogkivonat Java webes jogkivonat (JWT) formátumú.

### <a name="getting-a-token-powershell"></a>A token első: PowerShell

Az alábbi Windows PowerShell-parancsfájl bemutatja, hogyan egy hozzáférési jogkivonat beszerzése. Cserélje le `YOUR_SUBSCRIPTION_KEY` a beszédfelismerés szolgáltatás előfizetés kulccsal. Az előfizetés nincs USA nyugati régiója régióban, a megadott URI állomásnevét megfelelően módosítja.

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

### <a name="getting-a-token-curl"></a>A token első: cURL

cURL Linux (és a Linux Windows alrendszere) parancssori eszköz. Az alábbi cURL-parancsot a hozzáférési token beszerzése mutatja be. Cserélje le `YOUR_SUBSCRIPTION_KEY` a beszédfelismerés szolgáltatás előfizetés kulccsal. Az előfizetés nincs USA nyugati régiója régióban, a megadott URI állomásnevét megfelelően módosítja.

> [!NOTE]
> A parancs az olvashatóság érdekében több sorban jelenik meg, de rendszerhéj a parancssorból egy sorba kell megadott.

```
curl -v -X POST 
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" 
 -H "Content-type: application/x-www-form-urlencoded" 
 -H "Content-Length: 0" 
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

### <a name="getting-a-token-c"></a>A token első: C#

A C# osztály alábbi mutatja be a hozzáférési token beszerzése. Át a beszédfelismerés szolgáltatás előfizetés kulcs, a osztály példányának létrehozásakor. Ha az előfizetés nincs USA nyugati régiója régióban, módosítsa a `FetchTokenUri` megfelelően.

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

### <a name="using-a-token"></a>Egy tokent

REST API-kérelem jogkivonat használatához adja meg azt a a `Authorization` fejléc a következő a word `Bearer`. Itt például van egy minta szöveges jogkivonatot tartalmazó beszéd REST-kérelemre. Helyettesítse be a tényleges lexikális eleme `YOUR_ACCESS_TOKEN` , és használja a megfelelő állomásnév a `Host` fejléc.

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

### <a name="renewing-authorization"></a>Engedélyezési megújítása

Az engedélyezési jogkivonat 10 perc múlva lejár. Az engedélyezési megújítani megszerezni egy új jogkivonatot, ameddig – például kilenc perc múlva. 

A következő C#-kódban Esőcsepp helyettesíti a korábban bemutatott osztály. A `Authentication` osztály automatikusan kap egy új hozzáférési jogkivonat időzítő segítségével kilenc percenként. Ez a megközelítés biztosítja, hogy egy érvényes tokent mindig rendelkezésre áll a program futása közben.

> [!NOTE]
> Egy időzítő helyett tárolhatja a timestamp, ha az aktuális jogkivonatot kapott, akkor kérjen egy új, csak ha az aktuális jogkivonat hamarosan lejár. Ez a megközelítés új jogkivonatok feleslegesen kérő elkerülhető, és több alkalmasak lehetnek a programok, amelyek alkalomszerű beszéd kérelmeket.

Mint korábban, győződjön meg arról, hogy a `FetchTokenUri` értéke megegyezik az előfizetése régiójában. Az Előfizetés kulcs továbbítja, a osztály példányának létrehozásakor.

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

- [Beszéd próbaverziós előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [Akusztikus modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)

