---
title: Az entitások felismerésének használata a Text Analytics API
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan ismerheti fel az entitásokat az Text Analytics REST API használatával.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 10/21/2019
ms.author: aahi
ms.openlocfilehash: e51e5945df8b08ec81db0c85416b31b3ec788ffd
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488651"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Elnevezett entitások felismerésének használata a Text Analyticsban

Az [elnevezett entitás-felismerési API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) strukturálatlan szöveget vesz igénybe, és minden JSON-dokumentumhoz a disambiguated entitások listáját adja vissza, amely a weben található további információkra mutató hivatkozásokat tartalmaz (wikipedia és Bing).

## <a name="entity-linking-and-named-entity-recognition"></a>Entitások összekapcsolása és elnevezett entitások felismerése

A Text Analytics "`entities` végpontja támogatja az elnevezett entitások felismerését és az entitások összekapcsolását is.

### <a name="entity-linking"></a>Entitáskapcsolás
Az entitások összekapcsolása lehetővé teszi egy szövegben talált entitás identitásának azonosítását és egyértelműsítse (például azt, hogy a "Mars" a bolygó vagy a háború római isteneként van-e használatban). Ehhez a folyamathoz meg kell adni egy tudásbázist, amelyre az elismert entitások kapcsolódnak – a wikipedia az `entities` Endpoint Text Analytics tudásbázisa.

### <a name="named-entity-recognition-ner"></a>Elnevezett entitások felismerése
Az elnevezett entitások felismerése lehetővé teszi a különböző entitások azonosítását a szövegben, és azokat előre definiált osztályokra vagy típusokra kategorizálja. 

## <a name="named-entity-recognition-v3-public-preview"></a>Elnevezett entitás-felismerés v3 nyilvános előzetes verziója

