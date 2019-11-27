---
title: Authentication
titleSuffix: Azure Cognitive Services
description: 'Az Azure Cognitive Services-erőforrásokra vonatkozó kérések hitelesítésének három módja van: egy előfizetési kulcs, egy tulajdonosi jogkivonat vagy egy több szolgáltatás előfizetése. Ebben a cikkben az egyes módszerekről és a kérések elvégzéséről olvashat.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: erhopf
ms.openlocfilehash: 1c13c2cc4d4e562d3512de90338d874091dfeef6
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423946"
---
# <a name="authenticate-requests-to-azure-cognitive-services"></a>Kérelmek hitelesítése az Azure Cognitive Services

Az Azure kognitív szolgáltatás minden kérésének tartalmaznia kell egy hitelesítési fejlécet. Ez a fejléc egy előfizetési kulcs vagy egy hozzáférési jogkivonat mentén halad át, amely a szolgáltatás vagy a szolgáltatás előfizetésének ellenőrzésére szolgál. Ebből a cikkből megtudhatja, hogyan hitelesítheti a kéréseket és az egyes követelmények követelményeit.

* [Hitelesítés egyetlen szolgáltatású előfizetési kulccsal](#authenticate-with-a-single-service-subscription-key)
* [Hitelesítés több szolgáltatásból álló előfizetési kulccsal](#authenticate-with-a-multi-service-subscription-key)
* [Hitelesítés jogkivonat használatával](#authenticate-with-an-authentication-token)
* [Hitelesítés Azure Active Directorysal (HRE)](#authenticate-with-azure-active-directory)

## <a name="prerequisites"></a>Előfeltételek

A kérelem elkészítése előtt Azure-fiókra és Azure Cognitive Services-előfizetésre van szükség. Ha már rendelkezik fiókkal, ugorjon a következő szakaszra. Ha nem rendelkezik fiókkal, néhány perc alatt bemutatjuk, hogyan [hozhat létre Cognitive Services fiókot az Azure](cognitive-services-apis-create-account.md)-hoz.

Az előfizetési kulcsot a fiók létrehozása után, illetve az [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/my-apis)aktiválása után is lekérheti a [Azure Portal](cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) .

## <a name="authentication-headers"></a>Hitelesítési fejlécek

Gyorsan áttekintheti az Azure Cognitive Services használható hitelesítési fejléceket.

| Fejléc | Leírás |
|--------|-------------|
| OCP-Apim-Subscription-Key | Ezt a fejlécet használva hitelesítheti magát egy adott szolgáltatás vagy egy több szolgáltatás előfizetési kulcsának előfizetési kulcsával. |
| Ocp-Apim-Subscription-Region | Ez a fejléc csak akkor szükséges, ha többszolgáltatásos előfizetési kulcsot használ a [Translator Text API](./Translator/reference/v3-0-reference.md). Ez a fejléc az előfizetési régió megadására használható. |
| Engedélyezés | Használja ezt a fejlécet, ha hitelesítési jogkivonatot használ. A jogkivonat-csere végrehajtásának lépései a következő fejezetekben találhatók. A megadott érték a következő formátumot követi: `Bearer <TOKEN>`. |

## <a name="authenticate-with-a-single-service-subscription-key"></a>Hitelesítés egyetlen szolgáltatású előfizetési kulccsal

Az első lehetőség egy adott szolgáltatáshoz tartozó előfizetési kulccsal rendelkező kérelem hitelesítése, például Translator Text. A kulcsok a Azure Portalban érhetők el minden létrehozott erőforráshoz. Ahhoz, hogy előfizetési kulcsot használjon a kérelmek hitelesítéséhez, a `Ocp-Apim-Subscription-Key` fejlécének kell átadnia.

Ezek a példák azt mutatják be, hogyan használható a `Ocp-Apim-Subscription-Key` fejléc. Ne feledje, hogy a minta használatakor érvényes előfizetési kulcsot kell tartalmaznia.

Ez egy példa a Bing Web Search APIre:
```cURL
curl -X GET 'https://api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Ez egy példa a Translator Text APIre:
```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

Az alábbi videó egy Cognitive Services kulcs használatát mutatja be.

## <a name="authenticate-with-a-multi-service-subscription-key"></a>Hitelesítés több szolgáltatásból álló előfizetési kulccsal

>[!WARNING]
> Ezek a szolgáltatások jelenleg **nem** támogatják a több szolgáltatásból álló kulcsokat: QnA Maker, Speech services, Custom Vision és anomália detektor.

Ez a beállítás egy előfizetési kulcsot is használ a kérelmek hitelesítéséhez. A fő különbség az, hogy egy előfizetési kulcs nem kötődik egy adott szolgáltatáshoz, hanem egyetlen kulcsot is használhat több Cognitive Services kérelmek hitelesítéséhez. A regionális rendelkezésre állással, a támogatott funkciókkal és a díjszabással kapcsolatos információkért tekintse meg a [Cognitive Services díjszabását](https://azure.microsoft.com/pricing/details/cognitive-services/) .

Az előfizetési kulcsot minden kérelemben a `Ocp-Apim-Subscription-Key` fejlécként kell megadnia.

[![több szolgáltatás előfizetési kulcsának bemutatója Cognitive Services](./media/index/single-key-demonstration-video.png)](https://www.youtube.com/watch?v=psHtA1p7Cas&feature=youtu.be)

### <a name="supported-regions"></a>Támogatott régiók

Ha a többszolgáltatásos előfizetési kulcsot használja a `api.cognitive.microsoft.com`re irányuló kérelem elvégzéséhez, akkor a régiót az URL-címben is fel kell vennie. Például: `westus.api.cognitive.microsoft.com`.

Ha a Translator Text API több szolgáltatás előfizetési kulcsát használja, meg kell adnia az előfizetési régiót a `Ocp-Apim-Subscription-Region` fejléccel.

A többszolgáltatásos hitelesítés a következő régiókban támogatott:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |


### <a name="sample-requests"></a>Példák a kérelmekre

Ez egy példa a Bing Web Search APIre:

```cURL
curl -X GET 'https://YOUR-REGION.api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Ez egy példa a Translator Text APIre:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Ocp-Apim-Subscription-Region: YOUR_SUBSCRIPTION_REGION' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="authenticate-with-an-authentication-token"></a>Hitelesítés hitelesítési jogkivonattal

Egyes Azure-Cognitive Services elfogadnak, és bizonyos esetekben szükség van egy hitelesítési jogkivonatra. Ezek a szolgáltatások jelenleg támogatják a hitelesítési jogkivonatokat:

* Szöveges fordítási API
* Speech Services: beszéd – szöveg REST API
* Speech Services: szöveg – beszéd REST API

>[!NOTE]
> A QnA Maker az engedélyezési fejlécet is használja, de szükség van egy végponti kulcsra. További információ [: QnA Maker: Get válasz a Tudásbázisból](./qnamaker/quickstarts/get-answer-from-knowledge-base-using-url-tool.md).

>[!WARNING]
> A hitelesítési jogkivonatokat támogató szolgáltatások idővel változhatnak, mielőtt ezt a hitelesítési módszert használja, ellenőrizze a szolgáltatás API-referenciáját.

A hitelesítési jogkivonatok esetében az egyszolgáltatásos és a több szolgáltatásra kiterjedő előfizetési kulcsok is kicserélhetők. A hitelesítési jogkivonatok 10 percig érvényesek.

A hitelesítési jogkivonatok `Authorization` fejlécként szerepelnek a kérelemben. A jogkivonat megadott értékét a `Bearer`előtt kell megadni, például: `Bearer YOUR_AUTH_TOKEN`.

### <a name="sample-requests"></a>Példák a kérelmekre

Használja ezt az URL-címet egy hitelesítési jogkivonat előfizetési kulcsának cseréjére: `https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken`.

```cURL
curl -v -X POST \
"https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
-H "Content-type: application/x-www-form-urlencoded" \
-H "Content-length: 0" \
-H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

Ezek a több szolgáltatási régió támogatja a jogkivonat-Exchange-t:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |

A hitelesítési jogkivonat beszerzése után minden kérelembe át kell adni a `Authorization` fejlécként. Ez egy példa a Translator Text APIre:

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
