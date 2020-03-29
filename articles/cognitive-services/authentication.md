---
title: Hitelesítés
titleSuffix: Azure Cognitive Services
description: 'Az Azure Cognitive Services-erőforráshoz intézett kérelmek et három módon lehet hitelesíteni: előfizetési kulcsot, tulajdonosi jogkivonatot vagy többszolgáltatásos előfizetést. Ebben a cikkben megismerheti az egyes módszereket, és a kérés módját.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: erhopf
ms.openlocfilehash: 1c13c2cc4d4e562d3512de90338d874091dfeef6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74423946"
---
# <a name="authenticate-requests-to-azure-cognitive-services"></a>Az Azure Cognitive Services kéréseinek hitelesítése

Az Azure Cognitive Service minden egyes kérelemnek tartalmaznia kell egy hitelesítési fejlécet. Ez a fejléc egy előfizetési kulcs vagy hozzáférési jogkivonat mentén halad át, amely egy szolgáltatás vagy szolgáltatáscsoport előfizetésének érvényesítésére szolgál. Ebben a cikkben megtudhatja, hogy három módon hitelesítheti a kérelmeket, és a követelmények minden.

* [Hitelesítés egyszolgáltatásos előfizetési kulccsal](#authenticate-with-a-single-service-subscription-key)
* [Hitelesítés többszolgáltatásos előfizetési kulccsal](#authenticate-with-a-multi-service-subscription-key)
* [Hitelesítés jogkivonattal](#authenticate-with-an-authentication-token)
* [Hitelesítés az Azure Active Directoryval (AAD)](#authenticate-with-azure-active-directory)

## <a name="prerequisites"></a>Előfeltételek

A kérelem benyújtásához szüksége van egy Azure-fiókra és egy Azure Cognitive Services-előfizetésre. Ha már van fiókja, ugorjon a következő szakaszra. Ha nem rendelkezik fiókkal, van egy útmutatónk, amely percek alatt beállítja: [Kognitív szolgáltatások fiók létrehozása az Azure-hoz.](cognitive-services-apis-create-account.md)

Az előfizetési kulcsot a fiók létrehozása vagy az ingyenes [próbaverzió](https://azure.microsoft.com/try/cognitive-services/my-apis)aktiválása után szerezheti be az [Azure Portalról.](cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)

## <a name="authentication-headers"></a>Hitelesítési fejlécek

Tekintse át gyorsan az Azure Cognitive Services használatával használható hitelesítési fejléceket.

| Fejléc | Leírás |
|--------|-------------|
| Ocp-Apim-Subscription-Key | Ezzel a fejléccel hitelesítheti magát egy adott szolgáltatás vagy egy többszolgáltatásos előfizetési kulcs előfizetési kulcsával. |
| Ocp-Apim-Előfizetés-régió | Ez a fejléc csak akkor szükséges, ha többszolgáltatásos előfizetési kulcsot használ a [Translator Text API-val.](./Translator/reference/v3-0-reference.md) Ezzel a fejléccel adhatja meg az előfizetési régiót. |
| Engedélyezés | Ezt a fejlécet akkor használja, ha hitelesítési jogkivonatot használ. A jogkivonat-csere végrehajtásának lépéseit a következő szakaszok részletezik. A megadott érték a `Bearer <TOKEN>`következő formátumban: . |

## <a name="authenticate-with-a-single-service-subscription-key"></a>Hitelesítés egyszolgáltatásos előfizetési kulccsal

Az első lehetőség az, hogy hitelesítse a kérelmet egy előfizetési kulccsal egy adott szolgáltatáshoz, például a Translator Text. A kulcsok érhetők el az Azure Portalon minden erőforrás, amely létrehozott. Ahhoz, hogy egy előfizetési kulcsot használjon egy `Ocp-Apim-Subscription-Key` kérelem hitelesítéséhez, fejlécként kell továbbítani.

Ezek a mintakérelmek bemutatják a `Ocp-Apim-Subscription-Key` fejléc használatát. Ne feledje, hogy a minta használatakor érvényes előfizetési kulcsot kell megadnia.

Ez egy mintahívás a Bing Web Search API-hoz:
```cURL
curl -X GET 'https://api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Ez egy mintahívás a Translator Text API-hoz:
```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

Az alábbi videó bemutatja a Cognitive Services-kulcs használatával.

## <a name="authenticate-with-a-multi-service-subscription-key"></a>Hitelesítés többszolgáltatásos előfizetési kulccsal

>[!WARNING]
> Ezek a szolgáltatások **jelenleg nem támogatják** a többszolgáltatásos kulcsokat: QnA Maker, Speech Services, Custom Vision és Anomáliadetektor.

Ez a beállítás egy előfizetési kulcsot is használ a kérelmek hitelesítéséhez. A fő különbség az, hogy egy előfizetési kulcs nem kötődik egy adott szolgáltatáshoz, hanem egyetlen kulcs használható több Cognitive Services-kérelmek hitelesítésére. A [Cognitive Services díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services/) a regionális rendelkezésre állással, a támogatott funkciókkal és az árakkal kapcsolatos információkért olvassa el.

Az előfizetési kulcs minden kérelemben fejlécként `Ocp-Apim-Subscription-Key` van megadva.

[![Többszolgáltatásos előfizetési kulcs bemutatása a Cognitive Services számára](./media/index/single-key-demonstration-video.png)](https://www.youtube.com/watch?v=psHtA1p7Cas&feature=youtu.be)

### <a name="supported-regions"></a>Támogatott régiók

Ha a többszolgáltatásos előfizetési kulcsot `api.cognitive.microsoft.com`használja a kéréshez, a kívánt területet is meg kell adnia az URL-címben. Például: `westus.api.cognitive.microsoft.com`.

Ha többszolgáltatásos előfizetési kulcsot használ a Translator Text API-val, meg kell adnia az előfizetési régiót a `Ocp-Apim-Subscription-Region` fejléccel.

A többszolgáltatásos hitelesítés a következő régiókban támogatott:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |


### <a name="sample-requests"></a>Mintakérelmek

Ez egy mintahívás a Bing Web Search API-hoz:

```cURL
curl -X GET 'https://YOUR-REGION.api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Ez egy mintahívás a Translator Text API-hoz:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Ocp-Apim-Subscription-Region: YOUR_SUBSCRIPTION_REGION' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="authenticate-with-an-authentication-token"></a>Hitelesítés hitelesítési jogkivonattal

Egyes Azure Cognitive Services elfogadja, és bizonyos esetekben hitelesítési jogkivonatot igényel. Jelenleg ezek a szolgáltatások támogatják a hitelesítési jogkivonatokat:

* Szövegfordítási API
* Beszédszolgáltatások: Beszéd-szöveg RE API
* Beszédszolgáltatások: Szövegfelolvasó REST API

>[!NOTE]
> A QnA Maker az Engedélyezés fejlécet is használja, de végpontkulcsot igényel. További információ: [QnA Maker: Get answer from knowledge base](./qnamaker/quickstarts/get-answer-from-knowledge-base-using-url-tool.md).

>[!WARNING]
> A hitelesítési jogkivonatokat támogató szolgáltatások idővel változhatnak, kérjük, ellenőrizze a szolgáltatás API-hivatkozását, mielőtt ezt a hitelesítési módszert használná.

Az egyszolgáltatásos és a többszolgáltatásos előfizetési kulcsok hitelesítési jogkivonatokra is cserélhetők. A hitelesítési jogkivonatok 10 percig érvényesek.

A hitelesítési jogkivonatok fejlécként szerepelnek a `Authorization` kérelemben. A megadott tokenértéket például `Bearer`a következőnek kell megelőznie: `Bearer YOUR_AUTH_TOKEN`.

### <a name="sample-requests"></a>Mintakérelmek

Ezzel az URL-címmel előfizetési kulcsot `https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken`cserélt egy hitelesítési jogkivonathoz: .

```cURL
curl -v -X POST \
"https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
-H "Content-type: application/x-www-form-urlencoded" \
-H "Content-length: 0" \
-H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

Ezek a többszolgáltatásos régiók támogatják a tokencserét:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |

Miután megkapja a hitelesítési jogkivonatot, minden kérelemben `Authorization` fejlécként kell átadnia. Ez egy mintahívás a Translator Text API-hoz:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Authorization: Bearer YOUR_AUTH_TOKEN' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

[!INCLUDE [](../../includes/cognitive-services-azure-active-directory-authentication.md)]

## <a name="see-also"></a>Lásd még

* [Mi a Cognitive Services?](welcome.md)
* [A Cognitive Services díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services/)
* [Egyéni altartományok](cognitive-services-custom-subdomains.md)
