---
title: Névjegykártyák – űrlap-felismerő
titleSuffix: Azure Cognitive Services
description: A névjegykártya-elemzéssel kapcsolatos fogalmak megismerése az űrlap-felismerő API használatának és korlátainak használatával.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: pafarley
ms.openlocfilehash: f8f173291448d9da4d8967ff56b0fa027ca73409
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91334548"
---
# <a name="business-card-concepts"></a>Névjegykártyákra vonatkozó fogalmak

Az Azure-űrlap felismerője az előre elkészített modellek egyikének használatával elemezheti és kinyerheti a kapcsolattartási adatokat az üzleti kártyáról. A Business Card API ötvözi a hatékony optikai karakterfelismerési (OCR) képességeket a Business Card Understanding modell segítségével, hogy az üzleti kártyákról angol nyelven kinyerje a legfontosabb információkat. Kigyűjti a személyes kapcsolattartási adatokat, a vállalat nevét, a munkakör címét és egyebeket. Az előre elkészített névjegykártya API nyilvánosan elérhető a Form felismerő v 2.1 előzetes verziójában. 

## <a name="what-does-the-business-card-api-do"></a>Mit tesz a Business Card API?

A Business Card API Kinyeri a legfontosabb mezőket az üzleti kártyákból, és visszaadja őket egy szervezett JSON-válaszban.

![Contoso részletezett rendszerkép a FOTT + JSON kimenetből](./media/business-card-english.jpg)

### <a name="fields-extracted"></a>Kinyert mezők:

* Kapcsolattartók nevei 
  * Utónév
  * Vezetéknév
* Vállalati nevek 
* Részlegek 
* Feladatok címei 
* E-mail-címek 
* Webhelyek 
* Címek 
* Telefonszámok 
  * Mobiltelefonok 
  * Faxok 
  * Munkahelyi telefonok 
  * Egyéb telefonok 

A Business Card API az összes felismert szöveget visszaküldheti a névjegykártyából. Ez az OCR-kimenet szerepel a JSON-válaszban.  

### <a name="input-requirements"></a>Bemeneti követelmények 

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-business-card-operation"></a>A névjegykártya elemzése művelet

Az [elemzés](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync) a névjegykártyán egy, a bevitt adatokhoz tartozó képet vagy PDF-fájlt vesz fel, és Kinyeri a kamat értékeit. A hívás egy válasz fejléc nevű mezőt ad vissza `Operation-Location` . Az `Operation-Location` érték egy URL-cím, amely a következő lépésben használandó eredmény-azonosítót tartalmazza.

|Válasz fejléce| Eredmény URL-címe |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.1/prebuilt/businessCard/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-business-card-result-operation"></a>Az elemzés az üzleti kártya eredményének lekérdezése művelet

A második lépés a [Get elemezze névjegykártya-eredmény](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/GetAnalyzeBusinessCardResult) műveletének meghívása. Ez a művelet a névjegykártya elemzése művelet által létrehozott eredmény-azonosítót veszi figyelembe. Egy olyan JSON-választ ad vissza, amely tartalmazza az **állapot** mezőt a következő lehetséges értékekkel. Ezt a műveletet a iteratív hívja meg, amíg vissza nem tér a **sikeres** értékkel. A kérések másodpercenkénti (RPS) arányának meghaladása érdekében használjon 3 – 5 másodperces intervallumot.

|Mező| Típus | Lehetséges értékek |
|:-----|:----:|:----|
|status | sztring | notStarted: az elemzési művelet nem indult el.<br /><br />fut: az elemzési művelet folyamatban van.<br /><br />sikertelen: az elemzési művelet meghiúsult.<br /><br />sikeres: az elemzési művelet sikeresen befejeződött.|

Ha az **állapot** mező értéke **sikeres** , a JSON-válasz tartalmazni fogja a névjegykártya megértését és az opcionális szöveg-felismerési eredményeket, ha szükséges. Az üzleti kártya megértésének eredménye a megnevezett mezőértékek szótára lesz, ahol minden érték a kinyert szöveget, normalizált értéket, határoló mezőt, megbízhatóságot és a hozzá tartozó elemeket tartalmazza. A szöveg-felismerés eredményét vonalak és szavak hierarchiája rendezi, szöveg, határolókeret és bizalmas adatok formájában.

![névjegykártya-kimenet mintája](./media/business-card-results.png)

### <a name="sample-json-output"></a>Példa JSON-kimenetre

