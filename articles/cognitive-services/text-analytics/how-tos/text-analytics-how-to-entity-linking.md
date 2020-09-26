---
title: Az entitások felismerésének használata a Text Analytics API
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan azonosíthatja és egyértelműsítse a szövegben talált entitás identitását a Text Analytics REST API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 09/24/2020
ms.author: aahi
ms.openlocfilehash: 5f5122b5fa7c20bc0717ef1605e41bb5f2700be2
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91309098"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Elnevezett entitások felismerésének használata a Text Analyticsban

A Text Analytics API lehetővé teszi a strukturálatlan szöveg átrendezését és a disambiguated entitások listájának beolvasása, amely a weben található további információkra mutató hivatkozásokat tartalmaz. Az API támogatja az elnevezett entitások felismerését és az entitások összekapcsolását is.

### <a name="entity-linking"></a>Entitáskapcsolás

Az entitások összekapcsolása lehetővé teszi egy szövegben talált entitás identitásának azonosítását és egyértelműsítse (például annak meghatározását, hogy a "Mars" szó előfordulása a bolygóra vagy a háború római istenere vonatkozik). Ennek a folyamatnak a megfelelő nyelven kell megadnia egy tudásbázist, hogy az felismert entitások szövegben legyenek összekapcsolva. Az Entity Linking a [wikipedia](https://www.wikipedia.org/) -t használja a Tudásbázisban.


### <a name="named-entity-recognition-ner"></a>Elnevezett entitások felismerése

Az elnevezett entitások felismerése lehetővé teszi a különböző entitások azonosítását a szövegben, és azokat előre definiált osztályokra vagy típusokra kategorizálhatja, például: személy, hely, esemény, termék és szervezet.  

## <a name="named-entity-recognition-versions-and-features"></a>Elnevezett entitás-felismerési verziók és szolgáltatások

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

| Funkció                                                         | V 3.0 | V 3.1 – előzetes verzió. 2 |
|-----------------------------------------------------------------|--------|----------|
| Egyszeri és batch-kérelmek módszerei                          | X      | X        |
| Bővített entitások felismerése több kategória között           | X      | X        |
| Különálló végpontok az entitások összekapcsolását és a meghívást kérő kérelmek küldéséhez. | X      | X        |
| Személyes ( `PII` ) és Health ( `PHI` ) információs entitások felismerése        |        | X        |

További információért lásd a [nyelvi támogatást](../language-support.md) ismertető témakört.

### <a name="entity-types"></a>Entitástípusok

A nevesített entitások felismerése v3 több típusra kiterjedő kibővített észlelést biztosít. A "v 3.0" jelenleg az [általános entitások kategóriájában](../named-entity-types.md)ismeri fel az entitásokat.

A nevesített entitások felismerése v 3.1 – preview. 2 tartalmazza a v 3.0 észlelési funkcióit, valamint a személyes adatok észlelésének képességét `PII` a `v3.1-preview.2/entities/recognition/pii` végpont használatával. A nem kötelező paraméter használatával `domain=phi` azonosíthatja a bizalmas állapottal kapcsolatos információkat ( `PHI` ). További információért tekintse meg az [entitások kategóriáit](../named-entity-types.md) ismertető cikket, és a [végpontok kérése](#request-endpoints) című szakaszt.


## <a name="sending-a-rest-api-request"></a>REST API kérelem küldése

### <a name="preparation"></a>Előkészítés

A JSON-dokumentumoknak ebben a formátumban kell szerepelniük: azonosító, szöveg, nyelv.

Minden dokumentumnak 5 120 karakterből kell állnia, és gyűjteményben legfeljebb 1 000 elem (azonosító) lehet. A kollekció elküldése a kérelem törzsében történik.

### <a name="structure-the-request"></a>A kérelem szerkezete

Hozzon létre egy POST kérést. A következő hivatkozásokon a [Poster](text-analytics-how-to-call-api.md) vagy az **API-tesztelési konzol** használatával gyorsan feldolgozhatja és elküldheti az egyiket. 

> [!NOTE]
> Az Azure Portalon megtalálhatja Text Analytics erőforrásának kulcsát és végpontját. Ezek az erőforrás **gyors üzembe helyezés** lapján, az **Erőforrás-kezelés**területen találhatók. 


### <a name="request-endpoints"></a>Kérelmek végpontjai

#### <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

Az elnevezett entitások felismerése v3 külön végpontokat használ az Egypéldányos és az entitás-összekapcsolási kérelmekhez Az alábbi URL-formátumot használja a kérelme alapján:

Entitás összekapcsolása
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

[Elnevezett entitás-felismerés 3,0-es verziójának referenciája `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

[Elnevezett entitás-felismerés 3,0-es verziójának referenciája `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

#### <a name="version-31-preview2"></a>[3,1-es verzió – preview. 2](#tab/version-3-preview)

Az elnevezett entitások felismerése `v3.1-preview.2` külön végpontokat használ az Egypéldányos és az entitás-összekapcsolási kérelmekhez Az alábbi URL-formátumot használja a kérelme alapján:

Entitás összekapcsolása
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.2/entities/linking`

[Elnevezett entitás-felismerés verziója 3,1 – előzetes verzió referenciája a következőhöz: `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesLinking)

NER
* Általános entitások – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.2/entities/recognition/general`

[Elnevezett entitás-felismerés verziója 3,1 – előzetes verzió referenciája a következőhöz: `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionGeneral)

* Személyes ( `PII` ) információk – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.2/entities/recognition/pii`

A nem kötelező `domain=phi` paraméterrel is felderítheti az állapotadatok ( `PHI` ) szövegben való észlelését. 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.2/entities/recognition/pii?domain=phi`

Jegyezze fel a tulajdonság hozzáadását `redactedText` a válasz JSON-ban, amely tartalmazza azt a módosított bemeneti szöveget, ahol az észlelt személyre szabási entitásokat a rendszer az entitások minden karaktere esetében a * értékre cseréli.

[Elnevezett entitás-felismerés verziója 3,1 – előzetes verzió referenciája a következőhöz: `PII`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionPii)

---

A Text Analytics API kulcsot tartalmazó kérelem fejlécének beállítása. A kérelem törzsében adja meg az előkészített JSON-dokumentumokat.

### <a name="example-ner-request"></a>Példa a kérelemre 

Az alábbi példa az API-nak elküldhető tartalomra mutat. A kérelem formátuma megegyezik az API mindkét verziójával.

```json
{
  "documents": [
    {
        "id": "1",
        "language": "en",
        "text": "Our tour guide took us up the Space Needle during our trip to Seattle last week."
    }
  ]
}

```

## <a name="post-the-request"></a>A kérelem közzététele

Az elemzés a kérelem megkapásakor történik meg. Az áttekinthető kérelmek méretével és számával kapcsolatos információkat az [Áttekintés lapon](../overview.md#data-limits) tekintheti meg a percenkénti küldéshez.

A Text Analytics API állapot nélküli. A fiókjában nem tárolunk semmilyen adatmennyiséget, és az eredményeket azonnal visszaadja a válaszban.

## <a name="view-results"></a>Eredmények megtekintése

Az összes POST kérelem egy JSON formátumú választ ad vissza az azonosítók és az észlelt entitás tulajdonságaival.

A kimenetet visszaadása azonnali. Az eredmények adatfolyamát JSON elfogadó alkalmazáshoz küldheti vagy a kimenetet elmentheti fájlba a helyi rendszeren, majd importálható az adatokat rendezni, keresni és kezelni képes alkalmazásba. A többnyelvű és a Emoji-támogatás miatt a válasz szöveges eltolásokat is tartalmazhat. További információért lásd: [a szöveg eltolásának feldolgozása](../concepts/text-offsets.md) .

### <a name="example-responses"></a>Válaszok – példa

A 3. verzió külön végpontokat biztosít az Egypéldányos és az entitások összekapcsolásához. A két műveletre adott válaszok alább láthatók. 

#### <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "text": "tour guide",
          "category": "PersonType",
          "offset": 4,
          "length": 10,
          "confidenceScore": 0.45
        },
        {
          "text": "Space Needle",
          "category": "Location",
          "offset": 30,
          "length": 12,
          "confidenceScore": 0.38
        },
        {
          "text": "trip",
          "category": "Event",
          "offset": 54,
          "length": 4,
          "confidenceScore": 0.78
        },
        {
          "text": "Seattle",
          "category": "Location",
          "subcategory": "GPE",
          "offset": 62,
          "length": 7,
          "confidenceScore": 0.78
        },
        {
          "text": "last week",
          "category": "DateTime",
          "subcategory": "DateRange",
          "offset": 70,
          "length": 9,
          "confidenceScore": 0.8
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-04-01"
}
```
#### <a name="version-31-preview"></a>[3,1-es verzió – előzetes verzió](#tab/version-3-preview)

```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "bingId": "f8dd5b08-206d-2554-6e4a-893f51f4de7e", 
          "name": "Space Needle",
          "matches": [
            {
              "text": "Space Needle",
              "offset": 30,
              "length": 12,
              "confidenceScore": 0.4
            }
          ],
          "language": "en",
          "id": "Space Needle",
          "url": "https://en.wikipedia.org/wiki/Space_Needle",
          "dataSource": "Wikipedia"
        },
        {
          "bingId": "5fbba6b8-85e1-4d41-9444-d9055436e473",
          "name": "Seattle",
          "matches": [
            {
              "text": "Seattle",
              "offset": 62,
              "length": 7,
              "confidenceScore": 0.25
            }
          ],
          "language": "en",
          "id": "Seattle",
          "url": "https://en.wikipedia.org/wiki/Seattle",
          "dataSource": "Wikipedia"
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-02-01"
}
```

---


## <a name="summary"></a>Összefoglalás

Ebben a cikkben megtanulta az entitások összekapcsolásával kapcsolatos fogalmakat és munkafolyamatokat a Cognitive Services Text Analytics használatával. Összegezve:

* A kérelem törzsében található JSON-dokumentumok közé tartozik az azonosító, a szöveg és a nyelvi kód.
* A POST kéréseket egy vagy több végpontra küldi a rendszer, egy személyre szabott [hozzáférési kulccsal és egy](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) , az előfizetéséhez érvényes végpontot használva.
* Bármely alkalmazásban használhatók olyan válaszok, amelyek összekapcsolt entitásokból (beleértve a megbízhatósági pontszámokat, az eltolásokat és a webes hivatkozásokat) tartalmazzák

## <a name="next-steps"></a>Következő lépések

* [A Text Analytics áttekintése](../overview.md)
* [Az Text Analytics ügyféloldali kódtár használata](../quickstarts/text-analytics-sdk.md)
* [Újdonságok](../whats-new.md)
