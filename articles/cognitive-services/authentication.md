---
title: Authentication
titleSuffix: Cognitive Services - Azure
description: 'Hitelesíteni a kérelmet az Azure Cognitive Services-erőforrás három módja van: egy előfizetési kulcsot, tulajdonosi jogkivonattal vagy több szolgáltatásos előfizetést. Ebben a cikkben, ismerteti az egyes módszerek, és hogyan kérheti.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: erhopf
ms.openlocfilehash: 2f9b477e076b038a6a695952ee3f770b30ad179b
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429468"
---
# <a name="authenticate-requests-to-azure-cognitive-services"></a>Az Azure Cognitive Services-kérések hitelesítéséhez

Az Azure Cognitive Service minden kérelemhez tartalmaznia kell egy hitelesítési fejlécet. Ez a fejléc továbbítja egy előfizetési kulcsot vagy a hozzáférési jogkivonatot, amely egy szolgáltatás vagy -szolgáltatásokra vonatkozó előfizetés ellenőrzésére szolgál. Ebből a cikkből megismerheti a hitelesíteni a kérelmet, és a követelmények az egyes körülbelül három módon.

* [Hitelesítés – olyan egyetlen szolgáltatást előfizetési kulccsal](#authenticate-with-a-single-service-subscription-key)
* [Egy több szolgáltatásos előfizetési kulcsot a hitelesítéshez](#authenticate-with-a-multi-service-subscription-key)
* [A jogkivonat-hitelesítés](#authenticate-with-an-authentication-token)

## <a name="prerequisites"></a>Előfeltételek

Mielőtt a kérést, kell az Azure-fiók és a egy Azure Cognitive Services-előfizetést. Ha már rendelkezik fiókkal, lépjen tovább, és ugorjon a következő szakaszra. Ha nincs fiókja, van egy útmutatót ahhoz, hogy percek alatt beállítható: [Az Azure Cognitive Services-fiók létrehozása](cognitive-services-apis-create-account.md).

## <a name="authentication-headers"></a>Hitelesítési fejléc

Tekintsük át gyorsan a hitelesítési fejléceket használható az Azure Cognitive Services használatával.

| Fejléc | Leírás |
|--------|-------------|
| OCP-Apim-Subscription-Key | Ez a fejléc használatával egy előfizetési kulcsot adott szolgáltatásokhoz, vagy egy több szolgáltatásos előfizetési kulcsot a hitelesítéshez. |
| Ocp-Apim-Subscription-Region | Ez a fejléc csak akkor szükséges, ha egy több szolgáltatásos előfizetési kulccsal rendelkező a [a Translator Text API](./Translator/reference/v3-0-reference.md). Ez a fejléc használatával adja meg az előfizetés régióban. |
| Engedélyezés | Ha egy hitelesítési tokent használ, használja ezt a fejlécet. Az alábbi szakaszok a jogkivonatcsere végrehajtásához szükséges lépéseket részletezi. A megadott érték ezt a formátumot követi: `Bearer <TOKEN>`. |

## <a name="authenticate-with-a-single-service-subscription-key"></a>Hitelesítés – olyan egyetlen szolgáltatást előfizetési kulccsal

Az első lehetőség egy előfizetési kulcsot adott szolgáltatásokhoz, például a Translator Text kérelmet hitelesíteni. A kulcsok az Azure Portalon létrehozott minden erőforrás esetében érhetők el. Egy előfizetési kulcsot használatával hitelesíteni a kérelmet, azt kell átadni mentén, mint a `Ocp-Apim-Subscription-Key` fejléc.

Ezek a kérelmek minta bemutatja, hogyan használja a `Ocp-Apim-Subscription-Key` fejléc. Ne feledje, ez a minta tartalmaznak egy érvényes előfizetési kulcsot kell használatakor.

Ez a minta a Bing Web Search API hívását:
```cURL
curl -X GET 'https://api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Ez a minta a Translator Text API hívását:
```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

A következő videó bemutatja a Cognitive Services-kulcs használatával.

## <a name="authenticate-with-a-multi-service-subscription-key"></a>Egy több szolgáltatásos előfizetési kulcsot a hitelesítéshez

>[!WARNING]
> Jelenleg ezek a szolgáltatások **nem** támogatja több szolgáltatásos kulcsokat: A QnA Maker, beszédszolgáltatások és egyéni vizuális.

Ez a beállítás egy előfizetési kulcsot is használja-kérések hitelesítéséhez. A fő különbség az, hogy egy előfizetési kulcsot nem kötődik azonban egy adott szolgáltatáshoz, inkább egy kulcs több Cognitive Services-kérések hitelesítéséhez használható. Lásd: [Cognitive Services díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services/) régiónkénti rendelkezésre állás információ a támogatott szolgáltatások, és a díjszabás.

Az előfizetési kulcsot kéréseknek, megtalálható a `Ocp-Apim-Subscription-Key` fejléc.

[![Több szolgáltatásos előfizetési kulcs bemutató a Cognitive Services számára](./media/index/single-key-demonstration-video.png)](https://www.youtube.com/watch?v=psHtA1p7Cas&feature=youtu.be)

### <a name="supported-regions"></a>Támogatott régiók

Ha a több szolgáltatásos előfizetési kulcs segítségével kérheti `api.cognitive.microsoft.com`, az URL-címben szerepelnie kell a régióban. Például: `westus.api.cognitive.microsoft.com`.

A Translator Text API több szolgáltatásos előfizetési kulcs használatakor meg kell adnia az előfizetés-régióhoz a `Ocp-Apim-Subscription-Region` fejléc.

Több szolgáltatásos hitelesítési ezekben a régiókban támogatott:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |


### <a name="sample-requests"></a>Minta kérelmek

Ez a minta a Bing Web Search API hívását:

```cURL
curl -X GET 'https://YOUR-REGION.api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Ez a minta a Translator Text API hívását:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Ocp-Apim-Subscription-Region: YOUR_SUBSCRIPTION_REGION' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="authenticate-with-an-authentication-token"></a>Egy hitelesítési tokent a hitelesítéshez

Néhány Azure Cognitive Services fogadja el, és bizonyos esetekben szükség, egy hitelesítési tokent. Ezek a szolgáltatások jelenleg hitelesítési jogkivonatokat támogatja:

* Szövegalapú fordítási API
* Beszédszolgáltatások: Hang-szöveg transzformációs REST API-val
* Beszédszolgáltatások: Szöveg-hang transzformációs REST API-val

>[!NOTE]
> A QnA Maker is az engedélyezési fejléc használja, de a végpont kulcs szükséges. További információkért lásd: [QnA Maker: Első válasz a Tudásbázis](./qnamaker/quickstarts/get-answer-from-kb-using-curl.md).

>[!WARNING]
> A szolgáltatások, amelyek támogatják a hitelesítési tokenek idővel változhat, kérjük szolgáltatás ellenőrizze az API-referenciája a hitelesítési módszer használata előtt.

Mindkét szolgáltatás egyetlen és több szolgáltatásos előfizetési kulcsok kicserélhetők hitelesítési tokenek. Hitelesítési jogkivonatok érvényesek 10 perc.

Hitelesítési jogkivonatok szerepelnek egy kérelmet, mint a `Authorization` fejléc. A token megadott értéknek meg kell előznie `Bearer`, például: `Bearer YOUR_AUTH_TOKEN`.

### <a name="sample-requests"></a>Minta kérelmek

Az URL-cím használatával egy előfizetési kulcsot egy hitelesítési tokent: `https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken`.

```cURL
curl -v -X POST \
"https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
-H "Content-type: application/x-www-form-urlencoded" \
-H "Content-length: 0" \
-H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

Ezekben a régiókban több szolgáltatásos jogkivonatcsere támogatják:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |

A hitelesítési jogkivonat beszerzése, miután kell adja át azt az egyes kérelmek, a `Authorization` fejléc. Ez a minta a Translator Text API hívását:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Authorization: Bearer YOUR_AUTH_TOKEN' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="see-also"></a>Lásd még

* [Mi a Cognitive Services?](welcome.md)
* [Cognitive Services-díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/)
* [Fiók létrehozása](cognitive-services-apis-create-account.md)
