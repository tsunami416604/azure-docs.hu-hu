---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 03/29/2019
ms.author: erhopf
ms.openlocfilehash: dc5e251fee00ee22edb2261c1abd8404714834ba
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "78668403"
---
## <a name="authentication"></a>Hitelesítés

Minden kérelemhez engedélyezési fejléc szükséges. Ez a táblázat azt szemlélteti, hogy mely fejlécek támogatottak az egyes szolgáltatásokban:

| Támogatott engedélyezési fejlécek | Diktálás | Szövegfelolvasás |
|------------------------|----------------|----------------|
| Ocp-Apim-Subscription-Key | Igen | Nem |
| Engedélyezés: tulajdonos | Igen | Igen |

A `Ocp-Apim-Subscription-Key` fejléc használatakor csak az előfizetési kulcsot kell megadnia. Például:

```http
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

A `Authorization: Bearer` fejléc használatakor kérést kell tennie a `issueToken` végpontnak. Ebben a kérésben egy 10 percre érvényes hozzáférési tokenre cseréli az előfizetési kulcsot. A következő néhány szakaszban megtudhatja, hogyan kérhet le jogkivonatot, és hogyan használhat jogkivonatot.

### <a name="how-to-get-an-access-token"></a>Hozzáférési jogkivonat beszerzése

Hozzáférési jogkivonat beszerzéséhez a `issueToken` `Ocp-Apim-Subscription-Key` és az előfizetés-kulcs használatával kérelmet kell elvégeznie a végponthoz.

A `issueToken` végpont formátuma a következő:

```http
https://<REGION_IDENTIFIER>.api.cognitive.microsoft.com/sts/v1.0/issueToken
```

Cserélje `<REGION_IDENTIFIER>` le az elemet az előfizetés régiójának megfelelő azonosítóra ebből a táblából:

[!INCLUDE [](cognitive-services-speech-service-region-identifier.md)]

Használja ezeket a mintákat a hozzáférési jogkivonat kérelmének létrehozásához.

#### <a name="http-sample"></a>HTTP-minta

Ez a példa egy egyszerű HTTP-kérelem a token beszerzéséhez. Cserélje `YOUR_SUBSCRIPTION_KEY` le a billentyűt a beszédfelismerési szolgáltatás előfizetési kulcsára. Ha az előfizetése nem az USA nyugati régiójában található `Host` , cserélje le a fejlécet a régiója állomásneve.

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

A válasz törzse JSON Web Token (JWT) formátumban tartalmazza a hozzáférési jogkivonatot.

#### <a name="powershell-sample"></a>PowerShell-minta

Ez a példa egy egyszerű PowerShell-szkript a hozzáférési token beszerzéséhez. Cserélje `YOUR_SUBSCRIPTION_KEY` le a billentyűt a beszédfelismerési szolgáltatás előfizetési kulcsára. Ügyeljen arra, hogy a megfelelő végpontot használja az előfizetéséhez tartozó régióhoz. Ez a példa jelenleg az USA nyugati régiója.

```powershell
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

#### <a name="curl-sample"></a>cURL minta

a cURL egy Linux rendszeren (és a Linux Windows alrendszerében) elérhető parancssori eszköz. Ez a cURL-parancs bemutatja, hogyan szerezhet be hozzáférési tokent. Cserélje `YOUR_SUBSCRIPTION_KEY` le a billentyűt a beszédfelismerési szolgáltatás előfizetési kulcsára. Ügyeljen arra, hogy a megfelelő végpontot használja az előfizetéséhez tartozó régióhoz. Ez a példa jelenleg az USA nyugati régiója.

```console
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>C#-minta

Ez a C# osztály azt szemlélteti, hogyan lehet hozzáférési tokent beolvasni. Adja át a beszédfelismerési szolgáltatás előfizetési kulcsát az osztály példányainak létrehozásakor. Ha az előfizetése nem az USA nyugati régiójában található, módosítsa `FetchTokenUri` az értékét, hogy az megfeleljen az előfizetés régiójának.

```csharp
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

### <a name="how-to-use-an-access-token"></a>Hozzáférési jogkivonat használata

A hozzáférési jogkivonatot `Authorization: Bearer <TOKEN>` fejlécként kell elküldeni a szolgáltatásnak. Minden hozzáférési jogkivonat 10 percig érvényes. A hálózati forgalom és a késés csökkentése érdekében bármikor létrehozhat egy új jogkivonatot, de azt javasoljuk, hogy a tokent kilenc percre használja.

Íme egy minta HTTP-kérelem a szöveg-beszéd REST API:

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.stt.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

// Message body here...
```
