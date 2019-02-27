---
title: Entitások felismerése használata a szövegelemzési API
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan ismerhetik fel a Text Analytics REST API használatával.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 135b684c67776da7d722074abe185b5ed2a860fa
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889409"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics-preview"></a>Megnevezett entitások felismerése használata a Text Analytics (előzetes verzió)

A [entitás Recognition API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) strukturálatlan szöveges vesz igénybe, és egyes JSON-dokumentumok listáját adja vissza használatát entitásokat is tartalmaz, hivatkozásokkal további információhoz a weben (Wikipédia és a Bing). 

## <a name="entity-linking-and-named-entity-recognition"></a>Entitáskapcsolás és nevesített entitások felismerése

A Text Analytics `entities` mindkét végpont supprts nevű (NER) entitásfelismeréssel és -entitáskapcsolás.

### <a name="entity-linking"></a>Entitáskapcsolás
Entitáskapcsolás rendszer azon képessége, azonosíthatja és a egy entitás (például meghatározása-e a "Mars" globális vagy a latin god háború használja) szöveg található az identitás megkülönböztetéséhez. Ez a folyamat, amely felismeri a kapcsolódó entitások – Wikipedia szolgál a Tudásbázisban alapszintű ismerete szükséges a `entities` végpontot Text Analytics.

A Text Analytics [2.0-s verziójú](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634), csak entitáskapcsolás érhető el.

### <a name="named-entity-recognition-ner"></a>Nevesített entitások felismerése (NER)
Megnevezett entitások felismerése (NER) rendszer azon képessége, azonosíthatja a szöveg különböző entitásokat, és előre meghatározott osztályokba kategorizálja őket. Az entitások támogatott osztályokat alább láthatók.

A Text Analytics [2.1. dátumú előzetes Sémaverzióra](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634), entitáskapcsolás és nevesített entitások felismerése (NER) érhető el.

### <a name="language-support"></a>Nyelvi támogatás

Entitáskapcsolás különféle nyelveken használatához az egyes nyelvekhez tartozó Tudásbázis használatával. A Text Analytics entitáskapcsolás, ez azt jelenti, hogy minden nyelv által támogatott a `entities` végpont kapcsolja a megfelelő Wikipedia forrásgyűjteményébe az adott nyelveken. Korpuszok mérete nyelvek közé esik, mivel várható, hogy a funkció a visszaírási entitáskapcsolás is változnak.

## <a name="supported-types-for-named-entity-recognition"></a>Nevesített entitások felismerése támogatott típusai

| Typo  | SubType | Példa |
|:-----------   |:------------- |:---------|
| Személy        | N/A\*         | "Jeff", "Bill Gates"     |
| Hely      | N/A\*         | "Redmond, Washington", "Párizs"  |
| Szervezet  | N/A\*         | "Microsoft"   |
| Mennyiség      | Szám        | "6", "hat"     | 
| Mennyiség      | Százalék    | "50 %", "ötven százalék"| 
| Mennyiség      | Sorszám       | "2.", "a második"     | 
| Mennyiség      | NumberRange   | "4 – 8"     | 
| Mennyiség      | Kor           | "90 napnál nem régebbi", "30 évnél fiatalabb"    | 
| Mennyiség      | Currency (Pénznem)      | "$10.99"     | 
| Mennyiség      | Dimenzió     | "10 mérföld", "40 cm"     | 
| Mennyiség      | Hőmérséklet   | "32 fokos"    |
| DateTime      | N/A\*         | "6:30 = 1997031213 2012. február 4."      | 
| DateTime      | Dátum          | "2., 2017 május", "05/02/2017"   | 
| Dátum és idő     | Time          | "8 am", "8:00"  | 
| DateTime      | DateRange     | "Május 2. május 5-én a"    | 
| DateTime      | timeRange     | "du. 6, 7 pm"     | 
| DateTime      | Időtartam      | "1 perc 45 másodpercig"   | 
| DateTime      | Beállítás           | "minden kedden"     | 
| DateTime      | Időzóna      |    | 
| URL-cím           | N/A\*         | „http://www.bing.com”    |
| E-mail         | N/A\*         | „support@contoso.com” |
\* Attól függően, a bemeneti és a kinyert entitásokat, bizonyos entitások előfordulhat, hogy kihagyja a `SubType`.



