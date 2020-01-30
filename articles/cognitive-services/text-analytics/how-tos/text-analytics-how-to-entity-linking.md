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
ms.date: 01/27/2019
ms.author: aahi
ms.openlocfilehash: 9aa00898c6a567d495ed0c66bcf7bd475067fa0d
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774139"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Elnevezett entitások felismerésének használata a Text Analyticsban

A Text Analytics API lehetővé teszi a strukturálatlan szöveg átrendezését és a disambiguated entitások listájának beolvasása, amely a weben található további információkra mutató hivatkozásokat tartalmaz. Az API támogatja az elnevezett entitások felismerését és az entitások összekapcsolását is.

### <a name="entity-linking"></a>Entitáskapcsolás

Az entitások összekapcsolása lehetővé teszi egy szövegben talált entitás identitásának azonosítását és egyértelműsítse (például annak meghatározását, hogy a szó előfordulása `Mars`-e a bolygón vagy a háború római istene). Ennek a folyamatnak a megfelelő nyelven kell megadnia egy tudásbázist, hogy az felismert entitások szövegben legyenek összekapcsolva. Az Entity Linking a [wikipedia](https://www.wikipedia.org/) -t használja a Tudásbázisban.


### <a name="named-entity-recognition-ner"></a>Elnevezett entitások felismerése

A nevesített entitások felismerése lehetővé teszi a különböző entitások azonosítását a szövegben, és azokat előre definiált osztályokra vagy típusokra kategorizálhatja. Például: személyek, helyek és szervezetek.

## <a name="named-entity-recognition-versions-and-features"></a>Elnevezett entitás-felismerési verziók és szolgáltatások

A Text Analytics API az elnevezett Entity Recognition-v2 és v3 két verzióját kínálja. A 3. verzió (nyilvános előzetes verzió) nagyobb részletességet biztosít az észlelhető és kategorizálható entitásokban.

| Szolgáltatás                                                         | Felkerülő v2 | V3 |
|-----------------------------------------------------------------|--------|--------|
| Egyszeri és batch-kérelmek módszerei                          | X      | X      |
| Alapszintű entitások felismerése több kategória között              | X      | X      |
| Felismert entitások kiterjesztett besorolása                 |        | X      |
| Különálló végpontok az entitások összekapcsolását és a meghívást kérő kérelmek küldéséhez. |        | X      |
| Modell verziószámozása                                                |        | X      |

További információért lásd a [nyelvi támogatást](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition) ismertető témakört.

#### <a name="version-2tabversion-2"></a>[2-es verzió](#tab/version-2)

### <a name="entity-types"></a>Entitások típusai

> [!NOTE]
> Az elnevezett entitások felismerése () 2-es verziója csak a következő entitásokat támogatja. Nyilvános előzetes verzióban érhető el, és nagy mértékben kibővíti a szövegben felismert entitások számát és mélységét.   

| Type (Típus)  | Altípus | Példa |
|:-----------   |:------------- |:---------|
| Személy        | N/A\*         | "Jeff", "Bill Gates"     |
| Földrajzi egység      | N/A\*         | „Redmond, Washington”, „Paris”  |
| Szervezet  | N/A\*         | „Microsoft”   |
| Mennyiség      | Szám        | „6”, „six”     |
| Mennyiség      | Százalék    | „50%”, „fifty percent”|
| Mennyiség      | Sorszám       | „2nd”, „second”     |
| Mennyiség      | Kor           | "90 napos", "30 év régi"    |
| Mennyiség      | Currency (Deviza)      | „$10.99”     |
| Mennyiség      | Dimenzió     | „10 miles”, „40 cm”     |
| Mennyiség      | Hőmérséklet   | „32 degrees”    |
| Dátum és idő      | N/A\*         | „6:30PM February 4, 2012”      |
| Dátum és idő      | Dátum          | „May 2nd, 2017”, „05/02/2017”   |
| Dátum és idő      | Idő          | "08:00", "8:00"  |
| Dátum és idő      | Dátumtartomány     | „May 2nd to May 5th”    |
| Dátum és idő      | Időtartomány     | „6pm to 7pm”     |
| Dátum és idő      | Időtartam      | „1 minute and 45 seconds”   |
| Dátum és idő      | Beállítás           | „every Tuesday”     |
| URL-cím           | N/A\*         | "https:\//www.bing.com"    |
| E-mail cím         | N/A\*         | „support@contoso.com” |
| Egyesült államokbeli telefonszám  | N/A\*         | (Csak az Egyesült államokbeli telefonszámok) "(312) 555-0176" |
| IP-cím    | N/A\*         | 10.0.0.100 |

\* a bemeneti és a kinyert entitástól függően előfordulhat, hogy bizonyos entitások kihagyhatják a `SubType`.  A felsorolt összes támogatott entitás csak angol, Kínai – egyszerűsített, francia, német és spanyol nyelven érhető el.

### <a name="request-endpoints"></a>Kérelmek végpontjai

Az elnevezett Entity Recognition v2 egyetlen végpontot használ az egyhelyes és az entitás-összekapcsolási kérelmekhez:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

#### <a name="version-3-public-previewtabversion-3"></a>[3. verzió (nyilvános előzetes verzió)](#tab/version-3)

### <a name="entity-types"></a>Entitások típusai

A nevesített entitások felismerése v3 több típusra kiterjedő kibővített észlelést biztosít. Jelenleg az alábbi kategóriájú entitások is felismerhetők:

* Általános
* Személyes adatok 

A támogatott entitások és nyelvek részletes listáját a következő témakörben találja: a [támogatott entitások támogatása](../named-entity-types.md) .

### <a name="request-endpoints"></a>Kérelmek végpontjai

Az elnevezett entitások felismerése v3 külön végpontokat használ az Egypéldányos és az entitás-összekapcsolási kérelmekhez Az alábbi URL-formátumot használja a kérelme alapján:

NER
* Általános entitások – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Személyes adatok – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Entitás összekapcsolása
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>Modell verziószámozása

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

---

## <a name="sending-a-rest-api-request"></a>REST API kérelem küldése

### <a name="preparation"></a>Előkészítés

A JSON-dokumentumoknak ebben a formátumban kell szerepelniük: azonosító, szöveg, nyelv.

Minden dokumentumnak 5 120 karakterből kell állnia, és gyűjteményben legfeljebb 1 000 elem (azonosító) lehet. A kollekció elküldése a kérelem törzsében történik.

### <a name="structure-the-request"></a>A kérelem szerkezete

Hozzon létre egy POST-kérelmet. A következő hivatkozásokon a [Poster](text-analytics-how-to-call-api.md) vagy az **API-tesztelési konzol** használatával gyorsan feldolgozhatja és elküldheti az egyiket. 

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

#### <a name="version-2tabversion-2"></a>[2-es verzió](#tab/version-2)

[Nevesített entitások felismerése (megnevezett) v2 – dokumentáció](https://eastus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

A 2. verzió a következő végpontot használja az entitás-összekapcsolási és a Meghívási kérelmekhez: 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

#### <a name="version-3tabversion-3"></a>[3-as verzió](#tab/version-3)

[Elnevezett Entity Recognition v3-dokumentáció](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral)

A 3-as verzió külön végpontokat használ az Egypéldányos és az entitás-összekapcsolási kérelmekhez. Az alábbi URL-formátumot használja a kérelme alapján:

NER
* Általános entitások – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Személyes információk entitások – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Entitás összekapcsolása
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

---

A Text Analytics API kulcsot tartalmazó kérelem fejlécének beállítása. A kérelem törzsében adja meg az előkészített JSON-dokumentumokat.

### <a name="example-ner-request"></a>Példa a kérelemre 

Az alábbi példa az API-nak elküldhető tartalomra mutat. A kérelem formátuma megegyezik az API mindkét verziójával.

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "I had a wonderful trip to Seattle last week."
    }
  ]
}
```

## <a name="post-the-request"></a>A kérelem közzététele

Az elemzés a kérelem megkapásakor történik meg. Az áttekinthető kérelmek méretével és számával kapcsolatos információkat az [Áttekintés lapon](../overview.md#data-limits) tekintheti meg a percenkénti küldéshez.

A Text Analytics API állapot nélküli. A fiókjában nem tárolunk semmilyen adatmennyiséget, és az eredményeket azonnal visszaadja a válaszban.

## <a name="view-results"></a>Eredmények megtekintése

Az összes POST kérelem egy JSON formátumú választ ad vissza az azonosítók és az észlelt entitás tulajdonságaival.

A kimenetet visszaadása azonnali. Az eredmények adatfolyamát JSON elfogadó alkalmazáshoz küldheti vagy a kimenetet elmentheti fájlba a helyi rendszeren, majd importálható az adatokat rendezni, keresni és kezelni képes alkalmazásba.

#### <a name="version-2tabversion-2"></a>[2-es verzió](#tab/version-2)

### <a name="example-ner-v2-response"></a>Példa a kiválasztó v2-re
```json
{
  "documents": [{
    "id": "1",
    "entities": [{
      "name": "Seattle",
      "matches": [{
        "wikipediaScore": 0.15046201222847677,
        "entityTypeScore": 0.80624294281005859,
        "text": "Seattle",
        "offset": 26,
        "length": 7
      }],
      "wikipediaLanguage": "en",
      "wikipediaId": "Seattle",
      "wikipediaUrl": "https://en.wikipedia.org/wiki/Seattle",
      "bingId": "5fbba6b8-85e1-4d41-9444-d9055436e473",
      "type": "Location"
    }, {
      "name": "last week",
      "matches": [{
        "entityTypeScore": 0.8,
        "text": "last week",
        "offset": 34,
        "length": 9
      }],
      "type": "DateTime",
      "subType": "DateRange"
    }]
  }],
  "errors": []
}
```

#### <a name="version-3-public-previewtabversion-3"></a>[3. verzió (nyilvános előzetes verzió)](#tab/version-3)

### <a name="example-v3-responses"></a>V3-válaszok – példa

A 3. verzió külön végpontokat biztosít az Egypéldányos és az entitások összekapcsolásához. A két műveletre adott válaszok alább láthatók.

#### <a name="example-ner-response"></a>Példa a válaszra

```json
{
    "documents": [{
    "id": "1",
    "entities": [{
        "text": "Seattle",
        "type": "Location",
        "offset": 26,
        "length": 7,
        "score": 0.80624294281005859
    }, {
        "text": "last week",
        "type": "DateTime",
        "subtype": "DateRange",
        "offset": 34,
        "length": 9,
        "score": 0.8
    }]
    }],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

#### <a name="example-entity-linking-response"></a>Példa entitás-csatolási választ

```json
{
  "documents": [{
    "id": "1",
    "entities": [{
      "name": "Seattle",
      "matches": [{
        "text": "Seattle",
        "offset": 26,
        "length": 7,
        "score": 0.15046201222847677
      }],
      "language": "en",
      "id": "Seattle",
      "url": "https://en.wikipedia.org/wiki/Seattle",
      "dataSource": "Wikipedia"
    }]
  }],
  "errors": [],
  "modelVersion": "2019-10-01"
}
```

---

## <a name="summary"></a>Összefoglalás

Ebben a cikkben megtanulta az entitások összekapcsolásával kapcsolatos fogalmakat és munkafolyamatokat a Cognitive Services Text Analytics használatával. Összegezve:

* A nevesített entitások felismerése két verzióban érhető el a kiválasztott nyelvekhez.
* A kérelem törzsében található JSON-dokumentumok közé tartozik az azonosító, a szöveg és a nyelvi kód.
* A POST kéréseket egy vagy több végpontra küldi a rendszer, egy személyre szabott [hozzáférési kulccsal és egy](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) , az előfizetéséhez érvényes végpontot használva.
* Bármely alkalmazásban használhatók olyan válaszok, amelyek összekapcsolt entitásokból (beleértve a megbízhatósági pontszámokat, az eltolásokat és a webes hivatkozásokat) tartalmazzák

## <a name="next-steps"></a>Következő lépések

* [A Text Analytics áttekintése](../overview.md)
* [Az Text Analytics ügyféloldali kódtár használata](../quickstarts/text-analytics-sdk.md)
* [Újdonságok](../whats-new.md)
