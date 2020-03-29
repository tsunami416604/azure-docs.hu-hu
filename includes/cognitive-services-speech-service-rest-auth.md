---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 03/29/2019
ms.author: erhopf
ms.openlocfilehash: dc5e251fee00ee22edb2261c1abd8404714834ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78668403"
---
## <a name="authentication"></a>Hitelesítés

Minden kérelemhez engedélyezési fejléc szükséges. Ez a táblázat azt mutatja be, hogy mely fejlécek támogatottak az egyes szolgáltatásokban:

| Támogatott engedélyezési fejlécek | Diktálás | Szövegfelolvasás |
|------------------------|----------------|----------------|
| Ocp-Apim-Subscription-Key | Igen | Nem |
| Engedély: Tulajdonos | Igen | Igen |

A `Ocp-Apim-Subscription-Key` fejléc használatakor csak az előfizetési kulcsot kell megadnia. Példa:

```http
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

A `Authorization: Bearer` fejléc használatakor a `issueToken` végpontra kell kérnie. Ebben a kérelemben az előfizetési kulcsot egy 10 percig érvényes hozzáférési jogkivonatért cseréli ki. A következő néhány szakaszban megtudhatja, hogyan szerezhet be egy jogkivonatot, és hogyan használhat jogkivonatot.

### <a name="how-to-get-an-access-token"></a>Hozzáférési jogkivonat beszerezése

Hozzáférési jogkivonat bekérése érdekében a végpontra `issueToken` kell kérnie `Ocp-Apim-Subscription-Key` a és az előfizetési kulcsot.

A `issueToken` végpont formátuma a következő:

```http
https://<REGION_IDENTIFIER>.api.cognitive.microsoft.com/sts/v1.0/issueToken
```

Cserélje `<REGION_IDENTIFIER>` le az előfizetés régiójának megfelelő azonosítóra ebből a táblából:

[!INCLUDE [](cognitive-services-speech-service-region-identifier.md)]

Ezekkel a mintákkal hozza létre a hozzáférési jogkivonat-kérelmet.

#### <a name="http-sample"></a>HTTP-minta

Ebben a példában egy egyszerű HTTP-kérelem egy jogkivonat lekéréséhez. Cserélje `YOUR_SUBSCRIPTION_KEY` le a Beszédszolgáltatás-előfizetési kulcsra. Ha az előfizetés nem az USA nyugati `Host` régiójában található, cserélje le a fejlécet a régió gazdagépnevére.

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

A válasz törzse json webtoken (JWT) formátumban tartalmazza a hozzáférési jogkivonatot.

#### <a name="powershell-sample"></a>PowerShell-minta

Ebben a példában egy egyszerű PowerShell-parancsfájl egy hozzáférési jogkivonat beírásához. Cserélje `YOUR_SUBSCRIPTION_KEY` le a Beszédszolgáltatás-előfizetési kulcsra. Győződjön meg arról, hogy a megfelelő végpontot használja az előfizetésének megfelelő régióhoz. Ez a példa jelenleg az USA nyugati felére van állítva.

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

A cURL egy parancssori eszköz, amely Linux alatt (és a Windows Alrendszerben Linuxhoz) érhető el. Ez a cURL parancs bemutatja, hogyan szerezhet be egy hozzáférési jogkivonatot. Cserélje `YOUR_SUBSCRIPTION_KEY` le a Beszédszolgáltatás-előfizetési kulcsra. Győződjön meg arról, hogy a megfelelő végpontot használja az előfizetésének megfelelő régióhoz. Ez a példa jelenleg az USA nyugati felére van állítva.

```console
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>C#-minta

Ez a C# osztály bemutatja, hogyan szerezhet be egy hozzáférési jogkivonatot. Adja át a Beszédszolgáltatás-előfizetési kulcsot az osztály példányosításakor. Ha az előfizetés nem az USA nyugati régiójában `FetchTokenUri` található, módosítsa az értékét, hogy megfeleljen az előfizetés régiójának.

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

A hozzáférési jogkivonatot fejlécként `Authorization: Bearer <TOKEN>` kell elküldeni a szolgáltatásnak. Minden hozzáférési jogkivonat 10 percig érvényes. Bármikor beszerezhet egy új jogkivonatot, azonban a hálózati forgalom és a késés minimalizálása érdekében azt javasoljuk, hogy ugyanazt a jogkivonatot kilenc percig használja.

Íme egy http-kérelem a szövegfelolvasó REST API-hoz:

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.stt.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

// Message body here...
```