## <a name="preparation"></a>Előkészítés

A JSON-dokumentumnak ilyen formátumban kell lennie: azonosító, szöveg, nyelv

Lásd: által jelenleg támogatott nyelvek [ebben a listában](../text-analytics-supported-languages.md).

Dokumentum mérete kell lennie a 5,120 karakter / dokumentum, és legfeljebb 1000 rendelkezhet gyűjteményenként (azonosítók) elemet. A kollekció elküldése a kérelem törzsében történik. A következő példa olyan bemutatásáért, előfordulhat, hogy küldje el az entitás hivatkozási célból tartalom.

```
{"documents": [{"id": "1",
                "language": "en",
                "text": "Jeff bought three dozen eggs because there was a 50% discount."
                },
               {"id": "2",
                "language": "en",
                "text": "The Great Depression began in 1929. By 1933, the GDP in America fell by 25%."
                }
               ]
}
```    
    
## <a name="step-1-structure-the-request"></a>1. lépés: A kérelem struktúra

A kérés definícióval kapcsolatos részletek megtalálhatók a [Text Analytics API hívásának módja](text-analytics-how-to-call-api.md) részben. A következő pontokat a kényelem kedvéért itt megismételjük:

+ Hozzon létre egy **POST** kérést. Tekintse át a kérelem API-dokumentáció: [Entitáskapcsolási API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634)

+ Állítsa be a HTTP-végpontot entitások kinyeréséhez. Tartalmaznia kell a `/entities` erőforrást: `https://[your-region].api.cognitive.microsoft.com/text/analytics/v2.1-preview/entities`

+ A kérés fejlécet állítsa be úgy, hogy tartalmazza a Text Analytics műveletekhez a hozzáférési kulcsot. További információkért lásd: [Végpontok és hozzáférési kulcsok megkeresése](text-analytics-how-to-access-key.md).

+ A kérelem törzsében adja meg az elemzéshez előkészített JSON-dokumentum kollekciót

> [!Tip]
> Használható a [Postman](text-analytics-how-to-call-api.md) vagy nyissa meg az **API teszt konzolt** a [dokumentációban](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) a kérés felépítéséhez és a szolgáltatásnak történő POST elküldéséhez.

## <a name="step-2-post-the-request"></a>2. lépés: A kérelem küldése

Az elemzés a kérelem megkapásakor történik meg. A szolgáltatás percenként legfeljebb 100 kérést fogad. Mindegyik kérés legfeljebb 1 MB lehet.

Ne felejtse, hogy a szolgáltatás állapot nélküli. A fiókban nem tárol semmilyen adatot. Az eredményeket azonnal visszaadja a válaszban.

## <a name="step-3-view-results"></a>3. lépés: Eredmények megtekintése

Minden POST kérés egy JSON formátumú választ ad vissza az azonosítókkal és az észlelt tulajdonságokkal.

A kimenetet visszaadása azonnali. Az eredmények adatfolyamát JSON elfogadó alkalmazáshoz küldheti vagy a kimenetet elmentheti fájlba a helyi rendszeren, majd importálható az adatokat rendezni, keresni és kezelni képes alkalmazásba.

Egy példa a kimenetre entitáskapcsolás a következő látható:

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


## <a name="summary"></a>Összegzés

Ebben a cikkben megtanulta, fogalmak és a Cognitive Services Text Analytics használatával entitáskapcsolás munkafolyamatokat. Összegezve:

+ [Entitások API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) érhető el a kiválasztott nyelveken.
+ A kérés törzsében szereplő JSON-dokumentumok azonosítót, szöveget és nyelvkódot tartalmaznak.
+ POST-kérés a `/entities` végpontra, az előfizetésre érvényes személyre szabott [hozzáférési kulcs és végpont](text-analytics-how-to-access-key.md) használatával.
+ Válasz kimenete, amely entitáskészlet (beleértve a megbízhatósági pontszámok eltolások és webes hivatkozások, minden egyes dokumentum-azonosító) áll használható bármely alkalmazásban

## <a name="see-also"></a>Lásd még 

 [A Text Analytics áttekintése](../overview.md)  
 [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)</br>
 [Text Analytics termékoldala](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Text Analytics API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634)
