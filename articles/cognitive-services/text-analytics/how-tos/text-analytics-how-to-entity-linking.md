---
title: Use entity recognition with the Text Analytics API
titleSuffix: Azure Cognitive Services
description: Learn how to identify and disambiguate the identity of an entity found in text with the Text Analytics REST API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 11/21/2019
ms.author: aahi
ms.openlocfilehash: ae5222dcd05740ecb9747037b315c4e920b3eabd
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326627"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>How to use Named Entity Recognition in Text Analytics

The [Named Entity Recognition API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) takes unstructured text, and for each JSON document, returns a list of disambiguated entities with links to more information on the web (Wikipedia and Bing).

## <a name="entity-linking-and-named-entity-recognition"></a>Entity Linking and Named Entity Recognition

The Text Analytics' `entities` endpoint supports both named entity recognition (NER) and entity linking.

### <a name="entity-linking"></a>Entitáskapcsolás
Entity linking is the ability to identify and disambiguate the identity of an entity found in text (for example, determining whether the "Mars" is being used as the planet or as the Roman god of war). This process requires the presence of a knowledge base to which recognized entities are linked - Wikipedia is used as the knowledge base for the `entities` endpoint Text Analytics.

### <a name="named-entity-recognition-ner"></a>Named Entity Recognition (NER)
Named entity recognition (NER) is the ability to identify different entities in text and categorize them into pre-defined classes, or types. 

## <a name="named-entity-recognition-v3-public-preview"></a>Named Entity Recognition v3 public preview

The next version of Named Entity Recognition is now available for public preview. It provides updates to both entity linking and Named Entity Recognition. Try it using the [API test console](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral).

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
        Expanded entity types and subtypes
    :::column-end:::
    :::column span="":::
     Expanded classification and detection for several named entity types.
    :::column-end:::
:::row-end:::
<!-- separate endpoints row-->
:::row:::
    :::column span="":::
        Separate request endpoints 
    :::column-end:::
    :::column span="":::
        Separate endpoints for sending entity linking and NER requests.
    :::column-end:::
:::row-end:::
<!-- model-version row -->
:::row:::
    :::column span="":::
        `model-version` parameter
    :::column-end:::
    :::column span="":::
        An optional parameter for choosing a version of the Text Analytics model. Currently only the default model is available for use.
    :::column-end:::
:::row-end:::

### <a name="entity-types"></a>Entity types

Named Entity Recognition v3 provides expanded detection across multiple types. Currently, NER v3 can recognize the following categories of entities. For a detailed list of supported entities and languages, see the [Named entity types](../named-entity-types.md) article.

* Általános
* Personal Information 

### <a name="request-endpoints"></a>Request endpoints

Named Entity Recognition v3 uses separate endpoints for NER and entity linking requests. Use a URL format below based on your request:

NER
* General entities - `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Personal information entities - `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Entity linking
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>Model versioning

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

## <a name="supported-types-for-named-entity-recognition-v2"></a>Supported Types for Named Entity Recognition v2