Az [elnevezett entitások felismerésének következő verziója](https://cognitiveusw2ppe.portal.azure-api.net/docs/services/TextAnalytics-v3-0-Preview-1/operations/56f30ceeeda5650db055a3c7/console) már elérhető a nyilvános előzetes verzióban. Frissítéseket biztosít mind az entitások, mind a nevesített entitások felismeréséhez. 

:::row:::
    :::column span="":::
        **Funkció**
    :::column-end:::
    ::: column span="":::
        **Leírás** 
    :::column-end:::
:::row-end:::
<!-- expanded types and subtypes row-->
:::row:::
    :::column span="":::
        Kibontott entitások típusai és altípusai
    :::column-end:::
    :::column span="":::
     Több elnevezett entitás típusának kibontott besorolása és észlelése.
    :::column-end:::
:::row-end:::
<!-- separate endpoints row-->
:::row:::
    :::column span="":::
        Külön kérelem-végpontok 
    :::column-end:::
    :::column span="":::
        Különálló végpontok az entitások összekapcsolását és a meghívást kérő kérelmek küldéséhez.
    :::column-end:::
:::row-end:::
<!-- model-version row -->
:::row:::
    :::column span="":::
        `model-version` paraméter
    :::column-end:::
    :::column span="":::
        Választható paraméter a Text Analytics modell valamelyik verziójának kiválasztásához. Jelenleg csak az alapértelmezett modell használható.
    :::column-end:::
:::row-end:::

### <a name="entity-types"></a>Entitások típusai

A nevesített entitások felismerése v3 több típusra kiterjedő kibővített észlelést biztosít. Jelenleg az alábbi kategóriájú entitásokat ismeri fel: v3. A támogatott entitások és nyelvek részletes listáját az [elnevezett entitások típusai](../named-entity-types.md) című cikkben találja.

* Általános kérdések
* Személyes adatok 

### <a name="request-endpoints"></a>Kérelmek végpontjai

Az elnevezett entitások felismerése v3 külön végpontokat használ az Egypéldányos és az entitás-összekapcsolási kérelmekhez Az alábbi URL-formátumot használja a kérelme alapján:

NER
* Általános entitások – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Személyes információk entitások – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Entitás összekapcsolása
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>Modell verziószámozása

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

## <a name="supported-types-for-named-entity-recognition-v2"></a>Megnevezett Entity Recognition v2 támogatott típusai

> [!NOTE]
> A következő entitásokat a nevesített entitások felismerése (a (z) 2. verzió) támogatja. Nyilvános előzetes verzióban [érhető el, és](#named-entity-recognition-v3-public-preview) nagy mértékben kibővíti a szövegben felismert entitások számát és mélységét.   

| Típus  | Altípus | Példa |
|:-----------   |:------------- |:---------|
| Személy        | N/A\*         | "Jeff", "Bill Gates"     |
| Földrajzi egység      | N/A\*         | „Redmond, Washington”, „Paris”  |
| Szervezet  | N/A\*         | „Microsoft”   |
| Mennyiség      | Szám        | „6”, „six”     |
| Mennyiség      | Százalék    | „50%”, „fifty percent”|
| Mennyiség      | Sorszám       | „2nd”, „second”     |
| Mennyiség      | Kor           | "90 napos", "30 év régi"    |
| Mennyiség      | Currency (Pénznem)      | „$10.99”     |
| Mennyiség      | Dimenzió     | „10 miles”, „40 cm”     |
| Mennyiség      | Hőmérséklet   | „32 degrees”    |
| DateTime      | N/A\*         | „6:30PM February 4, 2012”      |
| DateTime      | Dátum          | „May 2nd, 2017”, „05/02/2017”   |
| DateTime      | Time          | "08:00", "8:00"  |
| DateTime      | Dátumtartomány     | „May 2nd to May 5th”    |
| DateTime      | Időtartomány     | „6pm to 7pm”     |
| DateTime      | Időtartam      | „1 minute and 45 seconds”   |
| DateTime      | Beállítás           | „every Tuesday”     |
| URL-cím           | N/A\*         | "https:\//www.bing.com"    |
| E-mail         | N/A\*         | „support@contoso.com” |

\* a bemeneti és a kinyert entitástól függően előfordulhat, hogy bizonyos entitások kihagyhatják a `SubType`.  A felsorolt összes támogatott entitás csak angol, Kínai – egyszerűsített, francia, német és spanyol nyelven érhető el.

### <a name="language-support"></a>Nyelvi támogatás

Az entitások különböző nyelveken való összekapcsolásához a megfelelő tudásbázist kell használnia minden nyelven. A Text Analyticsban található entitások esetén ez azt jelenti, hogy az `entities` végpont által támogatott összes nyelv a megfelelő wikipedia-corpusra hivatkozik az adott nyelven. Mivel a Corpora mérete eltérő lehet a nyelvek között, a rendszer azt várta, hogy az entitás összekapcsolása funkció visszahívása is változhat. További információért tekintse meg a [nyelvi támogatásról](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition) szóló cikket.

## <a name="preparation"></a>Előkészítés

A JSON-dokumentumoknak ebben a formátumban kell szerepelniük: azonosító, szöveg, nyelv

A jelenleg támogatott nyelvek esetében tekintse meg [ezt a listát](../text-analytics-supported-languages.md).

A dokumentum méretének 5 120 karakternél rövidebbnek kell lennie, és egy gyűjteményben legfeljebb 1 000 elem (azonosító) szerepelhet. A kollekció elküldése a kérelem törzsében történik. Az alábbi példa egy olyan tartalom szemléltetése, amelyet a rendszer az entitás csatolásának végére küld.

```json
    {
        "documents": [
            {
                "id": "1",
                "language": "en",
                "text": "Jeff bought three dozen eggs because there was a 50% discount."
            },
            {
                "id": "2",
                "language": "en",
                "text": "The Great Depression began in 1929. By 1933, the GDP in America fell by 25%."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>1\. lépés: A kérés felépítése

A kérés definícióval kapcsolatos részletek megtalálhatók a [Text Analytics API hívásának módja](text-analytics-how-to-call-api.md) részben. A következő pontokat a kényelem kedvéért itt megismételjük:

+ Hozzon létre egy **POST** kérést. Tekintse át a kérelem API-dokumentációját: [entitások API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

+ Állítsa be a HTTP-végpontot a kulcsfontosságú kifejezés kinyeréséhez az Azure-ban vagy egy példányban [text Analytics tárolóban](text-analytics-how-to-install-containers.md)Text Analytics erőforrás használatával. Tartalmaznia kell `/text/analytics/v2.1/entities`. Például: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`.

+ Állítsa be a kérelem fejlécét, hogy tartalmazza a Text Analytics műveletekhez tartozó [hozzáférési kulcsot](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) .

+ A kérelem törzsében adja meg az elemzéshez előkészített JSON-dokumentum kollekciót

> [!Tip]
> Használható a [Postman](text-analytics-how-to-call-api.md) vagy nyissa meg az **API teszt konzolt** a [dokumentációban](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) a kérés felépítéséhez és a szolgáltatásnak történő POST elküldéséhez.

## <a name="step-2-post-the-request"></a>2\. lépés: A kérés elküldése

Az elemzés a kérelem megkapásakor történik meg. Az áttekinthető kérelmek méretével és számával kapcsolatos információkat az [Áttekintés lapon](../overview.md#data-limits) tekintheti meg a percenkénti küldéshez.

Ne felejtse, hogy a szolgáltatás állapot nélküli. A fiókban nem tárol semmilyen adatot. Az eredményeket azonnal visszaadja a válaszban.

## <a name="step-3-view-results"></a>3\. lépés: Eredmények megtekintése

Minden POST kérés egy JSON formátumú választ ad vissza az azonosítókkal és az észlelt tulajdonságokkal.

A kimenetet visszaadása azonnali. Az eredmények adatfolyamát JSON elfogadó alkalmazáshoz küldheti vagy a kimenetet elmentheti fájlba a helyi rendszeren, majd importálható az adatokat rendezni, keresni és kezelni képes alkalmazásba.

Az entitások csatolásának kimenetét a következő példa szemlélteti:

```json
    {
        "Documents": [
            {
                "Id": "1",
                "Entities": [
                    {
                        "Name": "Jeff",
                        "Matches": [
                            {
                                "Text": "Jeff",
                                "Offset": 0,
                                "Length": 4
                            }
                        ],
                        "Type": "Person"
                    },
                    {
                        "Name": "three dozen",
                        "Matches": [
                            {
                                "Text": "three dozen",
                                "Offset": 12,
                                "Length": 11
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "50",
                        "Matches": [
                            {
                                "Text": "50",
                                "Offset": 49,
                                "Length": 2
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "50%",
                        "Matches": [
                            {
                                "Text": "50%",
                                "Offset": 49,
                                "Length": 3
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Percentage"
                    }
                ]
            },
            {
                "Id": "2",
                "Entities": [
                    {
                        "Name": "Great Depression",
                        "Matches": [
                            {
                                "Text": "The Great Depression",
                                "Offset": 0,
                                "Length": 20
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "Great Depression",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/Great_Depression",
                        "BingId": "d9364681-98ad-1a66-f869-a3f1c8ae8ef8"
                    },
                    {
                        "Name": "1929",
                        "Matches": [
                            {
                                "Text": "1929",
                                "Offset": 30,
                                "Length": 4
                            }
                        ],
                        "Type": "DateTime",
                        "SubType": "DateRange"
                    },
                    {
                        "Name": "By 1933",
                        "Matches": [
                            {
                                "Text": "By 1933",
                                "Offset": 36,
                                "Length": 7
                            }
                        ],
                        "Type": "DateTime",
                        "SubType": "DateRange"
                    },
                    {
                        "Name": "Gross domestic product",
                        "Matches": [
                            {
                                "Text": "GDP",
                                "Offset": 49,
                                "Length": 3
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "Gross domestic product",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/Gross_domestic_product",
                        "BingId": "c859ed84-c0dd-e18f-394a-530cae5468a2"
                    },
                    {
                        "Name": "United States",
                        "Matches": [
                            {
                                "Text": "America",
                                "Offset": 56,
                                "Length": 7
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "United States",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/United_States",
                        "BingId": "5232ed96-85b1-2edb-12c6-63e6c597a1de",
                        "Type": "Location"
                    },
                    {
                        "Name": "25",
                        "Matches": [
                            {
                                "Text": "25",
                                "Offset": 72,
                                "Length": 2
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "25%",
                        "Matches": [
                            {
                                "Text": "25%",
                                "Offset": 72,
                                "Length": 3
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Percentage"
                    }
                ]
            }
        ],
        "Errors": []
    }
```

## <a name="summary"></a>Összefoglalás

Ebben a cikkben megtanulta az entitások összekapcsolásával kapcsolatos fogalmakat és munkafolyamatokat a Cognitive Services Text Analytics használatával. Összegezve:

+ Az [entitások API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) a kiválasztott nyelvekhez érhető el.
+ A kérelem törzsében található JSON-dokumentumok közé tartozik az azonosító, a szöveg és a nyelvi kód.
+ POST-kérés a `/entities` végpontra, az előfizetésre érvényes személyre szabott [hozzáférési kulcs és végpont](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) használatával.
+ Bármely alkalmazásban használhatók olyan válaszok, amelyek összekapcsolt entitásokból (beleértve a megbízhatósági pontszámokat, az eltolásokat és a webes hivatkozásokat) tartalmazzák

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Text Analytics API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

* [A Text Analytics áttekintése](../overview.md)
* [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)</br>
* [Text Analytics termékoldala](//go.microsoft.com/fwlink/?LinkID=759712)
