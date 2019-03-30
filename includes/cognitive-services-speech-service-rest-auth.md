---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 03/29/2019
ms.author: erhopf
ms.openlocfilehash: 9cad860b8808dd2682995768c282d8376ab5d9be
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58671739"
---
## <a name="authentication"></a>Authentication

Minden egyes kérelem engedélyeztetési fejléc van szükség. Ez a táblázat mutatja be, hogy mely fejlécek az egyes szolgáltatások támogatottak:

| Támogatott engedélyezési fejléceket | Speech-to-text | Szövegfelolvasás |
|------------------------|----------------|----------------|
| OCP-Apim-Subscription-Key | Igen | Nem |
| Hitelesítés: Tulajdonosi | Igen | Igen |

Használatakor a `Ocp-Apim-Subscription-Key` fejléc, már csak számára meg kell adnia az előfizetési kulcs. Példa:

```
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

Használatakor a `Authorization: Bearer` fejléc, Ön szükséges kérheti a `issueToken` végpont. Ebben a kérelemben cserél az előfizetési kulcs hozzáférési jogkivonat helyeként, 10 percig érvényes. A következő néhány szakaszban fog elsajátíthatja egy token beszerzéséhez, -tokennel, és frissítse a jogkivonatot.


### <a name="how-to-get-an-access-token"></a>Hozzáférési jogkivonat beszerzése

A hozzáférési jogkivonatot kapjon kell kérheti a `issueToken` végpont használatával a `Ocp-Apim-Subscription-Key` és az előfizetési kulcs.

Ezek a régiók és végpontok támogatottak:

[!INCLUDE [](./cognitive-services-speech-service-endpoints-token-service.md)]

Ezek a minták segítségével hozzon létre a hozzáférési jogkivonat kérése.

#### <a name="http-sample"></a>HTTP-minta

Ebben a példában egy egyszerű HTTP-kérelem egy token beszerzéséhez. Cserélje le `YOUR_SUBSCRIPTION_KEY` az Speech Service előfizetési kulccsal végzett. Ha az előfizetés nem, az USA nyugati régiójában, cserélje le a `Host` saját régiójában gazdagép nevű fejléc.

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

A válasz törzse tartalmazza a hozzáférési jogkivonat JSON webes jogkivonat (JWT) formátumú.

#### <a name="powershell-sample"></a>PowerShell-minta

Ebben a példában egy egyszerű PowerShell-parancsfájlt, hogy hozzáférési jogkivonatot kapjon. Cserélje le `YOUR_SUBSCRIPTION_KEY` az Speech Service előfizetési kulccsal végzett. Ellenőrizze, hogy a helyes-e a végpontot használja a régiót, amelyben megegyezik az előfizetés. Ebben a példában az USA nyugati RÉGIÓJA van beállítva.

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

#### <a name="curl-sample"></a>a cURL-minta

a cURL egy Linux (és a Linux Windows alrendszere) elérhető parancssori eszköz. A cURL-parancs bemutatja, hogyan hozzáférési jogkivonatot kapjon. Cserélje le `YOUR_SUBSCRIPTION_KEY` az Speech Service előfizetési kulccsal végzett. Ellenőrizze, hogy a helyes-e a végpontot használja a régiót, amelyben megegyezik az előfizetés. Ebben a példában az USA nyugati RÉGIÓJA van beállítva.

```cli
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>C#-minta

Ez C# osztály mutatja be a hozzáférési jogkivonat beszerzése. Át a az Speech Service előfizetési kulcs, ha az osztály példányosítania. Ha az előfizetés nem szerepel az USA nyugati régiója, az értékének módosítása `FetchTokenUri` a régióban, az előfizetés megfelelően.

```cs
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

#### <a name="python-sample"></a>Python-minta

```python
# Request module must be installed.
# Run pip install requests if necessary.
import requests

subscription_key = 'REPLACE_WITH_YOUR_KEY'

def get_token(subscription_key):
    fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
    headers = {
        'Ocp-Apim-Subscription-Key': subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    access_token = str(response.text)
    print(access_token)
```

### <a name="how-to-use-an-access-token"></a>Hozzáférési token használatával

A hozzáférési jogkivonatot kell küldeni a service-t a `Authorization: Bearer <TOKEN>` fejléc. Minden hozzáférési jogkivonatot a 10 percig érvényes. Bármikor megtekintheti az új jogkivonatot, azonban a hálózati forgalom és a késés minimalizálása érdekében azt javasoljuk, ugyanezt a tokent kilenc percig.

Íme egy példa HTTP-kérelem a szöveg-hang transzformációs REST API:

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.stt.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

// Message body here...
```