> [!NOTE]
> The following entities are supported by Named Entity Recognition(NER) version 2. [NER v3](#named-entity-recognition-v3-public-preview) is in public preview, and greatly expands the number and depth of the entities recognized in text.   

| Type (Típus)  | SubType | Példa |
|:-----------   |:------------- |:---------|
| Személy        | N/A\*         | "Jeff", "Bill Gates"     |
| Földrajzi egység      | N/A\*         | „Redmond, Washington”, „Paris”  |
| Szervezet  | N/A\*         | „Microsoft”   |
| Mennyiség      | Szám        | „6”, „six”     |
| Mennyiség      | Százalék    | „50%”, „fifty percent”|
| Mennyiség      | Sorszám       | „2nd”, „second”     |
| Mennyiség      | Kor           | "90 day old", "30 years old"    |
| Mennyiség      | Currency (Deviza)      | „$10.99”     |
| Mennyiség      | Dimenzió     | „10 miles”, „40 cm”     |
| Mennyiség      | Hőmérséklet   | „32 degrees”    |
| Dátum és idő      | N/A\*         | „6:30PM February 4, 2012”      |
| Dátum és idő      | Dátum          | „May 2nd, 2017”, „05/02/2017”   |
| Dátum és idő      | Idő          | "8am", "8:00"  |
| Dátum és idő      | Dátumtartomány     | „May 2nd to May 5th”    |
| Dátum és idő      | Időtartomány     | „6pm to 7pm”     |
| Dátum és idő      | Időtartam      | „1 minute and 45 seconds”   |
| Dátum és idő      | Beállítás           | „every Tuesday”     |
| URL-cím           | N/A\*         | "https:\//www.bing.com"    |
| E-mail cím         | N/A\*         | „support@contoso.com” |
| US Phone Number  | N/A\*         | (US phone numbers only) "(312) 555-0176" |
| IP-cím    | N/A\*         | "10.0.0.100" |

\* Depending on the input and extracted entities, certain entities may omit the `SubType`.  All the supported entity types listed are available only for the English, Chinese-Simplified, French, German, and Spanish languages.

### <a name="language-support"></a>Nyelvi támogatás

Using entity linking in various languages requires using a corresponding knowledge base in each language. For entity linking in Text Analytics, this means each language that is supported by the `entities` endpoint will link to the corresponding Wikipedia corpus in that language. Since the size of corpora varies between languages, it is expected that the entity linking functionality's recall will also vary. See the [language support](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition) article for more information.

## <a name="preparation"></a>Előkészítés

You must have JSON documents in this format: ID, text, language

For currently supported languages, see [this list](../text-analytics-supported-languages.md).

Document size must be under 5,120 characters per document, and you can have up to 1,000 items (IDs) per collection. A kollekció elküldése a kérelem törzsében történik. The following example is an illustration of content you might submit to the entity linking end.

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

+ Hozzon létre egy **POST** kérést. Review the API documentation for this request: [Entities API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

+ Set the HTTP endpoint for key phrase extraction by using either a Text Analytics resource on Azure or an instantiated [Text Analytics container](text-analytics-how-to-install-containers.md). You must include `/text/analytics/v2.1/entities`. Például: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`.

+ Set a request header to include [the access key](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) for Text Analytics operations.

+ A kérelem törzsében adja meg az elemzéshez előkészített JSON-dokumentum kollekciót

> [!Tip]
> Használható a [Postman](text-analytics-how-to-call-api.md) vagy nyissa meg az **API teszt konzolt** a [dokumentációban](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) a kérés felépítéséhez és a szolgáltatásnak történő POST elküldéséhez.

## <a name="step-2-post-the-request"></a>2\. lépés: A kérés elküldése

Az elemzés a kérelem megkapásakor történik meg. See the [data limits](../overview.md#data-limits) section in the overview for information on the size and number of requests you can send per minute and second.

Ne felejtse, hogy a szolgáltatás állapot nélküli. A fiókban nem tárol semmilyen adatot. Az eredményeket azonnal visszaadja a válaszban.

## <a name="step-3-view-results"></a>3\. lépés: Eredmények megtekintése

Minden POST kérés egy JSON formátumú választ ad vissza az azonosítókkal és az észlelt tulajdonságokkal.

A kimenetet visszaadása azonnali. Az eredmények adatfolyamát JSON elfogadó alkalmazáshoz küldheti vagy a kimenetet elmentheti fájlba a helyi rendszeren, majd importálható az adatokat rendezni, keresni és kezelni képes alkalmazásba.

An example of the output for entity linking is shown next:

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

In this article, you learned concepts and workflow for entity linking using Text Analytics in Cognitive Services. Összegezve:

+ [Entities API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) is available for selected languages.
+ JSON documents in the request body include an ID, text, and language code.
+ POST-kérés a `/entities` végpontra, az előfizetésre érvényes személyre szabott [hozzáférési kulcs és végpont](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) használatával.
+ Response output, which consists of linked entities (including confidence scores, offsets, and web links, for each document ID) can be used in any application

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Text Analytics API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

* [A Text Analytics áttekintése](../overview.md)
* [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)</br>
* [Text Analytics termékoldala](//go.microsoft.com/fwlink/?LinkID=759712)