Tekintse meg a sikeres JSON-válasz következő példáját: a "readResults" csomópont tartalmazza az összes felismert szöveget. A szöveget az oldal, a sor, majd az egyes szavak szerint rendezi. A "documentResults" csomópont a modell által felderített névjegykártya-specifikus értékeket tartalmazza. Itt találhat hasznos kapcsolattartási adatokat, például az utónév, a vezetéknév, a vállalat neve és egyebek.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T17:41:19Z",
    "lastUpdatedDateTime": "2020-08-20T17:41:24Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel",
                   "lines": 
                             {
                        "text": "Dr. Avery Smith",
                        "boundingBox": [
                            419.3,
                            1154.6,
                            1589.6,
                            877.9,
                            1618.9,
                            1001.7,
                            448.6,
                            1278.4
                        ],
                        "words": [
                            {
                                "text": "Dr.",
                                "boundingBox": [
                                    419,
                            ]
    
            }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                    },
                                    "LastName": {
                                        "type": "string",
                                        "valueString": "Smith",
                                        "text": "Smith",
                                        "boundingBox": [
                                            1186,
                                            976,
                                            1585,
                                            879,
                                            1618,
                                            998,
                                            1218,
                                            1096
                                        ],
                                        "page": 1
                                    }
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.97
                            }
                        ]
                    },
                    "JobTitles": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Senior Researcher",
                                "text": "Senior Researcher",
                                "boundingBox": [
                                    451.8,
                                    1301.9,
                                    1313.5,
                                    1099.9,
                                    1333.8,
                                    1186.7,
                                    472.2,
                                    1388.7
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Departments": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Cloud & Al Department",
                                "text": "Cloud & Al Department",
                                "boundingBox": [
                                    480.1,
                                    1403.3,
                                    1590.5,
                                    1129.6,
                                    1612.6,
                                    1219.6,
                                    502.3,
                                    1493.3
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    },
                    "MobilePhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 7911 123456",
                                "boundingBox": [
                                    2434.9,
                                    1033.3,
                                    3072,
                                    836,
                                    3096.2,
                                    914.3,
                                    2459.1,
                                    1111.6
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "OtherPhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 9876 5432",
                                "boundingBox": [
                                    2473.2,
                                    1115.4,
                                    3139.2,
                                    907.7,
                                    3163.2,
                                    984.7,
                                    2497.2,
                                    1192.4
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Faxes": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 6789 2345",
                                "boundingBox": [
                                    2525,
                                    1185.4,
                                    3192.4,
                                    977.9,
                                    3217.9,
                                    1060,
                                    2550.5,
                                    1267.5
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Addresses": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "2 Kingdom Street Paddington, London, W2 6BD",
                                "text": "2 Kingdom Street Paddington, London, W2 6BD",
                                "boundingBox": [
                                    1230,
                                    2138,
                                    2535.2,
                                    1678.6,
                                    2614.2,
                                    1903.1,
                                    1309,
                                    2362.5
                                ],
                                "page": 1,
                                "confidence": 0.977
                            }
                        ]
                    },
                    "CompanyNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Contoso",
                                "text": "Contoso",
                                "boundingBox": [
                                    1152,
                                    1916,
                                    2293,
                                    1552,
                                    2358,
                                    1733,
                                    1219,
                                    2105
                                ],
                                "page": 1,
                                "confidence": 0.97
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

Az névjegykártya-adatok kinyeréséhez a Python és a REST API használatával hajtsa végre az névjegykártya- [adatok](./QuickStarts/python-business-cards.md) kinyerése című témakört.

## <a name="customer-scenarios"></a>Felhasználói forgatókönyvek  

A Business Card API-val kinyert adatok különféle feladatok elvégzésére használhatók. A kapcsolattartási adatok kinyerése automatikusan időt takarít meg az ügyfelek felé irányuló szerepkörökben. Az alábbiakban néhány példát láthat arra, hogy ügyfeleink hogyan teljesítik a Business Card API-t:

* A kapcsolattartási adatok kinyerése az üzleti kártyákból és a telefonos névjegyek gyors létrehozása. 
* Integrálja a CRM-sel, hogy automatikusan hozzon létre névjegyeket a névjegykártya-lemezképek használatával. 
* Tartsa nyomon az értékesítési érdeklődőket.  
* A meglévő névjegykártya-rendszerképekből tömegesen kinyerheti a kapcsolattartási adatokat. 

A Business Card API a [AIBuilder névjegykártya-feldolgozási funkciót](https://docs.microsoft.com/ai-builder/prebuilt-business-card)is felhasználja.

## <a name="next-steps"></a>Következő lépések

- Kövesse a [Business Cards API Python](./quickstarts/python-business-cards.md) gyors útmutatóját, és ismerkedjen meg az üzleti kártyák felismerésével.

## <a name="see-also"></a>Lásd még

* [Mi a Form Recognizer?](./overview.md)
* [REST API dokumentációs dokumentumok](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync)
