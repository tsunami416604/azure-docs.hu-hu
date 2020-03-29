---
title: Entitásfelismerés használata a Text Analytics API-val
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan azonosíthatja és semmisítheti meg a szövegben található entitás identitását a Text Analytics REST API-val.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/10/2020
ms.author: aahi
ms.openlocfilehash: 243086ddaae47eba20eea6877fe6d7f8f9889290
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79203491"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>A Named Entity Recognition használata a Text Analytics szolgáltatásban

A Text Analytics API lehetővé teszi, hogy strukturálatlan szöveget, és visszaadja a nem egyértelmű entitások listáját, a hivatkozásokat további információkra az interneten. Az API támogatja mind a névvel ellátott entitásfelismerést (NER) és az entitáscsatolást.

### <a name="entity-linking"></a>Entitáskapcsolás

Az entitásösszekapcsolás képes azonosítani és félretenni a szövegben található entitás személyazonosságát (például annak meghatározása, `Mars` hogy a szó előfordulása a bolygóra vagy a háború római istenére utal-e). Ehhez a folyamathoz egy tudásbázis meglétére van szükség a megfelelő nyelven, hogy a felismert entitásokat szövegben összekapcsolják. Az Entity Linking a [Wikipédiát](https://www.wikipedia.org/) használja erre a tudásbázisra.


### <a name="named-entity-recognition-ner"></a>Elnevezett entitásfelismerés (NER)

A névvel ellátott entitásfelismerés (NER) lehetővé teszi a különböző entitások azonosítását a szövegben, és előre meghatározott osztályokba vagy típusokba sorolja őket, mint például: személy, hely, esemény, termék és szervezet.  

A Text Analytics API 3-as verziójától kezdve a Text Analytics API ezen szolgáltatása olyan személyes és bizalmas adattípusokat is képes azonosítani, mint például: telefonszám, társadalombiztosítási szám, e-mail-cím és bankszámlaszám.  Ezeknek az entitásoknak az azonosítása segíthet a bizalmas dokumentumok besorolásában és a személyes adatok átminősítésében.

## <a name="named-entity-recognition-versions-and-features"></a>Elnevezett entitásfelismerési verziók és -szolgáltatások

A Text Analytics API a Named Entity Recognition két verzióját kínálja – a 2-es és a v3-as verziót. 3-as verzió (Nyilvános előzetes verzió) nyújt nagyobb részletességgel az entitások, amelyek észlelhetők és kategorizálhatók.

| Szolgáltatás                                                         | NER v2 | NER v3 |
|-----------------------------------------------------------------|--------|--------|
| Az egyedi és kötegelt kérelmek módszerei                          | X      | X      |
| Alapvető entitások kiismerése több kategóriában              | X      | X      |
| Ismert entitások kiterjesztett besorolása                 |        | X      |
| Külön végpontok az entitás-összekapcsolás és a NER-kérelmek küldéséhez. |        | X      |
| Modellverziószámozás                                                |        | X      |

Az információkat a [nyelvi támogatásban](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition) talál.


#### <a name="version-30-preview"></a>[3.0-s verziójú előzetes](#tab/version-3)

### <a name="entity-types"></a>Entitástípusok

Az elnevezett entitásfelismerés v3 több típusra bontott észlelést biztosít. Jelenleg a NER v3 a következő entitáskategóriákat ismeri fel:

* Általános kérdések
* Személyes adatok 

A támogatott entitások és nyelvek részletes listáját a [NER v3 által támogatott entitástípusok](../named-entity-types.md) című cikkben olvashatja.

### <a name="request-endpoints"></a>Végpontok kérése

A Named Entity Recognition v3 külön végpontokat használ a NER és az entitáscsatolási kérelmekhez. A kérés alapján az alábbi URL-formátumot használja:

Ner
* Általános entitások -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Személyes adatok -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Entitáscsatolás
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>Modellverziószámozás

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

### <a name="entity-types"></a>Entitástípusok

> [!NOTE]
> A Named Entity Recognition(NER) 2-es verziója csak a következő entitásokat támogatja. A NER v3 nyilvános előzetes verzióban érhető el, és jelentősen kibővíti a szövegben felismert entitások számát és mélységét.   

| Típus  | Altípus | Példa |
|:-----------   |:------------- |:---------|
| Személy        | N/a\*         | "Jeff", "Bill Gates"     |
| Hely      | N/a\*         | "Redmond, Washington", "Párizs"  |
| Szervezet  | N/a\*         | "Microsoft"   |
| Mennyiség      | Szám        | "6", "hat"     |
| Mennyiség      | Százalék    | "50%", "ötven százalék"|
| Mennyiség      | Ordinális       | "2.", "második"     |
| Mennyiség      | Kor           | "90 napos", "30 éves"    |
| Mennyiség      | Currency (Pénznem)      | "10,99 $ "     |
| Mennyiség      | Dimenzió     | "10 mérföld", "40 cm"     |
| Mennyiség      | Hőmérséklet   | "32 fok"    |
| DateTime      | N/a\*         | "2012. február 4., 18:30"      |
| DateTime      | Dátum          | "May 2nd, 2017", "05/02/2017"   |
| DateTime      | Time          | "8:00", "8:00"  |
| DateTime      | Dátumtartomány     | "Május 2-tól május 5-ig"    |
| DateTime      | Időtartomány     | "18:00-19:00"     |
| DateTime      | Időtartam      | "1 perc 45 másodperc"   |
| DateTime      | Beállítás           | "minden kedden"     |
| URL-cím           | N/a\*         | "https:\//www.bing.com"    |
| E-mail         | N/a\*         | "support@contoso.com" |
| Amerikai telefonszám  | N/a\*         | (csak amerikai telefonszámok esetén) "(312) 555-0176" |
| IP-cím    | N/a\*         | "10.0.0.100" |

\*A bemeneti és kinyert entitásoktól függően bizonyos `SubType`entitások kihagyhatják a .  Az összes felsorolt támogatott entitástípus csak az angol, a kínai egyszerűsített, a francia, a német és a spanyol nyelvekhez érhető el.

### <a name="request-endpoints"></a>Végpontok kérése

Az elnevezett entitásfelismerés v2 egyetlen végpontot használ a NER és az entitásösszekapcsolási kérelmekhez:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

## <a name="sending-a-rest-api-request"></a>REST API-kérelem küldése

### <a name="preparation"></a>Előkészítés

A JSON-dokumentumoknak ebben a formátumban kell lennie: azonosító, szöveg, nyelv.

Minden dokumentumnak 5120 karakternél kisebbnek kell lennie, és gyűjteményenként legfeljebb 1000 elem (azonosító) lehet. A kollekció elküldése a kérelem törzsében történik.

### <a name="structure-the-request"></a>A kérelem strukturálása

Hozzon létre egy POST kérést. A [Postman](text-analytics-how-to-call-api.md) vagy az API-tesztkonzol segítségével gyorsan strukturálhatja és elküldheti az **API-t.** 

> [!NOTE]
> Az azure-portálon megtalálhatja a kulcs átési végpontját a Text Analytics-erőforráshoz. Ezek az erőforrás **gyorskezdőlapján,** az **erőforrás-kezelés**csoportban találhatók. 

#### <a name="version-30-preview"></a>[3.0-s verziójú előzetes](#tab/version-3)

[Elnevezett entitásfelismerés v3 hivatkozása](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral)

A 3-as verzió külön végpontokat használ a NER és az entitásösszekapcsolási kérelmekhez. A kérés alapján az alábbi URL-formátumot használja:

Ner
* Általános entitások -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Személyes adatokat közrenyekkel foglalkozó szervezetek -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Entitáscsatolás
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

[Elnevezett entitásfelismerés (NER) v2 hivatkozás](https://eastus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

A 2-es verzió a következő végpontot használja az entitás-összekapcsoláshoz és a NER-kérelmekhez: 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

Állítsa be a kérelem fejlécét, hogy tartalmazza a Text Analytics API-kulcsot. A kérelem törzsében adja meg az elkészített JSON-dokumentumokat.

### <a name="example-ner-request"></a>Példa NER kérésre 

Az alábbi példa az API-nak küldhető tartalmakat. A kérelem formátuma megegyezik az API mindkét verziójában.

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

## <a name="post-the-request"></a>A kérelem feladása

Az elemzés a kérelem megkapásakor történik meg. Az [adatkorlátok](../overview.md#data-limits) szakaszát az áttekintésben a percenként és másodpercenként elküldhető kérelmek méretéről és számáról olvashat.

A Text Analytics API állapot nélküli. A rendszer nem tárol adatokat a fiókjában, és az eredményeket azonnal visszaadja a válasznak.

## <a name="view-results"></a>Eredmények megtekintése

Minden POST-kérelem JSON-formátumú választ ad vissza az azonosítókkal, és entitástulajdonságokat észlelt.

A kimenetet visszaadása azonnali. Az eredmények adatfolyamát JSON elfogadó alkalmazáshoz küldheti vagy a kimenetet elmentheti fájlba a helyi rendszeren, majd importálható az adatokat rendezni, keresni és kezelni képes alkalmazásba. A többnyelvű és emoji támogatásnak köszönhetően a válasz szövegeltolásokat tartalmazhat. További információ [a szövegeltolások feldolgozásáról.](../concepts/text-offsets.md)

#### <a name="version-30-preview"></a>[3.0-os verzió-előnézet)](#tab/version-3)

### <a name="example-v3-responses"></a>Példa v3 válaszok

A 3-as verzió külön végpontokat biztosít a NER és az entitáskötéshez. A két műveletre adott válaszok az alábbiakban találhatók. 

#### <a name="example-ner-response"></a>Példa NER válasz

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

#### <a name="example-entity-linking-response"></a>Példa entitáscsatolási válaszra

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

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

### <a name="example-ner-v2-response"></a>Példa NER v2 válasz
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

---

## <a name="summary"></a>Összefoglalás

Ebben a cikkben megtanulta a fogalmakés munkafolyamat entitás összekapcsolása a Cognitive Services szövegelemzés használatával. Összegezve:

* Az elnevezett entitásfelismerés két változatban érhető el a kiválasztott nyelvekhez.
* A kérelem törzsében lévő JSON-dokumentumok azonosítót, szöveget és nyelvi kódot tartalmaznak.
* Post kérelmek küldése egy vagy több végpontra, egy személyre szabott [hozzáférési kulcs és egy végpont,](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) amely érvényes az előfizetéshez.
* A válaszkimenet, amely összekapcsolt entitásokból áll (beleértve a megbízhatósági pontszámokat, az eltolásokat és a webes hivatkozásokat, minden egyes dokumentumazonosítóhoz) bármely alkalmazásban használható.

## <a name="next-steps"></a>További lépések

* [Szövegelemzés – áttekintés](../overview.md)
* [A Text Analytics ügyféltár használata](../quickstarts/text-analytics-sdk.md)
* [Újdonságok](../whats-new.md)
